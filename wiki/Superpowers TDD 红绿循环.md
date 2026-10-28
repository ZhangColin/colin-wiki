---
type: source
title: "Superpowers TDD 红绿循环"
domain: [AI编程, 软件工程, 测试]
tags: [Superpowers, 老谭superpowers系列, test-driven-development, TDD, 红绿重构, JUnit, Vitest, BigDecimal]
sources: []
source_path: "raw/articles/05｜superpowerstest-driven-development TDD测试驱动开发.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484246&idx=1&sn=96a5bb63e9c29b9d2451d66d9e3c6365"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-28"
created: 2026-09-10
updated: 2026-09-11
status: active
aliases: [superpowers TDD, superpowers tdd, 红绿重构]
---

# Superpowers TDD 红绿循环

> 一句话要点：`test-driven-development` 把"写对"变成"可证明"——红（写会失败的测试）→ 绿（最少代码通过）→ 重构；先写测试 = "先记账，再花钱"，同一三拍子后端 JUnit / 前端 Vitest 通吃。

## 关键要点

- **为什么先写测试**：代码你写、测试你写 → "自己哄自己"的测试陷阱；先写测试等于先把账记下（要什么结果），再写代码把账平上。"先看到它失败，再让它通过"是"验证管用"与"以为管用"的分水岭。
- **红绿重构三拍子**：红（描述要的行为、必须失败）→ 绿（写**最少**代码让它过，不画蛇添足）→ 重构（测试保护下改干净）。实现"开始实现功能"时自动激活，"一个测试一个测试地逼你前进"。
- **后端实录 `sumExpenses`**：第一拍空列表求和 = 0 不是 null（先红：类不存在编译不过 = 有意义的红）；第二拍朴素实现转绿；第三拍守护用例 15.00 + 88.50 + 199.00 = 302.50——首跑即绿也有价值："以后谁把实现改坏了，它立刻红给你看"。
- **哨兵测试（novel 教学装置）**：`doubleSummationHasError` 断言 `0.1×10 累加 ≠ 1.0`，**永远绿**（因为它断言的就是 double 确实有误差）——谁把金额类型改回 double，这道防线立刻提醒。15 篇将其复用为 root-cause-tracing 的防回归收尾并补正例 `bigDecimalSummationIsExact`（见 [[Superpowers root-cause-tracing 根因追踪]]，其 ⚠️ 区注明：恒绿测试的"防线"价值在宣示层面，机器可判定的防线是 MoneyRules 与类型纪律）。
- **前端同一三拍子**：Vitest 写 `useLedger` 账本切换（切到乐乐 ownerId=2）先红后绿——"技能不绑定语言"。
- **TDD 的产出是一套测试网**：每多一个用例多一条防护，重构/改 bug 都靠它兜底。

## 详细笔记

注意事项："先红"必须红在"行为还没实现"上（编译不过 ≠ 有意义的红，要区分）；绿的判据是测试输出不是"看起来对"；金额断言 `isEqualByComparingTo` 全系列统一。对应 [[Superpowers]] 三条铁律之①"没有失败测试就不写生产代码"。

## 与已有内容的关联

- **同一作者两条路线的 TDD 教学**：老谭在 mattpocock 系列教 `/tdd`（[[tdd 与 diagnosing-bugs 反馈回路]]），本篇教 Superpowers tdd——路线对照：Superpowers 含 refactor 步，mattpocock v1.1 把 refactor 划给 code-review（[[Superpowers 与 mattpocock skills 路线对照]] 表 TDD 行）。
- mattpocock 侧独有概念 tracer bullet / pre-agreed seam / mocking 边界（[[diagnosing-bugs 与 tdd]]）本源未涉及——Superpowers 版 TDD 叙述更朴素。
- 哨兵测试与 07 篇测试反模式（[[Superpowers testing-anti-patterns 测试反模式]]）的"测行为"原则互补：一个锁类型纪律，一个锁行为；15 篇 root-cause-tracing 复用哨兵测试作防回归（[[Superpowers root-cause-tracing 根因追踪]]）。

## ⚠️ 矛盾 / 待澄清

- 无矛盾。mattpocock 教学版与 Superpowers 版均为有效口径，差异在 refactor 步归属（已在上文标注）。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers executing-plans 一步一确认]] · [[Superpowers condition-based-waiting 条件等待]] · [[Superpowers root-cause-tracing 根因追踪]] · [[tdd 与 diagnosing-bugs 反馈回路]] · [[diagnosing-bugs 与 tdd]] · [[Superpowers 与 mattpocock skills 路线对照]]
