---
type: source
title: "Superpowers brainstorming 需求访谈"
domain: [AI编程, 软件工程]
tags: [Superpowers, 老谭superpowers系列, brainstorming, 需求对齐, 苏格拉底式提问, 领域建模]
sources: []
source_path: "raw/articles/02｜superpowersbrainstorming 先把需求聊清楚.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484237&idx=1&sn=d8dbdd22f64b4b0c13a7d8d4898b1c65"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-25"
created: 2026-09-10
updated: 2026-09-10
status: active
aliases: [superpowers brainstorming, /brainstorm, 头脑风暴技能]
---

# Superpowers brainstorming 需求访谈

> 一句话要点：Superpowers 第一张入场券 `brainstorming`——用苏格拉底式提问**一次一问**把"做个家庭记账"这种一句话需求，聊成带范围、领域模型、页面草图、验收标准的可施工规格。

## 关键要点

- **场景**：一句话需求信息量为零，直接甩给 AI = "三千行看不懂的代码"。"需求没说清楚就动手，是对自己和 AI 的双重折磨。"
- **三个特征**：① 一次只问一个问题（不轰炸，每个回答都是认真想过的）；② 问的是决策点——目标、边界、取舍、验收标准，不问感受；③ 产出是文档（spec 存进项目，成为施工图）。核心不是"想点子"而是**收敛**。
- **四轮实战（幸福账本）**：
  1. 钉范围：家庭记账收缩成五件事——记账 / 预算 / 对账 / 报表 / 乐乐零花钱子账本。"范围钉死之前，不写一行代码。"
  2. 聊领域对象：分类 Category（预置、每笔必选）、预算 Budget（**唯一性约束 = 分类 + 月份**）、BudgetCheckResult（超支/已花/限额的不可变结果对象）。
  3. 聊页面草图：五屏（账目/记账/报表/预算/零花钱）→ 直接落成前端路由表（`/`、`/entry`、`/reports`、`/budgets`、`/allowance`）。
  4. 问验收标准：艳姐原话三条（工资到账日账对平 / 网页点几下看花销与预算 / 乐乐账与家庭账隔离）——成为**全系列验收标尺**。
- **约束在需求阶段就定死**：月份用 `YearMonth` 不用 String、金额用 `BigDecimal` 不用 double——"这些决定现在定下来，后面二十篇都不走样"。
- **三张入场券分工**：brainstorming（What，问成规格）→ writing-plans（How，拆成计划）→ executing-plans（Do，照着执行）。

## 详细笔记

注意事项：别跳过 brainstorm 直接写代码（需求里藏着大量没意识到的决策）；一个问题一个问题答（急着一次交代完反而漏决策点）；产出必须落文档（口头聊完等于没聊）。`/brainstorm` 触发后 AI "先当面试官"，问得比艳姐还细。

## 与已有内容的关联

- **同题对照**：[[2026 年再看 Superpowers：grill-me 场景选型]] 的 grill-me vs brainstorming 选型（要压力测试共享理解→grill-me；要规格文档→brainstorming）——本源给出 brainstorming 的机制细节与完整实录。
- **路线分化观察**：brainstorming 坚持"一次一问"，而 mattpocock 侧 grilling v1.2 已重构为 round-by-round 批量轮次（13 问约 3 轮，[[Matt Skills v1.2 grilling 重构]]）——两条路线在**访谈节奏**上正式分道扬镳，已登记 [[Superpowers 与 mattpocock skills 路线对照]]。
- 老谭在上一系列教的正是 grill 家族访谈（[[grill 家族与需求冰山]]），同一作者对两条路线的访谈技能都有教学实录。
- 四轮产出（范围/领域/草图/验收）与 [[Matt Pocock main flow 五环节]] 的规格化环节产出结构相近。

## ⚠️ 矛盾 / 待澄清

- **机制侧重不完整（非冲突）**：wiki 既有口径（[[Superpowers 与 mattpocock skills 路线对照]] 表）把 brainstorming 描述为"规格生产流程（2-3 方案 → 设计文档 → writing-plans）"；本源只讲"一次一问访谈 → spec"，未提"2-3 方案"环节——判为教学化简化，引用官方机制时以 SKILL 原文为准。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[2026 年再看 Superpowers：grill-me 场景选型]] · [[Superpowers writing-plans 施工图]] · [[Matt Skills v1.2 grilling 重构]] · [[grill 家族与需求冰山]] · [[Superpowers 与 mattpocock skills 路线对照]]
