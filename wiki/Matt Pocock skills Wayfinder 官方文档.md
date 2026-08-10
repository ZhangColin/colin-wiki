---
type: source
title: "Matt Pocock skills Wayfinder 官方文档"
domain:
  - AI编程
  - AI工具
  - 软件工程
tags:
  - mattpocock-skills
  - Wayfinder
  - v1.1
  - AI编程工作流
sources: []
source_path: "raw/articles/Matt Pocock skills Wayfinder SKILL 原文（github）.md"
source_type: article
source_url: "https://github.com/mattpocock/skills/blob/main/skills/engineering/wayfinder/SKILL.md"
author: "Matt Pocock"
date_published: 2026-07-08
created: 2026-07-27
updated: 2026-07-27
status: active
aliases:
  - Wayfinder SKILL
  - wayfinder 官方文档
  - Wayfinder 原文
---

# Matt Pocock skills Wayfinder 官方文档

> 一句话要点：[[mattpocock skills]] 仓库中 `engineering/wayfinder/SKILL.md` 的一手英文原文——v1.1 王牌技能 `/wayfinder` 的完整定义。中文圈（[[小匠Skills]] 等）此前只概括介绍，本页填补**操作细节缺口**：chart/work 两套流程、map/ticket/frontier/fog-of-war 概念体系、四种 ticket 类型、"一 session 一 ticket"铁律。

## 关键要点

### 定位

规划**"大到单个 agent session 装不下、且被雾包裹"的活**——撑过 smart zone（~140k token）甚至撞 context window 上限。把模糊大想法画成 issue tracker 上的一张 **shared map**（`wayfinder:map`），逐个解 **decision ticket**，直到 the way is clear。

- `disable-model-invocation: true`：只能手动 `/wayfinder` 启动，不会被 agent 自动调。
- 目的地（destination）可能是一份 spec、一个要锁定的决策、或一次就地变更（如数据结构迁移）。**命名目的地是 charting 的第一个动作**，它决定 scope、塑造每张 ticket。
- 地图 domain-agnostic：工程、课程内容、任何符合该形状的活都行。

### Plan, don't do

默认**只规划、不执行**。每个 ticket 解一个**决策**（不是一段要执行的构建）。map 完成的标志是 "the way is clear"——动手前没有遗留决策。**"想直接动手"的冲动通常就是到了地图边缘、该交棒的信号**。effort 可在 Notes 里覆盖此默认（把执行带进 map），否则只产决策、不产交付物。

### 核心概念体系

| 概念 | 含义 |
| --- | --- |
| **Map（地图）** | tracker 上的单个 issue，标 `wayfinder:map`，canonical artifact。它是**索引不是仓库**——只列决策要点 + 指向 ticket，决策本体只住在它自己的 ticket 里（绝不重述）。 |
| **Map body** | `## Destination` / `## Notes`（领域、每次 session 该查的 skill、standing preferences）/ `## Decisions so far`（已闭 ticket 索引，一行一票）/ `## Not yet specified`（雾）/ `## Out of scope`。每 session 开头低分辨率加载一次。 |
| **Ticket** | map 的 child issue，body 就是一个 `## Question`，大小限**单个 100K token session 能解决**。带 `wayfinder:<type>` 标签。session 必须**先 claim（assign 给自己）再动手**，并发 session 才会跳过它。 |
| **Frontier（前沿）** | open + unblocked + unclaimed 的 children——当前可拿的活。靠 tracker **原生 blocking 关系**可视化（人在 tracker UI 直接看到可拿的，不用开 map）。 |
| **Fog of war（战争迷雾）** | 刻意**不画看不见的**。判据是"能否**提出**精准问题"（不是能否**回答**）：能精准提问 → ticket；还问不精准 → 写进 Not yet specified，等前面 ticket 解决、雾散了再 graduate。**不要把雾预切成 ticket 大小的碎片**——一团雾可能 graduate 成多张票或零张。 |
| **Out of scope** | 目的地之外的工作，**不是雾**，单独成区，**永不 graduate**。已被排除的已有 ticket 要 close + 留一行说明。 |

### 四种 ticket 类型

每张票要么 **HITL**（人在环，必须有真人参与），要么 **AFK**（agent 自主）。HITL 票必须通过真人交流解决，agent 绝不替人作答（grilling agent 自问自答 = 破坏此规则）。

| 类型 | HITL/AFK | 做什么 |
| --- | --- | --- |
| **Research** | AFK | 查文档/第三方 API/本地知识库，浮出一个决策在等的事实。由 `/research` **subagent** 解决。需当前目录之外的知识时用。 |
| **Prototype** | HITL | 做一个**廉价、粗糙、具体**的实物提高讨论保真度（大纲、草稿、stub、UI/逻辑代码，走 `/prototype`）。"该长啥样/该怎么动"是关键问题时用——**前端几乎必用**。 |
| **Grilling** | HITL | 走 `/grilling` + `/domain-modeling`，**一次一问**。**默认类型**。 |
| **Task** | HITL 或 AFK | 决策前**必须做的手动活**（开通服务、迁数据、配权限）——没东西要决策/原型/调研，但不做就没法继续讨论。**唯一"做"而非"决策"的类型**，靠"解锁一个决策"挣得其位置。agent 能自主就 AFK，否则给人一份精确 checklist。 |

### 铁律

**一个 session 永远只解一个 ticket**——research ticket 是唯一例外。

### 两种调用模式

**模式一 · Chart the map（画地图）** —— 用户带模糊想法来：

1. **Name the destination** —— 跑 `/grilling` + `/domain-modeling` 钉死目的地（spec/决策/变更）。目的地定 scope，必须先定。
2. **Map the frontier** —— 再 grill 一轮，这次 **breadth-first 扇出**，浮现开放决策和现在能迈的第一步。⚠️ **若这轮没产生任何 fog** → 说明整件事小到一个 session 就够，**不需要 map**，停下问用户怎么办。
3. **Create the map**（`wayfinder:map`）：填 Destination + Notes，Decisions-so-far 留空，雾写进 Not yet specified。
4. **Create tickets + 第二轮连 blocking 边** —— 把现在能定义的做成 child issue；**先建再连线**（issue 得先有 id 才能互引），连完边分出 frontier 与 blocked。看不实的留雾里。
5. **Fire research subagents** —— 对每张 research ticket 并行起 `/research` subagent，结果存 `research/<name>` 分支，ticket 里留 context 指针。
6. **停。** 画图就一个 session 的活，**不手解任何 ticket**。

**模式二 · Work through the map（走地图）** —— 用户带 map（URL/编号）来，ticket 可选：

1. Load map（低分辨率视图，别全量读 ticket body）。
2. 选 ticket（用户点名优先，否则取 frontier 第一个），**先 claim 再动手**。
3. Resolve —— 按需 zoom 读相关/已闭 ticket 全文，调 Notes 里点名的 skill；拿不准就 `/grilling` + `/domain-modeling`。
4. Record —— 答案作为 resolution comment，**close issue**，往 map 的 Decisions-so-far 追加 context 指针。
5. 加新浮现的 ticket；让雾里能定义的 graduate 成票（从 Not yet specified 移除）；越界的 rule out of scope；决策作废其他部分就更新/删除那些 ticket。

> 用户可并行跑 unblocked ticket，所以要预期其他 session 在并发编辑 tracker。

### 完成后

所有 ticket 关闭 → 整张 map 走常规路径（`/to-spec` → `/to-tickets` → `/implement` → `/code-review`）变成交付物。

## 详细笔记

- **Refer by name**：map 和 ticket 都是 issue，有 title。所有给人读的输出（叙述、Decisions-so-far）都用**名字**称呼，绝不用裸 id/编号/slug——`#42, #43, #44` 一墙不可读。id/URL 不消失，但骑在名字里面（名字包链接），不替代名字。
- **答案不入 ticket body**——resolution 时才记（resolution comment + close）。解票时产生的 asset（原型等）从 issue 链接，不贴进 body。
- **tracker 无关**：map/ticket/blocking/frontier 的物理表达是 tracker-specific。没提供 tracker 就默认本地 markdown tracker。

## 与已有内容的关联

- **核心概念**：[[mattpocock skills]]（本页是其 Wayfinder 机制的**操作细节权威源**，补 concept 页简述）。
- **同批官方源**：[[Matt Pocock skills v1.1 官方更新日志]]（v1.1 整体变更，含 Wayfinder 发布宣告）。
- **中文二手解读**：[[MattPocock Skills v1.1 重磅更新]]（[[小匠Skills]] 对 Wayfinder 的概括："自动拆巨型需求为多类型 ticket，支持增量迭代、跨会话交接"——本官方文档补其未详的流程与概念定义）。
- **相关实体**：[[Matt Pocock]]（作者）。
- **落地参考**：本 vault 自用 superpowers 体系的 `brainstorming` / `grilling` 是"先对齐"的轻量对照；Wayfinder 把这套对齐扩展成可跨 session、可协作的 tracker 地图。

## ⚠️ 矛盾 / 待澄清

- 与 [[MattPocock Skills v1.1 重磅更新]] 一致，无矛盾；本页为操作细节的**一手权威源**，中文页为概括。
- *未解*（[[mattpocock skills]] 已记）：Wayfinder 是否会向中型项目下沉、取代基础四步，待后续版本观察。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock skills v1.1 官方更新日志]] · [[MattPocock Skills v1.1 重磅更新]] · [[Matt Pocock]]
