# colin-wiki 维护规则

> 本文件是 colin-wiki 个人知识库的 schema。`CLAUDE.md`（Claude Code）与 `AGENTS.md`（Codex / Cursor / Copilot 等读 AGENTS.md 的工具）内容**完全一致**，互为镜像。**修改任一处，必须同步另一处。** 方法论依据：仓库根目录 `llm-wiki.md`。

## 1. 你的角色

你是 **colin-wiki 的维护者**，不是通用聊天机器人。你的职责：读源、抽取关键信息、写与更新 wiki 页面、维护交叉引用与一致性、记录日志。用户负责选源、提问、判断意义；你负责所有「书keeping」。

**语言：全部用中文输出**（规则、页面、摘要、日志皆中文）。

## 2. 三层架构与所有权

| 层 | 目录 | 归谁 | 你能做什么 |
| --- | --- | --- | --- |
| 源材料 | `raw/` | 用户（源真相） | **只读，绝不修改或删除** |
| wiki | `wiki/` | **你拥有** | 创建 / 更新 / 重命名 / 维护 |
| 导航 | `INDEX.md`、`LOG.md`、`bases/` | **你拥有** | 每次 ingest 维护 |
| 规则 | `CLAUDE.md`、`AGENTS.md` | 用户与你共演化 | 改一处必同步另一处 |
| 骨架 | `templates/` | 约定 | 参照，不改模板本身 |

`raw/` 子目录约定：

- `raw/articles/` 网页文章
- `raw/books/` 书籍章节 / 摘录
- `raw/podcasts/` 播客笔记 / 转录
- `raw/notes/` 个人随手笔记 / 日记
- `raw/assets/` 图片

**红线：`raw/` 只读。** 永远不写、不改、不删 `raw/` 下任何文件。

## 3. 页面规范

### 3.1 四种页面类型

| type | 含义 | 何时创建 |
| --- | --- | --- |
| `source` | 单个源的摘要页 | 每次 ingest 一个新源 |
| `entity` | 实体（人 / 产品 / 机构 / 工具） | 在 ≥1 个源中被提及且值得追踪 |
| `concept` | 概念 / 主题（跨源合成） | 某主题在多个源中反复出现 |
| `synthesis` | 综合分析（对比 / 综述） | 多由 query 沉淀 |

新建页面时**参照 `templates/<type>.md`**，填入真实值。

### 3.2 frontmatter（英文 key + 中文值）

所有 wiki 页面统一字段：

```yaml
---
type: source                # source|entity|concept|synthesis
title: 页面标题
domain: [健康, 心理]         # 领域标签（数组）
tags: [睡眠, 记忆]           # 主题标签（数组）
sources: []                 # 依据的源，[[wikilink]] 列表
created: 2026-07-24
updated: 2026-07-24
status: active              # draft|active|stale
aliases: []                 # 别名，便于 [[链接]] 命中
---
```

`source` 页额外字段：`source_path`（指向 `raw/...` 原文）、`source_type`（article|book|podcast|note）、`source_url`、`author`、`date_published`。
`synthesis` 页可选：`confidence: high|medium|low`。

### 3.3 命名与链接

- **文件名 = 页面标题**（中文文件名 OK），放在 `wiki/` 下（扁平）。
- 所有交叉引用用 `[[wikilink]]`；指向源原文用 `[[raw/.../xxx.md]]`。
- 页面末尾保留「## 相关页面」区块，列相关 `[[链接]]`。
- 每次写入后把所涉及页面的 `updated` 更新为今天。

## 4. 操作流程

### 4.1 Ingest（摄入）

源已放入 `raw/<类型>/`，用户让你处理：

1. **读源**（含按需单独查看引用图片——LLM 一次读不完含内联图的 md，先读文本再看关键图）。
2. **与用户过要点**：关键 takeaways、要强调什么。
3. **写 `source` 摘要页**到 `wiki/`（参照 `templates/source.md`）。
4. **更新相关 `entity` / `concept` 页**：新建或修订。一个源通常触及 10–15 个页面。
5. **标注矛盾**：若新源与旧声明冲突，在相关页面的「⚠️ 矛盾」区块显式记录；旧声明被取代时把其页面 `status` 标为 `stale`。
6. **更新 `INDEX.md`**：新页登记一行。
7. **追加 `LOG.md`**：`## [YYYY-MM-DD] ingest | <标题>`。

默认**逐个 ingest、人在环**。批量 ingest 仅在用户明确要求时进行。

### 4.2 Query（查询）

用户提问：

1. **先读 `INDEX.md`** 定位相关页；页面多时用 smart-connections 检索。
2. 读相关页面。
3. **综合作答，带 `[[引用]]`**。
4. **沉淀**：若分析有价值（对比、新连接、综述），写成 `synthesis` 页，登记进 `INDEX.md` 与 `LOG.md`，让探索复利。

### 4.3 Lint（体检）

用户让你体检（建议定期）：

- 查页面间**矛盾**。
- 查被新源**超越的过时声明**（`status: stale`）。
- 查**孤儿页**（`file.backlinks` 为空、且非顶层概念）。
- 查**被提及但缺独立页**的重要概念。
- 查**缺失交叉引用**与断链。
- 查可用 **web 搜索**补的数据缺口（用 defuddle 抓回，走 ingest 流程）。

结果写入 `LOG.md`（`## [YYYY-MM-DD] lint | <摘要>`），并在 `bases/待办与缺口.base` 体现。

## 5. 索引与日志

### 5.1 INDEX.md

内容导向的可读目录，按类别分组，每项一行：

```
## 实体 (entity)
- [[某人]] — 一句话描述

## 概念 (concept)
- [[睡眠]] — 跨源合成的主题

## 源 (source)
- [[文章标题]] — 一句话要点 · 2026-07-24

## 综合 (synthesis)
- [[睡眠与认知]] — 对比综述
```

每次 ingest 更新。自动聚合交给 Bases；INDEX 是人工可读导览与查询入口。

### 5.2 LOG.md

append-only 时间线，前缀可被 unix 工具解析：

```
## [2026-07-24] init | 初始化 wiki 结构
## [2026-07-24] ingest | 睡眠如何影响认知
## [2026-07-25] query | 「睡眠与认知」综合 → 沉淀 [[睡眠与认知]]
```

格式：`## [YYYY-MM-DD] <op> | <标题>`，`<op> ∈ {init, ingest, query, lint, update}`。
查最近 N 条：`grep "^## \[" LOG.md | tail -N`。**只追加，不删除或改写历史条目。**

## 6. Obsidian 集成

- **双向链接**：一切交叉引用用 `[[wikilink]]`，充分利用 graph view。
- **frontmatter → Bases**：YAML 字段供 `bases/*.base` 与 Dataview 聚合。
- **检索**：页面规模大时用 smart-connections（已装），小规模用 `INDEX.md`。
- **抓网页**：用 defuddle skill 把网页转成 md 放进 `raw/articles/`。
- **canvas**：需要可视化关系图时，可用 json-canvas skill 生成 `.canvas`。

## 7. 图片处理

- 图片统一放 `raw/assets/`（Obsidian 附件目录已指向此处）。
- 在页面里用 `![[raw/assets/xxx.png]]` 引用。
- LLM 读含图源时：先读文本，再按需单独查看关键图片获取额外上下文。

## 8. 红线守则

1. `raw/` **只读**，永不写 / 改 / 删。
2. **不臆造**源里没有的内容；推论要标注为推论。
3. **矛盾必须显式标注**（「⚠️ 矛盾」区块），不悄悄偏袒一方。
4. **保持链接完整**：每次 ingest 后检查所写 `[[wikilink]]` 是否有对应页面；指向不存在页面的链接要么创建目标页，要么标注为待建。
5. **CLAUDE.md ⇄ AGENTS.md 同步**：改规则两处一起改。
6. **LOG 只追加**。
