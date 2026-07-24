# 日志

> append-only 时间线。格式：`## [YYYY-MM-DD] <op> | <标题>`。
> 查最近 N 条：`grep "^## \[" LOG.md | tail -N`。只追加，不改写历史。

## [2026-07-24] init | 初始化 wiki 结构

按 `llm-wiki.md` 方法论搭建三层架构（raw / wiki / schema），建立规则文件（CLAUDE.md + AGENTS.md）、四种页面模板、INDEX/LOG 与 Bases 视图，并把 Obsidian 附件目录指向 `raw/assets`。
