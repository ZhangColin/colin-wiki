---
type: source
title: "improve-codebase-architecture 代码库体检"
domain: [AI编程, AI工具]
tags: [mattpocock-skills, improve-codebase-architecture, 代码体检, 深浅模块, codebase-design, 删除测试]
sources: []
source_path: "raw/articles/improve-codebase-architecture 专题 ｜ mattpocockskills 代码写久了为什么变浆糊？.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484140&idx=1&sn=cfd44eac03ad3287fdfda89212cdcb27"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-18"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases: [improve-codebase-architecture 专题, 代码库体检, 代码浆糊]
---

# improve-codebase-architecture 代码库体检

> 一句话要点：AI 越用越不对劲不是模型变笨而是代码库变浆糊——`improve-codebase-architecture` 给代码库做「只读体检」，扫出热点、摆出候选、挑一个 grilling 聊到底。

## 关键要点

- **核心机制（开篇）**：AI 每次动手前读代码库，代码库越乱它读得越慢、猜得越偏，写完后留下更多乱——「循环的起点，是你从没认真看过自己的代码库」；浆糊是攒出来的，团队没人说破。
- **定位**：user-invoked、**只读不写**；三步：Explore 扫热点 → HTML 报告摆候选 → grilling 聊到底。
- **Explore 纪律**：**范围优先（YAGNI）**，不翻全库；先 `git log --oneline` 找**热点**（最近频繁变动的区域权重最高，「半年没碰的模块再乱也不急着动」）；先读 CONTEXT.md（给好接缝的名字）+ ADR（已拍板别重吵）；Explore 子代理顺着走记摩擦：跨小模块跳着理解？模块浅？为可测性抽出的纯函数缺局部性？紧耦合跨接缝泄漏？没测试/难测？
- **两个核心词**：**深/浅模块**（浅 = 大接口 + 透传薄实现；深 = 小接口 + 厚实现）；**删除测试**——想象删掉该模块，复杂度消失 = 透传壳该折；复杂度搬家到 N 个调用方 = 值得留。
- **报告形态**：自包含 HTML，写系统临时目录**不落仓库**，自动浏览器打开；Tailwind + Mermaid + 手绘 SVG；每候选一卡片五字段：Files / Problem / Solution / Benefits / **Recommendation strength（Strong / Worth exploring / Speculative 徽章）**；末节 Top recommendation；词汇纪律：领域概念用 CONTEXT.md 说法、架构概念用 codebase-design 说法（「术语一乱，报告失去一半价值」）。
- **leverage（杠杆：一份实现回馈 N 个调用点和 M 个测试）与 locality（局部性：变更/bug/知识/验证集中一处）**是报告说「收益」的语言。
- **完整案例（EShop 订单创建）**：候选一「折叠订单创建管线」Strong——OrderController→OrderService→Validator/PriceCalculator 一串浅模块 + OrderPriceCalculator 把 PricingClient 泄漏给调用方；重构为 **OrderIntake 深模块（唯一入口 createOrder）**，Validator/PriceCalculator 退为内部接缝，删 4 个透传壳。技能停在「你想看哪一个」，不替你选、不替你重构。
- **第三阶段 grilling 五问**（一次一问+推荐答案，对象从「要什么功能」换成「深模块怎么切」）：接口越窄深度越深；内部件可 mock 可替换但**不属于接口 = 内部接缝**；「**接口就是测试面**」——想测到接口后面去，多半是模块形状不对；删除测试判断可写进 ADR；**adapter：一个适配器是假设接缝，两个才是真接缝**（生产真实客户端+测试内存假实现才成立）。
- **配套纪律**：domain-modeling 全程在旁边——新概念（OrderIntake）定名当场写进 CONTEXT.md；**承重的否决才记 ADR**（「以后肯定还会再被建议」的决定，如候选二不做），不然下次体检还冒出来。分工三角：codebase-design 给词汇（一个给语言，一个用语言扫描）；挑中候选生成想法带进 `/grill-with-docs` 走正式流程（它只负责找到路不负责走完路）；diagnosing-bugs 排错排到病根是「没有好测试接缝/调用方缠成一团」时把活递上来。
- **边界**：只体检不改码；**别先认定答案再扫描**（「它是侦查，不是证词」）；别当万金油（热点找偏 = 空中楼阁；一次性交付的项目不值得体检，深折没有回报）。

## 详细笔记

主系列 7 课 + 4 篇专题之后的第 5 篇专题（08-18），主题 = 代码库「体检/维护侧」——是 [[mattpocock skills 标准工作流]] 中「变体 A 架构体检」入口的机制深拆，也把 06 课的 diagnosing-bugs 与 04 课的 grilling 串进「递上游/收尾」分工里。wiki 首个该 skill 的深度源（此前 [[还在用 grill-with-docs]] 页仅提一句）。

## 与已有内容的关联

- 与 [[Matt Skills v1.2 grilling 重构]] 的记录互证（该页载明 v1.2 起 improve-codebase-architecture 加 YAGNI 过滤、Explore 只读最近约 20 条 commit——本文「翻一段 git 历史找热点」未给数字，兼容无冲突）。
- 与 [[diagnosing-bugs 与 tdd]]（上游交接）、[[mattpocock skills 标准工作流]] 变体 A「架构体检」流程互补。

## ⚠️ 矛盾 / 待澄清

- 无重大矛盾。小注：① 文中把 grilling 用作「一次一问」的收尾阶段（符合 v1.1 及老谭系列口径），未提 v1.2 round-based（若按 v1.2，第三阶段应也是 round 推进，机制层面可平移）；② 词汇纪律与 [[Matt Pocock 三个 skill 结构词汇]] 记载的 codebase-design 职责一致。

## 相关页面

- [[mattpocock skills]] · [[超级奶爸老谭]] · [[mattpocock skills 标准工作流]] · [[diagnosing-bugs 与 tdd]] · [[Matt Skills v1.2 grilling 重构]] · [[Matt Pocock 三个 skill 结构词汇]]
