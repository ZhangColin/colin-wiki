---
type: source
title: "LLM Wiki 方法论 gist"
domain: [知识管理, AI工具]
tags: [LLM Wiki, 方法论, 知识库, Memex, git]
sources: []
source_path: "raw/articles/llm-wiki.md"
source_type: article
source_url: "https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f"
author: "Andrej Karpathy"
date_published: ""
created: 2026-07-24
updated: 2026-07-24
status: active
aliases: ["Karpathy LLM Wiki gist", "llm-wiki.md", "LLM Wiki 原文"]
---

# LLM Wiki 方法论 gist

> 一句话要点：Karpathy 提出的 LLM Wiki 模式原文——LLM 不只是查询时检索，而是增量构建并维护一个持久、复利的 markdown wiki；人负责 sourcing 与提问，LLM 负责所有记账。本 gist 是 colin-wiki 的方法论宪法。

## 关键要点

- **与 RAG 的区别**：RAG 每次查询重新检索、拼凑片段、生成回答，知识不累积。LLM Wiki 让 LLM **增量构建并维护一个持久 wiki**——读全文、提取关键信息、整合进现有 wiki、更新实体页、修订综述、标注矛盾。**wiki 是一个持久的、复利的产物（persistent, compounding artifact）**。
- **核心分工**："You never (or rarely) write the wiki yourself — the LLM writes and maintains all of it." 人负责 sourcing、探索、提问；LLM 做所有 grunt work（summarizing、cross-referencing、filing、bookkeeping）。"Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase."
- **三层架构**：
  - **Raw sources**：不可变，LLM 只读，事实来源。
  - **The wiki**：LLM 全权拥有的 markdown 目录；创建、更新、维护交叉引用、保持一致；人只读。
  - **The schema**：一个文档（e.g. `CLAUDE.md` for Claude Code 或 `AGENTS.md` for Codex），告诉 LLM wiki 结构、约定、ingest/query/维护的工作流。**这是让 LLM 成为有纪律维护者而非通用聊天机器人的关键配置**。人与 LLM 共演化此文件。
- **三大操作**：
  - **Ingest**：放新源进 raw，让 LLM 处理。流程：读源 → 与你讨论要点 → 写摘要页 → 更新 index → 更新相关实体/概念页 → 追加 log。一个源可能触及 10–15 页。偏好逐篇、保持参与；也可批量少监督。
  - **Query**：提问。LLM 找相关页、读、综合作答带引用。**关键洞察：好回答可沉淀回 wiki 成新页面**——对比、分析、连接不应消失在聊天记录里。答案可多种形态：md 页、对比表、slide deck（Marp）、chart（matplotlib）、canvas。
  - **Lint**：定期健康检查。查矛盾、过时声明、孤儿页、被提但缺页的重要概念、缺失交叉引用、可用 web 搜索补的数据缺口。
- **两个枢纽文件**：
  - `index.md`（内容导向）：全库目录，每页链接 + 一句话摘要 + 可选元信息，按类别分组。LLM 每次 ingest 更新；查询时先读 index 定位。中等规模（~100 源、几百页）出奇地够用，避免向量 RAG。
  - `log.md`（时间导向）：append-only 时间线。一致前缀 `## [2026-04-02] ingest | Article Title` 使其可被 unix 工具解析（`grep "^## \[" log.md | tail -5`）。
- **可选 CLI 工具**：规模大了可加搜索引擎。[qmd](https://github.com/tobi/qmd)——本地 markdown 搜索，混合 BM25/向量 + LLM re-ranking，全设备端；有 CLI 和 MCP server。也可让 LLM 帮忙 vibe-code 一个简单脚本。
- **Tips**：Obsidian Web Clipper（网页→md）；图片下载到本地（Attachment folder 设 `raw/assets/`，绑快捷键）；**LLM 无法一次读完含内联图的 md，先读文本再按需看图**；Graph view 看 wiki 形态；Marp（md slide）；Dataview（frontmatter 查询）；**wiki 就是 git 仓库**，免费拿到版本历史/分支/协作。
- **Why this works**：维护知识库累人的不是读/想，是记账。人类因维护成本增长快于价值而放弃 wiki。LLM 不无聊、不忘更新、能一次碰 15 文件。维护成本趋近于零。
- **思想源头**：Vannevar Bush 1945 **Memex**——私人、主动策划、文档间联想连接。Bush 没解决的"谁做维护"，LLM 解决了。
- **元说明**（结尾）："This document is intentionally abstract. It describes the idea, not a specific implementation." 一切可选、模块化——挑有用的，忽略没用的，与你的 LLM agent 共同实例化一个适合你的版本。

## 详细笔记

### 适用场景（Karpathy 列举）

个人（目标/健康/心理/自我提升）、研究（数周/数月深入主题，搭带演进论点的综述 wiki）、读书（每章建页，像 [Tolkien Gateway](https://tolkiengateway.net/wiki/Main_Page) 那样的 fan wiki——人物/地点/事件/语言互联）、团队（内部 wiki，喂 Slack/会议转录/项目文档/客户电话，可能需人在环审核）、竞争分析、尽调、旅行规划、课程笔记、爱好深挖。

### 与中文教程的关系

本 gist 是原文（抽象方法论）。[[Karpathy 的 LLM Wiki 搭建实战]] 是对其进行解读 + 给出 Obsidian 落地实操（目录结构、建页规则、frontmatter 具体字段）的中文教程。二者同源，互为补充。

## 与已有内容的关联

- **宪法地位**：本 gist 是 colin-wiki 的方法论依据，规则文件 `CLAUDE.md` / `AGENTS.md` 即据此实例化。
- **核心概念**：[[LLM Wiki]]。
- **中文解读**：[[Karpathy 的 LLM Wiki 搭建实战]]。
- **相关实体**：[[Andrej Karpathy]]、[[Vannevar Bush]]（Memex）、[[Obsidian]]、[[Dataview]]。

## ⚠️ 矛盾 / 待澄清

- 本 gist 原文用 `CLAUDE.md` 命名 schema（注明亦可 `AGENTS.md`）。colin-wiki 取**双文件镜像同步**（见 [[Karpathy 的 LLM Wiki 搭建实战]] 的差异表）。
- gist 未规定目录结构、页面类型细分、frontmatter 字段（"intentionally abstract"）。colin-wiki 的扁平 `wiki/`、四种 type（source/entity/concept/synthesis）、扩展 frontmatter 均为本地化实例化。

## 相关页面

- [[Karpathy 的 LLM Wiki 搭建实战]] — 中文解读
- [[LLM Wiki]] — 核心概念
- [[Andrej Karpathy]]、[[Vannevar Bush]]、[[Obsidian]]、[[Dataview]]
