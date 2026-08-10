---
type: source
title: "MattPocock Skills 21 个 skill 工程纪律体系"
domain: [AI编程]
tags: [Agent Skills, 工程纪律, v1.1.0, 架构总览]
sources: []
source_path: "raw/articles/AI 编码总失控？MattPocock Skills 用 21 个 skill 给 Agent 上工程纪律.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491394&idx=1&sn=bd4f2611e552d4089be677fdd17fd9d4"
author: "[[运维有术]]"
date_published: "2026-07-13"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [21 个 skill 工程纪律体系, 术哥 v1.1.0 总览]
---

# MattPocock Skills 21 个 skill 工程纪律体系

> 一句话要点：[[运维有术]]（术哥）基于 v1.1.0 源码的全景总览，主张这套 skill 的内核是"几十年软件工程纪律的 Agent 化"，而非 AI 技巧。

## 关键要点

- v1.1.0 含 21 个正式 skill（参见下方矛盾区——另有源称 v1.1.0 promoted skill 实为 18 个）；定位面向真实工程，不是 vibe coding。
- 四大痛点对应四本经典：不听话→沟通鸿沟（grill-me/grill-with-docs，《人月神话》式 feedback loop）；太啰嗦→缺共享语言（domain-modeling + CONTEXT.md，Eric Evans ubiquitous language）；跑不通→无反馈循环（tdd/diagnosing-bugs）；代码变烂→熵增（to-spec/improve-codebase-architecture，John Ousterhout deep modules、Michael Feathers seam）。
- 分层架构铁律：user-invoked（`disable-model-invocation: true`，零 context load）vs model-invoked（省略该字段，description 占每轮上下文）；**user-invoked skill 永远不能调用另一个 user-invoked skill**，这是 `ask-matt` 作为路由器存在的理由。
- v1.1.0 关键演进：**facts 与 decisions 分离**（facts 自己查代码、decisions 必须问用户），并加 confirmation gate 阻断未确认就实施；命名重构：`to-prd`→`to-spec`，`to-issues`+`to-plan`→`to-tickets`，"spec"成为统一术语。
- `to-tickets` 三概念：tracer bullet（垂直切片切透 schema/API/UI/tests）、blocking edges（每张票据声明阻塞依赖）、frontier（所有 blockers 完成的票据可被领取）。
- `tdd` v1.1.0 重构为纯 reference 文档，移除 refactor 阶段（refactor 划给 code-review）；三大测试反模式：Implementation-coupled、Tautological、Horizontal slicing。
- `code-review` 两轴并行（作为 parallel sub-agents）：Standards（repo 文档化标准+Fowler 12 smell）、Spec（是否忠实 PRD）；repo 自带文档永远盖过 baseline，12 smell 全是判断题无硬性违规。
- wayfinder 是 v1.1.0 正式化 skill；概念体系：Destination / Map（带 `wayfinder:map` 标签的 issue）/ Fog of war / Frontier；铁律"never resolve more than one ticket per session"；定位"plan, don't do"。
- `diagnosing-bugs` Phase 1 是核心（原文"This is the skill"）：构造 tight + red-capable 反馈循环，四判据 Red-capable/Deterministic/Fast（秒级）/Agent-runnable；禁止先读代码猜原因。
- `writing-great-skills` 元设计词汇：Context load / Cognitive load / Progressive disclosure / Leading word / No-op / Negation / Negative Space；v1.1.0 新增 Negation（"不要做 X"反激活 X）与 Negative Space 两种失败模式。
- 安装必须选中 `setup-matt-pocock-skills`，初始化探测 repo 状态、配置 issue tracker、写 `docs/agents/*.md`。

## 详细笔记

CONTEXT.md 被作者称为"single coolest technique"——严格定位为 glossary，"totally devoid of implementation details"。ADR 三条触发条件须同时满足：Hard to reverse、Surprising without context、The result of a real trade-off。项目用 changesets versioning，把 skill 当可发布/演进/维护的软件包。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[mattpocock skills 推荐工作流速查]]
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[MattPocock Skills v1.1 重磅更新]]、[[Matt Pocock skills v1.1 官方更新日志]]、[[mattpocock skills 标准工作流]]、[[Matt Pocock 6 个 skill 工程师本能]]

## ⚠️ 矛盾 / 待澄清

- **skill 数量口径不一**：本文（早期写作）称 v1.1.0 含 21 个正式 skill；而 [[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]] 基于 GitHub API 源码核对，称 v1.1.0 promoted skill 实为 **18 个**（12 user-invoked + 6 model-invoked），并指出"21 是早期流传数"。两说并存，建议以源码核对（18）为准，本文"21"为早期口径。详见 [[mattpocock skills]] 概念页口径说明。
- smart zone token 数字：本文未直接给数，但同作者后续 main flow 文章给出三处不一致（ask-matt 120k / dictionary 125-150k / YouTube 140k）——见 [[smart zone]]。

## 相关页面

- [[mattpocock skills]]
- [[运维有术]]
- [[Matt Pocock skills v1.1 官方更新日志]]
- [[Matt Pocock main flow 五环节]]
