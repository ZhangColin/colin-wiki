---
type: source
title: "Superpowers root-cause-tracing 根因追踪"
domain: [AI编程, 软件工程, 调试]
tags: [Superpowers, 老谭superpowers系列, root-cause-tracing, 根因分析, BigDecimal, 防回归测试]
sources: []
source_path: "raw/articles/15｜superpowersroot-cause-tracing 追踪根原因.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/0yweHvFrNrImJPpnJhlt8A"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-11"
created: 2026-09-11
updated: 2026-09-15
status: active
aliases: [root-cause-tracing, 根因追踪]
---

# Superpowers root-cause-tracing 根因追踪

> 一句话要点：`root-cause-tracing` 教**从症状出发、沿代码因果链一路往上追到根子**——与 systematic-debugging 的分工是"流程 vs 因果链定位"；「差一分钱」案例：表象在报表合计，根因藏在两行 `doubleValue()`。

## 关键要点

- **与 systematic-debugging 分工**：14 篇（[[Superpowers systematic-debugging 四阶段调试]]）给排查搭**流程**（复现→假设→验证→根因）；本技能教**怎么从症状沿因果链定位根因**——"不是猜哪可能错，而是顺着这一条链，追到源头"。
- **反直觉点**：表象往往离根因很远——"症状只是根因在水面的影子，你要潜下去找本体"。表面上"某笔账金额不对"，实际可能是几层之外的求和用了 `double`。
- **案例（0.01 元对账差）**：报表 5793.40 vs 流水逐笔 5793.41。"记账系统差一分钱，跟差一万块是同一个 bug——**账不平，就是系统在说谎**。"
- **因果链四跳（每层都有证据，不靠猜）**：定位症状（"吃饭"分类月度合计与逐笔相加差 0.01）→ 报表页显示 ← `GET /api/reports/monthly` ← `LedgerAppService.listMonthlyTotals` 聚合（`BigDecimal.add` 看着没毛病）→ 再往上追一层：数据从哪来 → `EntryRepository` 的 `sumExpensesAsDouble` 用 `doubleValue()` 累加——**根因在这一行**（二进制存 0.1 是近似值，几十笔近似值累加攒出末位误差）。
- **修根子，不修表象**：改 88.50 的值把账平了 = "用谎言盖谎言"、"拆东墙补西墙"；修法是求和全程换回 `BigDecimal` + `MoneyRules` 固化金额不变式。"**修根因，是一次修好一整类问题。**"
- **根因判据**："这个根因被修掉后，能不能解释**之前所有相关症状**？能，才是根子；只能解释一个症状，那只是又修了一个表象。"
- **根因要固化成防线**："不留防线的修复，等于没修。"一正一反两个测试钉死"钱必须走 BigDecimal"：反例哨兵 `doubleSummationHasError`（0.1×10 ≠ 1.0）+ 正例 `bigDecimalSummationIsExact`（0.1×10 = 1.00 精确）。

## 详细笔记

对应 [[Superpowers]] 18 技能 debugging 类第 2 个（v5.0.7 14 技能口径新增之一，[[Superpowers 安装与技能全景]]），是三条铁律之②"不做根因调查就不修 bug"的技能化展开。至此 debugging 类四技能已收三个（systematic-debugging / root-cause-tracing / verification-before-completion，见 [[Superpowers verification-before-completion 验证再收工]]），余 defense-in-depth（17 篇已预告）。哨兵测试与 05 篇 TDD 教学中的是同一个（见下关联）——老王两个提问分别由后续两篇接住：「验证修对了是不是有专门说法」→16 篇 verification-before-completion；「AI 绕过第一层防线，第二层还能兜住吗」→17 篇 defense-in-depth。

## 与已有内容的关联

- **前篇**：[[Superpowers systematic-debugging 四阶段调试]]（57 元案例是 ownerId 过滤缺失，本篇 0.01 元案例是 double 误差——两个不同 bug，恰好演示"流程"与"链上追踪"的互补）。
- **哨兵测试复用**：`doubleSummationHasError` 首现于 05 篇 TDD 教学（[[Superpowers TDD 红绿循环]]），本篇将其重定位为 root-cause-tracing 的防回归收尾——同一装置在"写对"与"追根"两个技能里的复用。
- **跨路线呼应**："每跳一层都靠证据"与 mattpocock `/diagnosing-bugs` 的 falsifiable 假设（[[diagnosing-bugs 与 tdd]]）同构；"根因判据 = 解释所有症状"与该页 Phase 5"回归测试写不进去本身就是发现"同属"根因质量"维度。
- `MoneyRules` 即 03 篇 writing-plans 的 A3 任务（[[Superpowers writing-plans 施工图]]）——系列代码前后自洽。

## ⚠️ 矛盾 / 待澄清

- **哨兵测试的"防线"实际拦截力（推论注记）**：`doubleSummationHasError` 恒绿（它断言的就是 double 有误差），技术上不会因代码退化而失败报警——老谭"谁改成 double 它会提醒你"的说法，其价值在**文档/红线宣示**层面（读测试的人被提醒），机器可判定的防线实为 `MoneyRules` 校验与 `BigDecimal` 类型纪律本身。教学叙事成立，勿将哨兵测试理解为会红的回归测试。
- 与 wiki 既有记载无冲突；该技能为此前"待收录"项，本篇收录后 pending 已更新。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers systematic-debugging 四阶段调试]] · [[Superpowers TDD 红绿循环]] · [[Superpowers verification-before-completion 验证再收工]] · [[Superpowers writing-plans 施工图]] · [[diagnosing-bugs 与 tdd]] · [[tdd 与 diagnosing-bugs 反馈回路]]
