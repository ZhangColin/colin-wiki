---
title: "还在用 grill-me？Matt Pocock 建议换 grill-with-docs：前者只问不落字，后者把对话搬进代码仓库"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491472&idx=1&sn=a524a5abf205b31b847e2a6142a0857e&chksm=cf43aac6f83423d0b83e97c842638a98460ac348988c08a7397787801a55da420b87a55e3f46&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-06
description:
tags:
---
运维有术 术哥无界 *2026年7月21日 08:29*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *173* 篇，AI 编程最佳实战「2026」系列第 *57* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

![[Image 65.webp|grill-with-docs 文章信息图封面：grilling 与 domain-modeling 双原语]]

grill-with-docs 文章信息图封面：grilling 与 domain-modeling 双原语

## 1\. 你将完成什么

读完之后，你应该能：

- 区分 `grill-me` / `grill-with-docs` / `domain-modeling` 三者各自的职责边界，知道面对不同场景该把谁拉出来
- 用一张判断表，决定面前这个共识该进 `CONTEXT.md` 、该进 ADR、还是干脆不写
- 跟着连续案例，把一段模糊需求演示到 `CONTEXT.md` 草案 + ADR 草案
- 认出 4 种最常见的文档化反模式，并知道怎么避

基础素材源： `mattpocock/skills` 仓库（路径 `skills/engineering/grill-with-docs/` 、 `domain-modeling/` 等），结构判断都按它原文的设计来。

## 2\. 从一个真实的术语灾难讲起

假设你刚和团队开完一次需求评审。PM、业务、后端、Agent 一起聊了 40 分钟，把下面这些都谈清楚了：

- **Order 是买家下单后生成的东西，不是付款记录**
- **Order 状态变更要走 Event Sourcing，读模型投影到 Postgres**
- **Invoice 要等 Order 出货后才能开**

会散了，大家同意这么干。

第二天，新 Agent 进仓库，看见 `lib/create_transaction.ts` 、 `purchase_service.ts` 、 `order_event.ts` 、 `payment_record.go` 四个文件。每一个名字看上去都说得通，但没人能一句话回答——\*\* `Order` 到底是哪一个？\*\* 这种场景，仓库 README 第 109-111 行有一段对照（BEFORE/AFTER）很有名——把模糊描述换成团队的共享词，比把抽象变成具体还难：

> BEFORE: "There's a problem when a lesson inside a section of a course is made 'real' (i.e. given a spot in the file system)" AFTER: "There's a problem with the materialization cascade"

落到这个例子里就是：把 **客户付了钱之后系统创建了一个 purchase** ，换成 \*\* `Order` 是下单动作产生的记录，状态从 `draft` 走到 `shipped` 或 `cancelled` \*\*。

`/grill-with-docs` 这个 skill 就是冲着这件事去的。它的设计比看上去有意思——核心动作只有两个： **grilling（追问）** 和 **domain-modeling（落字）** ，入口 skill 只是把它们按需串起来，不是把所有规则塞在一个大入口。

## 3\. 把 skill 拆成追问和落字两个原语，到底在解决什么

读 `grill-with-docs/SKILL.md` ，第一眼会觉得奇怪——整份文件 7 行，实质一句话：

> Run a `/grilling` session, using the `/domain-modeling` skill.

不是因为偷懒，是刻意做的拆分：

```
grill-with-docs（用户入口，user-invoked）
    │
    ├─→ grilling        ← 追问原语（一次一个问题，每问必带推荐答案）
    │
    └─→ domain-modeling ← 沉淀纪律（术语立刻写 CONTEXT.md，决策触发 ADR）
```

（来源： `docs/engineering/grill-with-docs.md` 配合 `skills/engineering/grill-with-docs/SKILL.md` ）

几个值得拎出来的点。

**`grilling` 是纯访谈，不写任何文件。** 仓库里有个 changeset（ `grilling-general-use.md` ）写得很简短：

> 把 grilling 从 **软件计划面试** 放宽到 **任何 plan、decision 或 idea** 。

换句话说，它是个被多个上层 skill 复用的追问引擎。

**`domain-modeling` 才负责落字。** 按 `domain-modeling/SKILL.md` 第 44-72 行的列举顺序，它做六件事——挑战与 `CONTEXT.md` 冲突的用法、把 overload 词锐化成规范词、用具体场景压力测试关系、与代码交叉对照、术语解决那一刻立即更新 `CONTEXT.md` （不攒批）、在三条门槛同时满足时提议 ADR。前四项是 **检验纪律** ，后两项是 **沉淀纪律** ——前者保证术语准，后者保证产物不丢。

**还有一个兄弟 `grill-me` ，跟 `grill-with-docs` 长得像但有个关键差异** ——它是 stateless 的，不写任何文件（来自 `docs/productivity/grill-me.md` ）：

> grill-me is stateless: it writes nothing and leaves no workspace behind.

适用场景：没有代码库的概念验证、纯设计讨论、写文章前先把需求厘清。

下表把它们的关系整理一下：

| Skill | 触发方式 | 是否落文件 | 何时用 |
| --- | --- | --- | --- |
| `grilling` | 模型自动调（被 `grill-me` 和 `grill-with-docs` 复用） | 否 | 任何需要一次次追问澄清的场景 |
| `grill-me` | 用户主动调 | 否 | 没代码库的纯讨论 / 写文前置 |
| `grill-with-docs` | 用户主动调 | 是（ `CONTEXT.md` / ADR） | 有代码库的真实工程 |
| `domain-modeling` | 模型自动调（被 `grill-with-docs` 和 `triage` 等驱动） | 是 | 维护 glossary + 提议 ADR |

**为什么两个原语被一个入口串起来比全塞进一个入口更好？** `docs/productivity/grilling.md` 里有句话很直白：

> Keeping the technique in one place means you can also reach for it directly when you just want the interview — without the ADR-writing or ticket-shaping that its wrappers add on top.

如果把追问纪律塞进 `grill-with-docs` ，那 `grill-me` 想复用就找不到了；如果把沉淀纪律塞进 `grill-with-docs` ，那 `triage` 、 `improve-codebase-architecture` 这些想复用 `domain-modeling` 的 skill 也找不到了。拆成两个原语后，每个入口按需组合—— `grill-me` 只挑 `grilling` ， `grill-with-docs` 同时挑 `grilling` + `domain-modeling` ， `triage` / `improve-codebase-architecture` 直接调 `domain-modeling` 。

说到这里得提一句：市面上还有其他强调前置对齐的 skill 工作流（之前文章里讨论过 Superpowers 等），看着方向也接近。但两个工作流主线不需要在这篇文章里重新比较——mattpocock 自己也写 "Make them your own"，下文按它的设计来。

![[Image 66.webp|grilling 与 domain-modeling 两个原语被上层 skill 按需组合的关系图]]

grilling 与 domain-modeling 两个原语被上层 skill 按需组合的关系图

*图：两个原语 + 三个入口的组合关系*

## 4\. 什么时候该写、什么时候该忍住

仓库里没有官方把决定 **二分 / 三分** 的判断表，但源码反复强调 glossary 与 ADR 两种产物对应两种需求。下表我 **逐行标注** 哪条是仓库规则、哪条是作者整理的分流启发式（标注在行的最右列）：

| 类型 | 落在哪 | 触发条件 | 来源 |
| --- | --- | --- | --- |
| 一次性口头约定，下游不会反复引用 | 不写 | 影响范围只在当次会话 | 作者建议 |
| 团队需要复用、Agent 也要复用的词 | `CONTEXT.md` | 项目独有 + 不是通用编程概念 + 一次解决就落字 | 仓库规则（ `CONTEXT-FORMAT.md` 第 29 行 + `domain-modeling/SKILL.md` 第 62 行）； **反复出现** 是作者附加的筛选启发式 |
| 一次会议拍板、影响未来代码走向的选择 | `docs/adr/NNNN-slug.md` | 三条门槛 **同时满足** （见下） | 仓库规则（ `ADR-FORMAT.md` 第 31-37 行） |
| 业务规则解释、API 用法、运行原理 | README / Wiki | 都不属于这套 glossary/ADR 范围 | 作者建议（不在 `mattpocock/skills` 仓库规则内） |

下面三条是 **什么才值得记 ADR** 的硬门槛， **缺一不可** （ `ADR-FORMAT.md` 第 31-37 行）：

1. **Hard to reverse** —— 改起来代价不小（事件溯源 + Postgres 投影就是这种）
2. **Surprising without context** —— 不读上下文会觉得奇怪（ **为什么用 Kafka 而不是 Redis Streams** ，只有写过 ADR 的人才解释得了）
3. **The result of a real trade-off** —— 真的有备选方案，且被严肃评估过

仓库里那句很实在（ `ADR-FORMAT.md` ）：

> An ADR can be a single paragraph. The value is in recording *that* a decision was made and *why* — not in filling out sections.

至于 `CONTEXT.md` ，措辞更狠（ `domain-modeling/SKILL.md` ）：

> `CONTEXT.md` should be totally devoid of implementation details. Do not treat it as a spec, a scratch pad, or a repository for implementation decisions. It is a glossary and nothing else.

把它当成 spec 写是头号反模式——后面专门讲。

![[Image 67.webp|把不同类型的共识分流到不写 / CONTEXT.md / ADR / README 的决策流程图]]

把不同类型的共识分流到不写 / CONTEXT.md / ADR / README 的决策流程图

*图：四类共识产物分流判断*

## 5\. 跟着一个连续案例跑一遍：grilling → CONTEXT.md → ADR

回到第一节那个团队。他们决定按 mattpocock/skills 的方式重新对齐术语。下面是完整走一遍。

**第一步：跑 grilling。** Agent 按 **一次一个问题、每问带推荐答案** 的模式来。比如：

> **Agent** ： `Order` 在我们系统里是指什么？我建议定义为：买家在 web/app 上提交 **我要买这件商品** 的动作产生的记录，状态从 `draft` 走到 `cancelled` 或 `shipped` 。你确认这个，还是想换词？
> 
> **你** ：定义差不多。但 `pending` 这个词我们 PM 也用来描述 **页面停留在加载中** ，换 `draft` 更清楚。
> 
> **Agent** ：收到，那 `_Avoid_` 里加 `pending` 。还有别的地方叫 `Order` 但意思不一样的吗？
> 
> **你** ：B 端的 `purchase_order` 是采购方发起的，叫 **采购单** ，不是 **订单** 。我倾向不在 ordering 里混叫。

按 `domain-modeling` 的纪律， **这种解决那一刻立刻落字** ——不攒批、不等会议结束。

**第二步：刚才那段对话沉淀下来， `CONTEXT.md` 长这样：**

```
# Ordering

这个上下文管"买家从下单到收货"的全过程。配套上下文还有 Billing 和 Inventory。

##Language

**Order**:
买家在 web/app 上提交"我要买这件商品"的意图后，由系统记录的购买请求。
_Avoid_: transaction, purchase, pending_order

**Purchase Order**:
采购方（不是终端买家）在 B2B 后台发起的采购请求，独立于 Order。
_Avoid_: PO（口语 / 聊天中保留，文档统一用全称）

**Draft**:
Order 创建后到支付成功之前的中间状态。
_Avoid_: pending（与前端加载态冲突）
```

格式源自 `CONTEXT-FORMAT.md` 第 5-23 行，几个值得注意的点：

- 定义 1-2 句话，说的是概念本身，不说它会做什么
- `_Avoid_` 列出弃用词——按团队确认流程写入时，就是 **统一用哪个词** 的硬证据
- 不含实现细节——不放 schema、不放方法签名、不放 status code；这些都是 spec 干的事

## 6\. 那三条 ADR 门槛到底在防什么

**第三步：决策拍板那天，单独走 ADR 流程。**

仓库有个 changeset（`.changeset/yagni-scope-improve-architecture.md` ），背景是给 `improve-codebase-architecture` 加 YAGNI 过滤器——只关注当前正在改的路径，不整理休眠角落。文档化纪律也是这个精神： **别把每个决定都写一遍** 。

上面那个团队定下来一条关键决定： **下单成功后，Order 走事件溯源 + Postgres 投影** 。这条决定同时满足 ADR 三条门槛：

1. Hard to reverse —— 改起来代价不小，要迁移 Projector、历史表重放
2. Surprising without context —— **为什么用 ES 而不是普通 CRUD** ，不读历史会让人疑惑
3. The result of a real trade-off —— 备选是简单 CRUD + 物化视图，写入 PPT 比较过

按 `ADR-FORMAT.md` 第 9-13 行的格式， **一份 ADR 可以短到一段话** ——价值在于记录 **我们做了这个决定 + 为什么** ，不是填章节：

```
# 0001: 写模型事件溯源，读模型 Postgres 投影

下单路径写量大、状态机复杂，多个上下文（Billing、Inventory、Reporting）需要订阅变更。我们选择写模型 Event Sourcing（append-only event log），读模型异步投影到 Postgres 的 \`orders\` / \`order_items\` 表。备选方案是普通 CRUD + CDC + 散落的状态机校验，权衡下来 ES 的可重放性与上下文解耦收益更大。
```

如果你想把后果拆开看，仓库允许加可选 Status frontmatter（ `proposed | accepted | deprecated | superseded by ADR-NNNN` ）以及 `## Considered Options` / `## Consequences` 两个可选二级标题——但 **不是默认模板** 。

反面例子—— **不该写 ADR 的典型** ：

> 我们团队决定在下单页用绿色按钮，不用红色。

三条门槛一条都不满足：可逆（有 A/B 可换）、不 surprising（颜色审美问题）、没真权衡（PM 一句话决定的）。把它记在 weekly summary 就够了， **别给它开一份 ADR 的闸门** ——一旦开了，三条门槛的口子就会被一点一点撕大，最后仓库里堆的都是 **我们下次复盘要 reverse 一下** 的非典型决定。

![[Image 29.png|电商团队用 grilling 追问 → 沉淀 CONTEXT.md 三条目 → 写 ADR 的完整三步工作流]]

电商团队用 grilling 追问 → 沉淀 CONTEXT.md 三条目 → 写 ADR 的完整三步工作流

*图：电商术语混乱案例的完整沉淀流程*

## 7\. 4 个反模式，多数团队都至少踩过两个

把刚才的流程反过来看，就是文档化的成本清单。

**反模式 1：过早记录。** 还没共识就先记。结果是 ADR 里出现 **我们打算怎么做** 的方案被反复推倒，推倒的旧版本没人看，但被 supersede 链拽在历史里。仓库里的 `grill-with-docs.md` 反复强调 **ADRs stay rare** ——不为可逆选择走流程。

**反模式 2：术语过多。** 把通用编程概念（timeout、id、error）也塞进 `CONTEXT.md` 。 `CONTEXT-FORMAT.md` 明确反对：

> Only collect terms unique to this context. Generic programming concepts (timeouts, error types, etc.) do not belong.

后果是 glossary 越长越没人读，最后退化成名词词典，没人在意维护。

**反模式 3：把 `CONTEXT.md` 写成 spec。** 这是仓库原文里 **最强硬的一句** （ `domain-modeling/SKILL.md` ）：

> `CONTEXT.md` should be **totally devoid of implementation details**. Do not treat it as a spec, a scratch pad, or a repository for implementation decisions. It is a glossary and nothing else.

实操表现为：把 schema、API、状态机表、甚至方法签名搬进 glossary。一旦这样做了， `CONTEXT.md` 就跟代码分叉——代码改了，glossary 不改，半年后没人信它。

**反模式 4：文档与代码漂移。** `grill-with-docs` 的运行流程里有一条 **codebase first** ——能从代码里读到的事实，Agent 自己读；只问决策类问题。反过来也成立：一旦代码改了术语（重命名函数、改类名、迁移文件），下一次 `grill-with-docs` 会读到旧词与新词不一致，从而推回去讨论。术语纪律不能 **写完即结束** ——它依赖每次会话重新读代码来发现漂移，这其实是从 Codebase first 延伸出来的人工维护纪律，仓库本身没把这个流程自动化。

老实说，这 4 条里第 1 条和第 3 条最容易一起踩。先把 ADR 写了、再补 `CONTEXT.md` ，结果 ADR 里 **我们用 X 而不用 Y** 已经泄漏成实现选择， `CONTEXT.md` 还得另写一遍。 **次序错了，反模式就一起出现** 。

![[Image 68.webp|文档化的 4 个反模式：过早记录 / 术语过多 / 写成 spec / 与代码漂移]]

文档化的 4 个反模式：过早记录 / 术语过多 / 写成 spec / 与代码漂移

*图：4 个最常见的文档化反模式*

## 8\. 哪些是仓库原话，哪些是我顺手延伸的

user 特别叮嘱过——下面把 **事实** 和 **延伸建议** 分一下：

| 哪些是仓库原文 | 哪些是我整理 / 延伸 |
| --- | --- |
| 三条 ADR 门槛（Hard to reverse / Surprising without context / Real trade-off） | **两个原语被一个入口串起来**  的反模式警觉 |
| `totally devoid of implementation details`  —— `CONTEXT.md` 是 glossary only | 4 个反模式按强度排序、 **次序错了反模式一起出现** |
| `grilling`  是被多个上层 skill 复用的原语， `grilling-general-use.md` 的修订 | 第一节那张 **该写什么** 判断表（仓库本身没这张表，是基于 `CONTEXT-FORMAT.md` + `ADR-FORMAT.md` 合并出来的） |
| Codebase first / ADRs stay rare 的纪律 | 第 4 / 第 5 节的连续案例（电商术语混乱 + 拍板事件溯源）——是组装的示意，不是仓库里真实存在 |
| README 里的 **Make them your own** | **30 天再 review 一次 ADR**  的检查节奏 |

mattpocock/skills 的 README 自承 **Skills For Real Engineers** ——这不是一个要求你照搬的工程方法论。我给的连续案例和判断表是 **怎么用它的示意** ；你的团队会拍板不同的事，会被不同的术语混淆点卡住， **这都正常** 。

## 9\. 当天就能跑的最小落地清单

如果团队还没动起来，下面这条 30 分钟清单能让你起步——别贪一次搞完。

**0-5 分钟：先认清 3 件事。** （ **不** 提前建文件，等真要写再建——仓库反复强调 lazy creation：第一个术语确认前不要 scaffold `CONTEXT.md` ，第一份 ADR 真正满足三条门槛前不要建 `docs/adr/` 。）

- 仓库里\*\*是否已经有 `CONTEXT.md` \*\*？已有就记住位置，没有就先不建
- 团队最近一次需求对齐是什么时候？那次定下来的词，有 **几个没写进** 任何地方？
- 现在脑子里有没有一条 **真满足 ADR 三条门槛** 的决定？没有就别建 `docs/adr/`

**5-20 分钟：挑一个反复出现的术语，开 grilling。**

让 Agent 按 **一次问一个问题、每问带推荐答案** 的模式跑，挑一个团队里不同人说法不一样的词开始。 **目标是产出 3-5 个 `CONTEXT.md` 条目** ，不是你一次搞完所有。

**20-30 分钟：试试三条 ADR 门槛。**

把脑子里 **我们应该是 X** 的候选决定过一遍三条门槛。同时满足的，挑一个 **优先级最高的** ，照 `ADR-FORMAT.md` 第 9-13 行的格式写出来。门槛没同时满足的，坚决不写。

写完之后，把文件链接贴进本周的 weekly summary。然后 **30 天后再看一次** ——那条 ADR 是被新 ADR supersede 了，还是被代码遗忘但仍有效。前者正常；后者说明你记了一个不该记的决定，下次把门槛拉紧。

## 总结：一次共识 ≠ 项目语言

需求访谈的共识，如果只活在当次会话里，那它就是一次性的。 `grill-with-docs` 这一类技能的力量， **不在于它能让 Agent 更聪明，而在于它把共识的载体从对话换成了仓库里的文件** ——一旦换了载体，下一个 Agent 进仓库就能直接吸收。

这个仓库的设计也未必适合你的团队。它对自己就描述成 **Skills For Real Engineers** ，后一句是 **Make them your own** ——所以别全套照搬。先用上面那条 30 分钟清单试一次，看看你团队的具体痛点落在哪，再决定要不要调整术语和 ADR 阈值。

试完之后，欢迎告诉我：你这次先踩中了哪个反模式？

> **说明** ：本文基于 Matt Pocock Skills 仓库和官方文档（ `docs/engineering/grill-with-docs.md` 等）整理，案例中演示的电商术语、 `CONTEXT.md` 与 ADR 片段是组装示意，不是真实跑通后的产物。 **文中的协作流程、文档模板和判断表仅供参考，实际效果请以你的项目语境和团队共识为准。** 如果你在自己的项目里试用过这套纪律，欢迎在评论区分享经验与取舍。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录