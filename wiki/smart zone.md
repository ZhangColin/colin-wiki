---
type: concept
title: smart zone
domain: [AI编程, 软件工程]
tags: [smart zone, dumb zone, context window, 会话管理, LLM 局限]
sources:
  - "[[Matt Pocock main flow 五环节]]"
  - "[[Matt Pocock wayfinder handoff 接力协议]]"
  - "[[Matt Pocock skills Wayfinder 官方文档]]"
  - "[[mattpocock skills]]"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases:
  - 聪明区
  - smart zone / dumb zone
  - context window 阈值
---

# smart zone

> 一句话定义：LLM 在会话早期处于"聪明区（smart zone）"，随 token 累积漂入"笨区（dumb zone）"——注意力稀薄、faithfulness 幻觉增加；是 [[mattpocock skills]] 决定何时拆 session / 何时 handoff 的核心判据。

## 核心观点

smart zone 是 [[Matt Pocock]] 的经验法则：LLM 在一定 token 预算内表现"聪明"，超出后质量下滑。这条线决定了 [[mattpocock skills]] 的多处设计：

- **main flow 的会话切分**：grill+spec+tickets 共享一长会话（约 80-100k tokens），但**每个 `/implement` 开新会话只读 ticket**——避免 implement 时 session 已漂入 dumb zone。
- **wayfinder 的"一 session 一 ticket"铁律**：ticket 间会互相污染 working state。
- **handoff 的存在理由**：会话快到 smart zone 边界时，`/handoff` 把上下文搬到新会话。
- Matt 原话："A ticket should be completable before the session drifts out of the smart zone — and that constraint is testable."

## ⚠️ 阈值数据冲突（重要，未收敛）

不同一手源给的阈值不一致，源码自标 `this is debated`，**别当硬阈值**：

| 来源 | 阈值 |
| --- | --- |
| `ask-matt/SKILL.md` | ~120K tokens |
| `dictionary-of-ai-coding` | 125K–150K tokens |
| YouTube 视频（Matt 口述） | ~140K tokens |
| AI Engineer workshop（社区） | ~100K tokens |
| [[Matt Pocock skills Wayfinder 官方文档]] | ~140K |

**处理建议**：引用时取区间"frontier 模型约 **120K–150K tokens**"，注明"经验法则、具体数字有争议（源码自标 debated）"，不要把任一数字当硬阈值。不同模型、不同任务复杂度下实际漂移点不同。

## 机制理解（非硬阈值）

smart zone 的本质不是某个精确 token 数，而是：随上下文增长，模型对早期内容的注意力衰减、更容易产生 faithfulness 幻觉、更容易偏离规格。理解这一点比记住某个数字更重要——所以 [[Matt Pocock]] 强调"用文件物化纪律"（见 [[Matt Pocock main flow 五环节]]："session 会变笨，文件不会"）。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock main flow 五环节]] · [[Matt Pocock wayfinder handoff 接力协议]] · [[Matt Pocock skills Wayfinder 官方文档]] · [[Matt Pocock skills Handoff 官方文档]] · [[HITL 与 AFK]]
