---
type: source
title: "Matt Pocock wayfinder + handoff 接力协议"
domain: [AI编程]
tags: [mattpocock-skills, wayfinder, handoff, 跨会话协作, 接力协议]
sources: []
source_path: "raw/articles/Matt Pocock wayfinder + handoff：AI Agent 跨 5 次会话接力赛不掉链.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491548&idx=1&sn=ace3c884ae515ee001127474ba3b0d31"
author: "[[运维有术]]"
date_published: "2026-07-30"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [wayfinder handoff 接力, 跨会话接力协议, 5 次会话接力]
---

# Matt Pocock wayfinder + handoff 接力协议

> 一句话要点：[[运维有术]] 扒开 `wayfinder/SKILL.md` 与 `handoff/SKILL.md`，讲清"工作本身跨多会话（wayfinder）"与"单会话快到边界（handoff）"两种"装不下"的区别，并附一张 5 次会话接力同一张 map 的虚构迁移案例。

## 关键要点

- **本质区别**：wayfinder 解决"工作本身就需要多会话"的装不下，一次启动就为多会话协作铺路；handoff 解决"当前会话快到边界"的装不下，本一两会话能干完只是要搬上下文。
- **smart zone 阈值**（来自 `dictionary-of-ai-coding`）：frontier 模型 dumb zone 常在 **125K-150K tokens** 开始；`ask-matt` 简化为 **~120K**；社区另有 100K 说法。原文自标 `this is debated`，**别当硬阈值**（详见 [[smart zone]]）。
- **三选一判断**：目的地清晰 + 一两会话能搞定 → main flow；会话快到 smart zone 但任务不大 → `/handoff`；任务太大 + 雾太大 + 说不清目的地 → `/wayfinder`。
- **map 是索引不是仓库**（原文 "an index, not a store"）：`Decisions so far` 每行只索引到已关闭 ticket，正文不存决策；在 map 正文写长篇决策 = 违反纪律。
- **Not yet specified 的入场判据**：能否**提出**精准问题（不是能否回答）。Out of scope 的判据是 "Scope, not sharpness"——清楚知道不在 destination 内，**永不 graduate**。
- **每会话最多解 1 个 ticket**：因 ticket 间会互相污染 working state；research 例外（AFK 可并发）。
- **HITL 铁律**：grilling 必须 HITL，agent 不能自答自问——"a grilling agent that answers its own questions has broken this"。社区 issue #667 反映 `wayfinder:task` 在 HITL/AFK 间摇摆未收敛；issue #683 讨论 Notes 的 `effort` 字段可覆盖 `Plan, don't do` 默认（留出越权执行口子）。
- **handoff 5 条硬约束**：存 OS 临时目录、必须含 `suggested skills`、不重复 specs/plans/ADRs/issues/commits/diffs（只引 path/URL）、脱敏、用户参数作为下次 focus。
- **/handoff forks，/compact continues**：handoff 开新会话引用文件；compact 留同会话压摘要（有损）。
- **map 清空后必须回 main flow**：wayfinder 推进到 "way is clear" 后走 `/to-spec` → `/to-tickets` → `/implement`，**不能直接跳 `/implement`**。
- 前置依赖：`setup-matt-pocock-skills` 必须在其他 engineering skill 首次使用前跑一次。

## 详细笔记

作者用虚构的 SQLite → ClickHouse 迁移任务，演示 5 次会话接力链：会话 1 chart（产 map 不解 ticket）→ 会话 2-4 各解 1 个 frontier ticket → 会话 5 map 清空回 main flow。严格区分"源码事实"（SKILL.md 可查原文）与"作者实践建议"，并明确**不承诺** wayfinder 减少返工、handoff 保留全部上下文——这两条源码都没写。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[smart zone]]、[[HITL 与 AFK]]
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[Matt Pocock skills Wayfinder 官方文档]]、[[Matt Pocock skills Handoff 官方文档]]

## ⚠️ 矛盾 / 待澄清

- **smart zone 阈值数据冲突**：本源引 `dictionary-of-ai-coding` 的 **125K-150K** + `ask-matt` 的 **~120K** + 社区 100K；而 [[mattpocock skills]] 概念页与 [[Matt Pocock skills Wayfinder 官方文档]] 用 ~140k。源码自标 `this is debated`，属**未收敛的经验值**——已沉淀到 [[smart zone]] 统一标注区间。
- 与 [[Matt Pocock skills Handoff 官方文档]] 的承载模型一致；本源把"用户参数作为 focus"单列，是同一件事的不同切分，无实质矛盾。
- issue #667（task 类型 HITL/AFK 摇摆）与 #683（effort 覆盖 plan-only）是**官方未解决的边界争议**。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock skills Wayfinder 官方文档]] · [[Matt Pocock skills Handoff 官方文档]] · [[Matt Pocock]] · [[运维有术]] · [[smart zone]] · [[HITL 与 AFK]]
