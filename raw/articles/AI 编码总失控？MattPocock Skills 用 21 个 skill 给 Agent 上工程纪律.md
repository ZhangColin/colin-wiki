---
title: "AI 编码总失控？MattPocock Skills 用 21 个 skill 给 Agent 上工程纪律"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491394&idx=1&sn=bd4f2611e552d4089be677fdd17fd9d4&chksm=cf43aa14f8342302731a5875c754e86764a21221c966f269918787ba7fe1be7438f44e11083d&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-03
description:
tags:
---
运维有术 术哥无界 *2026年7月13日 08:30*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *165* 篇，AI 编程最佳实战「2026」系列第 *51* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

![[Image 48.webp|MattPocock Skills 21个skill工程纪律体系信息图封面]]

MattPocock Skills 21个skill工程纪律体系信息图封面

> `mattpocock/skills` 是 Total TypeScript 创始人 Matt Pocock 开源的一套 AI Agent 技能库，面向真实工程，不是 vibe coding。截至 v1.1.0，它包含 21 个正式 skill，把需求澄清、TDD、调试、架构治理翻译成 Agent 可执行的纪律。

## 1\. 这个项目在解决什么

先说四个场景，看看你有没有遇到过。

Agent 给你写了一堆代码，但完全不是你要的东西。你反复改 prompt，来回拉扯，最后自己上手改更快。

Agent 喋喋不休，每个回答都把背景知识从头解释一遍，上下文窗口很快被废话占满。

代码写完了跑不通。Agent 改了五遍，每次都信誓旦旦说 **这次应该好了** ，但你根本没有可靠的反馈循环来验证。

一周后回头看，Agent 加速了你代码库的熵增。命名混乱、重复代码、耦合加深。

这四个痛点不是 AI 时代才有的新问题，软件工程里它们存在了几十年。Matt Pocock 的做法是把几十年验证过的工程纪律，翻译成 Agent 能执行的形式。

README 引用了四本经典： *The Pragmatic Programmer* 的 feedback loop、Eric Evans 的 ubiquitous language、John Ousterhout 的 deep modules、Michael Feathers 的 seam 概念。这不是 AI 原生的发明，是老派工程纪律的 Agent 化。

| 痛点 | 根因 | 对应 skill |
| --- | --- | --- |
| Agent 不听话 | 沟通鸿沟 | `grill-me`  、 `grill-with-docs` |
| Agent 太啰嗦 | 缺共享领域语言 | `domain-modeling`  \+ `CONTEXT.md` |
| 代码跑不通 | 没有反馈循环 | `tdd`  、 `diagnosing-bugs` |
| 代码库变烂 | Agent 加速熵增 | `to-spec`  、 `improve-codebase-architecture` |

![[Image 49.webp|四大工程痛点与对应skill的映射图]]

四大工程痛点与对应skill的映射图

*图 1：四大工程痛点不是 AI 时代的新问题，是老派工程纪律的 Agent 化*

## 2\. 核心架构：两层调用分层

整个项目有一个设计决策贯穿始终：user-invoked 和 model-invoked 的分层。

每条 SKILL.md 的 frontmatter 里有个字段叫 `disable-model-invocation` ，它决定谁能触发这个 skill。

| 类型 | 字段值 | 触发方 | 上下文成本 |
| --- | --- | --- | --- |
| User-invoked | `true` | 只能用户手动输入 `/skill-name` | 零 context load |
| Model-invoked | 省略 | 用户或模型都能触发 | description 占用每轮上下文 |

为什么要分两层？

如果所有 skill 都让模型自动触发，系统提示会变重，每个 skill 的 description 都要占上下文，模型还要在每轮推理里判断该不该用。

如果所有 skill 都只能用户手动触发，又会增加用户记忆成本，你得记住 21 个 skill 名字和它们的用途。

分层方案是个折中。高频需要用户主动决策的 skill 设为 user-invoked，零上下文成本；需要模型根据场景自动调用的 skill 设为 model-invoked，让模型自己判断。

这里有一条铁律： **user-invoked skill 永远不能调用另一个 user-invoked skill** 。

为什么？User-invoked skill 没有 description 暴露给模型，模型压根找不到它。这条规则也解释了 `ask-matt` 为什么作为路由器存在：它是 user-invoked skills 之间的索引层，帮用户导航到正确的入口。

## 3\. 主线流程：从 idea 到 ship

`ask-matt` 是理解整个项目的主钥匙。它把所有 skill 组织成一张 **地铁图** ，主线是从 idea 到 ship。

整个流程的思路是先澄清需求，再决定怎么构建，最后在纪律约束下实现。

### 需求澄清

起点是 `grill-with-docs` （有 codebase）或 `grill-me` （无 codebase）。两者底层都调用 `grilling` 这个 model-invoked 原语。

`grilling` 的核心规则用 SKILL.md 原文说：

> Interview me relentlessly about every aspect of this plan until we reach a shared understanding.

v1.1.0 有一个关键演进： **facts 和 decisions 分离** 。

Facts（事实）→ Agent 自己查代码，不要问用户。你的数据库用 PostgreSQL 还是 MySQL，Agent 自己看就知道了。

Decisions（决策）→ 必须问用户，等答案。用 PostgreSQL 还是用 MongoDB，这是个取舍，Agent 不能替你决定。

这个分离修掉了一个真实的失败模式：grilling agent 在嵌套调用时（比如 wayfinder 内部调 grilling），会自己回答自己的问题，然后基于错误假设继续推进。

v1.1.0 加了 confirmation gate 强制阻断——没有用户确认，不许 enact the plan。

### 规划与拆票

需求澄清后，如果工作超出单个 session 的容量，走 `to-spec` 整理规范，再走 `to-tickets` 拆成可执行的票据。

v1.1.0 这里有一个重大变化：旧的 `to-prd` 重命名为 `to-spec` ，旧的 `to-issues` 和 `to-plan` 合并成 `to-tickets` 。 **spec** 成为统一的术语。

`to-tickets` 借用了软件工程里的几个经典概念：

| 概念 | 含义 |
| --- | --- |
| Tracer bullet（曳光弹） | 垂直切片，每个 ticket 切透 schema/API/UI/tests |
| Blocking edges | 每张票据声明它的阻塞依赖 |
| Frontier | 所有 blockers 已完成的票据，可以被领取 |

这个模型的好处是：票据之间有明确的拓扑顺序，Agent 可以从 frontier 开始逐个击破，不会撞到未完成的依赖。

### 实现与评审

无论走哪条路，最终都收口到 `implement` 。这个 skill 只有 15 行，核心是组合：

- 能用 `tdd` 的地方尽量用
- 定期跑类型检查和单个测试
- 全量测试留到最后
- 完成后走 `code-review`
- 提交到当前分支

`tdd` 在 v1.1.0 被重构为纯 reference 文档，移除了 refactor 阶段。Refactor 划给了 `code-review` 。

三大测试反模式写得很干脆。

**Implementation-coupled** ：mock 内部协作者，重构就崩。 **Tautological** ：断言用和代码一样的算法重算，构造上不可能失败。 **Horizontal slicing** ：先写完所有测试再实现，测的是想象中的形状。

![[Image 50.webp|从idea到ship的主线流程图]]

从idea到ship的主线流程图

*图 2：ask-matt 把 21 个 skill 组织成地铁图，主线是先澄清、再规划、最后在纪律下实现*

## 4\. 新增 skill：wayfinder

这是 v1.1.0 正式化的 skill，参考文章完全没覆盖。

适用场景：绿地项目或巨型功能，路径从当前状态到终点还不可见。单 session 装不下。

wayfinder 引入了一套概念体系，借鉴了游戏里的战争迷雾：

| 概念 | 含义 |
| --- | --- |
| Destination | 终点，命名它是首要动作 |
| Map | issue tracker 上一个带 `wayfinder:map` 标签的 issue |
| Fog of war | 当前还无法 ticket 的模糊区域 |
| Frontier | 开放的、未阻塞、未领取的子 issue |

四种 ticket 类型：Research（AFK）、Prototype（HITL）、Grilling（HITL）、Task（HITL 或 AFK）。

AFK 是 away from keyboard，Agent 后台跑。HITL 是 human in the loop，需要人参与。

铁律： **never resolve more than one ticket per session** 。这是上下文管理的硬约束，一个 session 只干一件事，干完 handoff，下一个 session 接着干。

wayfinder 的定位是 **plan, don't do** ：产出决策，不产出交付物。它和 `to-tickets` 的区别要分清楚——to-tickets 用于需求已经清晰、只是需要拆解的场景；wayfinder 用于需求本身还模糊、路径不可见的场景。

## 5\. code-review：两轴并行

`code-review` 从 in-progress 转正后，v1.1.0 的完整形态是两轴并行评审。

关键是 **并行** ：作为两个 parallel sub-agents 运行，互不污染。

| 轴 | 关注点 |
| --- | --- |
| Standards | 是否符合 repo 文档化的编码标准 + Fowler smell baseline |
| Spec | 是否忠实实现了 originating issue / PRD / spec |

Standards 轴内置了 Martin Fowler 的 12 个 code smell。

完整列表是：Mysterious Name、Duplicated Code、Feature Envy、Data Clumps、Primitive Obsession、Repeated Switches、Shotgun Surgery、Divergent Change、Speculative Generality、Message Chains、Middle Man、Refused Bequest。

两条约束决定了它的手感：repo 自己文档化的标准永远盖过 baseline；12 个 smell 全是判断题，没有硬性违规。这样 code-review 不会退化成机械的规则检查器。

## 6\. CONTEXT.md：最酷的技术

README 原话：

> It's hard to explain how powerful this is. It might be the single coolest technique in this repo.

CONTEXT.md 是一个 glossary，仅此而已。但它的严格定位是关键：

> CONTEXT.md should be totally devoid of implementation details. Do not treat CONTEXT.md as a spec, a scratch pad, or a repository for implementation decisions. It is a glossary and nothing else.

这个约束其实挺难守住的。很容易把 CONTEXT.md 写成大杂烩，什么都往里塞。Matt 的纪律很硬：只放领域术语的定义。实现细节不放，临时笔记不放，架构决策也不放。

那架构决策放哪？ADR。但 ADR 有三条触发条件，同时满足才写：Hard to reverse（难逆）、Surprising without context（未来读者会疑惑）、The result of a real trade-off（真实取舍的结果）。

不是每个决策都值得记。只有那些你三个月后回头会问 **当时为什么这么选** 的决策，才进 ADR。

仓库自己的 CONTEXT.md 是范例，只定义了三个术语：Issue tracker、Issue、Triage role。还显式标注了弃用词： **backlog** 因歧义被淘汰。

## 7\. 调试纪律：先造循环

`diagnosing-bugs` 的 6 阶段里，Phase 1 是核心。SKILL.md 原文说得很直接：

> This is the skill. Everything else is mechanical.

Phase 1 的目标：构造一个 tight + red-capable 的反馈循环。形式不限——可以是 failing test、curl 命令、CLI fixture、Playwright 脚本、trace replay、throwaway harness、fuzz loop、git bisect harness、differential loop、HITL bash，随便哪种。

完成判据有四条 checklist：Red-capable（能在这个 bug 上变红）、Deterministic、Fast（秒级不是分钟级）、Agent-runnable。 最核心的纪律是这句：

> If you catch yourself reading code to build a theory before this command exists, stop — jumping straight to a hypothesis is the exact failure this skill prevents.

很多人 debug 的习惯是先读代码、猜原因、然后改。这个 skill 反过来：先造一个能复现 bug 的命令，让命令告诉你答案。

读代码猜原因会锚定，单一假设会误导，所以 Phase 3 要求列 3-5 个 ranked hypotheses 来对抗单一假设的锚定效应。

Phase 6 会把架构问题交接给 `improve-codebase-architecture` 。如果一个 bug 的根因是架构层面的，修 bug 没用，得改架构。

![[Image 51.webp|diagnosing-bugs调试6阶段流程图]]

diagnosing-bugs调试6阶段流程图

*图 3：Phase 1 是核心——先造 tight + red-capable 的反馈循环，让命令告诉你答案*

## 8\. skill 的元设计：writing-great-skills

这个 skill 把 skill 本身作为可设计的工程对象。核心概念：

| 概念 | 含义 |
| --- | --- |
| Context load | model-invoked skill 的 description 占用每轮上下文 |
| Cognitive load | user-invoked skill 需要用户记住它的存在 |
| Progressive disclosure | 把低频细节下沉到外部文件 |
| Leading word | 用模型预训练中已有的紧凑概念锚定一整套行为 |
| No-op | 模型默认就这么做的话，写了等于没写 |
| Negation | 写了 don't think of an elephant，反而激活大象 |
| Negative Space | 你没写的东西也在 steering，每个空缺都交给模型 priors |

Leading word 是这套体系里我最佩服的设计。Matt 用 `_tight_` 、 `_red_` 、 `_fog of war_` 、 `_tracer bullet_` 这些词锚定一整套行为。这些词在模型的预训练语料里有丰富的上下文，一个词就能触发模型对整个概念簇的理解，不需要用三段话解释。

v1.1.0 新增了两个失败模式。

**Negation** ：你写 **不要做 X** ，模型反而被激活去做 X，因为 **不要** 这个词和 X 紧密关联。 **Negative Space** ：你没写的东西也在影响模型行为，模型会用它的 priors 填补空白，而这些 priors 可能不是你想要的。

换句话说，写 skill 不只是写下你想要的那些，还得意识到你没写的部分也在影响模型行为。

## 9\. 版本管理与定位

项目用 changesets 做 versioning，每个 skill 的变更记录在 `.changeset/*.md` ，发布时聚合到 CHANGELOG.md。

这把 skill 当作可发布、可演进、可维护的软件包来对待。不是写完就扔的脚本，是有版本管理的工程产物。

关于定位，README 明确对标 GSD、BMAD、Spec-Kit：

> Approaches like GSD, BMAD, and Spec-Kit try to help by owning the process. But while doing so, they take away your control and make bugs in the process hard to resolve.

Matt 的主张：small、easy to adapt、composable。不接管你的流程，不绑定你的工具链。

| 维度 | mattpocock/skills | GSD/BMAD/Spec-Kit |
| --- | --- | --- |
| 流程接管 | 不接管，只提供 skill | 接管完整流程 |
| 可改性 | 小、可改、可组合 | 框架较重，定制成本高 |
| 工具绑定 | 跨 Agent（Claude Code、Codex 等） | 通常绑定特定平台 |
| 理论基础 | 几十年软件工程经典 | 各自的方法论 |

安装方式：

```
npx skills@latest add mattpocock/skills
```

选要装的 skill 和目标 Agent，\*\*必须选中 `setup-matt-pocock-skills` \*\*。初始化时会探测 repo 状态、配置 issue tracker、写入 `docs/agents/*.md` 。

![[Image 52.webp|MattPocock Skills与GSD/BMAD/Spec-Kit对比图]]

MattPocock Skills与GSD/BMAD/Spec-Kit对比图

*图 4：不接管流程、小可改可组合、跨 Agent、基于软件工程经典——这是 Matt 的四个主张*

## 总结

这套 skill 库的内核是工程纪律，不是 AI 技巧。

- `ask-matt` 做路由，idea → ship 的主线在一张图里
- `grilling` 做澄清，facts 和 decisions 分离，用户只管决策
- `CONTEXT.md` 做术语表，严格只放 glossary，不放大杂烩
- `to-tickets` 做拆票，tracer bullet + blocking edges + frontier
- `tdd` 做实现，只在 seam 上测试，red before green
- `diagnosing-bugs` 做调试，先造 tight + red-capable 循环再动手
- `code-review` 做评审，standards 和 spec 两轴并行
- `wayfinder` 做巨型规划，fog of war 概念把模糊显式化
- `writing-great-skills` 做元设计，leading word 锚定行为

如果你在做真实工程，用的是 Claude Code 或 Codex，这套 skill 值得花时间研究。它没什么高明的 AI 技巧，但把 **怎么跟 Agent 协作做工程** 这件事想清楚了。

> **说明** ：本文基于 `mattpocock/skills` v1.1.0 源码（GitHub: mattpocock/skills）分析整理，所有 SKILL.md 原文引用均逐行核对。 **文中的工作流解读和 skill 组合建议仅供参考，实际使用请以官方最新版本和你的项目环境测试为准。** 如果有实际使用经验，欢迎在评论区分享交流。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录