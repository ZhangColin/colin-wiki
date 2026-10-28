---
type: source
title: "Superpowers 安装与技能全景"
domain: [AI编程, AI工具, 软件工程]
tags: [Superpowers, 老谭superpowers系列, 插件安装, 技能分类, SpringBoot, Vue]
sources: []
source_path: "raw/articles/01｜安装：五分钟把 superpowers 装进 claude code.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484230&idx=1&sn=39f4ff9211d0a43fb8af2511da2c0464"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-24"
created: 2026-09-10
updated: 2026-09-10
status: active
aliases: [superpowers 安装, superpowers 技能全景, 18 个技能]
---

# Superpowers 安装与技能全景

> 一句话要点：手把手三步把 superpowers 装进 Claude Code，给出 **18 个技能四分类全景图**（2026-08 仓库口径），并顺手搭起「幸福账本」前后端双工程骨架（Spring Boot 3 + Vue 3）。

## 关键要点

- **定位纠偏**：superpowers 不是独立 App/服务，是 **Claude Code 插件**——装进去注入一套技能；技能 = "写给 AI 看的标准作业流程"（菜谱/员工手册比喻）。金句："AI 最怕的不是规矩多，是没规矩。"
- **安装三步**：`/plugin marketplace add obra/superpowers-marketplace` → `/plugin install superpowers@superpowers-marketplace` → `/plugin list` 验证。装完手动命令只有三张入场券：`/brainstorm`（brainstorming）、`/write-plan`（writing-plans）、`/execute-plan`（executing-plans），其余技能按场景自动激活——"不是一堆命令，而是一套会自动上身的习惯"。
- **18 技能四分类全景**（按 obra 仓库分类）：
  - collaboration 9：brainstorming / writing-plans / executing-plans / subagent-driven-development / dispatching-parallel-agents / using-git-worktrees / finishing-a-development-branch / requesting-code-review / receiving-code-review
  - testing 3：test-driven-development / **condition-based-waiting** / **testing-anti-patterns**
  - debugging 4：systematic-debugging / **root-cause-tracing** / verification-before-completion / **defense-in-depth**
  - meta 2：using-superpowers / writing-skills
- **双工程骨架**：后端 `happy-ledger`（Spring Boot 3.3.5 + Java 21 + H2 文件库 `jdbc:h2:file:./data/happy-ledger` + JPA，领域对象 Entry/Category/EntryType 用不可变 `record`，ownerId 区分家庭账 1 / 乐乐零花钱 2）；前端 `happy-ledger-web`（Vite + vue-router + Element Plus，`/api` 代理到 8080 免跨域）。
- **全系列第一纪律**：金额断言必须 `isEqualByComparingTo` 不能 `isEqualTo`（`BigDecimal("88.50")` vs `"88.5"` scale 不同会误判）——"现在偷的懒，都是月底对账时流的泪"。

## 详细笔记

老王视角的误会（"装完啥也看不见"）引出"技能自动上身"的解释。技能文件首次运行时铺到 `.claude/skills` 或 `.agents/skills`，可直接读 `SKILL.md` 原文。注意事项：命令以官方 README 为准别硬背；双工程要一起起；BigDecimal 从第一篇开始守。

## 与已有内容的关联

- 安装命令与 [[Superpowers]] ⚠️ 区两口径中的 `obra/superpowers-marketplace` 一致（另一口径 `claude-plugins-official`），不新增分歧。
- 18 技能口径 vs v5.0.7（2026-04）14 技能 → 已登记到 [[Superpowers]]「技能数口径」⚠️ 区，判为仓库版本演进。
- condition-based-waiting / testing-anti-patterns 为本 wiki 首次收录内容的技能（→ [[Superpowers condition-based-waiting 条件等待]]、[[Superpowers testing-anti-patterns 测试反模式]]）。

## ⚠️ 矛盾 / 待澄清

- **技能数口径（重要）**：本文 18 个（2026-08 仓库），wiki 既有 [[Superpowers 实战指南 7步流程14技能]] 为 14 个（v5.0.7，2026-04）。差异恰好是 testing +2（condition-based-waiting / testing-anti-patterns）与 debugging +2（root-cause-tracing / defense-in-depth）——判为**版本演进而非矛盾**，引用技能数量必须带时点/版本。
- 与 mattpocock skills 的 skill 数演进（18→24 promoted）同构：**skill 数口径随版本漂移**是两条路线共同现象。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers 开篇 幸福账本]] · [[Superpowers 实战指南 7步流程14技能]] · [[Superpowers brainstorming 需求访谈]] · [[幸福账本 REST API 基建]]
