---
type: concept
title: Skills 设计方法论
domain: [AI编程, AI工具]
tags: [Skills 设计方法论, 上下文工程, Context Engineering, progressive disclosure, Eval, gotchas]
sources:
  - "[[Anthropic 与 Perplexity 的 Skills 设计方法论]]"
  - "[[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]]"
  - "[[Matt Pocock writing-great-skills 八词诊断]]"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases:
  - Skills 即上下文工程
  - Context Engineering
  - Agent Skills 方法论
---

# Skills 设计方法论

> 一句话定义：[[Anthropic]] 与 [[Perplexity]] 在 2026 年官方博客中共同确立的共识——**Skills 不是 prompt 也不是插件，是上下文工程（Context Engineering）**；四根柱子：三层成本模型、Hub-and-Spoke 结构、Gotchas 飞轮、四层 Eval 套件。

## 核心观点

### 1. Skills = 上下文工程

Prompt Engineering 优化单次指令；Context Engineering 关注模型决策前如何组织所有相关信息（结构、优先级、加载时机）。把 Skill 当 prompt 写会踩坑：一次性堆一个文件、用描述性语言写功能说明而非触发条件、让模型自己写 Skills。**LLM 自己写的 Skills 平均无收益**（[[Perplexity]] 原话）。

### 2. 三层成本模型（[[Perplexity]]）

- **Index 层**：每 skill name+description，约 100 tokens/skill，**每 session 每用户始终付费**——"Every Skill is a Tax"。
- **Load 层**：完整 SKILL.md body，约 5,000 tokens，加载后每轮累积。
- **Runtime 层**：scripts/、references/、assets/，无上限，仅 agent 实际读取时付费。
- 核心："按需加载，分级付费"。每句指令要问"没有它 agent 会做错吗？不会就不能留"。

### 3. Hub-and-Spoke 结构 + 渐进式披露

`SKILL.md`(hub) + `scripts/` + `references/` + `assets/` + `config.json`。对应 Discovery → Activation → Execution 三阶段。Skill 价值不取决于含多少信息，而取决于模型能否在对的时机找到对的信息。

### 4. Gotchas 飞轮（持续迭代核心）

agent 失败→加 gotcha；不该加载却加载→收紧 description+加负面 evals；该加载没加载→加关键词+正面 evals。**不应靠加长指令解决问题**（加长抬升 Load 层成本影响所有用户），Gotchas 列表才是高 ROI 长期资产。

### 5. 四层 Eval 套件（[[Perplexity]]）

Skill 加载 Eval（精度+召回）→ 渐进加载 Eval（读对 spoke 文件）→ 端到端任务 Eval（LLM judge）→ 跨模型 Eval。Step 0 写 Evals 最先（负面示例比正面更重要）。

### 6. Description 是路由触发器，不是功能文档

以 "Load when..." 开头 ≤50 字。公认最难写的部分。

## 与 [[mattpocock skills]] / [[Superpowers]] 的同源

[[Matt Pocock]] 的 writing-great-skills 词汇（progressive disclosure、leading word、no-op、negation、completion criterion）与本文 progressive disclosure/gotchas 同源——都是"用最小锚点挤出确定性"。[[Anthropic]] 第一条最佳实践"不要说显而易见的事"与 [[Perplexity]] 的 Python 之禅失效论同义。详见 [[Matt Pocock writing-great-skills 八词诊断]] 与 [[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]]。

**v1.2 新证据（2026-08）**：wait-what 全文仅 7 行、一行正文——官方理由 **"concision skills fail by growing"**（精简指令写得越长模型照样 verbose），与 [[Perplexity]] "不应靠加长指令解决问题"完全同源；其 naming 命名的是**听者状态**而非输出形态（与"description 是路由触发器非功能文档"同理）。v1.2 的 #781 harness-neutral（去专有工具名）与双平台 invocation 语义对齐（`disable-model-invocation: true` ⇔ `policy.allow_implicit_invocation: false`），是"同一 skill 跨 harness 行为一致"的工程化实践。详见 [[Matt Pocock Skills v1.2 更新全景]]。

## ⚠️ 矛盾 / 未解问题

- **Python 之禅（PEP20）在 Skill 场景大面积失效**（[[Perplexity]] 对比表）："简单优于复杂""显式优于隐式""稀疏优于密集""特殊情况不足以打破规则""容易解释就是好主意"——5 条在通用编程成立但在 Skill 场景均错。引用 PEP20 作编码哲学时须限定适用域。
- **与本 wiki 自身的方法论缺口（推论）**：colin-wiki 当前未实现 Eval 体系，也未做 Gotchas 飞轮（CLAUDE.md 的"⚠️ 矛盾"区块是粗粒度近似）。这是本 wiki 相对成熟 Skills 工程体系的两条方法论缺口，建议在 Lint 流程补"Eval/gotchas 机制"待办。
- **"LLM 自己写的 Skills 平均无收益"与 colin-wiki 自动 ingest 的张力**：wiki 页面不是 Skill，影响有限，但提示自动生成的摘要应有人审 + 沉淀 gotcha。

## 相关页面

- [[Anthropic]] · [[Perplexity]] · [[Anthropic 与 Perplexity 的 Skills 设计方法论]] · [[mattpocock skills]] · [[Superpowers]] · [[Matt Pocock writing-great-skills 八词诊断]] · [[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]] · [[LLM Wiki]]
