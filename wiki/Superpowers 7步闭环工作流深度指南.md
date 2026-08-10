---
type: source
title: "Superpowers 7步闭环工作流深度指南"
domain: [AI编程]
tags: [Superpowers, Claude Code, 优惠券核销, TDD, 子代理开发, 代码审查, 三层架构]
sources: []
source_path: "raw/articles/Superpowers 最佳实战：标准开发 7 步法，从需求到交付工程级代码的闭环工作流深度指南.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247490410&idx=1&sn=068034869707c6d2f0abfcfd5a2dbaab"
author: "[[运维有术]]"
date_published: "2026-04-13"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [Superpowers 优惠券核销实战, Superpowers 深度指南]
---

# Superpowers 7步闭环工作流深度指南

> 一句话要点：以"优惠券核销 API"为实战主线端到端走完 [[Superpowers]] 7 阶段，提炼核心哲学"Skills are not prose — they are code that shapes agent behavior"。

## 关键要点

- 核心哲学原文："AI 不是用来写代码的，而是用来执行严格工程标准的"；Skills 不是散文式建议，而是代码级精确指令塑造 Agent 行为。
- **三层架构**：Agent 集成层（Claude Code/Cursor/Codex/OpenCode/Gemini CLI/Copilot CLI）／工作流层（强制流程控制）／Skills 层（14 个独立 SKILL.md，可发现、可调用、可组合）。
- brainstorming 反模式提醒："This Is Too Simple To Need A Design"——"Simple"项目是未审查假设导致浪费工作最多的地方。
- using-git-worktrees 把执行者假设为 "an enthusiastic junior engineer with poor taste, no judgement, no project context"，故必须隔离。
- writing-plans 三规矩：禁止 TBD/TODO 占位符；禁止跨任务引用；自查三要素（规格覆盖、占位符扫描、类型一致性）。
- subagent-driven-development 核心公式："Fresh subagent per task + two-stage review"；模型选择原则——机械实现用快速/廉价模型、集成判断用标准模型、架构/审查用最强模型，"不要用旗舰级模型去定义 DTO"。
- TDD 铁律原文："NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST"、"If you didn't watch the test fail, you don't know if it tests the right thing"；发现先写代码则 "Delete it. Start over."。
- requesting-code-review 三个强制审查时机（每个任务后/重大功能后/合并 main 前），结果分 Critical（阻塞）/Important（继续前修）/Minor（记下稍后）。
- verification-before-completion Gate Function：IDENTIFY→RUN→READ→VERIFY 四步，原文 "Claiming work is complete without verification is dishonesty, not efficiency."。
- 社区实测反馈："跨 15 文件功能，Claude Code 写到一半乱改之前代码需手动回滚；用 Superpowers 同任务自主跑两小时未偏离计划。"

## 详细笔记

实战主线"优惠券核销 API"覆盖三难点：核销状态转换、过期/已用检查、并发安全（对比 Redis 分布式锁 / 数据库乐观锁 / 悲观锁三方案）。相关资源：GitHub github.com/obra/superpowers；中文增强版 github.com/jnMetaCode/superpowers-zh；[[Jesse Vincent]] 首发博文 blog.fsck.com/2025/10/09/superpowers/。

## 与已有内容的关联

- 相关概念：[[Superpowers]]、[[子代理驱动开发]]、[[mattpocock skills]]
- 相关实体：[[运维有术]]、[[Jesse Vincent]]
- 相关源：[[Superpowers 5万 Star 工程纪律框架]]、[[Superpowers 实战指南 7步流程14技能]]、[[Superpowers 7阶段交付SKU库存扣减]]、[[Superpowers 6.0 reviewer 只读重写]]

## ⚠️ 矛盾 / 待澄清

- **⚠️ Star 数冲突（重点）**：本文（2026-04-13）记 GitHub 147,000+ Stars。这与 [[Superpowers 实战指南 7步流程14技能]]（仅早 4 天，2026-04-09）的 36.6K、[[Superpowers 5万 Star 工程纪律框架]]（早 2 月）的 51,400+ 严重不一致。147K vs 36.6K 四天内差 4 倍——高度存疑，疑为笔误或夸大，须以 GitHub 实时为准（详见 [[Superpowers]] 实体页）。
- 与 [[mattpocock skills]] 对照：两者都把 skill 当"塑造 agent 行为的代码"，但 Superpowers 强调 7 阶段闭环 + 强制触发，mattpocock skills 强调 SKILL.md frontmatter 规范 + Wayfinder/Handoff 交接机制。

## 相关页面

- [[Superpowers]] · [[子代理驱动开发]] · [[Superpowers 5万 Star 工程纪律框架]] · [[Superpowers 实战指南 7步流程14技能]] · [[Superpowers 7阶段交付SKU库存扣减]] · [[Superpowers 6.0 reviewer 只读重写]] · [[运维有术]] · [[Jesse Vincent]] · [[mattpocock skills]]
