---
type: source
title: "mattpocock skills 设计灵魂"
domain: [AI编程, AI工具]
tags: [mattpocock-skills, 设计理念, 失败模式, 积木与框架, tdd, codebase-design]
sources: []
source_path: "raw/articles/02 - 理解 mattpocockskills 设计灵魂.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247483993&idx=1&sn=e70e4afcedd3b746b59767bc535feece"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-04"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases: [02 - 理解 mattpocockskills 设计灵魂, 四坑与解药]
---

# mattpocock skills 设计灵魂

> 一句话要点：Matt「先有坑、后有技能」——用一张「四坑 → 解药」地图讲透整套技能的设计逻辑，并论证为什么走「积木」而非「框架」路线。

## 关键要点

- **方法论**：四坑来自 Matt 实战观察（anecdotal evidence），是「有用的地图不是严谨分类学」；命名是为了让讨论有共同锚点（作者类比当年给微服务模式命名）。
- **坑一（需求不清）**：引 Pragmatic Programmer「没有人确切知道自己想要什么」；EShop 反例——AI 直接甩出只有基础下单逻辑的接口。解药 = grill 系列：`grill-me`、`grill-with-docs`、`grilling`，动手前先访谈，把决策树每个分支问清楚。
- **坑二（AI 啰嗦）**：解药 = 共享语言 `CONTEXT.md`（术语、概念、模块关系）；「修复 materialization cascade（物化级联）的问题」五个词搞定。Eric Evans DDD 的 ubiquitous language 老思想，Matt 的新意是落成给 AI 看的文件——**Matt 自认「这个仓库里最酷的技巧」**。收益：命名一致、AI 导航快、思考省 token。
- **坑三（代码不工作）**：AI 写代码是「闭眼飞」没有反馈回路。解药两类：`tdd` = 红-绿-重构（先写会失败的测试→确认因正确原因失败→最小实现→重构）；`diagnosing-bugs` = 复现→最小化→假设→插桩→修复→回归。
- **坑四（架构腐化）**：AI 加速软件熵（引 Kent Beck「每天投资系统设计」、John Ousterhout「深模块」）。解药三件套：`codebase-design`（深模块纪律）、`improve-codebase-architecture`（体检扫描 + HTML 可视化报告 + 逐个决策）、`to-spec`（动手前画模块边界防乱拆）。
- **技能全景地图 = ask-matt 的内核**：需求不清→grill-me；啰嗦→CONTEXT.md / domain-modeling；代码不工作→tdd；浆糊→improve-codebase-architecture。地图是起点非终点，坑外场景别硬塞。
- **积木而非框架**：没有任何技能叫「接管整个项目」；只有一条规则（user-invoked 可调 model-invoked，绝不调另一 user-invoked）。代价 = 自己负责拼装；建议先挑一两个（如 tdd）用起来，别一上来全装。
- **警示**：架构体检是手段不是指标，别用「改善模块数」考核自己（引 CannotMeasureProductivity）。

## 详细笔记

系列第 2 篇定位是「给地图」：把 01 篇的四坑框架展开为设计逻辑——每个坑的来源、对应解药、技能全景地图（ask-matt 的内核），并预告 03–07 路线图：03 建 CONTEXT.md → 04 grill 访谈 → 05 流水线 → 06 tdd+diagnosing-bugs → 07 完整实战。

## 与已有内容的关联

- 四坑体系与 [[mattpocock skills]]、[[MattPocock Skills 21 个 skill 工程纪律体系]] 重叠；本文是其「设计逻辑」层解读，补充了「坑→技能」成因链条。
- 坑三 tdd 节奏与 diagnosing-bugs 六步与 [[diagnosing-bugs 与 tdd]] 记录一致。
- 「积木 vs 框架」叙事与 [[Superpowers 与 mattpocock skills 路线对照]] 主题相合。
- 相关概念：[[mattpocock skills]]；相关实体：[[Matt Pocock]]、[[超级奶爸老谭]]。

## ⚠️ 矛盾 / 待澄清

- **坑一解药口径**：本文把 grill-me / grill-with-docs 并列为解药（grill 系列三成员），是 v1.1 时代并置视角，未体现 Matt 的推荐序变化（[[grill-me 实战指南]] 记录 coding 已改推 grill-with-docs）。
- **版本落伍**：全文无 v1.2 信息；对坑一的叙事基础是「一次一问」grilling（v1.1 行为），v1.2 已重构为 round-by-round frontier（见 [[Matt Skills v1.2 grilling 重构]]）。
- 系列内轻微不一致：坑四解药在 01 篇只列两个技能，02 篇扩为三件套加入 to-spec——属后文细化，非真矛盾。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock]] · [[超级奶爸老谭]] · [[MattPocock Skills 21 个 skill 工程纪律体系]] · [[diagnosing-bugs 与 tdd]] · [[grill-me 实战指南]] · [[Superpowers 与 mattpocock skills 路线对照]]
