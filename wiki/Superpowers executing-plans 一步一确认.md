---
type: source
title: "Superpowers executing-plans 一步一确认"
domain: [AI编程, 软件工程]
tags: [Superpowers, 老谭superpowers系列, executing-plans, 计划执行, 人机确认, 回滚]
sources: []
source_path: "raw/articles/04｜superpowersexecuting-plans 让 AI 按计划执行.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484243&idx=1&sn=6567895526e2d02cd0fce73a8b478cca"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-27"
created: 2026-09-10
updated: 2026-09-10
status: active
aliases: [superpowers executing-plans, /execute-plan, 计划执行器]
---

# Superpowers executing-plans 一步一确认

> 一句话要点：`executing-plans` 用"分批 + 确认"把 AI 的执行框进计划——一次一步、步停确认、错了回滚；把 AI 从"一上来就狂写"变成"走一步、报一步、听指挥"。

## 关键要点

- **痛点场景**：没有约束时 AI 拿到大任务就"规划之外的发挥"（自作主张加 `core` 包重组项目结构）——"它不是坏，是缺一个照着计划走、走完一步跟你确认的约束"。
- **四机制**：① 一次只执行计划里的一个步骤，不一口气吞掉整个计划；② 每步做完停下来给你看结果；③ 确认才进下一步，有偏差就地修正或回滚，**绝不带着错往下走**；④ 有状态——知道现在走到第几步、下一步是什么。
- **A2 预算服务四步实录**：照计划写代码（金额比较用 `compareTo` 不用 `equals`；`limit == null` 按"不超支"处理）→ 跑验收测试**贴出证据**（BudgetServiceTest 三用例：超支/等于上限/无预算）→ 停下等确认（"A2 已完成，测试全绿。是否继续 A3？"）→ 写错回滚重来。
- **回滚实例**：AI 擅自把 `MoneyRules.check` 从返回 `Optional<String>` 改成抛异常——打回、回滚、重做、再跑绿。"一步的偏差只影响一步，不会滚雪球。"
- **验收纪律**："它说'测过了'，不如让它把 `mvn test` 的输出贴出来。**看不到绿的确认，等于没确认。**"
- 三张入场券至此集齐：问清楚（brainstorming）→ 写计划（writing-plans）→ 照计划干（executing-plans）。

## 详细笔记

注意事项：确认是双向的（别让确认变走过场）；别让它跳步（依赖顺序是铁律）；一步偏差就地解决（带错往下走是 bug 雪球的开端）。

## 与已有内容的关联

- 一步一确认是 [[HITL 与 AFK]] 中"执行层人在环"的典型落点——机械执行交给 AI，步进决策留给人。
- Qsuperpowers 四阶段闭环含 executing-plans（[[去哪儿网 AI Coding 研发平台实践]]），企业环境同样采用。
- 与 [[Matt Pocock main flow 五环节]] implement 环节同位：mattpocock 侧靠 tickets 驱动，Superpowers 靠计划文档 + 确认点。

## ⚠️ 矛盾 / 待澄清

- 无矛盾。注意与 v6.0 后 subagent-driven-development 的关系：executing-plans 是主会话逐步执行口径，复杂任务会让位给 subagent 流水线（[[Superpowers subagent-driven-development AI 项目经理]]）。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers writing-plans 施工图]] · [[Superpowers TDD 红绿循环]] · [[HITL 与 AFK]] · [[去哪儿网 AI Coding 研发平台实践]]
