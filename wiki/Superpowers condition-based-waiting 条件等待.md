---
type: source
title: "Superpowers condition-based-waiting 条件等待"
domain: [AI编程, 软件工程, 测试]
tags: [Superpowers, 老谭superpowers系列, condition-based-waiting, 异步测试, Awaitility, 轮询]
sources: []
source_path: "raw/articles/06｜superpowerscondition-based-waiting 条件等待-别让测试睡大觉.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484279&idx=1&sn=979c264b40fd7566a6b7d4a79c71ea81"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-31"
created: 2026-09-10
updated: 2026-09-10
status: active
aliases: [condition-based-waiting, 条件等待, Awaitility]
---

# Superpowers condition-based-waiting 条件等待

> 一句话要点：`condition-based-waiting` 把异步测试里的"等多久"（固定 sleep，慢且脆）换成"等什么"——轮询检查目标条件，成立即停、超时才弃；后端 Awaitility、前端轮询接口，同一种不猜时间的哲学。

## 关键要点

- **痛点**：异步任务完成时间不定，`Thread.sleep(1000)` 两毛病——慢（200ms 的活硬等 1s）+ 脆（任务变慢就假红、变快就白等）。本质："**你在等时间，而不是等结果**。"
- **核心机制**：轮询 + 超时——每隔一小会儿检查目标条件；条件成立立刻继续（不浪费 1ms）；设超时上限，一直不成立就报错（不无限等）。
- **后端 Awaitility 4.2.2**：对账异步案例（`AsyncReconciler` 后台线程 200ms 汇总 → `Reconciliation.summary` 由 null 变非 null），测试写 `await().atMost(Duration.ofSeconds(5)).untilAsserted(() -> assertThat(recon.summary()).isNotNull())`——"它等的是条件，不是时间。"
- **前端同构**：`POST /api/reconciliations` 触发后 `setInterval` 每 300ms 查 `GET .../latest`，`status === 'DONE'` 即 `clearInterval`——对比"睡大觉版"`setTimeout(拿结果, 3000)` 又在猜 3 秒够不够。
- **纪律细节**：超时上限必须有（`atMost` 是保命绳）；轮询间隔别太短（别把后端打爆）；异步任务被中断要恢复中断位（`catch (InterruptedException) { Thread.currentThread().interrupt(); }`——"线程协作的礼貌"）。

## 详细笔记

本技能是 18 技能口径（2026-08 仓库）新增技能之一（[[Superpowers 安装与技能全景]]），v5.0.7 的 14 技能口径里没有——wiki 此前从未收录其内容，本源是首个中文教学展开。

## 与已有内容的关联

- 与 07 篇坑二"依赖时机"互为正反（[[Superpowers testing-anti-patterns 测试反模式]]）：反模式篇列 sleep 为坑，本篇给正解。
- "等条件不猜时间"与 [[Agent 测评 Eval]] 的确定性原则（能判定的绝不用猜）精神同构——把不可靠的时间猜测换成可靠的条件判定。
- 前端轮询 DONE 状态与 [[从 Chat UI 到 Agent UI]] 的 Run 状态机（轮询执行状态）是同一模式在 UI 层与测试层的两面。

## ⚠️ 矛盾 / 待澄清

- 无矛盾。注意 Awaitility 为 Java 生态选型（"superpowers 生态最常用的轮询库"系老谭口径），技能本身语言无关。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers TDD 红绿循环]] · [[Superpowers testing-anti-patterns 测试反模式]] · [[Agent 测评 Eval]] · [[从 Chat UI 到 Agent UI]]
