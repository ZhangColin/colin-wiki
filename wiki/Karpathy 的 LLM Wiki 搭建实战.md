---
type: source
title: Karpathy 的 LLM Wiki 搭建实战：三层架构 + 三大操作
domain:
  - 知识管理
  - AI工具
tags:
  - Obsidian
  - 知识库
  - RAG
  - 方法论
sources: []
source_path: raw/articles/Karpathy 的 LLM Wiki 搭建实战：三层架构 + 三大操作，Obsidian + AGENTS.md 让知识库自我维护.md
source_type: article
source_url: https://mp.weixin.qq.com/s/UKJ16eyp1STkkkajTSrjnA
author: 运维有术（术哥）
date_published: 2026-07-03
created: 2026-07-24
updated: 2026-07-24
status: active
aliases:
  - 术哥 LLM Wiki 教程
  - Karpathy LLM Wiki 实战
---

# Karpathy 的 LLM Wiki 搭建实战：三层架构 + 三大操作

> 一句话要点：用 Obsidian 落地 Karpathy 的 LLM Wiki 方法论——人负责 sourcing 和提问，LLM 负责所有"记账"脏活，让知识库持续累积、维护成本趋近于零。

![[raw/assets/Image.png]]
*图：LLM Wiki 三层架构——Schema 配置层、Raw 原始资料只读层、Wiki 知识库由 LLM 全权维护（读写权限隔离）*

## 关键要点

- **RAG 不累积**：主流 RAG（NotebookLM、ChatGPT 文件上传）每次从零检索、问完即散，不记下发现、不标矛盾，"没有任何东西被建立起来"。
- **核心分工**（Karpathy 原话）：**你几乎不用自己写 wiki，只负责 sourcing、探索、提问；LLM 做所有 grunt work——summarizing、cross-referencing、filing、bookkeeping**。
- **维护成本趋近于零的根基**：维护知识库累人的不是读/想，是**记账**（更新交叉引用、保持摘要不过时、标注矛盾、跨几十页维持一致）。这正是人类放弃维护的原因——维护成本增长比价值增长快。LLM 接过记账，对用户成本趋近于零。
- **三层架构（读写权限隔离）**：
  - **Schema（行为配置）**：告诉 LLM 怎么干活的配置文件，让它成为"有纪律的维护者"而非"通用聊天机器人"。
  - **Raw sources（只读）**：文章/论文/图片/数据，只有你能写，LLM 视为只读，是事实来源。
  - **Wiki（LLM 全权维护）**：摘要/实体/概念/对比/综述，你只读不写。
- **三大操作**：
  - **Ingest（摄取）**：放新资料进 `raw/`，LLM 读全文、建摘要页、更新实体/概念页、标矛盾、更新 index 和 log。一次触及 10–15 个页面。偏好逐篇、保持参与。
  - **Query（查询）**：先读 index 定位 → 沿 `[[]]` 链接深读 → 综合作答带引用 → **好回答沉淀回 wiki 成新页面**（复利来源）。
  - **Lint（健康检查）**：扫矛盾/孤儿页/断链/过时/缺页/index 不同步/死链/孤立图。能自动修的（缺反向链接、计数对不上）直接修；不能自动修的（矛盾、过时）只报告，人在环确认。
- **四种页面 + 建页规则（自下而上，不急着抽象）**：
  - 来源：每篇资料都建。
  - 实体（人/公司/产品）：首次被专段讨论就建。
  - 概念：**被 ≥2 篇来源提到才建**，第一次出现先记进 pending 观察。
  - 对比：≥2 篇立场不同时建，或 Query 沉淀。
- **Frontmatter**：`type` / `tags` / `sources`（依据的来源 wikilink，来源页自己不填）/ `created` / `updated`。`type` 供 Dataview 聚合。
- **交叉引用**：`[[]]` 不包反引号；**图片不复制到 wiki，直接引用 raw 路径**，避免双份不同步。
- **两个枢纽文件**：
  - `index.md`（内容导向）：每页一行链接 + 一句话；Dataview 基于 frontmatter 动态聚合；中等规模（~100 源、几百页）够用，不需要向量 RAG。
  - `log.md`（时间导向）：append-only，`## [YYYY-MM-DD] op | 标题`，`grep "^## \[" log.md | tail -N` 查最近。
- **Obsidian 角色**："Obsidian 是 IDE，LLM 是程序员，wiki 是代码库"。Web Clipper 剪藏网页→md；Attachment folder 设固定目录 + 绑快捷键一键下载图片到本地（让 LLM 看本地图而非会失效的 URL）；Graph view 看 wiki 形态；Dataview 跑 frontmatter 查询。
- **wiki = git 仓库**：免费拿到版本历史、分支、协作。
- **刻意不做**：不复制图片/原文进 wiki、不引入向量搜索（100 篇内够用）、不建 tags 目录。判断：中等规模下简单结构比复杂基础设施扛得住。
- **思想源头**：Vannevar Bush 1945 **Memex**——私人、主动策划、文档间有联想连接。他有愿景，但没解决"谁来做维护"——LLM 把这步补上了。
- **元说明**（Karpathy）："This document is intentionally abstract." 方法论只传达模式，具体目录/Schema/格式/工具由领域和偏好决定。

## 详细笔记

### 为什么维护成本能趋近于零

Karpathy 关键洞察：维护知识库累人的不是读、不是想，是**记账**。人类放弃 wiki 是因为维护成本增长比价值增长快。LLM 不会无聊、不会忘记更新交叉引用、能一次碰十几个文件还保持一致。wiki 持续被维护，靠的不是人变勤快，而是记账被外部化给 LLM。

### Schema 命名：AGENTS.md vs CLAUDE.md

文章明确主张**统一用 `AGENTS.md`**：
- Karpathy 原文用 `CLAUDE.md`，但注明 "e.g. CLAUDE.md for Claude Code or AGENTS.md for Codex"——命名面向特定工具。
- 喂给 LLM 的工具不止 Claude Code（还有 OpenCode、Codex、Cursor）。命名 `CLAUDE.md` 换工具可能不被识别，Schema 无法跨工具复用。
- `AGENTS.md` 是跨工具通用约定，Schema 作为"方法论合约"不应绑定特定产品。

### 落地三步走

1. 新建空 Obsidian Vault。
2. `cd` 进 vault 启动 LLM Agent，把 Karpathy gist 链接丢给它 + 指令"按此方法论初始化结构，规则文件用 `AGENTS.md` 命名"。LLM 自动生成目录、`AGENTS.md`、`index.md`（配 Dataview 模板）、`log.md`。
3. （可选）装 Dataview（社区插件）+ Web Clipper（官方浏览器扩展）。

核心：搭 wiki 这件"记账活儿"本身也交给 LLM。人只需建空 vault + 丢 gist。

### 四类适用场景

个人（目标/健康/自我提升）、研究（深入主题增量搭综述 wiki）、读书（每章摄取，建人物/主题/情节页）、团队（内部 wiki，喂 Slack/会议/文档，可能需人在环审核）。

## 与已有内容的关联

- **同源**：本文是对 [[raw/articles/llm-wiki.md|Karpathy 英文 gist]] 的中文解读 + 落地实操，二者是同一方法论的两个表述。摘要见 [[LLM Wiki 方法论 gist]]。
- **核心概念**：[[LLM Wiki]]（本 wiki 的核心主题与方法论依据）。
- **相关实体**：[[Andrej Karpathy]]（方法论提出者）、[[Vannevar Bush]]（Memex 思想源头）、[[Obsidian]]（落地工具/"IDE"）、[[Dataview]]（index 动态聚合）、[[运维有术]]（本文作者）。

## ⚠️ 矛盾 / 待澄清

本文与 colin-wiki 自身设计有几处**有意分歧**（非错误，是本地化取舍，记录备查）：

| 维度 | 本文主张 | colin-wiki 实际做法 | 取舍理由 |
| --- | --- | --- | --- |
| Schema 命名 | 只用 `AGENTS.md` | `CLAUDE.md` + `AGENTS.md` **双镜像同步** | 兼容：CLAUDE.md 被 Claude Code 原生识别，AGENTS.md 保跨工具；改一处必同步另一处（红线第 5 条） |
| wiki 目录 | `wiki/来源/`、`wiki/实体/` 子目录 | `wiki/` **扁平**（文件名 = 标题） | 扁平更利于 wikilink 命中与全局检索 |
| `type` 值 | 中文（来源/实体/概念/对比） | 英文（source/entity/concept/synthesis） | 英文 key 更稳，便于 Bases/Dataview 与工具处理 |
| 第四类页面 | 「对比」comparison | 「综合」synthesis（更广义） | synthesis 涵盖对比 + 综述 + 任意跨源分析 |
| frontmatter | 5 字段 | +`title`/`domain`/`status`/`aliases` | 多维度元信息便于聚合与别名命中 |

→ 结论：colin-wiki 是对 Karpathy 方法论的**本地化实例**，文章说的"一切可选、模块化、按需取舍"正对应这种适配。

## 相关页面

- [[LLM Wiki 方法论 gist]] — 英文原文
- [[LLM Wiki]] — 核心概念
- [[Andrej Karpathy]]、[[Vannevar Bush]]、[[Obsidian]]、[[Dataview]]、[[运维有术]]
