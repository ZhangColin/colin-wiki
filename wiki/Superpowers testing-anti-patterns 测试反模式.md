---
type: source
title: "Superpowers testing-anti-patterns 测试反模式"
domain: [AI编程, 软件工程, 测试]
tags: [Superpowers, 老谭superpowers系列, testing-anti-patterns, 测试反模式, 测试行为, 边界用例]
sources: []
source_path: "raw/articles/07｜superpowerstesting-anti-patterns 测试反模式-这些坑我都踩过.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484282&idx=1&sn=b3449c30a475f6c8a05539c19d572198"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-01"
created: 2026-09-10
updated: 2026-09-10
status: active
aliases: [testing-anti-patterns, 测试反模式]
---

# Superpowers testing-anti-patterns 测试反模式

> 一句话要点：`testing-anti-patterns` 列出四个最常见的坏测试坑——测实现细节、依赖时机、复制粘贴、只测快乐路径；"反模式不是没用，是有害"——虚假安全感比没有测试更危险。

## 关键要点

- **四个坑（幸福账本真实案例）**：
  1. **测实现细节不测行为**：反例 `verify(service, times(1)).check(any(), any())`（锁"调了几次"，重构必碎而功能没坏）；正例只断言行为（`exceeded() == true`）。规则：**测试要能"重构自由，行为不变"**。
  2. **依赖时机**：固定 `Thread.sleep(1000)` 猜异步——正解是 06 篇条件等待（[[Superpowers condition-based-waiting 条件等待]]）。"凡是测试里出现 sleep，先问：我要等的是时间还是结果？"
  3. **复制粘贴**：改数字交差的复制用例"唯一的贡献是让测试数量好看一点"；隐蔽版 = 断言写错不自知（该断 limit 却断了 spent，两个都是数字，红不了）。规则：**每个用例必须覆盖一条别人没测过的行为**。
  4. **只测快乐路径**：`sumExpenses` 最该测的恰恰是空列表返回 0 不是 null。规则：**每个方法先想三件事——空输入、最大边界、非法输入**。
- **金句**："快乐路径让测试好看，边界路径让测试值钱"；"测试的数量不是 KPI，覆盖的行为才是"。
- **前端版同一原则**：反例断言组件内部状态（`wrapper.vm.list.length`，实现细节）；正例断言渲染结果（`.el-table__row` 行数）——**测行为，不测实现**。
- **立场**：反模式测试"会让你误以为有保障，其实什么都没守住——比没有测试更危险"。

## 详细笔记

与 18 技能口径对应：本技能为 2026-08 仓库新增（testing 类第 3 个），v5.0.7 14 技能口径无（[[Superpowers 安装与技能全景]]）。作者自称四坑"每一条都踩过"。

## 与已有内容的关联

- 坑①与 mattpocock 侧 mocking 边界纪律（只在系统边界 mock，不 mock 内部协作者，[[diagnosing-bugs 与 tdd]]）同源——都是"别锁内部结构"。
- 坑②直接引用 06 篇正解，两篇构成"反模式 → 正解"配对。
- "负向/边界用例才值钱"与 [[Agent 测评 Eval]] 的负向触发用例（防技能过度触发）同一思想在不同层（单测层 vs Eval 层）。
- 与老谭 mattpocock 系列对 TDD 反模式的批判（覆盖率批判，[[tdd 与 diagnosing-bugs 反馈回路]]）立场一致。

## ⚠️ 矛盾 / 待澄清

- 无矛盾。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers condition-based-waiting 条件等待]] · [[Superpowers TDD 红绿循环]] · [[Agent 测评 Eval]] · [[diagnosing-bugs 与 tdd]]
