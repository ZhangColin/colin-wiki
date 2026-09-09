---
type: source
title: "wayfinder 迷雾地图实战"
domain: [AI编程, AI工具]
tags: [mattpocock-skills, wayfinder, 战争迷雾, 决策工单, HITL, AFK, 大工程规划]
sources: []
source_path: "raw/articles/mattpocockskills AI 装不下的大工程？把迷雾画成地图，一次探一步.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484086&idx=1&sn=df782c8e1d1c6780a9b19a8dd5950c32"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-12"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases: [wayfinder 专题, 迷雾画成地图]
---

# wayfinder 迷雾地图实战

> 一句话要点：把 05 课一句带过的 wayfinder 展开讲透——一次会话装不下、路径不明的超大工作，用「决策工单」画成共享地图，一次探一步，直到路线完全清晰再移交施工。

## 关键要点

- **适用路由（Matt 自己的分流）**：小修改/小 bug → 直接改或 `/diagnosing-bugs`；中型 → `/grill-with-docs`→`/to-spec`→`/implement`；大型 → 加 `/to-tickets`；**超大/全新/multi-module → `/wayfinder`→`/to-spec`→`/to-tickets`→`/implement`**。触发场景：greenfield、巨型功能（超单次会话上下文）。
- **前身叫 decision-mapping**，Matt 嫌其「既术语化又不准确」（四类工单里只有一类是真决策），改名统一「战争迷雾」隐喻。
- **四件套**：Map（`wayfinder:map` 标签 issue，唯一正本）、Fog of War（范围内说不清的未知）、Frontier（open+unblocked+unclaimed、可认领的工单）、Destination（spec/决策/就地改造）。Map 五区块：Destination/Notes/Decisions so far/Not yet specified/Out of scope；**地图是索引不是仓库**（细节只住工单里）；**按名称引用工单，绝不裸 ID**（`#42` 是人眼噪声）。
- **四类工单 = 一次 100K token 会话可消化，按 HITL/AFK 分两半**：research（AFK，/research 子代理）、prototype（HITL，/prototype）、grilling（HITL，/grilling+/domain-modeling，默认就它）、task（AFK 或 HITL，不产决策、给决策开路）。HITL 必须有真人，Agent 绝不替真人回答。
- **两步工作流**：**Charting 一次性 7 步**（命名 Destination→广度优先探雾→建 map→建首批工单→第二遍用 tracker 原生依赖接线 blocking edges→并行触发 research 子代理→停）；**Working Through 循环 5 步**（认领 assign 先于一切→按 Notes 调 skill 解析→决议 comment+关 issue+Decisions so far 追加一行→毕业新工单+清 Not yet specified→清理/判 out of scope）。纪律：一 session 只解一张工单（research 除外）。
- **gh 命令实战**：`gh issue create --label "wayfinder:map|research|grilling|task"`；接线用 **database id** 而非 issue 编号（`gh api repos/<o>/<r>/issues/48/dependencies/blocked_by -F issue_id=$BLOCKER_DB_ID`）。
- **完整案例**：EShop OrderService if-else 满减/券/会员价堆叠 → 重构成 DiscountPolicy 策略链（`supports`/`apply`，FullReductionPolicy），基线测试先锁死三条行为（满 1000 减 50、券 100、上限 0），重构后保持全绿。
- **数据点**：Matt 用它规划一门完整课程，四天几乎每分钟在用、攒近 **100 个独立 grilling/prototyping/research session** 汇总到一张中央地图；「对问题了解越多地图越长，找到的答案越多，地图越缩」。
- **六大误区**：当代码生成器（想直接开干 = 到地图边缘的信号，该移交 /implement）、工单过大、用 ID 引用、先干活再认领（并发会撞车）、把细节写进地图、HITL 工单让 Agent 自问自答。核心纪律一句话：**别让 AI 同时负责「想清楚做什么」和「怎么写代码」**——两者解耦，大项目才不会散架。

## 详细笔记

主系列外第 1 篇专题，是 05 课「一句话带过 wayfinder」的展开深拆，主题 = 超大工程规划探路。08-12 发布，接在 07 组装课后作为「更大场面」的扩展。

## 与已有内容的关联

- 与 [[Matt Pocock skills Wayfinder 官方文档]]（v1.1 官方 SKILL 原文）机制**高度一致**（四件套、四类 ticket、chart/work、先 claim 再动手、map=索引不是仓库、Not yet specified 判据），本文是教学化 + gh/Java 实操版，可互相补充。
- 与 [[Matt Pocock wayfinder handoff 接力协议]]、[[HITL 与 AFK]]、[[Matt Pocock 三条 on-ramp 分流]]（路由分流可对照）相关。

## ⚠️ 矛盾 / 待澄清

- **文内自相矛盾/口径漂移**：路由段明确推 `/grill-with-docs`，但「与其他技能的配合」链路图写的是 `/wayfinder → /grill-me → /to-spec → …`——同一篇内 grill-me 与 grill-with-docs 混用。
- 未提 v1.2（research 票并行烧掉在 [[Matt Pocock Skills v1.2 更新全景]] 已有记载，本文「决策工单」叙述与其兼容无冲突）。

## 相关页面

- [[Matt Pocock skills Wayfinder 官方文档]] · [[Matt Pocock wayfinder handoff 接力协议]] · [[HITL 与 AFK]] · [[mattpocock skills]] · [[超级奶爸老谭]] · [[Matt Pocock 三条 on-ramp 分流]]
