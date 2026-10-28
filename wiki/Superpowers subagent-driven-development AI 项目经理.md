---
type: source
title: "Superpowers subagent-driven-development AI 项目经理"
domain: [AI编程, 软件工程, 多代理]
tags: [Superpowers, 老谭superpowers系列, subagent-driven-development, 子代理, 两级审查, 项目经理]
sources: []
source_path: "raw/articles/13｜superpowerssubagent-driven-development 子代理开发 - 请个 AI 当项目经理.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484330&idx=1&sn=5a62f33abf0ad9efd1c07ef280fabec8"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-09"
created: 2026-09-10
updated: 2026-09-10
status: active
aliases: [subagent-driven-development, SDD 教学版, AI 项目经理]
---

# Superpowers subagent-driven-development AI 项目经理

> 一句话要点：`subagent-driven-development` 让主会话当项目经理——读计划 → 拆任务 → 派子代理 → **两级审查（子代理自审 + 主会话复审）** → 合并收尾；"一个功能从计划到落地，AI 自己转完整个流水线"，人只喂计划、说范围、最终验收。

## 关键要点

- **与 dispatching 的分工**：dispatching-parallel-agents = 我拆好任务派几个 AI 并行（人当调度员）；SDD = **整个功能委托出去**，AI 自己拆、自己派、自己审（AI 当项目经理，"它不用发工资"）。
- **运转模型**：主会话（项目经理）读计划（吃透目标/任务/验收）→ 拆子任务派子代理（各用各的 worktree，任务单同样含文件边界 + 验收）→ 两级审查 → 循环（不达标打回，达标合入）。
- **两级审查（老谭口径）**：第一级**子代理自审**（跑 `mvn test` 贴结果 + 对照任务验收逐条自查）；第二级**主会话复审**（读代码、对照计划、查契约一致性、抓夹带私货）。"只信自审 = 选手给自己打分；只信复审 = 细节问题容易被漏，两层互补。"打回要说清理由（实例：字段名 `spentAmount` ≠ 契约的 `spent`）。
- **实录**：超支提醒全栈落地，"我全程只说了三句话：喂计划、说范围、最后点头验收。中间的拆、派、审、合，全是项目经理 AI 干的。"
- **两个清醒剂**：① 项目经理质量上限 = 计划质量——"喂得好它才是项目经理，喂得烂它是无头苍蝇"；② **项目经理也在你的上下文里干活**——"它拆的任务越多，上下文越挤。一次别让它吞整个大项目，分批来。"
- **何时上**：任务大、步骤多、跨前后端；子任务能独立验证。判断标准："这个活，拆分和审查的收益，盖过派发的开销吗？"20 行小改动别用（杀鸡牛刀）。

## 详细笔记

对应 [[Superpowers]] 7 步流程第 4 步。子代理仍各用各的 worktree（10 篇），收尾走 11 篇的合并 + 主干再测——系列至此把"并行四篇"（worktrees/finishing/dispatching/SDD）串成完整谱系。

## 与已有内容的关联

- 概念页 [[子代理驱动开发]] 已并入本源（第 8 源），三口径并排：v5.x 两轮独立 review / v6.0 单 task-reviewer（只读怀疑论者）/ 老谭教学版自审+复审。
- "上下文越挤"的清醒剂与 [[smart zone]] 会话预算纪律、[[Matt Pocock 会话边界五问决策树]] 的 subagent 分流同题。
- "喂的计划决定项目经理质量"呼应 [[TAB 大仓 Harness 工程化实战]] 的"方案一致性"审查——计划/契约质量是 SDD 的上游命门。
- 与 [[Cole Medin 黑灯工厂实验]] 对比：SDD 人在环（人喂计划、最终验收），黑灯工厂 AFK 极端（人只提 issue + 发版）——[[HITL 与 AFK]] 谱系两档。

## ⚠️ 矛盾 / 待澄清

- **两级审查口径差异（重要）**：本文"子代理自审 + 主会话复审"与 wiki 既有两口径均不同——v5.x 是**两轮独立 review**（规格合规 + 代码质量，独立审查代理，[[Superpowers 7阶段交付SKU库存扣减]]）；v6.0 合并为**单 task-reviewer**（只读、怀疑论者"Do Not Trust the Report"、controller 禁止辅导 reviewer，[[Superpowers 6.0 reviewer 只读重写]]）。老谭版未体现"审查者与实现者不同上下文""reviewer 只读"等硬约束——判为**教学化简化口径**（或对应某中间版本），已在 [[子代理驱动开发]] 三口径并排标注，不推翻既有结论。
- "子代理自审"作为第一级与"防自审护短"的原始动机（执行者被假设为判断力差的 junior）存在张力——官方框架靠独立 reviewer 解决，教学版靠主会话复审兜底。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[子代理驱动开发]] · [[Superpowers dispatching-parallel-agents 并行派发]] · [[Superpowers 6.0 reviewer 只读重写]] · [[Superpowers 7阶段交付SKU库存扣减]] · [[smart zone]] · [[HITL 与 AFK]]
