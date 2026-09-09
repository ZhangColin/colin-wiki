---
type: source
title: "grill 家族与需求冰山"
domain: [AI编程, AI工具]
tags: [mattpocock-skills, grill-me, grill-with-docs, grilling, 需求访谈, 决策树, 冰山需求]
sources: []
source_path: "raw/articles/04 - mattpocockskills 为啥不让我直接写代码？grill-me 让你怀疑人生.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484057&idx=1&sn=12d2da9f6cf44655ec2fc7efa6169559"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-06"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases: [04 - grill-me 让你怀疑人生, 冰山需求]
---

# grill 家族与需求冰山

> 一句话要点：一句话需求是冰山一角，水下藏着十几个没说出口的决策；grill 家族把访谈变成 AI 手艺，动手前把隐形决策逼到台面上（本文为老谭系列唯一的 grill-me 专项）。

## 关键要点

- **冰山模型**：「用户能下单买商品」七个字是水面上的部分；水底下 90%——优惠券怎么算、库存不足怎么办、并发会不会重复、支付失败怎么回滚——AI 只能脑补，脑补必跑偏。
- **grill 家族三兄弟（引擎/壳结构）**：`grill-me` = 轻量壳，user-invoked，问完就走；`grill-with-docs` = 重量壳，user-invoked，问完写文档；`grilling` = 内核引擎，model-invoked，两个壳背后都是它在跑。作者类比引擎与外壳：改提问套路改引擎，日常用记住两个壳的区别。
- **grill-me 实战（EShop 下单，4 问全实录）**：① 订单金额怎么算（满减按订单总额，规则配在促销中心——带推荐答案）② 库存不足怎么办（下单预占、不足拒绝）③ 并发会重复吗（要幂等，幂等键 = 用户 ID + SKU + 时间窗口）④ 支付失败已扣库存怎么回滚（待支付状态、预占保留 15 分钟超时自动释放——用户改成 30 分钟）。作者称提问套路与真实咨询几乎一致，区别只是「AI 坐在你对面问」。
- **决策树扫描四「每」**：每个业务分支（如果 X 怎么办）、每个数字（写死还是可配置）、每个边界（失败/异常/并发行为）、每个术语（你说的 X 与 CONTEXT.md 的 Y 是否同物）。
- **三条访谈纪律（比问题本身更值得学）**：① 一次只问一个问题——引 grilling 原文「asking multiple questions at once is bewildering」；② 每个问题带推荐答案（访谈不是考试是过方案）；③ 事实自己查、决策才问你。
- **grill-with-docs 多两件事**：边访谈边建模（调用 domain-modeling，沉淀术语表）+ 写 ADR（难以解释的设计决策如幂等键选择，防 AI 反复问同一问题）。一句话：**grill-me 问完拉倒，grill-with-docs 问完留档**。写文档不是目的，想清楚才是目的。
- **累积效应**：with-docs 把决策钉进文档，访谈越往后越短——第一次 4 轮，第二次「加优惠券叠加」可能只剩 1 轮；grill-me 无积累每次从头问。
- **何时不要用**：需求极简（换按钮颜色）；你已完全想清楚；团队没打算用流程（作者 2006 年起就反对强推 agile——同理 grill）。一句话：grill 给「需求没想清楚、但值得想清楚」的任务。

## 详细笔记

系列第 4 篇对应「坑一：需求不清」的解药实操课。全系列唯一 grill-me 专项，把 EShop 下单需求的全流程访谈实录拆开讲解，是理解 grill 家族「引擎/壳」结构与访谈纪律的最佳教学文本。

## 与已有内容的关联

- 与 [[grill-me 实战指南]] 核心同主题（grill-me/grilling 机制深拆）。对照：该页记录的 v1.1 五条规则在本文能找到 4 条对应（缺「共享理解确认门禁」）。本文是「故事化实战教程」层，该页是「源码与官方态度」层，互补。
- grill-with-docs「问完留档 + 调 domain-modeling + ADR」与 [[还在用 grill-with-docs]] 记录一致。
- 与 [[2026 年再看 Superpowers：grill-me 场景选型]] 同题材不同角度。

## ⚠️ 矛盾 / 待澄清

- **「要效率用 me，要记录用 with-docs」vs「grill-with-docs 取代 grill-me」**（本系列与既有口径冲突最集中的一篇）：本文把 grill-me 写成日常 coding 对齐的默认轻量选项，两壳平行并置、按效率/记录取舍。而 wiki 既有口径（[[grill-me 实战指南]] + [[还在用 grill-with-docs]]）是：Matt 已停用 `/grill-me` 做 coding，改推 `/grill-with-docs`。按既有口径，本文的推荐场景（EShop coding 需求对齐）恰是 Matt 已不推荐 grill-me 的场景。属教学系列滞后，标注并存、不推翻既有结论。
- **grilling 行为版本落伍**：本文把「一次只问一个问题」当作 grilling 现行铁律；wiki 记录 v1.2（08-05，本文发布次日上线）已重构为 round-by-round frontier（13 问约 3 轮），一次一问降级为 opt-out。见 [[Matt Skills v1.2 grilling 重构]]。

## 相关页面

- [[grill-me 实战指南]] · [[还在用 grill-with-docs]] · [[mattpocock skills]] · [[超级奶爸老谭]] · [[Matt Skills v1.2 grilling 重构]] · [[2026 年再看 Superpowers：grill-me 场景选型]]
