# colin-wiki 结构初始化 实现计划

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 在已有 Obsidian Vault（`colin-wiki`）中搭建 LLM Wiki 的三层结构与规则文件，让 LLM 成为有纪律的 wiki 维护者。

**Architecture:** 严格三层隔离 —— `raw/`（源，只读）/ `wiki/`（LLM 拥有）/ `CLAUDE.md`+`AGENTS.md`（schema）。`wiki/` 扁平 + frontmatter 分类，Obsidian 原生（`[[wikilink]]`、Bases `.base`、smart-connections）。

**Tech Stack:** Markdown + YAML frontmatter；Obsidian Bases（`.base` + YAML）；git。

**Spec:** `docs/superpowers/specs/2026-07-24-wiki-structure-init-design.md`

## Global Constraints

- 语言：所有产出文件（规则、模板、INDEX、LOG、Bases）均为**中文**。
- `raw/` 只读红线：本计划只在 `raw/` 下建空的 `.gitkeep` 占位目录，不创建/修改任何源内容。
- `CLAUDE.md` 与 `AGENTS.md` 必须**逐字一致**（跨工具同步）；最终用 `diff` 验证。
- Bases 文件用 `.base` 扩展名 + **YAML**（依据已装的 obsidian-bases skill），**不是** `.bases`/JSON。
- 日期统一用 `2026-07-24`（init 日）。
- 所有提交到 `main` 分支（个人 vault，单用户工作流）。
- YAML 验证命令：`python3 -c "import yaml,sys; yaml.safe_load(open(sys.argv[1])); print('YAML OK')" <file>`。

## File Structure

| 文件/目录 | 职责 | 任务 |
| --- | --- | --- |
| `raw/{articles,books,podcasts,notes,assets}/.gitkeep`、`wiki/.gitkeep` | 占位目录骨架 | Task 1 |
| `templates/source.md`、`entity.md`、`concept.md`、`synthesis.md` | 四类页面骨架（定义 frontmatter schema） | Task 2 |
| `CLAUDE.md`、`AGENTS.md` | 规则文件（互为镜像） | Task 3 |
| `INDEX.md` | 内容目录（LLM 维护） | Task 4 |
| `LOG.md` | append-only 时间线 | Task 4 |
| `bases/全部页面.base`、`bases/待办与缺口.base` | Bases 动态视图 | Task 5 |
| `.obsidian/app.json` | 附件目录指向 `raw/assets` | Task 6 |

依赖顺序：Task 2（模板）定义 frontmatter → 被 Task 3（规则）与 Task 5（Bases）引用。Task 1 先行建目录。

---

### Task 1: 目录骨架 + 提交 spec 修正

**Files:**
- Create: `raw/articles/.gitkeep`、`raw/books/.gitkeep`、`raw/podcasts/.gitkeep`、`raw/notes/.gitkeep`、`raw/assets/.gitkeep`、`wiki/.gitkeep`、`templates/.gitkeep`、`bases/.gitkeep`
- 已修改（待提交）: `docs/superpowers/specs/2026-07-24-wiki-structure-init-design.md`（`.bases`→`.base` 修正）

**Interfaces:** 产出空目录骨架，供后续任务放入文件。

- [ ] **Step 1: 提交 spec 的 `.base` 修正**

```bash
git add docs/superpowers/specs/2026-07-24-wiki-structure-init-design.md
git commit -m "docs: 修正 Bases 文件为 .base 扩展名 + YAML

Co-Authored-By: Claude <noreply@anthropic.com>"
```

预期：`1 file changed`。

- [ ] **Step 2: 创建目录骨架与 .gitkeep**

```bash
mkdir -p raw/articles raw/books raw/podcasts raw/notes raw/assets wiki templates bases
touch raw/articles/.gitkeep raw/books/.gitkeep raw/podcasts/.gitkeep raw/notes/.gitkeep raw/assets/.gitkeep wiki/.gitkeep templates/.gitkeep bases/.gitkeep
```

- [ ] **Step 3: 验证目录结构**

```bash
find raw wiki templates bases -type d | sort
```

预期输出包含：`raw/articles`、`raw/books`、`raw/podcasts`、`raw/notes`、`raw/assets`、`wiki`、`templates`、`bases`。

- [ ] **Step 4: 提交**

```bash
git add raw wiki templates bases
git commit -m "chore: 初始化 wiki 三层目录骨架

Co-Authored-By: Claude <noreply@anthropic.com>"
```

---

### Task 2: 四种页面模板

**Files:**
- Create: `templates/source.md`、`templates/entity.md`、`templates/concept.md`、`templates/synthesis.md`
- Delete: `templates/.gitkeep`（模板文件就位后移除占位）

**Interfaces:**
- Produces: 四类页面的 frontmatter schema（`type`/`title`/`domain`/`tags`/`sources`/`created`/`updated`/`status`/`aliases`，`source` 额外 `source_path` 等）。Task 3 规则与 Task 5 Bases 引用这些字段名。

- [ ] **Step 1: 创建 `templates/source.md`**

````markdown
---
type: source
title: "{{title}}"
domain: []
tags: []
sources: []
source_path: ""
source_type: article
source_url: ""
author: ""
date_published: ""
created: "{{date}}"
updated: "{{date}}"
status: draft
aliases: []
---

# {{title}}

> 一句话要点：

## 关键要点

-

## 详细笔记

## 与已有内容的关联

- 相关概念：
- 相关实体：

## ⚠️ 矛盾 / 待澄清

（若本源与 wiki 中已有声明冲突，在此显式记录）

## 相关页面

-
````

- [ ] **Step 2: 创建 `templates/entity.md`**

````markdown
---
type: entity
title: "{{title}}"
domain: []
tags: []
sources: []
created: "{{date}}"
updated: "{{date}}"
status: draft
aliases: []
---

# {{title}}

> 一句话定义：

## 概述

## 关键属性 / 事实

-

## 演变 / 时间线

（按时间记录该实体的变化，引用 [[源]]）

## 相关页面

-
````

- [ ] **Step 3: 创建 `templates/concept.md`**

````markdown
---
type: concept
title: "{{title}}"
domain: []
tags: []
sources: []
created: "{{date}}"
updated: "{{date}}"
status: draft
aliases: []
---

# {{title}}

> 一句话定义：

## 核心观点

（跨多个源合成的当前理解）

## 证据 / 来源

- [[源A]] —
- [[源B]] —

## ⚠️ 矛盾 / 未解问题

## 相关页面

-
````

- [ ] **Step 4: 创建 `templates/synthesis.md`**

````markdown
---
type: synthesis
title: "{{title}}"
domain: []
tags: []
sources: []
created: "{{date}}"
updated: "{{date}}"
status: draft
confidence: medium
aliases: []
---

# {{title}}

> 一句话结论：

## 问题

（本次综合想回答的问题）

## 对比 / 分析

| 维度 | 观点 A | 观点 B |
| --- | --- | --- |
|  |  |  |

## 结论

## 引用

- [[源]]

## 相关页面

-
````

- [ ] **Step 5: 移除占位并验证 YAML frontmatter 可解析**

```bash
rm templates/.gitkeep
for f in templates/source.md templates/entity.md templates/concept.md templates/synthesis.md; do
  python3 -c "import yaml,sys; yaml.safe_load(open('$f').read().split('---')[1]); print('$f YAML OK')"
done
```

预期：四行 `<file> YAML OK`。

- [ ] **Step 6: 提交**

```bash
git add templates
git commit -m "feat: 添加 source/entity/concept/synthesis 四种页面模板

Co-Authored-By: Claude <noreply@anthropic.com>"
```

---

### Task 3: 规则文件 CLAUDE.md + AGENTS.md

**Files:**
- Create: `CLAUDE.md`、`AGENTS.md`

**Interfaces:**
- Consumes: Task 2 的 frontmatter 字段名、目录结构（Task 1）。
- Produces: schema 文档，定义 ingest/query/lint 流程与 INDEX/LOG 格式，被 Task 4/5 实例化。

- [ ] **Step 1: 创建 `CLAUDE.md`（完整内容如下）**

````markdown
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
````

- [ ] **Step 2: 创建 `AGENTS.md`，内容与 `CLAUDE.md` 完全一致**

```bash
cp CLAUDE.md AGENTS.md
```

- [ ] **Step 3: 验证两文件逐字一致**

```bash
diff CLAUDE.md AGENTS.md && echo "IDENTICAL"
```

预期：无输出，随后打印 `IDENTICAL`。

- [ ] **Step 4: 提交**

```bash
git add CLAUDE.md AGENTS.md
git commit -m "feat: 添加 CLAUDE.md 与 AGENTS.md 维护规则（跨工具镜像）

Co-Authored-By: Claude <noreply@anthropic.com>"
```

---

### Task 4: INDEX.md 与 LOG.md

**Files:**
- Create: `INDEX.md`、`LOG.md`
- Delete: `bases/.gitkeep` 不在此任务（留给 Task 5）；本任务无占位删除。

**Interfaces:**
- Consumes: Task 3 定义的 INDEX/LOG 格式。
- Produces: 导航文件，首次记录 init。

- [ ] **Step 1: 创建 `INDEX.md`**

````markdown
# 索引

> 内容目录。按类别组织，每项一行 `[[链接]] — 一句话描述`。每次 ingest 后由 LLM 维护。
> 自动聚合见 `bases/全部页面.base`。

## 实体 (entity)

（暂无）

## 概念 (concept)

（暂无）

## 源 (source)

（暂无）

## 综合 (synthesis)

（暂无）
````

- [ ] **Step 2: 创建 `LOG.md`**

````markdown
# 日志

> append-only 时间线。格式：`## [YYYY-MM-DD] <op> | <标题>`。
> 查最近 N 条：`grep "^## \[" LOG.md | tail -N`。只追加，不改写历史。

## [2026-07-24] init | 初始化 wiki 结构

按 `llm-wiki.md` 方法论搭建三层架构（raw / wiki / schema），建立规则文件（CLAUDE.md + AGENTS.md）、四种页面模板、INDEX/LOG 与 Bases 视图，并把 Obsidian 附件目录指向 `raw/assets`。
````

- [ ] **Step 3: 验证 LOG 前缀可被 grep**

```bash
grep "^## \[" LOG.md
```

预期：输出一行 `## [2026-07-24] init | 初始化 wiki 结构`。

- [ ] **Step 4: 提交**

```bash
git add INDEX.md LOG.md
git commit -m "feat: 初始化 INDEX.md 与 LOG.md

Co-Authored-By: Claude <noreply@anthropic.com>"
```

---

### Task 5: Bases 动态视图

**Files:**
- Create: `bases/全部页面.base`、`bases/待办与缺口.base`
- Delete: `bases/.gitkeep`

**Interfaces:**
- Consumes: Task 2 的 frontmatter 字段 `type`/`status`/`updated`；`file.inFolder("wiki")` 过滤。

- [ ] **Step 1: 创建 `bases/全部页面.base`**

````yaml
filters:
  and:
    - 'file.inFolder("wiki")'
    - 'file.ext == "md"'

properties:
  type:
    displayName: 类型
  status:
    displayName: 状态
  updated:
    displayName: 更新

views:
  - type: table
    name: 按类型
    order:
      - file.name
      - type
      - status
      - updated
    groupBy:
      property: type
      direction: ASC
````

- [ ] **Step 2: 创建 `bases/待办与缺口.base`**

````yaml
filters:
  and:
    - 'file.inFolder("wiki")'
    - 'file.ext == "md"'
    - or:
        - 'status == "stale"'
        - 'status == "draft"'

properties:
  type:
    displayName: 类型
  status:
    displayName: 状态
  updated:
    displayName: 更新

views:
  - type: table
    name: 待完善页面
    order:
      - file.name
      - type
      - status
      - updated
    groupBy:
      property: status
      direction: ASC
````

- [ ] **Step 3: 验证 YAML 可解析**

```bash
rm bases/.gitkeep
python3 -c "import yaml; yaml.safe_load(open('bases/全部页面.base')); yaml.safe_load(open('bases/待办与缺口.base')); print('Bases YAML OK')"
```

预期：`Bases YAML OK`。

- [ ] **Step 4: 提交**

```bash
git add bases
git commit -m "feat: 添加 Bases 动态视图（全部页面 / 待办与缺口）

Co-Authored-By: Claude <noreply@anthropic.com>"
```

---

### Task 6: Obsidian 附件目录配置

**Files:**
- Modify: `.obsidian/app.json`（当前内容为 `{}`）

**Interfaces:** 无下游依赖。完成后 vault 可用。

- [ ] **Step 1: 写入 `attachmentFolderPath`**

把 `.obsidian/app.json` 改为：

```json
{
  "attachmentFolderPath": "raw/assets"
}
```

- [ ] **Step 2: 验证 JSON 合法**

```bash
python3 -m json.tool .obsidian/app.json
```

预期：打印格式化的 JSON，含 `"attachmentFolderPath": "raw/assets"`。

- [ ] **Step 3: 提交**

```bash
git add .obsidian/app.json
git commit -m "chore: Obsidian 附件目录指向 raw/assets

Co-Authored-By: Claude <noreply@anthropic.com>"
```

- [ ] **Step 4: 人工确认（提示用户）**

在 Obsidian 设置 → Files and links，确认「Default location for new attachments」选择 **「In the folder specified below」**（即下方指定文件夹），下方填 `raw/assets`。Obsidian 对该下拉的持久化可能不完全落在 `app.json`，故以 UI 确认为准。

---

### Task 7: 最终验收

**Files:** 无新增，仅核对。

- [ ] **Step 1: 核对目录结构**

```bash
find . -path ./.git -prune -o -path ./.obsidian/plugins -prune -o -type f -print | grep -vE '^\./\.(claude|agents|claudian)/' | sort
```

预期包含：`CLAUDE.md`、`AGENTS.md`、`INDEX.md`、`LOG.md`、`templates/{source,entity,concept,synthesis}.md`、`bases/{全部页面,待办与缺口}.base`、`raw/{articles,books,podcasts,notes,assets}/.gitkeep`、`wiki/.gitkeep`、`.obsidian/app.json`、`llm-wiki.md`。

- [ ] **Step 2: 核对规则文件同步**

```bash
diff CLAUDE.md AGENTS.md && echo "CLAUDE.md == AGENTS.md"
```

预期：`CLAUDE.md == AGENTS.md`。

- [ ] **Step 3: 核对 git 状态干净**

```bash
git status --short
```

预期：无输出（全部已提交）。

- [ ] **Step 4: 在 Obsidian 中打开 vault，确认**

- graph view 可见（暂只有少量节点，正常）。
- `bases/全部页面.base` 能打开（wiki/ 暂空，列表为空属正常）。
- 两个规则文件加载后，新会话能正确执行 ingest/query/lint。
