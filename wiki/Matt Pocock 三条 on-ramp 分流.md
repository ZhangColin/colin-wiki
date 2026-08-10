---
type: source
title: "Matt Pocock 三条 on-ramp 分流"
domain: [AI编程]
tags: [mattpocock-skills, ask-matt, triage, diagnosing-bugs, wayfinder, 输入分流]
sources: []
source_path: "raw/articles/周一 30 issue + 3 bug + 1 新模块：先做哪个？Matt Pocock 用 3 条 on-ramp 帮你分流.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491557&idx=1&sn=5788f73bd0041864b021db4ce2904c52"
author: "[[运维有术]]"
date_published: "2026-07-31"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [三类 on-ramp, on-ramp 分流, ask-matt on-ramp]
---

# Matt Pocock 三条 on-ramp 分流

> 一句话要点：[[运维有术]] 解读 `ask-matt/SKILL.md` 开头的三类 on-ramp——`/triage`（别人提的 raw issue）、`/diagnosing-bugs`（症状清晰根因未知）、`/wayfinder`（目的地模糊的超大活）——讲清"什么输入走哪条入口"，汇入 main flow 前先分流。

## 关键要点

- **on-ramp 定义**（原文）："A starting situation that generates work, then merges onto the main flow"。判断依据是**输入的来源和形态**，不是优先级或紧迫度。
- **三类入口**：(1) 别人提的 raw bug report / 外部 feature request / 堆积 issue → `/triage`；(2) CI 红 / 行为突然异常（症状清楚根因未知）→ `/diagnosing-bugs`；(3) 全新模块 / 巨型 feature / 超出单会话且目的地未定 → `/wayfinder`。
- **"起点不是我"是 triage 的核心过滤器**：原文 "Triage is only for issues you didn't create"。`/to-tickets` 产出的工单已是 agent-ready，**不能再走 triage**。
- **triage 的 2 category + 5 state**：category（`bug`/`enhancement`）答"是什么"；state（`needs-triage`/`needs-info`/`ready-for-agent`/`ready-for-human`/`wontfix`）答"现在该谁处理"。产物是 **agent-ready brief**。
- **diagnosing-bugs 的灵魂 = Phase 1 tight feedback loop**：强制先造一个 **red-capable command**（跑就红、且只对这个 bug 红），**No red-capable command, no Phase 2**。loop 须满足：Red-capable / Deterministic / Fast（秒级）/ Agent-runnable。
- **diagnosing-bugs Phase 3 必须 3-5 个 ranked falsifiable hypotheses**，禁单假设锚定。修完后留下 regression test + seam。
- **wayfinder 的反例闸门**：开图后第一步检查有无 fog，没 fog 就关掉。wayfinder **不做实现只做决策**，产物是 issue tracker 上的 map。
- **三出口汇入点**：`/triage` → 直接 `/implement`；`/diagnosing-bugs` → 修复完即结束；`/wayfinder` → 必须 `/to-spec` → `/to-tickets` → `/implement`（**不能跳 to-spec**）。
- agent-ready brief 四要点（triage `AGENT-BRIEF.md` 契约）：Durability over precision、Behavioral not procedural、Complete acceptance criteria、Explicit scope boundaries。这套契约也是后台 AFK agent 工作的边界机制（见 [[Matt Pocock 后台 research 与主线程并行]]）。

## 详细笔记

作者明确：文中的"30 issue + 3 bug + 1 新模块"是**写作构造的示例**，非真实项目数据；不承诺 triage 减少积压等结果。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[HITL 与 AFK]]
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[Matt Pocock skills Wayfinder 官方文档]]、[[Matt Pocock wayfinder handoff 接力协议]]、[[Matt Pocock 后台 research 与主线程并行]]

## ⚠️ 矛盾 / 待澄清

- 与 [[mattpocock skills]] 概念页的 main flow 描述**互补不矛盾**：concept 页讲 `/grill-with-docs` 起步的分岔，本页讲的是**更上游**的 ask-matt 三入口如何在进 main flow 前分类输入。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock skills Wayfinder 官方文档]] · [[Matt Pocock wayfinder handoff 接力协议]] · [[Matt Pocock 后台 research 与主线程并行]] · [[Matt Pocock]] · [[运维有术]]
