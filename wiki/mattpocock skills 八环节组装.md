---
type: source
title: "mattpocock skills 八环节组装"
domain: [AI编程, AI工具]
tags: [mattpocock-skills, 完整工作流, ask-matt, to-spec, to-tickets, code-review, 渐进引入]
sources: []
source_path: "raw/articles/07 - mattpocockskills 零件都齐了，装成机器才是真本事.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484085&idx=1&sn=09b668da67a6caf2d05f28a95503ef6b"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-11"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases: [07 - 零件都齐了装成机器, 八环节完整链]
---

# mattpocock skills 八环节组装

> 一句话要点：把 01-06 的零件（开关/地图/词典/访谈/流水线/反馈）用 EShop「优惠券叠加满减」需求装成一台完整机器，走完从路由到审查的全流程，并给渐进引入顺序。

## 关键要点

- **八环节完整链**：`ask-matt` 路由 → `grill-me` 访谈 → `to-spec` 沉淀 → `to-tickets` 拆单 → `implement`+`tdd` 写码 → `diagnosing-bugs` 抓错 → `code-review` 审查，全程由 03 课建的 `CONTEXT.md`（共享语言）支撑。
- **ask-matt = 技能地图的导航员**：「技能多了，最大成本是选择成本」。
- **共享语言复利**：访谈里 AI 直接用术语表词（FullReduction、MaterializationCascade）对话、不问「什么是满减」；「第一次建 CONTEXT.md 花半小时，之后每一次访谈都在省这半小时」。
- **to-spec 隐性价值**：让「评审」有抓手——对话结论没法让别人评审，规格可评审、可回看、可传下一个 AI。
- **to-tickets 隐性价值**：逼你把功能拆成**能独立验证的块**（演化式设计小步走），给 AI 装上「分步执行」骨架。
- **集成 bug 示例印证 06 课「测试不是银弹」**：单测全绿但两张券同时提交双抵扣（单订单单券校验漏）→ diagnosing-bugs 六步，修复 = 校验改为「订单已应用券数 + 本次提交数 ≤ 1」。
- **code-review 双轴**：标准轴（@RestController、Result 包装、SLF4J 日志约定）+ 规格轴（忠实实现规格）；「AI 生成代码又快又自信，只有人+审查流程能让它可信」。
- **渐进引入（重点建议）**：第 1 周只上 tdd → 第 2 周加 CONTEXT.md → 第 3 周加 grill-me → 第 4 周加 to-spec 到 implement 完整流水线；每步回答「多上的环节帮我解决了什么」；援引 Strangler Fig（小块替换不推翻重建）。
- **本质论**：整套全是老手艺（面试/规格/拆任务/测试/调试/审查），区别是执行者换成 AI；决定你是当「工具」还是当「信仰」。何时不用：一次性交付、小团队小项目（两三个人只挑 tdd+CONTEXT.md）、流程仪式化时停下。

## 详细笔记

系列第 7 篇 = 组装/整合课，把前六课零件装成机器，是**主系列终章**（主系列共 7 课，后续 wayfinder/loop-me/batch-grill-me/teach/improve-codebase-architecture 五篇均为专题）。注：loop-me/batch-grill-me/teach 三篇文内自称「主系列 8 课」，与实际发布口径（01-07）不符，按 7 课计。

## 与已有内容的关联

- 与 [[mattpocock skills 标准工作流]]（v1.0 四步主流程 + 变体）高度对应——命令名 to-spec/to-tickets/implement 已是 v1.1 名称，与 wiki 演进表一致；是「从需求到上线」链的教学化逐环节演示。
- 相关：[[mattpocock skills 推荐工作流速查]]、[[Matt Pocock 会话边界五问决策树]]。

## ⚠️ 矛盾 / 待澄清

- **访谈环节用 grill-me 而非 grill-with-docs**：与 wiki 核心口径「Matt 已公开声明 coding 不再用 /grill-me、改推 /grill-with-docs」（[[grill-me 实战指南]]）直接冲突；且本文 CONTEXT.md 是 03 课单独建的，分工与官方「访谈+落字一体」的 grill-with-docs 不同。属老谭系列系统性滞后，标注并存。
- **ask-matt 路由器的存废**：本文视为现行工具，而 v1.2 全景只列 3 新技能+删 6 旧技能、未提 ask-matt——teach 篇（08-17）称 ask-matt 路由器仍挂 teach 的号，故按「当时仍存活」处理，待核实。
- 全文无 v1.2 痕迹（grilling round-based、wizard 等均未提）。

## 相关页面

- [[mattpocock skills]] · [[超级奶爸老谭]] · [[mattpocock skills 标准工作流]] · [[grill-me 实战指南]] · [[mattpocock skills 推荐工作流速查]] · [[diagnosing-bugs 与 tdd]]
