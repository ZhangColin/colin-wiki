---
type: entity
title: Perplexity
domain: [AI编程, AI工具]
tags: [Perplexity, Perplexity Research, Skills 设计方法论, 上下文工程]
sources:
  - "[[Anthropic 与 Perplexity 的 Skills 设计方法论]]"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases:
  - Perplexity Research
---

# Perplexity

> 一句话定义：AI 搜索/研究公司；其 Perplexity Research 团队于 2026-05 发布 Skills 设计方法论（三层成本模型、Hub-and-Spoke、Gotchas 飞轮、四层 Eval），与 [[Anthropic]] 共同确立"Skills 即上下文工程"共识。

## 概述

在本 wiki 中，Perplexity 作为 [[Skills 设计方法论]] 的两方权威来源之一出现（另一为 [[Anthropic]]）。两文发布相差两个月（Anthropic 2026-03，Perplexity 2026-05），场景不同（Anthropic 讲开发者扩展机制，Perplexity 讲终端用户 Agent 能力模块），理念几乎一致：**Skills 不是 prompt 也不是插件，是上下文工程**。

## 关键属性 / 事实

- **核心论断**："如果你像写代码一样写 Skill，你会失败"；"LLM 自己写的 Skills 平均无收益"。
- **三层成本模型**：Index 层（约 100 tokens/skill，每 session 始终付费，"Every Skill is a Tax"）/ Load 层（约 5,000 tokens）/ Runtime 层（无上限，按需付费）。
- **Hub-and-Spoke 结构**：`SKILL.md`(hub) + `scripts/` + `references/` + `assets/` + `config.json`；对应渐进式披露三阶段 Discovery→Activation→Execution。
- **5 步开发流程**：Step 0 写 Evals（最先，负面示例比正面更重要）→ Step 1 写 Description → Step 2 写 Body → Step 3 搭层次结构 → Step 4 迭代发布。
- **Gotchas 飞轮**：失败模式→gotcha 积累的持续迭代机制（不应靠加长指令解决问题）。
- **四层 Eval 套件**：Skill 加载、渐进加载、端到端任务、跨模型。
- **Python 之禅（PEP20）在 Skill 场景大面积失效**（"简单优于复杂"等 5 条均错）。
- **设计负责人**：Henry Modisett（设计类 Skills 由其编写，纯主观品味模型学不到）。

## 相关页面

- [[Anthropic]] · [[Skills 设计方法论]] · [[Anthropic 与 Perplexity 的 Skills 设计方法论]] · [[mattpocock skills]] · [[Superpowers]]
