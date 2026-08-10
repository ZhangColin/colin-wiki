---
type: source
title: "grill-me 实战指南"
domain: [AI编程]
tags: [mattpocock-skills, grill-me, grilling, 需求对齐, AI编程工作流]
sources: []
source_path: "raw/articles/grill-me 实战指南：让 Agent 在开工前替你把需求问干净.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491460&idx=1&sn=0e915abcfde48a6c9080bf814ccc282f"
author: "[[运维有术]]"
date_published: "2026-07-20"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [grill-me, /grill-me, grilling, /grilling]
---

# grill-me 实战指南

> 一句话要点：拆解 [[mattpocock skills]] 里 `/grill-me`（用户入口 wrapper）与 `/grilling`（12 行硬规则访谈原语）的分层关系、五条防跑偏规则，并强调 Matt 本人已公开声明**不再推荐 `/grill-me` 作为 coding 入口**。

## 关键要点

- **两个 skill 分层**：`/grill-me` 是 user-invoked 入口，正文只有一行（"Run a `/grilling` session"），靠 `disable-model-invocation: true` 只能人手敲；`/grilling` 是 model-invoked 原语，12 行正文承载五条硬规则。仓库硬规范：user-invoked skill 可调 model-invoked skill，反向不行。
- **五条规则**：①沿决策树逐分支推进（防"静态问卷一次性 30 问"）②每问必带推荐答案 ③一次只问一个 ④事实 Agent 查、决策用户答 ⑤共享理解确认才动手。
- **共享理解确认门禁**是 v1.1.0（PR #464）显式加的硬约束——Agent 准备动手前必须把决策清单回放，用户说"都对"才进实现。此前 `triage` 等 skill 把"能在 codebase 找答案"误读为"凡能查都自己答"，连决策一起吞掉；v1.1.0 才把 `fact` 与 `decision` 拆开。
- **真实失败案例 Issue #274**：`improve-codebase-architecture` 嫁接 grilling 段后变 "borderline unusable"，对单条建议硬塞 10–上百个问题。团队 2026-07-06 回复标为 bug。
- **stateless**：`/grill-me` 默认不产 `CONTEXT.md`/ADR/ticket，运行结束什么都不留；要持久化得手动写。
- **数据点（旁证，单源）**：典型 grilling session 约 45 分钟（Matt 自述）；极端可膨胀到 ~540 个问题；第三方实测 16–50 问/次。
- **⚠️ 作者态度信号（重要）**：Matt 在 AI Hero 官方页公开 Update 声明 **"I stopped using `/grill-me` for coding. I now recommend `/grill-with-docs`"**，新链路：`/grill-with-docs → /to-prd → /to-issues → tdd`，`/grill-me` 退为"窄压力测试"。

## 详细笔记

完整模拟场景是"给内部看板加导出功能"，演示 Agent 先自查 `package.json`/`routes/`/`db/schema.prisma`/`docs/`，再沿依赖链逐题追问，最后回放 8 条决策清单确认才进实现。不适合 grill-me 的反向清单六项：High-fidelity 问题（需原型）、超大工作量（换 `/wayfinder`）、纯执行任务、需长期持久化 ADR（换 `/grill-with-docs`）、gibberish/vibe 需求、hard rule 已被 harness 覆盖。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[mattpocock skills 推荐工作流速查]]
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[还在用 grill-with-docs]]、[[Matt Pocock skills Handoff 官方文档]]、[[Matt Pocock skills Wayfinder 官方文档]]、[[2026 年再看 Superpowers：grill-me 场景选型]]

## ⚠️ 矛盾 / 待澄清

- **演进/取代关系（重要）**：本文与 [[还在用 grill-with-docs]] 之间是显式的"取代"关系——Matt 本人已在 AI Hero 官方页声明 coding 场景不再推荐 `/grill-me`，改推 `/grill-with-docs`/`domain-model`。`/grill-me` 现定位为"非编码压力测试 / 临时轻量对齐"。
- 轻度矛盾：v1.1.0 之前的 grilling 规则被 `improve-codebase-architecture` 误读（Issue #274），导致"凡能查都自己答"吞掉决策；v1.1.0 才显式拆分 fact/decision。属仓库内部演进修正。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock]] · [[运维有术]] · [[还在用 grill-with-docs]] · [[Matt Pocock skills Wayfinder 官方文档]] · [[mattpocock skills 推荐工作流速查]] · [[2026 年再看 Superpowers：grill-me 场景选型]]
