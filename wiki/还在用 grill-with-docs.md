---
type: source
title: "还在用 grill-with-docs"
domain: [AI编程]
tags: [mattpocock-skills, grill-with-docs, domain-modeling, CONTEXT.md, ADR, 需求对齐]
sources: []
source_path: "raw/articles/还在用 grill-me？Matt Pocock 建议换 grill-with-docs：前者只问不落字，后者把对话搬进代码仓库.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491472&idx=1&sn=a5245aabf205b31b847e2a6142a0857e"
author: "[[运维有术]]"
date_published: "2026-07-21"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [grill-with-docs, /grill-with-docs, domain-modeling, /domain-modeling]
---

# 还在用 grill-with-docs

> 一句话要点：`/grill-with-docs` = `/grilling`（追问）+ `/domain-modeling`（落字）两个原语串联的工程入口，把需求共识从"只活在对话里"沉淀为仓库里的 `CONTEXT.md`（glossary）与 ADR（决策记录）——Matt 本人推荐的 coding 入口，取代 `/grill-me`。

> 🔖 **v1.2 追记**（2026-08-17）：本文记录的 grilling 分层（7 行入口 + 12 行原语）在 v1.2 仍成立，但 grilling 原语已重构为 round-by-round frontier（13 问约 3 轮，可 opt-out 回一次一问）。本文"一次一问"相关表述按 v1.2 前行为理解。详见 [[Matt Skills v1.2 grilling 重构]]。

## 关键要点

- **核心一句话**：`grill-with-docs/SKILL.md` 全文 7 行，实质就一句 "Run a `/grilling` session, using the `/domain-modeling` skill"——刻意把追问纪律（`grilling`，不写文件）和沉淀纪律（`domain-modeling`，落 `CONTEXT.md`/ADR）拆成两个原语，入口按需组合。
- **三入口分工**：`grill-me` = 只挑 `grilling`，stateless 不写文件，用于没代码库的纯讨论；`grill-with-docs` = 同时挑 `grilling` + `domain-modeling`，写 `CONTEXT.md`/ADR，用于真实工程；`triage`/`improve-codebase-architecture` 直接调 `domain-modeling`。
- **`grilling` 已泛化**：changeset `grilling-general-use.md` 把 grilling 从"软件计划面试"放宽到"任何 plan、decision 或 idea"，是被多上层 skill 复用的追问引擎。
- **`domain-modeling` 做六件事**：前四件是检验纪律（挑战与 `CONTEXT.md` 冲突的用法 / 锐化 overload 词 / 用具体场景压测关系 / 与代码交叉对照），后两件是沉淀纪律（术语解决那一刻立即更新 `CONTEXT.md`，不攒批 / 三条门槛同时满足时提议 ADR）。
- **ADR 三条硬门槛（缺一不可）**：① Hard to reverse ② Surprising without context ③ The result of a real trade-off。仓库原话："An ADR can be a single paragraph. The value is in recording *that* a decision was made and *why*."
- **`CONTEXT.md` 是 glossary 而非 spec**（仓库最强硬一句）："totally devoid of implementation details... It is a glossary and nothing else." 只收项目独有、非通用编程概念的术语。
- **4 个反模式**：①过早记录（还没共识就写 ADR）②术语过多（把通用编程概念塞进 glossary）③把 `CONTEXT.md` 写成 spec ④文档与代码漂移。
- **lazy creation 原则**：第一个术语确认前不要 scaffold `CONTEXT.md`，第一份满足门槛的 ADR 出现前不要建 `docs/adr/`。

## 详细笔记

连续案例（作者组装示意，非仓库真实产物）：电商团队术语混乱（`Order` 在 4 个文件里指代不清）→ grilling 逐题追问 → 解决即刻落字不攒批 → 产出 `CONTEXT.md` 三条目（每条带 `_Avoid_`）→ 关键决策同时满足 ADR 三门槛 → 写成一段话 ADR。作者明确区分仓库原文与延伸。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[mattpocock skills 推荐工作流速查]]
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[grill-me 实战指南]]、[[Matt Pocock skills Handoff 官方文档]]、[[mattpocock skills 推荐工作流速查]]

## ⚠️ 矛盾 / 待澄清

- **演进/取代关系（核心）**：本文是 `/grill-me` → `/grill-with-docs` 取代关系的"取代方"侧。Matt 在 AI Hero 官方页公开声明 coding 不再用 `/grill-me`，改推 `/grill-with-docs`。与 [[grill-me 实战指南]] 的态度信号互证。
- 轻度张力：作者把"4 反模式按强度排序、连续案例"标注为"我整理/延伸"，非仓库原文——引用时需注明是二手启发式。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock]] · [[运维有术]] · [[grill-me 实战指南]] · [[Matt Pocock skills Handoff 官方文档]] · [[mattpocock skills 推荐工作流速查]]
