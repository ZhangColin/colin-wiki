---
title: "Matt Pocock 的工作流：从一个模糊想法到可交付代码"
source: "https://mp.weixin.qq.com/s/Fm42nn07D9D41UR1Vx9zng"
author:
  - "[[鸟窝]]"
published:
created: 2026-07-31
description: "TypeScript 名师 Matt Pocock 开源了一套 Claude Code Skills（http"
tags:
---
鸟窝 鸟窝聊技术 *2026年7月14日 17:13*

TypeScript 名师 Matt Pocock 开源了一套 Claude Code Skills（https://github.com/mattpocock/skills）。他本人在twitter上介绍，真正的核心是这 5 个命令串起来的一条主线：

`/grill-with-docs` → `/to-spec` → `/to-tickets` → `/implement` → `/code-review`

一句话概括这条线：

**先把想法拷问清楚 → 写成规范 → 拆成卡片 → 动手实现 → 双轴评审。**

Matt的这套skills有一个很大的问题就是功能 最好 ，但是文档跟不上，很多人不知道该怎么用。因为作者也开发了一套类似的流程，而且为了了解Matt的这套流程，也使用它们开发了一个Go语言版本的pi agent: https://github.com/smallnest/pigo（你可以看它的issues,都是这套skill生成的）, 多少对他的这套流程有所了解，本文就是介绍他的这套流程。

你可以通过下面的命令安装他的这套skills:

```
npx skills add mattpocock/skills
```

---

## 开篇：一条从"想法"到"交付"的产线

![[Image 4.webp|图片]]

Matt 的设计哲学是： **动手写代码之前，先把话说清楚、把结构定下来。** 这条产线的前三步几乎都不写代码，全聚焦在"对齐认知"。

---

## 先看全景：Engineering 分类里都有哪些 skill

在钻进那条 5 步主线之前，先看一眼 README 里 engineering https://github.com/mattpocock/skills#engineering 这一大类的全貌。Matt 把它们分成两拨—— **用户主动调用的（User-invoked）** 和 模型自己会调的（Model-invoked）：

**用户主动调用的的（User-invoked）**

| Skill | 一句话 |
| --- | --- |
| `ask-matt` | 拿不准该用哪条流程时问它，它是所有 user-invoked 技能的"路由器" |
| `wayfinder` | 目标又大又模糊、一个会话装不下时，先在 issue 上铺一张调查地图 |
| `grill-with-docs` | 一次一个问题拷问计划，同步沉淀 `CONTEXT.md` / ADR |
| `to-spec` | 把对话综合成规范并发到 issue 跟踪器 |
| `to-tickets` | 把计划拆成一串带阻塞依赖的"示踪弹"票据 |
| `implement` | 照票据/规范施工，驱动 `/tdd` 、收尾自动 `/code-review` |
| `triage` | 让 issue 在一套分诊角色状态机里流转 |
| `improve-codebase-architecture` | 扫描代码库找"深化机会"，出 HTML 报告，再逐个拷问 |
| `setup-matt-pocock-skills` | 每个仓库先跑一次，配好 issue 跟踪器/分诊标签/领域文档结构 |

**模型自己会调的（Model-invoked）**

| Skill | 一句话 |
| --- | --- |
| `code-review` | 对 diff 做双轴评审（标准 × 规范），并行子 Agent |
| `tdd` | 红-绿-重构，一次一个垂直切片 |
| `codebase-design` | 设计深模块的共享纪律与词汇（Design It Twice） |
| `domain-modeling` | 主动构建并打磨项目领域模型 |
| `prototype` | 造一个用完即弃的原型来回答某个设计问题 |
| `diagnosing-bugs` | 硬 bug/性能回归的纪律化诊断循环 |
| `research` | 后台 Agent，对高可信一手资料调研并落成带引用的 Markdown |
| `resolving-merge-conflicts` | 逐块解 merge/rebase 冲突，绝不 `--abort` |

![[Image 5.webp|图片]]

这一整套技能中，Matt 亲自点名的 **主线核心是 5 个 user-invoked 技能** （外加上游的 `wayfinder` ），形成了一套开发流程。下面逐步介绍这流程中的五步。

---

## 第一步 · /grill-with-docs —— 用文档拷问你的计划

**⚠️ Matt现在主推他的新技能 `wayfinder` ：目标太大的话优先使用 `wayfinder`**

![[Image 6.webp|图片]]

很多人以为 `wayfinder` 替代了 `grill-with-docs` ，其实 **两者互补，wayfinder 在上游** ：

- ❋ `grill-with-docs` 处理的是 **一个会话内、一个已经明确的想法** 。
- ❋ `wayfinder` 处理的是 **又大又模糊、一个会话根本装不下** 的绿地项目或大型功能。

**wayfinder 怎么干** ：它在 issue 跟踪器上开一个打着 `wayfinder:map` 标签的"地图"总 issue，把项目笔记、已定决策、仍模糊的部分都记在上面；再把每个待调查的问题拆成带 `wayfinder:&lt;type&gt;`（research / prototype / grilling / task）标签的子 issue，用原生阻塞链接串起依赖。它靠 **"前沿查询"** 找出"所有前置都已完成、且没人认领"的子任务作为可动手的前沿，Agent 认领→解决→把答案和上下文指针写回地图。 **wayfinder 的产出是决策，不是交付物** ——等所有决策都明确、通往目标的路径清晰了，它就把活儿移交给 `grill-with-docs` / `to-spec` / `implement` 。

事实上 `grill-with-docs` 自己会主动向上提示： **如果发现工作量太大，请先去用 `wayfinder` 推开迷雾。** 所以想法越模糊庞大，越该从 wayfinder 起步；想法已经聚焦，直接进 grill。

**它做什么** ：它不停地 **一次抛一个问题** ，逼你把模糊想法想透；同时把过程中敲定的术语和决策实时写进 `CONTEXT.md` 词汇表和 ADR（架构决策记录）。

**核心方法** ：

- ❋拷问（grill）：像遍历一棵决策树，先解决依赖关系再往下走，一次只问一个问题。
- ❋ **能查代码就不问你** ：答案能从代码库里读到的，它自己去读，不浪费你的注意力。
- ❋ **实时沉淀** ：模糊话术被提炼成规范术语，边聊边写进 `CONTEXT.md` ；只有"难以逆转、且是真权衡"的决策才升级成 ADR。
![[Image 7.webp|图片]]

---

## 第二步 · /to-spec —— 把对话凝结成规范

**它做什么** ：它把刚才拷问出来的对话和对代码库的理解，综合成一份规范（Spec/PRD），发布到 issue 跟踪器。

**核心方法** ：

- ❋ **不重新访谈** ：只综合已有信息，不再从头盘问。
- ❋ **提前找接缝和深模块** ：写规范前，先勾出功能将来会被测试的"接缝（seam）"，并寻找"用简单接口藏住大量能力"的深模块机会。
![[Image 8.webp|图片]]

---

## 第三步 · /to-tickets —— 拆成"示踪弹"垂直切片

**它做什么** ：它把规范/计划/对话拆成一串 **票据** （卡片，issues），每张票据是一颗"示踪弹（tracer-bullet）"垂直切片，并声明彼此的阻塞依赖，按依赖顺序发到 issue 跟踪器。

**先说人话：什么是"示踪弹"和"垂直切片"？**

**示踪弹** ，本义是军队里那种会拖着亮光的子弹——打出去你能顺着光看到它真的飞到了哪、打没打中。借到软件里意思是： **先打一发"能从头亮到尾"的最小功能，把整条链路先跑通、看得见** ，而不是憋着做很多看不见的零件。

**垂直切片 vs 水平横切** ，用做菜类比：

- ❋水平横切（不推荐）：这周先把所有的"数据库表"建完，下周再把所有"接口"写完，再下周才拼"界面"。前两周你啥都演示不了，全是半成品，最后一拼才发现对不上。
- ❋ **垂直切片（推荐） **：先只做"用户登录"这一个功能，但** 一竿子捅到底** ——数据库、接口、界面、测试全都做，只做这一条。做完当天就能真的点一下登录、看到它跑通。

所以一颗"示踪弹垂直切片"= **一个窄到只有一条线、但从上到下完整能跑、当场能演示** 的小功能。下一颗再叠一条线上去，像搭积木一样越叠越全。

**核心方法** ：

- ❋ **示踪弹 = 薄而完整的垂直切片** ：一刀贯穿 schema / API / UI / 测试所有层，而不是只做某一层的水平横切。
- ❋ **每片可独立演示验证** 。
- ❋ **按依赖顺序发布** ：这样每张票的"Blocked by"能引用到真实存在的票据；遇到大范围重构，用"扩展-收缩（expand-contract）"策略。
![[Image 9.webp|图片]]

---

## 第四步 · /implement —— 照着票据，TDD 把它造出来

**它做什么** ： **它不决定做什么，只执行已定的计划** 。按票据/规范，用 TDD、类型检查、完整测试套件把代码造出来，收尾时自动交给 `/code-review` 并提交到当前分支。

**核心方法** ：

- ❋ **围绕预定接缝写代码** ：接口是动手前就选好的稳定"接缝"，用 `/tdd` 针对接缝写测试。
- ❋ **节奏感** ： **频繁类型检查** 、定期跑单个测试文件、最后跑 **整个测试套件** 。
- ❋ **写完自评** ：完成即调用 `/code-review` 。
![[Image 10.webp|图片]]

---

## 第五步 · /code-review —— 双轴并行评审

**它做什么** ：它对"自某个固定点以来的 diff"做 **双轴评审** ，两条轴作为 **并行子 Agent** 同时跑、互不干扰。

**核心方法** ：

- ❋标准轴（Standards）：查代码是否守仓库编码规范，内置 Fowler 的代码异味基线——神秘命名、重复代码、特性依恋等。
- ❋ **规范轴（Spec） **：查代码是否** 忠实实现了最初的问题 / PRD** ，别写着写着跑偏。
![[Image 3.png|图片]]

---

## 小结：这条主线为什么值得抄

| 步骤 | 命令 | 一句话 |
| --- | --- | --- |
| ① 拷问 | `/grill-with-docs` | 一次一个问题逼透想法，实时写 `CONTEXT.md` / ADR |
| ② 规范 | `/to-spec` | 不重新访谈，综合已有信息成 PRD，先划接缝找深模块 |
| ③ 票据 | `/to-tickets` | 拆成示踪弹垂直切片（issues），声明依赖按序发布 |
| ④ 实现 | `/implement` | 只执行不决定，围绕接缝 TDD，勤查类型跑全套 |
| ⑤ 评审 | `/code-review` | 双轴并行：标准（异味）× 规范（是否忠于 PRD） |

**这条主线真正的巧思：把"想清楚"和"写代码"彻底分开。** 前三步不写一行代码，只做认知对齐（拷问→规范→票据）；后两步才动手，且实现完立刻用双轴评审兜底。想法越模糊，越该从 `/grill-with-docs` 开始，而不是直接开干。

---

## 番外：productivity 分类——不写代码，但让你想得更清、传得更顺

除了 engineering，README 里还有一类 productivity https://github.com/mattpocock/skills#productivity。它们不产出代码，适用于通用工作流,专治"想不透、接不上、教不会"。

**用户主动调用的（User-invoked）**

| Skill | 一句话 |
| --- | --- |
| `grill-me` | 被无情连环追问一个计划/设计，直到决策树每个分支都有答案 |
| `handoff` | 把当前对话压成一份交接文书，让下一个 Agent 无缝接手 |
| `teach` | 跨多次会话教你一个新技能，把当前目录当作有状态的教学工作区 |
| `writing-great-skills` | 写好、改好 skill 的参考：让一个 skill 可预测的词汇与原则 |

**模型自己会调的（Model-invoked）**

| Skill | 一句话 |
| --- | --- |
| `grilling` | 无情访谈的 **可复用底层循环** ， `grill-me` 和 `grill-with-docs` 都建在它之上 |

有意思的呼应：主线 第一 步的 `grill-with-docs` ，其实就是 `grilling` 这套"拷问循环"原语 + 领域建模的组合。 **"拷问"是 Matt 整套方法论的地基** ——先把你问透，再谈写代码。

![[Image 11.webp|图片]]

`handoff` 则和主线首尾呼应：一条流程跑不完、要换个 Agent 接力时， `handoff` 把上下文压成交接文书（还附一段"建议下一步该调哪个 skill"），下一个 Agent 读完就能从断点续上。这个skill也被一些其他工程skills借用。

---

仓库地址：https://github.com/mattpocock/skills https://github.com/mattpocock/skills

另外仓库里还有个 `ask-matt` 路由技能，会根据你当前处境帮你挑该用哪条工作流。