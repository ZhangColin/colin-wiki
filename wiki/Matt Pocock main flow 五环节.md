---
type: source
title: "Matt Pocock main flow 五环节"
domain: [AI编程]
tags: [Agent Skills, main flow, 会话管理, tracer bullet, code-review]
sources: []
source_path: "raw/articles/Matt Pocock main flow：5 环节 3 反例，把 AI 拽回工程纪律.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491540&idx=1&sn=6aefb6205d6c42a18907a7825ed502ce"
author: "[[运维有术]]"
date_published: "2026-07-29"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [main flow 五环节三反例, 术哥 main flow, 多格式导出案例]
---

# Matt Pocock main flow 五环节

> 一句话要点：用"多格式导出"案例从头走完 main flow 五环，主张 main flow 的本质是"文件即纪律"——把思考物化成文件，让纪律穿越会话边界（session 会变笨，文件不会）。

## 关键要点

- main flow 真问题："session 是 disposable 的，但大块工作不是"；目标是把工程纪律物化成文件。Matt 原话"A ticket should be completable before the session drifts out of the smart zone — and that constraint is testable."
- **⚠️ smart zone token 数三处不一致**：ask-matt 写 ~120k、dictionary-of-ai-coding 写 125k–150k、YouTube 视频说 140k——同作者三个来源互相打架（详见 [[smart zone]]）。
- 五环节产物对照：①grill-with-docs 产 `CONTEXT.md`+`docs/adr/*.md`+精炼问题定义；②to-spec 产 spec 发 issue tracker 打 `ready-for-agent`；③to-tickets 产 tracer-bullet ticket 带 blocking edges；④implement 产 tdd 红绿代码；⑤code-review 产 Standards+Spec 两份独立报告。
- `/grill-with-docs` SKILL.md 全文仅 7 行："Run a `/grilling` session, using the `/domain-modeling` skill."——它是包装 skill，本身不写代码。
- `/to-spec` 核心硬规则："Do NOT interview the user — just synthesize what you already know."；grilling 结束时已做大量设计决策，是"pure gold"，清空 context 重写 PRD 等于扔掉设计。
- 第二条硬规则："Use the highest seam possible... The fewer seams across the codebase, the better - the ideal number is one."——seam 必须在 spec 阶段定，implement 阶段无权决定。
- `/to-tickets` tracer bullet 定义原文："Each slice cuts a narrow but COMPLETE path through every layer (schema, API, UI, tests) — vertical, NOT a horizontal slice"；引用《The Pragmatic Programmer》"outrunning your headlights"批评 AI"一口气写完整个 feature"。
- blocking edges 边界澄清：ask-matt 源码只描述串行模型；dictionary 承认叶子节点可并发是官方认可的扩展——叶子可并发，但每个 implement 仍各自一 session。
- `/implement` SKILL.md 全文 15 行；三条隐含规则：①pre-agreed seams；②between each ticket clear context（grill+spec+tickets 共享一长会话，**每个 implement 开新会话只读 ticket**）；③closing out with code-review（无例外，纯内部重构可走轻量 review）。
- `/code-review` 两轴并行机制：diff 喂两个并行子代理，原文"Reporting them separately stops one axis from masking the other"；并行的理由是防 Standards 判"实现有 bug"稀释 Spec 发现。
- 三个反例（"链接断在中间"）：①跳过 to-spec 直接 to-tickets→Spec 轴判无可判；②跳过 to-tickets 直接 implement→大 ticket 装多 seam、context 爆掉无 handoff 锚点；③跳过 code-review 直接 commit→Standards 漂移+Spec drift。
- 规模判断：小改动可跳过 grill+spec；中型 feature 走 5 环但 spec 可简化、ticket 控 3-5 张；大型/greenfield 走 5 环+wayfinder+多次 handoff。

## 详细笔记

作者明确不写"减少返工/减少 token/减少事故"等承诺（"vibe coding 时代被反复证伪的承诺"），只卖"让 AI 编程有工程纪律"。Reddit 有用户自报"质量优先版本约 1 小时/小 issue"，但 Matt 自己没要求这样串。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[mattpocock skills 推荐工作流速查]]、[[smart zone]]、[[HITL 与 AFK]]
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[mattpocock skills 标准工作流]]、[[Matt Pocock skills Wayfinder 官方文档]]、[[Matt Pocock skills Handoff 官方文档]]、[[MattPocock Skills 21 个 skill 工程纪律体系]]

## ⚠️ 矛盾 / 待澄清

- **smart zone token 数三处不一致**（120k / 125-150k / 140k），来自 ask-matt、dictionary-of-ai-coding、YouTube 三个一手源——属 Matt 本人不同场合口径不一，已沉淀到 [[smart zone]] 概念页统一标注为"约 120k–150k（有争议）"。
- 文章称 main flow 内部默认假设用 `/handoff` 切会话，但源码 README 没把 handoff 列进 main flow——与 Wayfinder/Handoff 官方文档存在表述差异（非实质矛盾，是 README 未显式列示）。

## 相关页面

- [[mattpocock skills]]
- [[运维有术]]
- [[Matt Pocock skills Handoff 官方文档]]
- [[Matt Pocock skills Wayfinder 官方文档]]
- [[smart zone]]
