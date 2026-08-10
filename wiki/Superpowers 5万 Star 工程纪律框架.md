---
type: source
title: "Superpowers 5万 Star 工程纪律框架"
domain: [AI编程]
tags: [Superpowers, Claude Code, Agent Skills, TDD, 代码审查, 工作流]
sources: []
source_path: "raw/articles/Superpowers（5万+ Star）：开发慢30%，代码稳定性+50%，测试覆盖率92%.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247489092&idx=1&sn=3d13e3573d3373431bafed997526ad11"
author: "[[运维有术]]"
date_published: "2026-02-15"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [Superpowers 5万 Star]
---

# Superpowers 5万 Star 工程纪律框架

> 一句话要点：[[Superpowers]] 是给 AI 编程代理套上"工作规范框架"的开源技能系统，用 7 阶段强制流程（头脑风暴→worktree→计划→子代理→TDD→审查→收尾）把 AI 从"急于写代码"变成"三思而后行"。

## 关键要点

- 定位：不是让 AI 更聪明，而是让 AI 更守规矩——核心理念是"不让 AI 想跳就跳"，每个技能都是一个强制性检查点。
- 创建者 [[Jesse Vincent]]（GitHub: obra），知名开源项目 RT（Request Tracker）作者、多个热门 npm 包贡献者。
- 截至 2026 年 2 月，GitHub 51,400+ stars、16+ 位贡献者，社区贡献 30 多个额外技能；MIT 协议完全免费。
- 7 阶段工作流：头脑风暴（Brainstorming）→ 工作树隔离（Git Worktrees）→ 编写计划（Writing Plans）→ 子代理开发（Subagent Development）→ 测试驱动开发（TDD）→ 代码审查（Code Review）→ 完成分支（Finishing Branch）。
- Brainstorming 设有 HARD-GATE：用户批准设计前禁止写任何代码。
- TDD 铁律：严格 RED→GREEN→REFACTOR 循环，且会验证测试确实失败；先写代码再补测试则"删掉代码，重新开始"。
- 子代理协作：把大任务拆成独立子任务并行处理，每个子任务过两轮审查（规格合规 + 代码质量），审查者是独立 AI 代理。
- 作者实测：单个功能开发时间增加约 30%，但整体项目交付时间反而缩短约 20%；实战案例测试覆盖率 92%。
- 支持平台：官方支持 Claude Code（推荐）、OpenAI Codex、OpenCode；Cursor/Windsurf/Continue 等暂不支持。本教程基于 v4.3。

## 详细笔记

安装：Claude Code 中 `/plugin marketplace add obra/superpowers-marketplace` → `/plugin install superpowers@superpowers-marketplace` → 重启。可定制自有技能放 `.superpowers/skills/`；可用 `.superpowers/config.json` 的 `disabledSkills` 关闭技能。

## 与已有内容的关联

- 相关概念：[[Superpowers]]、[[子代理驱动开发]]、[[mattpocock skills]]（对照的另一套 skills）
- 相关实体：[[运维有术]]、[[Jesse Vincent]]、[[Obsidian]]（vault 自身使用 superpowers）
- 相关源：[[Superpowers 实战指南 7步流程14技能]]、[[Superpowers 7步闭环工作流深度指南]]、[[Superpowers 7阶段交付SKU库存扣减]]、[[Superpowers 6.0 reviewer 只读重写]]

## ⚠️ 矛盾 / 待澄清

- **⚠️ Star 数严重冲突**：本文（2026-02）记 51,400+ stars；[[Superpowers 实战指南 7步流程14技能]]（2026-04-09）记 36.6K；[[Superpowers 7步闭环工作流深度指南]]（2026-04-13）记 147,000+。三篇均为 [[运维有术]] 同一系列，数字随时间应递增，但 51,400(2月) → 36,600(4月) 出现**倒挂**，且 147,000 相对 36,600 两个月暴涨约 4 倍——**高度存疑**，须以 GitHub 实时数据为准。详见 [[Superpowers]] 实体页"⚠️ Star 数溯源"区块。
- 版本号：本文基于 v4.3，与后续 v5.0.7、v6.0 为同一项目不同节点。

## 相关页面

- [[Superpowers]] · [[子代理驱动开发]] · [[Superpowers 实战指南 7步流程14技能]] · [[Superpowers 7步闭环工作流深度指南]] · [[Superpowers 7阶段交付SKU库存扣减]] · [[Superpowers 6.0 reviewer 只读重写]] · [[运维有术]] · [[Jesse Vincent]] · [[mattpocock skills]]
