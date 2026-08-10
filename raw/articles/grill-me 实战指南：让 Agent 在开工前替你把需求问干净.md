---
title: "grill-me 实战指南：让 Agent 在开工前替你把需求问干净"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491460&idx=1&sn=0e915abcfde48a6c9080bf814ccc282f&chksm=cf43aad2f83423c464792415e42c0494ffa65daf074208dd66aeffcdd48c2f595562cf4c7ecb&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-05
description:
tags:
---
运维有术 术哥无界 *2026年7月20日 08:56*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *172* 篇，AI 编程最佳实战「2026」系列第 *56* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

需求看起来很清楚，写到一半发现全是默认假设——这是 AI 编程最常见的返工源头。

Matt Pocock 在 `mattpocock/skills` 仓库里放了两个相邻的 skill。

`/grill-me` 是用户入口， `/grilling` 是访谈原语。它们专门处理 *边界未确认就先动手* 的问题。

这篇指南只聚焦一件事。

当你拿到一个方向明确、边界却没对齐的需求，先别放手让 Agent 开工。

用 `/grill-me` 把决策一个个逼出来，事实留给 Agent 自己查，直到双方建立共享理解。

![[Image 28.png|/grill-me 实战指南信息图封面：决策树追问 + 共享理解门禁]]

/grill-me 实战指南信息图封面：决策树追问 + 共享理解门禁

## 1\. 你将看到什么

- `/grill-me` 和 `/grilling` 的分层关系：为什么前者只有一行正文，后者才是 12 行硬规则
- 一个完整的模拟场景：从 **给应用加导出功能** 这种听起来很简单但边界全没定的需求开始，看 Agent 怎么查事实、逐题追问、带推荐答案、等到确认门禁
- 12 行正文里的五条硬规则，每条对应一种跑偏
- 场景判断表：什么时候用 `/grill-me` ，什么时候换 `/grill-with-docs` 、 `/to-spec` 、 `/wayfinder`
- 启动方式和最小提示词模板（注意 slash command 在不同 harness 下不完全一样）
- Matt Pocock 本人已经在 AI Hero 公开声明不再推荐 `/grill-me` 作为 coding 入口——这是不能省略的态度信号
- 不适合用 `/grill-me` 的反向清单

## 2\. 先把两个 skill 分清楚

先看仓库里两个 skill 的字面大小：

| 角色 | 文件 | 正文 | 触发方式 |
| --- | --- | --- | --- |
| 用户入口 | `skills/productivity/grill-me/SKILL.md` | 7 行，正文一句话： **Run a `/grilling` session** | `disable-model-invocation: true`  \+ `allow_implicit_invocation: false` ，只能人手敲 `/grill-me` |
| 访谈原语 | `skills/productivity/grilling/SKILL.md` | 12 行，承载五条硬规则 | `model-invoked`  ，人和模型都能 reach，其他 skill 也可调用 |

为什么这么拆？ `docs/productivity/grilling.md:29-31` 写得很直白：

> Pulled out on purpose — `grilling` is the single source of truth for the interview technique, split out as a model-invoked primitive so every skill that needs an interview can reach it instead of reinventing one.

加上 `README.md:171` 的硬约束：user-invoked skill 可以调 model-invoked skill，但不能反过来调另一个 user-invoked skill。

`/grill-me` 启动 `/grilling` 是合法的，反过来不行。

这套分层让 *启动入口* 和 *访谈纪律* 不揉在一起。

后面看 Issue #274 的失败案例就会发现，这条边界一旦被破坏，用户体验立刻崩。

![[Image 60.webp|/grill-me 与 /grilling 分层关系图：用户入口调用访谈原语]]

/grill-me 与 /grilling 分层关系图：用户入口调用访谈原语

## 3\. 一个完整的模拟场景：给应用加导出功能

需求听起来简单：你的内部看板应用，用户希望能导出报表。

你写下：

```
/grill-me 给内部看板加导出功能。
```

Agent 不会直接动键盘。它会先看一眼环境，再开始逐题追问。

### 第 1 步：Agent 自己查事实

Agent 先在仓库里找现有线索。它会在 `grilling/SKILL.md:10` 的指令下做这些事：

- 看 `package.json` ，确认技术栈（React + Node.js + Postgres）
- 找 `routes/` 里有没有 `/export` 之类的占位接口
- 找 `db/schema.prisma` 看现有数据模型
- 翻 `docs/` 看项目里有没有 **导出** 相关的 ADR 或 glossary 词条

这一步 Agent 不该把问题甩给你，它得自己查。

*这个项目用什么 ORM* 在仓库里就能找到答案，属于事实，不是决策；规则四卡的正是这种跑偏。

### 第 2 步：一个问题——带推荐答案

查完环境，Agent 抛出第 1 个问题：

> **Agent** ：先把导出格式定下来。我建议用 **CSV** 。你的用户主要是 PM 和运营，大概率不会装 Excel 之外的工具；CSV **上手顺，也不用再加依赖** 。想要更贴近 Excel 的体验，可以换 XLSX，不过要多引一个依赖。CSV 和 XLSX 一起做也行，代价是导出接口会更复杂。
> 
> 我的建议很明确： **先只做 CSV，XLSX 后面有需要再补** 。这个方向你能接受吗？

Agent 没把 **你想要什么格式** 这个空问题丢过来。推荐、理由和备选都摆在面前。

你只要决定：接受、换 XLSX，还是两个都要。规则二就是为了省掉这段凭空猜答案的负担。

### 第三步：一次只问一个（规则三）

接下来要定导出范围。

Agent 不会一次甩出 *导出哪些字段、是否含过滤、时间范围* 三个问题。它一次只问一个：

> **Agent** ：格式定了，接着看导出哪些字段。我建议 **导出当前看板视图里的所有列** ：用户看到什么，就导出什么，不再单独裁剪字段。
> 
> 这样和 **导出全表** 相比，少了字段映射的歧义；和 **导出默认列** 相比，避免了 *默认列到底是哪些* 需要再问一次的依赖。
> 
> 这个范围你能接受吗？

你回答 *同意* 。

下一个问题：

> **Agent** ：字段定了，再看过滤。假如用户在看板上筛了 *只看北京大区* ，导出的 CSV 只带北京大区，还是导出全量？
> 
> 我推荐： **跟随看板当前过滤** 。这样符合用户的心智模型——看到的就能带走。如果你希望即使有过滤也能强制导出全量，那需要多一个 *忽略过滤* 的开关。
> 
> 按当前过滤导出，可以吗？

规则一管的是问题顺序。字段集没定，过滤就没法谈；过滤没定，文件名、触发位置和权限也会一路悬着。

Agent 沿着这条依赖链往下问，而不是把几个平行问题一次性砸给你。

### 第四步：决策由你，事实由 Agent

某一步 Agent 会问：

> **Agent** ：导出文件名怎么生成？我推荐： `{看板名}-{YYYYMMDD-HHmm}.csv` ，比如 `weekly-report-20260719-1430.csv` 。
> 
> 这避免了重名覆盖，也方便用户排序。如果你担心泄露看板名，可以用 `{YYYYMMDD}-{UUID}.csv` ，但用户侧要 *一眼看出哪个文件是哪个* 就麻烦些。

这种是决策，Agent 不能替你答。它只能给推荐。

另一种情况，Agent 会自己去查：

> **Agent** ：（内心）看了 `routes/reports.ts` ，发现已经有一个 `/api/reports/:id/export` 路由，但是抛 501……（查文档）……所以这条链路已经有占位了，我应该基于它扩展，而不是新建一个 `/api/export` 。
> 
> （继续问你）：这个占位接口应该被沿用吗？我推荐 **沿用** ，因为前端已经在调它了，改路径需要同步改前端和文档。

这里 *项目里有没有占位路由* 是事实——Agent 自己查的； *要不要沿用* 是决策——它在问你。

### 第五步：共享理解确认门禁

聊到第 8-10 个问题时，Agent 会做一次回顾：

> **Agent** ：到这里我把决策捋一遍：
> 
> 1. 格式：CSV（暂不上 XLSX）
> 2. 字段集：当前看板视图的全部列
> 3. 过滤行为：跟随看板当前过滤
> 4. 文件名： `{看板名}-{YYYYMMDD-HHmm}.csv`
> 5. 触发位置：看板右上角 *导出* 按钮
> 6. 权限：仅看板成员可导出，未登录重定向到登录页
> 7. 沿用现有 `/api/reports/:id/export` 路由
> 8. 不带进度条（导出量预估 < 10MB，前端无需进度反馈）
> 
> 这些都对吗？如果都对，我就进入实现阶段。

这就是 v1.1.0 在 PR #464 / commit `639df6e` 里新加的 **共享理解确认门禁** （ `CHANGELOG.md:13-18` ）。

在你说 *都对* 之前，Agent 不会动键盘写代码。

![[Image 61.webp|导出功能场景决策树有向图：六个节点串成依赖链]]

导出功能场景决策树有向图：六个节点串成依赖链

## 4\. 把五条规则从场景里抽出来

场景里其实已经把五条规则演完了一遍。把它们抽出来对照 *防什么跑偏* 摆在一起：

| 规则（节选自 `grilling/SKILL.md` ） | 跑偏场景 | 源码行号 |
| --- | --- | --- |
| 沿决策树逐个分支推进，依赖关系一个一个解 | 静态问卷一次性抛 30 个问题，依赖被压平成平行项 | `grilling/SKILL.md:6` |
| 每个问题必须带推荐答案 | 用户面对空白 prompt 自己做选择题，体验极差 | `grilling/SKILL.md:6` |
| 一次只问一个问题，等反馈再继续 | 一连串问题一起扔，前后互相干扰，用户答不清 | `grilling/SKILL.md:8` |
| 事实由 Agent 查（探索环境），决策由用户答 | Agent 把 *项目用 Postgres 还是 MySQL* 也当决策问你 | `grilling/SKILL.md:10` |
| 共享理解确认后才行动 | 表面聊过了，Agent 就自己脑补 *应该就是这个意思* 然后写代码 | `grilling/SKILL.md:12` |

前两条由 `description` 的 leading word `grill` 锚定，pretraining 里已经带 *反复追问* 的语义。

后三条是 v1.1.0 显式加上的硬约束。

最容易出问题的是共享理解门禁。

`CHANGELOG.md:13-18` 记录过一次具体修补。

在 `triage` 这类 skill 的 *解决工单* 语境里，Agent 会误读旧规则。

*能在 codebase 找到答案就去 codebase 找* 被读成 *凡是看起来能查到的事我都自己答* ，连决策也悄悄吞掉。

v1.1.0 把 *fact* 和 *decision* 拆成两个独立范畴，才堵上这个漏洞。

你在前面的场景里看到 Agent 自己查 `routes/reports.ts` ，而没有问 *项目有没有占位接口* ，原因就在这里。

占位路由是否存在由 Agent 查， *沿不沿用* 才交给用户决定。

![[Image 62.webp|五条硬规则与五种跑偏场景对照图：左侧规则，右侧跑偏]]

五条硬规则与五种跑偏场景对照图：左侧规则，右侧跑偏

## 5\. 共享理解的确认门禁

为什么要把 *共享理解* 做成一个 **显式门禁** 而不是默认假设？因为开源社区里已经出现过真实的失败案例。

`mattpocock/skills` 仓库的 GitHub Issue #274（fuzzyhope1502 在 2026-05-29 提交）描述了一个问题。

`improve-codebase-architecture` skill 在加入了 grilling 段之后， *borderline unusable and just spews completely irrelevant information constantly* 。

用户复现路径是问 *如何降低这两个模块之间的摩擦* 。

Agent 探索完环境后直接提议一个方案，然后就 grilling 用户 10 个甚至上百个问题。

用户原话是 *actively attempts to offload basically all work to the user every opportunity it gets* 。

Matt 团队 2026-07-06 由 LucasGHE 提交的 triage 回复（标记为 AI 辅助生成，见 `github-issue-274-comments.md` ）把这条标为 bug 并写了 acceptance criteria：

> - simple improvement requests can complete without mandatory long grilling loops
> - grilling escalation has clear triggers or opt-in controls
> - the skill preserves both quick suggestion mode and deep-interview mode

我的判断很明确。

`improve-codebase-architecture` 已经有 *探索 → 给建议* 的快速入口，再硬接 grilling，只会把轻量入口拖成长访谈。

grilling 留给 *需求边界不齐、决策要逐个对齐* 的任务。

如果你只想先看几个方案再自己选，就用 quick suggestion 模式。

Issue #274 的 acceptance criteria 第三条说的也是这件事： *the skill preserves both quick suggestion mode and deep-interview mode* 。

## 6\. 场景判断表：什么时候用 grill-me

`/grill-me` 不默认产出 `CONTEXT.md` 、ADR、ticket 等持久产物（ `docs/productivity/grill-me.md:29` ）。

它的设计意图是 *stateless* ：运行结束什么也不留下。

**留下来的只有会话里收敛的共享理解** 。这和 `/grill-with-docs` 是有意识地区分开的。

| 场景 | 推荐入口 | 理由 |
| --- | --- | --- |
| 临时方案压力测试 / 轻量需求对齐 | `/grill-me` | 纯对话收敛，不写文件 |
| 项目级共享语言尚未沉淀 + 重要决策需要留底 | `/grill-with-docs` | 写 `CONTEXT.md` 词条 + `docs/adr/` 决策记录 |
| 工作量超出单个 session / 目标路径模糊 | `/wayfinder` | 在 issue tracker 建 `wayfinder:map` 决策地图 |
| 共享理解已经收敛，想合成正式 spec | `/to-spec` | 默认假设共享理解已经收齐，不再发起访谈 |
| 想给现有代码加领域模型 + 长期规划 | `domain-model`  （Matt 2026 年的新默认） | 把项目语言沉淀为 domain 词汇 |
| 短小修复 / Bug 修复 / 单文件改动 | 直接进入 `triage → tdd` 链路 | 无需 grilling |
| 需要 UX 原型 / 设计稿才能答的问题 | 不要 grill | 这是 high-fidelity 问题，grill 不擅长 |

表格里 `domain-model` 这一行是新加的——这是 Matt 本人最近在公开渠道反复强调的方向。

## 7\. 启动方式和最小提示词模板

三种触发方式，从最窄到最宽：

**A. 通过 skills.sh 安装器** （ `README.md:27-31` 、 `docs/productivity/grill-me.md:3-9` ）：

```
npx skills@latest add mattpocock/skills
npx skills update grill-me
```

装好后执行 `/setup-matt-pocock-skills` 一次性配置仓库（ `README.md:35-39` ）。

**B. 作为 Claude Code 原生 plugin** （ `README.md:49-58` ）：

```
/plugin marketplace add mattpocock/skills
/plugin install mattpocock-skills@mattpocock
```

plugin 模式是只读、跟随官方升级的托管 bundle，不在仓库里改 skill 源码。

skills.sh 模式把 skill 复制到本地仓库，方便你 fork 修改。

**C. 其它 harness** ：README 第 67 行写明， `skills.sh` 安装器已经能装进 Codex 等符合 Agent-Skills 标准的 harness。

官方 Codex plugin 还在路线图上（ `README.md:67` 、`.agents/adr/0002-ship-as-a-claude-code-plugin.md` ）。

最小提示词模板——这是概念层通用的，落到具体 harness 要按 slash command 实现细节调整：

```
/grill-me {你方向明确但边界不齐的需求}

约束（可选但强烈建议）：
- 你只能在确认共享理解之后才能动手
- 事实问题你自己查，不准问
- 每个问题都要带推荐答案
- 一次只问一个
```

这几条约束最好直接写进 prompt。 `/grill-me` 的 wrapper 太薄，主体行为全靠 `/grilling` 的 12 行正文。

harness 如果没把 frontmatter 正确加载，Agent 很容易退回 *自由发挥* ，规则四和规则五往往先丢。

把你最在意的硬规则再写一遍，就是给执行兜底。

![[Image 63.webp|/grill-me 三种触发方式对比：skills.sh 安装器、Claude Code plugin、其它 harness]]

/grill-me 三种触发方式对比：skills.sh 安装器、Claude Code plugin、其它 harness

## 8\. Matt Pocock 本人已经在公开声明不再推荐 /grill-me 作为 coding 入口

到这里得踩一脚刹车：Matt 已经改了推荐，这会直接改变你该把 `/grill-me` 用在哪里。

`https://www.aihero.dev/skills-grill-me` （AI Hero 官方文档页）顶部有一段醒目的 Update 声明：

> **Update**: I stopped using `/grill-me` for coding. I now recommend `/grill-with-docs` when you want to align a plan with your codebase before implementation.
> 
> Matt now generally recommends `domain-model` over `grill-me` for planning workflows. Use `domain-model` as the default starting point when you want to shape a feature against your codebase language, `CONTEXT.md`, and ADRs.
> 
> `grill-me` is still useful as a narrower pressure-test when you only want a relentless interview about a plan.

GitHub Issue #102 里 Matt 本人也明确回了一个类似的问题：

> `/grill-with-docs`, then `/to-prd`, then `/to-issues` in the same conversation
> 
> I'll work on something that explains what to do when the grilling session gets too long.

新工作流是 `domain-model → to-prd → to-issues → tdd` 。

`/grill-me` 在这条链里只做 *窄压力测试* 。这个态度信号不能轻描淡写：

- **如果你正在做 coding 相关的需求对齐** ，Matt 本人建议你优先考虑 `/grill-with-docs` 或 `domain-model`
- **`/grill-me` 现在的合理定位** 是：非编码场景的压力测试、临时轻量对齐、确认候选方案前的小范围压力测试
- 同时 Hacker News 上有用户指出 `/grill-me` 在 productivity 场景（决定下一门课、规划非编码项目）依然好用，HN 用户 `AlexErrant` 的原话是 *I heckin love his /grill-me skill. Terse, to the point, and delivers outsized results.* ——这也对应了 Matt 自己那句非编码用例的自述

我的判断是： `/grill-me` 仍然有用，只是该待的位置已经变了。

## 9\. 不适合使用 grill-me 的反向清单

我的建议很干脆：碰到下面这些情况，先别启动 `/grill-me` 。它很容易从 **利器** 变成 **负担** 。

判断依据来自 `mattpocock/skills` 仓库的 9 Things 文章、Issue #274 的复现描述和社区反馈。

| 情况 | 为什么不该用 | 替代 |
| --- | --- | --- |
| **High-fidelity 问题**  ——需要 UX 原型、设计稿、视觉走查才能答的 | Grill 适合 *低保真问题* （URL 路由挂哪个、错误处理策略），遇到 *这个 UI 应该是什么手感* grill 出不来 | 先做原型 → 再 grill 验收 |
| **超大工作量**  ——单 session 装不下 | Matt 自述典型的 grilling session 时长约 45 分钟（来源：AI Hero *My Grill-Me Skill Has Gone Viral* 文章），极端情况下单 session 最多能膨胀到约 540 个问题——Matt 在 *9 Things* 文章里用的原话是 *explode the scope with requests about things that are way too low-fidelity* | `/wayfinder`  先建 decision map |
| **纯执行任务**  ——决策已经完全对齐，只差写代码 | 进来就 grill 是浪费——这种场景下决策已经收敛，Agent 应该直接进入 `triage → tdd` | 直接进实现 |
| **需要长期持久化决策**  ——重要 ADR 需要留底 | `/grill-me`  不写文件（ `docs/productivity/grill-me.md:29` ），共享理解只活在对话里，下次开新窗口 grill-me 必须重跑 | `/grill-with-docs` |
| **gibberish 输入 / vibe coding 需求**  ——需求本身就是模糊的 | Grill 的前提是 *方向明确但边界未确认* ，需求本身模糊它也无能为力 | 先用 `/ask-matt` 路由 |
| **Hard rule 已经被 harness 覆盖**  ——某些 Agent 已经内置决策问询流程 | 重复触发反而造成规则打架 | 关掉 grill-me 触发器 |

两张表不用拿来做折中。只要命中反向清单中的任意一项，我更建议先换入口，再考虑要不要 grill。

## 10\. 提问数量、依赖、用户负担、行动门禁之间的关系

别把 *提问越多越好* 当成 `/grill-me` 的卖点。我的看法正相反：问题一旦开始堆积，就该先检查 Agent 有没有越界。

`/grilling` 的三条约束都在压低无效提问：

- **决策树推进** ：每个问题只解一个依赖，解完才走下一个分支。评价问题质量，看的是精准度，不是数量
- **每个问题带推荐** ：Agent 先把理由和选项整理好，用户再做决定。用户要处理的是明确选项，不是从空白处替 Agent 补分析
- **共享理解门禁** ：Agent 准备动手前，用户还能做最后一次显式确认。访谈越长，这次回看越不能省，否则前后决策很容易漂移

我会把 *问题突然变多* 当成预警，而不是进展。

Matt 在 *9 Things* 里给过一个反向指标：被动接收会让 agent *bombard you with 540 questions* 。

问题膨胀到这种程度，访谈早已失控。

第三方 Medium 文章给过一个区间，只能当旁证。

作者 adityakumarpuri 自述： *In practice Claude asks 16 to 50 questions per session. Mine ran 38 the first time* 。

如果一个 session 已经接近或超过这个区间上界，我会优先排查两件事：

1. 需求本身太大，需要换 `/wayfinder` 拆 session
2. Agent 没做好 fact vs decision 的区分，把应该自己查的事当决策问你——这时候打断它，明确说 *这个你自己查*

门禁就是用户的明确 *叫停点* 。Agent 如果绕过确认直接实现，前面的 grilling 再长也只是 *对话装饰* 。

## 11\. 关键源码事实速查

下面这些路径是这次文章的事实锚点，全部出自 `mattpocock/skills` 仓库。

| 事实 | 文件路径 |
| --- | --- |
| `/grill-me`  是 wrapper，正文只有一行 | `skills/productivity/grill-me/SKILL.md` |
| `/grilling`  是 12 行原语，五条硬规则都在这 | `skills/productivity/grilling/SKILL.md` |
| User-invoked vs model-invoked 分类硬规范 | `.agents/invocation.md:5-10` |
| User-invoked skill 不能调另一个 user-invoked skill | `README.md:171` |
| `/grill-me`  stateless，不写文件 | `docs/productivity/grill-me.md:29` |
| `/grilling`  是 model-invoked 原语被其它 skill reach | `docs/productivity/grilling.md:29-31` |
| v1.1.0 confirmation gate / fact vs decision | `CHANGELOG.md:13-18` |
| Matt 本人公开声明改推 `/grill-with-docs` | AI Hero Update 声明（ `articles/research/aihero-skills-grill-me.md` ） |
| `domain-model → to-prd → to-issues → tdd`  新工作流 | 同上 + GitHub Issue #102 Matt 回复 |
| Issue #274 grilling 嫁接失败案例 | `articles/research/github-issue-274.md`  \+ `comments.md` |
| 典型 grilling session ~45 分钟 | Matt 自述（AI Hero *My Grill-Me Skill Has Gone Viral* 文章） |
| 单 session 最多可膨胀到 ~540 个问题 | Matt 自述（AI Hero *9 Things* 文章，原话 *bombard you with 540 questions* ） |

![[Image 64.webp|结尾闭环示意图：决策树推进 + 共享理解门禁的循环流程]]

结尾闭环示意图：决策树推进 + 共享理解门禁的循环流程

## 总结

`/grill-me` 和 `/grilling` 解决的是同一个问题： **在 AI 编程里，决策没收敛就开始动手** 。把它理解成一个薄壳：

- wrapper `/grill-me` 负责让人能敲命令触发
- primitive `/grilling` 用 12 行硬规则强制 *事实 Agent 查、决策用户答、一次一题、带推荐、共享理解确认才动手*

但它 **更合适的位置** 已经被作者本人重新定义。coding 场景优先考虑 `/grill-with-docs` 或 `domain-model` 。

`/grill-me` 现在更像一个轻量的压力测试工具：临时方案验证、非编码场景对齐、grill-with-docs 之前的预热。

如果你做完一个 grilling session 后发现重要决策需要留底，记得手动把共享理解写到 `CONTEXT.md` 或 ADR。

grill-me 不替你做这件事。

> **说明** ：本文基于 `mattpocock/skills` 仓库源码（commit ≥ v1.1.0）、AI Hero 官方文档页（含 Matt Pocock 本人 公开的 *Update* 声明）、仓库 GitHub Issue 等公开材料整理。社区数据点（如典型 session 时长、问题数区间）来自 Matt 本人 AI Hero 教程或第三方 Medium 单源引用，已在正文中标记可信度。文中的配置模板和参数建议仅供参考，实际效果请以你的业务数据和环境测试结果为准。如果你有实际使用经验，欢迎在评论区分享交流。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录