---
type: source
title: "Superpowers dispatching-parallel-agents 并行派发"
domain: [AI编程, 软件工程, 多代理]
tags: [Superpowers, 老谭superpowers系列, dispatching-parallel-agents, 并行派发, 接口契约, 任务拆分]
sources: []
source_path: "raw/articles/12｜superpowersdispatching-parallel-agents 并行派发多个 AI.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484318&idx=1&sn=b675d5193dd74346291819dc68e51f7f"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-08"
created: 2026-09-10
updated: 2026-09-10
status: active
aliases: [dispatching-parallel-agents, 并行派发]
---

# Superpowers dispatching-parallel-agents 并行派发

> 一句话要点：`dispatching-parallel-agents` 讲的不只是"怎么让 AI 并行"，更是**什么活才配并行**——任务互不依赖、各自独立验证、契约先钉死、边界写清楚；判断标准："拆开之后，每个任务能不能孤零零地完成并验证？"

## 关键要点

- **核心是"拆"**：① 拆到任务之间不互相依赖（A 改 A 的文件、B 改 B 的文件，谁也不等谁）；② 拆到每个任务能独立验证（A 跑后端测试、B 看前端页面）；③ **拆之前先定接口契约**（前端调什么接口、返回什么 JSON，先钉死）；④ 每个子代理只领一个任务，边界写清不许越界。"拆不好，并行就是灾难；拆得好，收工时拼起来就是完整功能。"
- **实例（超支提醒拆两路）**：契约 `GET /api/budgets?month=2026-08` 每项附"本月已花 `spent`"（超支判定放后端，前端只渲染标红）→ 任务单 A（后端加 spent 字段，只改 `ReportController`/`LedgerAppService`/测试，验收 `mvn test` 绿）/ 任务单 B（前端 `Budgets.vue` 标红 + 提示，只改前端，验收页面可见）——**每张任务单都含文件边界 + 验收标准**，"这是防止并行变踩踏的护栏"。
- **收工纪律**：逐个验收不打包（谁没过打回去谁重做，不拖累另一边）；拼接时验契约没破（字段名对得上）；合并后**主干再跑完整测试**。
- **什么时候不该并行（四情形）**：两个任务改同一个文件；B 依赖 A 的产出；任务太小（10 分钟的活，"派发本身的沟通成本比干活还高"）；验收标准说不清。
- 可配合 worktree（10 篇）或各领各的文件；主会话当"调度员"等结果。

## 详细笔记

与 13 篇的分工：dispatching = **我拆好任务派几个 AI 并行**（人当调度员）；subagent-driven-development = **整个功能委托出去，AI 自己拆、自己派、自己审**（AI 当项目经理）。两者是并行协作的两档自动化。

## 与已有内容的关联

- 手动前置形态见 [[子代理驱动开发]]（dispatching 是人肉版、SDD 是委托版）。
- "契约先钉死再并行"与 [[TAB 大仓 Harness 工程化实战]] 的"下游不可改上游产物"同为并行/接力防冲突纪律——一个锁接口契约，一个锁产物边界。
- 越界防护（"只改后端不碰前端任何文件"）与 TAB 角色"必读/必写/不许做"三栏契约同构。
- 与 [[Cole Medin 黑灯工厂实验]] 的 bash 调度器相比：本技能是人主导的轻量并行，黑灯工厂是全自动多 agent 管道——自主度谱系（[[AI Coding 自主度分级]]）上的不同档位。

## ⚠️ 矛盾 / 待澄清

- 无矛盾。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers using-git-worktrees 工作树隔离]] · [[Superpowers subagent-driven-development AI 项目经理]] · [[子代理驱动开发]] · [[HITL 与 AFK]] · [[TAB 大仓 Harness 工程化实战]]
