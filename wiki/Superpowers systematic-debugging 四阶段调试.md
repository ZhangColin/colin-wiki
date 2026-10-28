---
type: source
title: "Superpowers systematic-debugging 四阶段调试"
domain: [AI编程, 软件工程, 调试]
tags: [Superpowers, 老谭superpowers系列, systematic-debugging, 四阶段调试, 根因, 防回归测试]
sources: []
source_path: "raw/articles/14｜superpowerssystematic-debugging 系统化调试.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484333&idx=1&sn=f9aebd906a3c2067ccd4fc35d029aa84"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-10"
created: 2026-09-10
updated: 2026-09-11
status: active
aliases: [systematic-debugging, 系统化调试, 四阶段调试]
---

# Superpowers systematic-debugging 四阶段调试

> 一句话要点：`systematic-debugging` 把"账对不上"这类焦虑 bug 变成四阶段探案——**复现 → 假设 → 验证 → 根因**，中间任何一步没做完都不许碰代码；用"报表多 57 元"的深夜排查全程实录演示。

## 关键要点

- **案例**：报表页家庭支出 3791 元 vs 账目逐笔相加 3734 元，差 57——后端测试全绿但数字不对。第一反应想扑上去改代码，被第一条铁律拦住："**先别急着改。先搞清楚到底哪里不对、为什么不对。**"
- **四阶段**：
  1. **复现**：curl 绕开前端分开验证（"前后端分开验证，一锅粥地看找不到根因"），把模糊的"数字不对"钉成可重复现象——产出是一句话："后端报表接口的支出合计比账目列表多 57.00 元"。
  2. **假设**：列全可能病因不急着下结论——A 前端算错 / B 后端聚合错 / C 后端漏过滤账本（乐乐的钱算进家庭账）。
  3. **验证**：逐个排除——后端裸接口就多 57 → A 排除；查乐乐 2026-08 两笔（文具 45 + 零食 12 = 57）**正好等于差额 → 证据链闭合，C 成立**。
  4. **根因**：`LedgerAppService.listMonthlyTotals` 聚合没按 ownerId 过滤，子账本混入家庭账——修完**回到复现那句话验证**，再补**防回归测试**（`familyReportExcludesKidLedger`：家庭报表不含子账本账目）。
- **纪律**：没复现就改代码等于蒙；假设要列全（只列一条容易一条道走到黑）；修完要验证 + 防回归（"别让 bug 卷土重来"）；"改代码前的那几分钟冷静，能省一整夜"。
- 对应 [[Superpowers]] 三条铁律之②"不做根因调查就不修 bug"。下一篇预告 root-cause-tracing（从表象挖到根子）。

## 详细笔记

bug 根源在 08 基建篇的设计面：`X-User-Id` 切账本机制下，聚合查询漏按 ownerId 过滤——**基建设计面即 bug 漏洞面**（跨篇呼应）。有 bug 的代码示意里"参数传了却没用到"的坏味道也是教学点。

## 与已有内容的关联

- 四阶段与 [[diagnosing-bugs 与 tdd]] 记载的 Superpowers `systematic-debugging` 4 阶段口径一致，本文是**中文教学化完整展开**（含可跑案例与防回归测试）；与 mattpocock `/diagnosing-bugs` 6 阶段（red-capable command 硬约束入场券）对照依旧"不能无条件互换"——本文未提 red-capable command 概念，复现阶段用 curl 手工替代。
- "列全假设逐个排除"与 diagnosing-bugs Phase 3 "3-5 个 ranked、falsifiable 假设"同构，Superpowers 版不强调 falsifiable 表述。
- 老谭在 mattpocock 系列教过六步排错教学简化版（[[tdd 与 diagnosing-bugs 反馈回路]]）——同一作者对两条路线的调试技能均有教学实录。
- 证据链闭合（差额 = 乐乐两笔合计）是"数字对账"直觉在调试里的应用，与系列主题（账本）自洽。
- **后篇（2026-09-11 收录）**：[[Superpowers root-cause-tracing 根因追踪]] 给出两技能分工——systematic-debugging 搭**流程**，root-cause-tracing 教**沿因果链定位根因**（"症状只是根因在水面的影子"）；其 0.01 元 double 误差案例与本篇 57 元 ownerId 案例互补，恰好演示"流程 + 链上追踪"的配合。

## ⚠️ 矛盾 / 待澄清

- 无矛盾。四阶段口径与 wiki 既有记载一致；与 root-cause-tracing 的分工已由后篇澄清（流程 vs 因果链定位），前述"待该篇收录后核对"事项关闭。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers root-cause-tracing 根因追踪]] · [[diagnosing-bugs 与 tdd]] · [[tdd 与 diagnosing-bugs 反馈回路]] · [[幸福账本 REST API 基建]] · [[Superpowers TDD 红绿循环]]
