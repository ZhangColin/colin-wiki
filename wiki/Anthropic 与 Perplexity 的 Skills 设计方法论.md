---
type: source
title: "Anthropic 与 Perplexity 的 Skills 设计方法论"
domain: [AI编程]
tags: [Agent Skills, 上下文工程, Skills 设计方法论, 评估, Anthropic, Perplexity]
sources: []
source_path: "raw/articles/Anthropic 和 Perplexity 公开了内部 Skills 设计方法论：9 类分类、3 层成本、4 层 Eval.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491095&idx=1&sn=5cb086d7eee87743768d68025a5ae6da"
author: "[[运维有术]]"
date_published: "2026-06-12"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [9 类分类 3 层成本 4 层 Eval, Agent Skills 上下文工程方法论, Anthropic Perplexity Skills 方法论]
---

# Anthropic 与 Perplexity 的 Skills 设计方法论

> 一句话要点：[[运维有术]] 综述 [[Anthropic]]（Thariq Shihipar，Claude Code 团队）与 [[Perplexity]] Research 两篇官方技术博客，提炼出 Skills 即上下文工程的共识，以及三层成本模型、Hub-and-Spoke 结构、Gotchas 飞轮、四层 Eval 套件四根方法论柱子。

## 关键要点

- 核心共识：**Skills 不是 prompt 也不是插件，是上下文工程（Context Engineering）**。[[Perplexity]] 原话："如果你像写代码一样写 Skill，你会失败"；[[Anthropic]] 的 Thariq Shihipar 纠正"Skills 不只不过是个 markdown 文件"——它是含脚本、资源、数据的文件夹。
- **LLM 自己写的 Skills 平均无收益**——[[Perplexity]] 原话"模型无法可靠地编写它们受益的程序性知识"；高质量 Skill 必须人基于实战写 + Eval 套件持续验证。
- Python 之禅（PEP20）在 Skill 领域大面积失效（[[Perplexity]] 对比表）："简单优于复杂"错、"显式优于隐式"错、"稀疏优于密集"错、"特殊情况不足以打破规则"错、"容易解释就是好主意"错。
- **三层成本模型**（[[Perplexity]]）：Index 层（每 skill name+description，约 100 tokens/skill，每 session 每用户始终付费，"Every Skill is a Tax"）；Load 层（完整 SKILL.md body，约 5,000 tokens）；Runtime 层（scripts/、references/、assets/，无上限，仅 agent 实际读取时付费）。核心"按需加载，分级付费"。
- **Hub-and-Spoke 目录结构**：`SKILL.md`（hub）+ `scripts/` + `references/` + `assets/` + `config.json`。对应渐进式披露三阶段：Discovery → Activation → Execution。
- [[Anthropic]] 9 类分类体系：库与 API 参考、产品验证、数据获取与分析、业务流程与团队自动化、代码脚手架与模板、代码质量与审查、CI/CD 与部署、运维手册、基础设施运维。亮点案例：`babysit-pr`、`/careful`（`PreToolUse` 钩子拦截危险命令）、`adversarial-review`。
- [[Perplexity]] 5 步开发流程：**Step 0 写 Evals（最先，负面示例比正面更重要）→ Step 1 写 Description（公认最难，以 "Load when..." 开头 ≤50 字）→ Step 2 写 Body → Step 3 搭层次结构 → Step 4 迭代发布**。
- **Gotchas 飞轮**：agent 失败→加 gotcha；不该加载却加载→收紧 description+加负面 evals；该加载没加载→加关键词+正面 evals。**不应靠加长指令解决问题**，Gotchas 列表才是高 ROI 的长期资产。
- **四层 Eval 套件**（[[Perplexity]]）：Skill 加载 Eval、渐进加载 Eval、端到端任务 Eval、跨模型 Eval。
- 开放标准与生态：2025-12-18 [[Anthropic]] 将 Agent Skills 发布为开放标准并捐给 Agentic AI Foundation（AAIF）。合作伙伴：Atlassian、Figma、Notion、Canva、Sentry、Cloudflare、Ramp、Box；微软在 VS Code/GitHub 采纳。

## 详细笔记

- 品味类 Skills 需持续人类投入：[[Perplexity]] 设计 Skills 由设计负责人 Henry Modisett 编写；[[Anthropic]] `frontend-design` 不教写 CSS 而是与用户反复迭代改进设计品味。
- 需要 Skill：模型没特殊上下文会犯错 / 需跨运行保持一致 / 知识持久但不在训练数据。不需要 Skill：模型本就对 / 信息变得比维护快 / 与系统 prompt 重复。
- 作者收尾概括四根柱子：**三层成本模型管 token 经济学，Hub-and-Spoke 管信息组织，Gotchas 飞轮管持续迭代，四层 Eval 套件管质量底线**。

## 与已有内容的关联

- 相关概念：[[Skills 设计方法论]]、[[mattpocock skills]]（writing-great-skills 的 progressive disclosure / leading words 与本文同源）、[[LLM Wiki]]
- 相关实体：[[运维有术]]、[[Anthropic]]、[[Perplexity]]、[[Obsidian]]、[[Andrej Karpathy]]
- 相关源：[[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]]、[[Matt Pocock writing-great-skills 八词诊断]]、[[LLM Wiki 方法论 gist]]

## ⚠️ 矛盾 / 待澄清

- **与 colin-wiki 自身规则（CLAUDE.md / Karpathy LLM Wiki）的对照（推论，需显式记录）**：
  - colin-wiki 当前未实现 **Eval/评估体系**（本文 Step 0 + 四层 Eval），也未做 **Gotchas 飞轮**（CLAUDE.md 的"⚠️ 矛盾"区块是粗粒度近似）。这是本 wiki 相对成熟 Skills 工程体系的两条方法论缺口。
  - "LLM 自己写的 Skills 平均无收益"与 colin-wiki 让 Claude 自动 ingest/写 wiki 页面的做法存在**张力**：wiki 页面不是 Skill，影响有限，但提示自动生成的摘要应有人审 + 沉淀 gotcha，而非纯模型产物。
- **Python 之禅失效**：若 wiki 其他源引用 PEP20 作为编码哲学，需注意 [[Perplexity]] 此表将其限定为"通用编程场景成立，Skill 场景大面积失效"。
- 数据时点：开放标准捐 AAIF 时间（2025-12-18）、Claude Skills 上线（2025-10）、两篇博客时间（2026-03 / 2026-05）为原文陈述，可靠。

## 相关页面

- [[Skills 设计方法论]] · [[Anthropic]] · [[Perplexity]] · [[mattpocock skills]] · [[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]] · [[Matt Pocock writing-great-skills 八词诊断]] · [[运维有术]] · [[LLM Wiki 方法论 gist]] · [[LLM Wiki]] · [[Obsidian]] · [[Andrej Karpathy]]
