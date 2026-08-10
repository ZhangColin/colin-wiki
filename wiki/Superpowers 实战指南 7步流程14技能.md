---
type: source
title: "Superpowers 实战指南 7步流程14技能"
domain: [AI编程]
tags: [Superpowers, Claude Code, Agent Skills, TDD, 子代理开发, 工作流, 强制触发]
sources: []
source_path: "raw/articles/Superpowers 实战指南：7 步流程 + 14 个技能 + 3 条铁律，搭建让 AI 编程更稳、更守规矩的工作流.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247490369&idx=1&sn=1ce412f444a0dd6a479fd84574e7f017"
author: "[[运维有术]]"
date_published: "2026-04-09"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [Superpowers 14技能3铁律, Superpowers 实战指南]
---

# Superpowers 实战指南 7步流程14技能

> 一句话要点：把 [[Superpowers]] 官方文档与源码翻透，梳理出 14 个 Skills（四类）、7 步工作流、3 条铁律，并按"新项目/老项目加功能/修 bug"三场景给出裁剪后的流程。

## 关键要点

- [[Superpowers]] = 给 AI 编程 Agent 的标准化开发流程；当前版本 v5.0.7（2026-03-31），GitHub 36.6K Star，MIT 协议；核心理念八字："不是更强，而是更稳"。
- 支持六平台：Claude Code、Cursor、Codex、OpenCode、Gemini CLI、GitHub Copilot CLI（不绑定单一工具是其优势）。
- 7 步工作流：`brainstorming → using-git-worktrees → writing-plans → subagent-driven-development → test-driven-development → requesting-code-review → finishing-a-development-branch`。
- 14 个 Skills 四分类：协作类 9 个；测试类 1 个（test-driven-development）；调试类 2 个（systematic-debugging、verification-before-completion）；元类 2 个（writing-skills、using-superpowers）。
- **强制触发机制**：存在适用技能则 Agent 必须使用、无选择余地；其设计坦承借用了 Cialdini 的说服学原理——权威性、承诺、社会证明（详见 [[Superpowers]]）。
- 三场景三流程：新项目走完整 7 步；老项目加功能走完整 7 步（原则"入乡随俗"——遵循现有架构、不提议无关重构）；修 bug 精简到 3 步（systematic-debugging → TDD → verification-before-completion）。
- systematic-debugging 四阶段：根因调查（只收集信息不改）→ 模式分析 → 假设与测试 → 实施修复。
- **三条铁律**：① 没有失败测试就不写生产代码（TDD）；② 不做根因调查就不修 bug；③ 没有新鲜验证证据就不做完成声明（强调"刚刚验证过"）。三条对抗三种典型偷懒。
- writing-plans 任务粒度经验值：2-5 分钟一个 action，每个任务含精确文件路径、完整代码（禁止 TBD/TODO 占位符）、验证步骤。

## 与已有内容的关联

- 相关概念：[[Superpowers]]、[[子代理驱动开发]]、[[mattpocock skills]]
- 相关实体：[[运维有术]]、[[Jesse Vincent]]
- 相关源：[[Superpowers 5万 Star 工程纪律框架]]、[[Superpowers 7步闭环工作流深度指南]]、[[Superpowers 7阶段交付SKU库存扣减]]

## ⚠️ 矛盾 / 待澄清

- **Star 数冲突**：本文（2026-04-09）记 36.6K，早于本文两月的 [[Superpowers 5万 Star 工程纪律框架]] 却记 51,400+——数字倒挂，存疑（详见 [[Superpowers]] 实体页）。
- **安装命令不一致**：本文给 Claude Code 用 `/plugin install superpowers@claude-plugins-official`，而 [[Superpowers 5万 Star 工程纪律框架]] 用 `marketplace add obra/superpowers-marketplace` + `install superpowers@superpowers-marketplace`；可能是版本/市场变更，以官方 README 为准。
- 与 [[mattpocock skills]] 的对照：Superpowers 重"流程纪律 + 强制触发 + 多平台"，mattpocock skills 重"skill 工程化标准（SKILL.md 结构、Wayfinder、Handoff）"，是两条不同路线。

## 相关页面

- [[Superpowers]] · [[子代理驱动开发]] · [[Superpowers 5万 Star 工程纪律框架]] · [[Superpowers 7步闭环工作流深度指南]] · [[Superpowers 7阶段交付SKU库存扣减]] · [[Superpowers 6.0 reviewer 只读重写]] · [[运维有术]] · [[Jesse Vincent]] · [[mattpocock skills]]
