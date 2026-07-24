# 日志

> append-only 时间线。格式：`## [YYYY-MM-DD] <op> | <标题>`。
> 查最近 N 条：`grep "^## \[" LOG.md | tail -N`。只追加，不改写历史。

## [2026-07-24] init | 初始化 wiki 结构

按 `llm-wiki.md` 方法论搭建三层架构（raw / wiki / schema），建立规则文件（CLAUDE.md + AGENTS.md）、四种页面模板、INDEX/LOG 与 Bases 视图，并把 Obsidian 附件目录指向 `raw/assets`。

## [2026-07-24] ingest | LLM Wiki 方法论 gist

摄入根目录 `llm-wiki.md`（[[Andrej Karpathy|Karpathy]] 方法论原文，本 wiki 宪法）为 source 页 [[LLM Wiki 方法论 gist]]。与中文教程同源、互为补充；为 [[LLM Wiki]] 概念页提供第二来源。

## [2026-07-24] ingest | Karpathy 的 LLM Wiki 搭建实战

摄入 [[运维有术]] 的中文实战教程（微信公众号，2026-07-03）。建 source 摘要页，并据此建实体 [[Andrej Karpathy]]、[[Obsidian]]、[[Vannevar Bush]]、[[Dataview]]、[[运维有术]] 与核心概念 [[LLM Wiki]]。显式记录文章与 colin-wiki 的本地化取舍（Schema 双镜像、扁平 wiki/、英文 type、synthesis 第四类）。pending 登记 RAG/Ingest/Query/Lint/Memex/Schema 等，待后续不同脉络源触发独立建页。

## [2026-07-24] update | llm-wiki.md 归位至 raw/articles/

将 Karpathy LLM Wiki gist 原文从仓库根目录移至 `raw/articles/llm-wiki.md`，使其符合三层架构「源在 raw」规范。同步更新 `CLAUDE.md` + `AGENTS.md`（方法论依据引用）、[[LLM Wiki 方法论 gist]] 的 `source_path`、[[Karpathy 的 LLM Wiki 搭建实战]] 的原文链接。另删除根目录 0 字节空占位文件 `LLM Wiki.md`（命名歧义致 Obsidian 误建，使 `[[LLM Wiki]]` 链接错误指向空文件，已消除歧义）。
