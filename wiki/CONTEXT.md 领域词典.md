---
type: source
title: "CONTEXT.md 领域词典"
domain: [AI编程, AI工具]
tags: [mattpocock-skills, CONTEXT.md, 共享语言, ubiquitous-language, 语义扩散, domain-modeling, token优化]
sources: []
source_path: "raw/articles/03 - 为什么 mattpocockskills 让你别再调 prompt，而是给 AI 建领域词典.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484056&idx=1&sn=51869f7e7f56d2f0b30c603c548332ee"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-05"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases: [03 - 领域词典, CONTEXT.md]
---

# CONTEXT.md 领域词典

> 一句话要点：AI 啰嗦的根因是「没有你的领域词典」；治法是项目根目录一份不到 30 行的 `CONTEXT.md`（Matt 最酷技巧），并给出建法与「语义扩散」防毁纪律。

## 关键要点

- **根因**：不是 AI 笨，是没词典——「满 1000 减 50」在 AI 眼里只是无业务含义文字；对话示例展示「用 50 个字试探」vs「三句话全懂」的差别。
- **定义与定位**：`CONTEXT.md` 是项目根目录文件，写清术语、概念、模块关系，让 AI 一进来就说人话。**不是 README**：README 讲「怎么跑起来」给人类看，CONTEXT.md 讲「项目在说什么」给 AI 看。
- **EShop 模板（不到 30 行 = 领域宪法）**：项目定位 + 术语表「术语与代码同源」（SKU→Sku、满减→FullReduction、物化级联→MaterializationCascade、幂等→Idempotent）+ 模块地图（订单/支付/用户/售后各自 Controller/Service）+ 编码约定（@RestController + Result 包装、SLF4J、Service 是业务逻辑唯一归宿）。一份文件同时填「AI 啰嗦」与「代码风格跑偏」两个坑。
- **三种建法（从轻到重）**：① 手写初稿（最快，10 分钟，术语和模块地图各先写五条，会自己长）；② `domain-modeling` 打磨（Matt 的推荐——挑战术语、压力测试边界场景）；③ `grill-with-docs` 边访谈边建（最重：访谈同时把领域模型写进文档、难解释决策记 ADR）。作者建议：新项目先用①跑两周，术语老解释不清再上②；③「锦上添花不是必需品」。
- **语义扩散（semantic diffusion）是敌人**：同一概念在代码/文档长出不同名字（术语表写 AfterSaleOrder，代码躺着 RefundRecord / ReturnRequest），词典白建。防御靠纪律：改名要改全、新词先查表；AI 时代优势 = 让 AI 扫库找出「一个东西三个名字」。
- **算账**：无 CONTEXT.md 修退款任务光对齐烧约 2000 token（3 轮猜 x 300 token + 2 轮确认）；有则 0 轮试探第一轮开工。但 token 不是唯一标准，真正收益是返工减少、代码一致、新 AI 上手快。

## 详细笔记

系列第 3 篇对应「坑二：AI 太啰嗦」的解药实操课。用 EShop 案例演示完整模板，核心主张「别再调 prompt，给 AI 建词典」——把跨会话共享语言沉淀为文件，是 grill-with-docs/domain-modeling 两个技能共同服务的对象。

## 与已有内容的关联

- 与 [[还在用 grill-with-docs]] 直接重叠（该页记录官方 CONTEXT.md 口径与 grill-with-docs 落字机制）；本文的「三种建法」把 domain-modeling / grill-with-docs 都定位成 CONTEXT.md 的构建工具，是入门向 how-to 层，互补。
- 术语表结构与 [[mattpocock skills]] 概念页、「术语解决即更新 CONTEXT.md」纪律一致。

## ⚠️ 矛盾 / 待澄清

- **CONTEXT.md 内容边界与官方口径张力**：wiki 记录的官方硬线是「CONTEXT.md is a glossary and nothing else，totally devoid of implementation details」（反模式：写成 spec、塞通用概念）。而本文模板含模块地图（类名级 OrderController/OrderService）与编码约定（@RestController、SLF4J）——比官方「纯术语表」宽。可作「作者延伸教学模板 vs 官方原语」并存记录。
- **grill-with-docs 推荐力度与既有口径方向相反**：本文称 grill-with-docs 建词典是「选择三（最重），锦上添花不是必需品」，推荐新手手写初稿；而 [[还在用 grill-with-docs]] 记录 grill-with-docs 是 Matt 本人推荐的 coding 默认入口。属教学推荐序与官方演进的分歧，标注并存。
- 未提 v1.2（本文发布于 v1.2.0 上线当日，全部为旧行为口径）。

## 相关页面

- [[mattpocock skills]] · [[超级奶爸老谭]] · [[还在用 grill-with-docs]] · [[grill-me 实战指南]] · [[Matt Pocock]] · [[mattpocock skills 完全入门指南]]
