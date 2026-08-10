---
type: concept
title: LLM Wiki
domain:
  - 知识管理
  - AI工具
tags:
  - 知识库
  - 方法论
  - RAG
  - Memex
sources:
  - "[[LLM Wiki 方法论 gist]]"
  - "[[Karpathy 的 LLM Wiki 搭建实战]]"
created: 2026-07-24
updated: 2026-07-24
status: active
aliases:
  - LLM Wiki 模式
  - Karpathy Wiki
---

# LLM Wiki

> 一句话定义：[[Andrej Karpathy|Karpathy]] 提出的知识库构建模式——LLM 不只在查询时检索，而是**增量构建并维护一个持久、复利的 markdown wiki**；人负责 sourcing 与提问，LLM 负责所有记账。本概念是 colin-wiki 的核心主题与方法论依据。

## 核心观点

### 1. 持久、复利的产物（vs RAG 的不累积）

主流 RAG（NotebookLM、ChatGPT 文件上传）每次查询从零检索、拼凑片段、问完即散——"没有任何东西被建立起来"。LLM Wiki 反过来：摄入新源时，LLM 读全文、提取关键信息、整合进现有 wiki、更新实体页、修订综述、标注矛盾。**交叉引用已建好、矛盾已标出、综述反映你读过的一切**。一次摄取常触及 10–15 个页面。每加一篇、每问一次，wiki 都在变厚。

### 2. 核心分工：人 sourcing，LLM 记账

> "You never (or rarely) write the wiki yourself — the LLM writes and maintains all of it."

人负责 sourcing、探索、提问、判断意义；LLM 做所有 grunt work（summarizing、cross-referencing、filing、bookkeeping）。金句：「Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase.」

### 3. 三层架构（读写权限隔离）

| 层 | 归谁 | 读写 |
| --- | --- | --- |
| Schema（`AGENTS.md`/`CLAUDE.md`） | 人与 LLM 共演化 | 定义行为：页面类型、工作流、约定 |
| Raw sources（`raw/`） | 人 | 只读给 LLM，事实来源，不可变 |
| Wiki（`wiki/`） | LLM 全权维护 | 人只读，浏览/点链接/看图谱 |

读写隔离是骨架：raw 防止 LLM 篡改事实源，wiki 防止人手动改坏一致性。

### 4. 三大操作

- **Ingest（摄取）**：放新源进 `raw/` → LLM 读全文 → 与你过要点 → 写 source 摘要页 → 更新相关 entity/concept 页 → 标矛盾 → 更新 index + log。偏好**逐篇、人在环**。
- **Query（查询）**：先读 index 定位 → 沿 `[[]]` 深读 → 综合作答带引用 → **好回答沉淀回 wiki 成新页面**（对比/分析/连接不消失进聊天记录）——复利的主要来源。
- **Lint（健康检查）**：扫矛盾/孤儿页/断链/过时/缺页/index 不同步/死链/孤立图。能自动修的（反向链接、计数）直接修；需判断的（矛盾、过时）只报告、人在环。

### 5. 四种页面 + 建页规则（自下而上，不急着抽象）

| 类型 | 建页规则 | 理由 |
| --- | --- | --- |
| source（来源） | 每篇资料都建 | 每个源对应一个摘要页 |
| entity（实体） | 首次被专段讨论即建 | 人/产品/机构，单篇已能刻画 |
| concept（概念） | **≥2 篇来源提到才建**，先 pending | 概念易过度抽象，先观察 |
| synthesis（综合） | ≥2 篇立场不同时建，或 Query 沉淀 | 对比/综述/跨源分析 |

### 6. 两个枢纽文件

- `INDEX.md`（内容导向）：全库目录，每页一行链接 + 一句话；Dataview/Bases 基于 frontmatter 动态聚合；中等规模（~100 源、几百页）够用，免向量 RAG。
- `LOG.md`（时间导向）：append-only，`## [YYYY-MM-DD] op | 标题`，`grep "^## \[" LOG.md | tail -N` 查最近。

### 7. 为什么成立

维护知识库累人的不是读/想，是**记账**。人类因维护成本增长快于价值而放弃 wiki。LLM 不无聊、不忘更新交叉引用、能一次碰十几个文件还一致——**记账被外部化给 LLM，对用户维护成本趋近于零**。思想源头是 [[Vannevar Bush]] 1945 的 Memex：他有愿景，没解决"谁维护"，LLM 补上了。

## 证据 / 来源

- [[LLM Wiki 方法论 gist]] — Karpathy 原文，抽象模式（core idea、architecture、operations、indexing/logging、why this works）。
- [[Karpathy 的 LLM Wiki 搭建实战]] — [[运维有术]] 的中文解读 + Obsidian 落地（目录、frontmatter 字段、建页规则、初始化步骤）。

## ⚠️ 矛盾 / 未解问题

colin-wiki 对模式有几处**有意本地化**（详见 [[Karpathy 的 LLM Wiki 搭建实战]] 差异表）：

- **Schema 命名**：原文/文章主张单一文件（gist 用 `CLAUDE.md`，文章主张 `AGENTS.md`）；colin-wiki 取**双镜像同步**以兼顾 Claude Code 原生识别与跨工具通用。
- **目录结构**：文章用子目录（`wiki/实体/` 等）；colin-wiki 用**扁平** `wiki/`。
- **页面类型第四类**：文章称「对比」comparison；colin-wiki 用更广义的「综合」synthesis。
- **未解**：随源增多，扁平 `wiki/` 与单一 `INDEX.md` 是否仍够用（Karpathy 估~100 源为界），届时需引入搜索（如 qmd）。

## 相关页面

- **来源**：[[LLM Wiki 方法论 gist]] · [[Karpathy 的 LLM Wiki 搭建实战]]
- **实体**：[[Andrej Karpathy]] · [[Vannevar Bush]] · [[Obsidian]] · [[Dataview]] · [[运维有术]]
- *待建概念（pending，≥2 独立来源脉络再正式建）：RAG · Ingest · Query · Lint · Memex · Schema/AGENTS.md*
