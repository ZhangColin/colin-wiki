---
type: source
title: "DeltaBox Lite 终端文件沙箱"
domain: [AI编程, 系统工程, AI工具]
tags: [DeltaBox, 文件沙箱, CB-CoW, Copy-Down, 事务文件系统, FUSE, DeepSeek Harness, Pi, 审批]
sources: []
source_path: "raw/articles/DeltaBox Lite：面向终端 AI Agent 的轻量文件沙箱.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/uFswgOfOJK-yrYwZslTDaA"
author: "[[IPADS]]"
date_published: "2026-10-16"
created: 2026-10-28
updated: 2026-10-28
status: active
aliases: [DeltaBox Lite 发布文, 终端 AI Agent 文件沙箱]
---

# DeltaBox Lite 终端文件沙箱

> 一句话要点：[[IPADS]] 开源的终端 AI Agent 轻量文件沙箱——原生事务级 **Diff / Commit / Abort** 语义 + 审批机制，核心创新 **CB-CoW（Copy-Down 写时复制）** 把零开销路径留给大概率发生的「提交」。「把尝试交给 Agent，把选择留给你。」

## 关键要点

- **解决的三大焦虑**：改动黑盒（Agent 多步工具调用改了什么不清楚）、脏工作区难回滚（git status/checkout 恢复不了未跟踪文件）、Docker/VM 隔离太重（冷启动迟缓、与宿主环境割裂）。主张：**每一次改动都应可验证、可保留、可撤销**。
- **核心设计：Copy-Up vs Copy-Down 的成本不对称**——
  - 传统 Copy-Up（OverlayFS/容器）：宿主挂只读底层，写时先复制到上层 → **Abort-biased**：撤销 O(1)（丢弃上层），但提交要写回底层（关键路径阻塞）。适合大多数结果会被丢弃的任务。
  - **CB-CoW Copy-Down（本文首创）**：直接写宿主工作区，覆盖前在 FUSE 拦截层把旧数据块记为 Undo Log → **Commit-biased**：提交 O(1)（日志标退役+异步删），撤销才逆序重放日志。洞察：**日常个人编程中大多数 Agent 改动最终被采纳**——零开销路径应留给大概率事件；数据直接落盘，VS Code/LSP/热重载实时可见（体验无割裂）。
  - 两种模式并存，`--fs-mode` 启动时选择。
- **会话状态机**：EXEC（受策略约束执行）→ DIFF（汇总本轮变更，含二进制标记）→ COMMIT（冻结为可信基线）/ ABORT（全局回滚+清除脏文件）。回滚点三级：CLI 单命令 / Task 级（对话边界，内部循环只呈现累积修改）/ Tool 级（单次敏感工具调用）。
- **生态**：独立 CLI（`deltabox run --abort` 试运行撤销；`session start/exec/diff/commit` 持久会话）+ **DeepSeek Harness 插件** `dsh-deltabox`（每轮改动弹审批控制台：Allow Once 固化 / Reject 回滚并反馈 Agent，含桌面版）+ **Pi 扩展**（`/sandbox-review|commit|abort`）。
- **性能**：实测超 Bubblewrap+Git；局部写入延迟优于 Copy-Up 的 Sandlock；空指令调用 13–14ms 且**不随文件数（10→1000）退化**。
- 定位边界：面向**终端侧**个人开发者（轻量 + 系统原生），与云端/RL 场景的传统沙箱区分。

## 详细笔记

开源：`github.com/delta-box/deltabox-lite`（文档 deepwiki）。本篇是 IPADS 官方发布文（系统视角的第一手设计论述），含 Copy-Up/Copy-Down 对比图、状态机图、三个演示 gif 与性能图（`raw/assets/Image 228-234`、`Image 6-8.gif`）。行为语义（试运行→审查→采纳/撤销）与 git 的工作流同构，但把「未跟踪文件、非 git 目录、连续多命令」一并纳入事务——补 git 在工作区卫生上的盲区（推论）。

## 与已有内容的关联

- **DSH 集成**：dsh-deltabox 是 [[DeepSeek Harness]] 的插件（Everything is a plugin 的又一实例）；Pi 扩展与 [[翟星人的实验室]]（Pi 深度玩家视角）同一 Agent 生态。
- **HITL 边界的文件系统级实现**：Allow Once/Reject 审批面板把 [[HITL 与 AFK]] 的「最终决定权握在人手里」下沉到 FS 事务层；与 git 护栏（[[Matt Pocock 的 Git 护栏]]）、Superpowers 分支收尾（[[Superpowers finishing-a-development-branch 分支收尾]]）同为「尝试可撤销」装置谱系——DeltaBox 把它做成运行时原语而非流程纪律。
- **与 worktrees 对照**：[[Superpowers using-git-worktrees 工作树隔离]]「隔离靠目录不靠自觉」用 git 分支做隔离，DeltaBox 用 CoW 层做隔离——同一需求的系统级方案（推论）。

## ⚠️ 矛盾 / 待澄清

- 与 wiki 既有记载无冲突；新领域首源。
- 注记：性能对比数据（Bubblewrap+Git / Sandlock）均出自官方发布文自测口径，无第三方复核——引用时注明来源层级。CB-CoW 称「首次提出」为官方表述。

## 相关页面

- [[DeltaBox Lite]] · [[IPADS]] · [[DeepSeek Harness]] · [[翟星人的实验室]] · [[HITL 与 AFK]] · [[Superpowers using-git-worktrees 工作树隔离]] · [[Superpowers finishing-a-development-branch 分支收尾]] · [[Matt Pocock 的 Git 护栏]]
