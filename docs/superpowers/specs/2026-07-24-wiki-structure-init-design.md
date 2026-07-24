# colin-wiki 结构初始化 · 设计文档

- **日期**：2026-07-24
- **状态**：已与用户确认设计，待写实现计划
- **方法论来源**：`llm-wiki.md`（LLM Wiki pattern）
- **Vault**：`colin-wiki`（Obsidian + git）

## 1. 目标

依据 `llm-wiki.md` 定义的方法论，在已有的 Obsidian Vault 中初始化一个完整的个人知识库结构，使 LLM 成为「有纪律的 wiki 维护者」而非通用聊天机器人。

结构需满足三个硬性要求：

1. **规则文件用 CLAUDE.md**，作为主 schema。
2. **同时建 AGENTS.md**，内容与 CLAUDE.md 同步，保证跨工具通用（Codex / Cursor / Copilot 等读 AGENTS.md 的工具也能工作）。
3. **Obsidian 原生**：用 `[[wikilink]]` 双向链接、YAML frontmatter、Bases 动态视图、smart-connections 检索；不引入 qmd 等外部检索（小规模 `INDEX.md` 足够，大规模用已装的 smart-connections）。

## 2. 已确认的关键决策

| 维度 | 决策 |
|---|---|
| 主题/用途 | 个人综合知识库（多领域长期积累：目标、健康、心理、读书/播客、文章剪藏等，按主题分类） |
| 规则文件 | `CLAUDE.md`（根，主）+ `AGENTS.md`（根，镜像同步） |
| 语言 | 全中文（规则文件与 wiki 页面内容均用中文） |
| 目录布局 | 混合：顶层极简，`wiki/` 扁平 + frontmatter 分类，`raw/` 松散归类，Bases 做动态聚合 |
| 检索 | smart-connections（已装）+ `INDEX.md`，不引入 qmd |

## 3. 架构与目录结构

三层严格隔离，对应 `llm-wiki.md` 的 Raw sources / The wiki / The schema：

```
colin-wiki/
├── CLAUDE.md            # 规则文件（主，中文，人类可读）
├── AGENTS.md            # 规则文件（与 CLAUDE.md 内容同步 → 跨工具）
├── llm-wiki.md          # 方法论原文（保留，只读参考，不改动）
├── raw/                 # 源材料层 —— 不可变，LLM 只读
│   ├── articles/        # 网页文章（Web Clipper / defuddle 产出）
│   ├── books/           # 书籍章节/摘录
│   ├── podcasts/        # 播客笔记/转录
│   ├── notes/           # 个人随手笔记、日记
│   └── assets/          # 图片（Obsidian 附件目录指向这里）
├── wiki/                # wiki 层 —— LLM 完全拥有，扁平 + frontmatter 分类
├── templates/           # 各类页面骨架（source/entity/concept/synthesis）
├── INDEX.md             # 内容目录（LLM 每次 ingest 维护）
├── LOG.md               # 时间线日志（append-only）
└── bases/               # Obsidian Bases 动态视图（.base 文件，YAML）
    ├── 全部页面.base
    └── 待办与缺口.base
```

**所有权规则**：

- `raw/` —— 用户的源真相。LLM 只读，绝不修改或删除。
- `wiki/`、`INDEX.md`、`LOG.md`、`bases/` —— LLM 完全拥有，负责创建、更新、维护。
- `CLAUDE.md` / `AGENTS.md` —— 用户与 LLM 共同演化；LLM 改其中一处时必须同步另一处。
- `templates/` —— 约定骨架，LLM 创建新页时参照，不改模板本身（除非用户要求）。

## 4. 页面模型与 frontmatter 规范

### 4.1 四种页面类型

| type | 含义 | 何时创建 |
|---|---|---|
| `source` | 单个源材料的摘要页 | 每次 ingest 一个新源 |
| `entity` | 实体页（人、产品、机构、工具、具体对象） | 该实体在 ≥1 个源中被提及且值得追踪 |
| `concept` | 概念/主题页（跨多个源的合成） | 某主题在多个源中反复出现 |
| `synthesis` | 综合分析页（对比、综述、发现） | 多由 query 沉淀；探索性分析的复利 |

### 4.2 frontmatter schema

用**英文 key + 中文值**（Bases/Dataview 查询语法对英文 key 更稳）。所有 wiki 页面统一字段：

```yaml
---
type: source                # source|entity|concept|synthesis
title: 睡眠如何影响认知
domain: [健康, 心理]         # 领域标签（数组）
tags: [睡眠, 记忆巩固]       # 主题标签（数组）
sources: []                 # 本页依据的源，用 [[wikilink]] 列表；source 页指向 raw 原文
created: 2026-07-24
updated: 2026-07-24
status: active              # draft|active|stale
aliases: []                 # 别名，便于 [[双向链接]] 命中
---
```

`source` 类型页面额外字段：

```yaml
source_path: "[[raw/articles/睡眠如何影响认知.md]]"   # 指向原始源
source_type: article                                  # article|book|podcast|note
source_url: https://example.com/...
author: 
date_published: 
```

`synthesis` 类型可选字段：`confidence: high|medium|low`（综合结论可信度）。

### 4.3 命名与链接规则

- **文件名 = 页面标题**（中文文件名 OK，Obsidian 原生支持），使 `[[标题]]` 自然解析、无需别名映射。
- 所有交叉引用用 `[[wikilink]]`；指向源原文用 `[[raw/.../xxx.md]]`。
- 文件统一放在 `wiki/` 下（扁平），靠 frontmatter 分类，不靠子文件夹。
- 页面正文末尾保留「相关页面」区块，列 `[[相关 wikilink]]`，强化 graph。

## 5. 规则文件内容大纲（CLAUDE.md / AGENTS.md）

两文件内容完全相同。中文撰写，分节如下：

1. **角色与总则** —— 你是 colin-wiki 的维护者；三层架构与所有权；全中文输出。
2. **目录约定** —— 每个目录归谁、谁能改；`raw/` 只读红线。
3. **页面规范** —— 四种类型定义、frontmatter schema、命名与 `[[wikilink]]` 规则、引用源的方式（指向 §4）。
4. **操作流程** —— ingest / query / lint 三大流程（指向 §6）。
5. **索引与日志维护** —— `INDEX.md` 结构、`LOG.md` 前缀格式（指向 §7）。
6. **跨工具一致性** —— `CLAUDE.md` 是主、`AGENTS.md` 是镜像；改规则时两处必须同步。
7. **Obsidian 集成** —— `[[wikilinks]]`、frontmatter 喂 Bases/Dataview、smart-connections 做检索、defuddle 抓网页、可选 json-canvas。
8. **图片处理** —— 图片落 `raw/assets/`；LLM 读带图文章时先读文本再按需单独查看引用图。
9. **红线守则** —— `raw/` 只读不写；不臆造源外内容；矛盾必须显式标注（在页面内「⚠️ 矛盾」区块）；保持链接完整（每次 ingest 后检查断链）。

## 6. 操作流程（数据流）

### 6.1 Ingest（摄入）

用户把源放入对应 `raw/<类型>/` → 告诉 LLM 处理 →

1. 读源（含按需查看引用图）。
2. 与用户过关键要点（用户决定强调什么）。
3. 在 `wiki/` 写一篇 `source` 摘要页。
4. 更新相关 `entity` / `concept` 页（新建或修订；一个源可能触及 10–15 个页面）。
5. 在页面内标注新数据与旧声明的矛盾（若有）。
6. 更新 `INDEX.md`（新页登记）。
7. 追加 `LOG.md` 一条记录。

默认逐个 ingest、保持人在环；批量 ingest 作为可选变体在规则中说明。

### 6.2 Query（查询）

用户提问 →

1. LLM 先读 `INDEX.md` 定位相关页。
2. 读相关页面（必要时 smart-connections 检索）。
3. 综合作答，带 `[[引用]]`。
4. 若分析有价值（对比、新连接、综述），沉淀为 `synthesis` 页，并登记进 `INDEX.md` / `LOG.md`，让探索复利。

### 6.3 Lint（体检）

LLM 检查：页面间矛盾、被新源超越的过时声明、孤儿页（无入链）、被提及但缺独立页的重要概念、缺失交叉引用、可用 web 搜索补的数据缺口。结果写入 `LOG.md` 并体现在 `bases/待办与缺口.bases`。

## 7. 索引 / 日志 / Bases

### 7.1 INDEX.md

内容导向的可读目录，按类别分组，每项一行 `[[链接]] — 一句话描述`：

```
# 索引
按类别组织的内容目录。每次 ingest 后由 LLM 维护。

## 实体 (entity)
- [[某人]] — 一句话描述

## 概念 (concept)
- [[睡眠]] — 跨源合成的主题

## 源 (source)
- [[文章标题]] — 一句话要点 · 2026-07-24

## 综合 (synthesis)
- [[睡眠与认知]] — 对比综述
```

> 自动聚合由 Bases 提供；`INDEX.md` 是人工可读的导览与 LLM 查询入口。

### 7.2 LOG.md

append-only 时间线。前缀可被 unix 工具解析：

```
# 日志

## [2026-07-24] init | 初始化 wiki 结构
## [2026-07-24] ingest | 睡眠如何影响认知
## [2026-07-25] query | 「睡眠与认知」综合 → 沉淀 [[睡眠与认知]]
## [2026-07-26] lint | 第 1 次健康检查（3 处矛盾，2 个孤儿页）
```

格式：`## [YYYY-MM-DD] <op> | <标题/摘要>`，`<op> ∈ {init, ingest, query, lint, update}`。
查询最近 N 条：`grep "^## \[" LOG.md | tail -N`。

### 7.3 Bases 视图（`.base` 文件，YAML）

- `bases/全部页面.base` —— 列出 `wiki/` 全部页面，按 `type` 分组，显示 `status` / `updated`。
- `bases/待办与缺口.base` —— 过滤 `status: stale|draft` 的待完善页面，按 `status` 分组，作为 lint 的可视化入口。（孤儿页 / 缺失概念页由 LLM 在 lint 时基于 `file.backlinks` 判定并写入 LOG，不强行塞进 Bases 过滤，避免不确定语法。）

> Bases 文件用 `.base` 扩展名 + YAML（依据已装的 obsidian-bases skill）。需 Obsidian 1.9+（或对应 Bases 特性）。纯文本 YAML，git 友好。

## 8. Obsidian 配置变更

修改 `.obsidian/app.json`（当前为 `{}`），设置：

```json
{ "attachmentFolderPath": "raw/assets" }
```

让粘贴/下载的图片自动落入 `raw/assets/`（对应 `llm-wiki.md` 的图片处理 tip）。其余 Obsidian 设置不动。

## 9. 本次初始化交付物清单

实现阶段将创建以下文件（全部为骨架，不含真实知识内容）：

- `CLAUDE.md`、`AGENTS.md`（内容相同）
- `raw/articles/.gitkeep`、`raw/books/.gitkeep`、`raw/podcasts/.gitkeep`、`raw/notes/.gitkeep`、`raw/assets/.gitkeep`
- `wiki/.gitkeep`
- `templates/source.md`、`templates/entity.md`、`templates/concept.md`、`templates/synthesis.md`（四种页面骨架，含 frontmatter 与正文结构提示）
- `INDEX.md`（空骨架，含四类小标题）
- `LOG.md`（含首条 `## [2026-07-24] init | 初始化 wiki 结构`）
- `bases/全部页面.base`、`bases/待办与缺口.base`（YAML 格式）
- 更新 `.obsidian/app.json` 设置 `attachmentFolderPath`

## 10. 非目标（YAGNI）

- 不引入 qmd 或其它外部检索引擎（smart-connections + INDEX 已够）。
- 不预置真实知识内容（只搭骨架）。
- 不配置 Obsidian Templates 核心插件自动插入（模板文件仅作 LLM 与人工参照；用户后续可自行接入）。
- 不做 Marp / 图表 / canvas 等输出格式预设（需要时再按需扩展）。
- 不动 `.gitignore`（已正确忽略 `.obsidian/workspace*` 与 `.smart-env`）。

## 11. 验收标准

- 目录结构与第 3 节一致，所有权规则在 `CLAUDE.md`/`AGENTS.md` 中明确。
- 四种页面骨架存在且 frontmatter 与第 4 节 schema 一致。
- `INDEX.md`、`LOG.md`（含 init 记录）、两个 `.base` 文件就位。
- `.obsidian/app.json` 指向 `raw/assets`。
- `CLAUDE.md` 与 `AGENTS.md` 内容逐字一致（跨工具同步）。
- 两个规则文件加载后，LLM 能正确执行 ingest/query/lint 三流程并维护 INDEX/LOG。
