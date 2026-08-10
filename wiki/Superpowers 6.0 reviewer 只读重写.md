---
type: source
title: "Superpowers 6.0 reviewer 只读重写"
domain: [AI编程]
tags: [Superpowers, Claude Code, v6.0, multi-agent, reviewer, 只读裁决者, Progress Ledger, 上下文经济学]
sources: []
source_path: "raw/articles/Superpowers 6.0 不是性能调优：158 个 commit 把 reviewer 重写成只读裁决者.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491219&idx=1&sn=fa5e181128a5ccbd324fda11a2e4ae18"
author: "[[运维有术]]"
date_published: "2026-06-22"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [Superpowers 6.0, Superpowers reviewer 重写, Superpowers v6.0.3]
---

# Superpowers 6.0 reviewer 只读重写

> 一句话要点：[[Superpowers]] 6.0（v6.0.3）表面是"约 2 倍速、近 50% 省 token"，实质是一次围绕 reviewer 角色的结构性重写——把它从可被辅导/可被绕过/可被静默升级顶配模型的配角，重写成只读、怀疑、独立、强制读文件的裁决者；提速降本是结构改造的副产品。

## 关键要点

- Release Notes v6.0.0 原文定位：a rewrite of how subagent-driven-development reviews each task — cheaper, stricter, and harder to game。
- 性能数据（官方评测，主要跑 Claude Code 与 Codex）：约 2 倍速度、近 50% token 减少；公告标题更激进"up to 50% faster and up to 60% cheaper"；官方免责声明 these numbers won't hold on every harness。
- **核心改造①：两 reviewer 合一**。5.x 每 task 后跑两轮独立 review（spec-compliance + code-quality），6.0 合并为单一 `task-reviewer`，"reads the task's diff once and returns two verdicts"。
- **核心改造②：reviewer 变只读怀疑论者**。"Do Not Trust the Report"——把 implementer report 当未经证实的说法；只读约束（曾发生 reviewer 跑 `git checkout` 致孤儿提交）；controller 禁止辅导 reviewer。
- **plan 不为自己的产物背书**：若 plan 明确要求了一件被 rubric 判为缺陷的事，仍算发现、标 Important、打 `plan-mandated` 标签；"The plan's authorship does not grade its own work; the human decides"。
- **核心改造③：文件替代粘贴（上下文经济学）**。5.x 把 task 文本和 diff 粘进 dispatch prompt，永久占用 controller context；6.0 用三脚本（`task-brief`/`review-package`/`sdd-workspace`），subagent 读文件而非接收粘贴，单独省约 10% token 和时间。
- **Progress Ledger 对抗 compaction 失忆**：git-ignored 的 `progress.md` 每完成一 task 追加一行；compaction 后 controller 读 ledger + git log 恢复进度；"trust the ledger and git log over your own recollection"。
- **Model 纪律**：5.x 派 subagent 不指定 model→静默继承 session 顶配；6.0 两模板开头强制声明 model。反直觉规律：便宜模型多步任务要跑 2-3 倍轮次反更贵，故按复杂度匹配档位。
- writing-plans 结构变更：每个 plan 以固定 header 开头，每个 task 必须带 Interfaces block（Consumes/Produces 精确签名）。
- **6.0.3 工作产物搬家**：从 `.git/sdd/` 全搬到工作树 `.superpowers/sdd/`（self-ignoring）。
- **skills 去方言化**：6.0 把所有 skills 改写为 vendor-neutral（skills speak in actions 而非命名某 runtime 工具）。
- 收尾哲学："Advisory language tests comprehension. Hard gates and checklists test compliance."——5.x 用偏建议性语言，6.0 换成硬闸门。

## 详细笔记

作者称翻遍 v6.0.3 本地仓库 158 个 commit、3 个核心 prompt 文件和 3 个 shell 脚本。[[Jesse Vincent]] 在 Heavybit 播客表态"way more expensive to make broken software"，更看重正确性而非单纯压价。

## 与已有内容的关联

- 相关概念：[[Superpowers]]、[[子代理驱动开发]]、[[mattpocock skills]]
- 相关实体：[[运维有术]]、[[Jesse Vincent]]
- 相关源：[[Superpowers 5万 Star 工程纪律框架]]（v4.3 起点）、[[Superpowers 实战指南 7步流程14技能]]（v5.0.7）、[[Superpowers 7步闭环工作流深度指南]]、[[Superpowers 7阶段交付SKU库存扣减]]

## ⚠️ 矛盾 / 待澄清

- **版本演进一致性**：本簇构成 Superpowers 版本时间线 v4.3(2026-02) → v5.0.7(2026-04) → v6.0/v6.0.3(2026-06)，6.0 的"两 reviewer 合一"是对 [[Superpowers 7阶段交付SKU库存扣减]] 与 [[Superpowers 7步闭环工作流深度指南]] 中描述的"两阶段审查"的**结构性变更**——旧页的"两轮独立审查"描述在 6.0 后已改为"单 reviewer 一次 diff 出两个裁决"。
- **存疑数据（作者已自标注）**：Pulumi/Termdock 文章称 chardet 用 Superpowers 重写后性能提升 41 倍、96.8% accuracy——单一来源、无官方确认，作者明确标注存疑，本页仅转述不采信为事实。
- 社区批评背景：4 月那波"slow me down"批评针对 5.x；[[Jesse Vincent]] 在 6.0 公告明确"slow isn't fun"是改进动机。

## 相关页面

- [[Superpowers]] · [[子代理驱动开发]] · [[Superpowers 5万 Star 工程纪律框架]] · [[Superpowers 实战指南 7步流程14技能]] · [[Superpowers 7步闭环工作流深度指南]] · [[Superpowers 7阶段交付SKU库存扣减]] · [[运维有术]] · [[Jesse Vincent]] · [[mattpocock skills]]
