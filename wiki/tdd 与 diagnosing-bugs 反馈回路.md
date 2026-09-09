---
type: source
title: "tdd 与 diagnosing-bugs 反馈回路"
domain: [AI编程, AI工具]
tags: [mattpocock-skills, tdd, diagnosing-bugs, 反馈回路, 测试, 覆盖率]
sources: []
source_path: "raw/articles/06 - mattpocockskills AI 写代码是闭眼飞？tdd 加 diagnosing-bugs 让它睁开眼.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484084&idx=1&sn=5f2c8a81c1453e388821df2ab455a442"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-10"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases: [06 - tdd 加 diagnosing-bugs, 反馈回路]
---

# tdd 与 diagnosing-bugs 反馈回路

> 一句话要点：给 AI 编码装上两条反馈回路——`tdd` 管「事前不写坏」（红→绿→重构），`diagnosing-bugs` 管「事后修得好」（六步科学排错），并点破覆盖率崇拜。

## 关键要点

- **场景锚点**：AI 改代码「第五轮还是红」= 闭眼飞，无反馈回路（呼应 02 课「AI 写代码是闭眼飞」）。
- **tdd 三循环**：红 = 先写会失败的测试，**确认因正确原因失败**（作者称「最值钱的一步」，断言写错则测试绿了也白测）；绿 = 最小实现「刚好能通过，不多写一行」；重构 = 不改行为整理代码。引《Refactoring》：没有测试的重构不是重构，是赌博。
- **历史定位**：红绿重构源自 Kent Beck 的 XP；Matt 的贡献是把它变成 **AI 的强制流程**（AI 天生没有「先想清楚再动手」的耐心）。
- **纪律**：垂直切片，**一次只写一个测试**，红了变绿再下一个，步子小反馈快。
- **diagnosing-bugs 六步（老谭版命名）**：复现 → 最小化 → 假设 → 插桩验证 → 修复 → 回归。医学类比：先检查再开药；反模式「不插桩直接改 = 碰运气」。
- **两套是同一逻辑两面**：**反馈速度就是限速**（引 The Pragmatic Programmer）——测试给秒级反馈，插桩给证据反馈。
- **两个反指标**：① 别把覆盖率当目标（见过 100% 覆盖照样一堆 bug、60% 稳如泰山；给 AI 设「覆盖率 95% 门禁」= 手段当目标）；② 测试不是银弹（兜不住性能退化、远端副作用、并发时序——所以 06 结尾预告 07 课整合）。
- **何时不用**：一次性脚本/探索原型、纯配置改动、团队没测试文化（强推 tdd 会得到「最小化假绿」）。

## 详细笔记

系列第 6 篇对应「坑三：代码不工作」的解药实操课，是「反馈」课。老谭版是教学简化：官方 6 阶段含 Phase 1「red-capable command」4 判据硬约束与 Phase 6 Cleanup+post-mortem，老谭版均未单列（把复现、回归拆成独立步，缺官方首尾两阶段）；tdd 侧也未提 tracer bullet、pre-agreed seam、mocking 边界原则。

## 与已有内容的关联

- 与 [[diagnosing-bugs 与 tdd]]（运维有术版）主题重叠，要点基本一致（反馈限速、垂直切片、假设先于修复），本文是教学简化版，不推翻该页。
- 与 [[mattpocock skills 标准工作流]] 的 bug 变体（diagnosing-bugs→tdd）流程互补。

## ⚠️ 矛盾 / 待澄清

- **与官方 SKILL 完整度有落差**：缺 red-capable command 硬约束——这是既有 wiki 页强调的「入场券」，若照老谭版用，AI 可能跳过建反馈回路直接猜。引用时并排标注（见 [[diagnosing-bugs 与 tdd]]）。
- 全文无 v1.2 痕迹（v1.2 里 diagnosing-bugs 加了 Redact 章节 #779）；「修复+回归」闭环口径与 wiki 警告「别砍掉 fix+regression 步骤」一致，无冲突。

## 相关页面

- [[diagnosing-bugs 与 tdd]] · [[mattpocock skills]] · [[超级奶爸老谭]] · [[mattpocock skills 标准工作流]] · [[Matt Pocock]]
