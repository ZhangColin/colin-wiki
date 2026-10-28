---
title: "mattpocock/skills: implement-spec 拆解，一次跑完整份 spec"
source: "https://mp.weixin.qq.com/s/rrRRTgkarRoZL-ddSIBefg"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-10-15
description: "共渡时艰 · 拆解implement-spec 拆解：一次跑完整份 spec任务图、并发前沿、集成分支——它和"
tags:
---
超级奶爸老谭！ 共渡时艰 *2026年10月15日 07:50*

共渡时艰 · 拆解

## implement-spec 拆解：一次跑完整份 spec

任务图、并发前沿、集成分支——它和 implement 到底怎么选

你刚用 `/to-spec` 和 `/to-tickets` 把一摊想法拆成了一份 spec 和一组票。接下来发生什么？大多数人的做法是：开一个新会话，敲 /implement 跑一张票，清空上下文，再开下一个。一个五票的功能跑完，你就是那个调度员——调度了一整天。

Matt Pocock 的 implement-spec 技能就是冲着这个动作去的。它的官方描述只有一句：把 /to-spec 和 /to-tickets 的产物实现成代码。翻译成大白话：整份 spec，一次跑完。这个技能 8 月 21 日才以 in-progress 身份进仓库，10 月 4 日随 v1.3.0 正式毕业进入 Engineering 桶——和此前拆过的 retro、pr 是同一批（PR #1120）。截至 10 月 14 日，仓库星标已到 290612。

## 01 它是什么：一个编排会话，加一队并行工人

先看定位。它和 implement 一样，frontmatter 里写着 `disable-model-invocation: true` ，openai.yaml 里还有 `allow_implicit_invocation: false` 。AI 不会自动触发它，必须你亲手敲 /implement-spec。这是全仓库多数技能的共识：编排权在你，不在模型。

官方文档对工作方式的描述，一句话就能说完：编排 agent 把每张票交给一个 implementer subagent（实现子代理），每个子代理在各自的 git worktree 里干活，完成的分支并到一条集成分支上，最后在集成分支上跑 code-review，按 issue tracker 的方式关掉每一张票。

SKILL.md 全文 40 行，核心是九个步骤，压缩一下是这样的：

- 读 spec 和 tickets，把票读成任务图，而不是清单；
- 可选：派一个 exploration subagent 做代码与外部文档探索，笔记存到仓库外、所有子代理都能访问的目录，实现子代理由此只管实现；
- 创建集成分支；若 tracker 以 PR 收尾，第一次合并后开一个 draft PR；
- 每张票派一个 implementer subagent，各自 worktree、各自分支：开工前确认基于集成分支，用 tdd 建票，报告完成前把集成分支 tip 合进自己分支；
- 有子代理报告完成，派一个 merger subagent 把它的分支合进集成分支；
- 前沿因此变化，立刻向新解锁的票派出更多实现子代理，要的是最大并发；
- 全部票落地后，在集成分支上调一次 code-review，所有问题交给唯一一个实现子代理去修；
- 有 draft PR 就标 ready，否则按 tracker 的方式逐张关票，汇报集成分支；
- 清理所有 worktree。

## 02 三个核心概念：任务图、前沿、集成分支

这个技能的全部设计，压在三个概念上。

**第一是任务图。** SKILL.md 的原话：票不是步骤清单，而是带阻塞关系的任务图。阻塞边决定谁能先开工，于是任何时刻都存在一个前沿（frontier）：阻塞项全部落地的那些票，前沿上的每一张都同时跑。官方文档的说法更狠：定节奏的是图的形状，不是票在 tracker 上的排列顺序。

**第二是集成分支。** 所有成果只落到一条分支上。每个实现子代理报告完成前，必须先把集成分支的最新 tip 合进自己分支——这样它落地时是一次快进（fast-forward），不是一次冲突解决。合并动作则由专门的 merger subagent 执行。

**第三是上下文指针。** 技能要求与子代理的通信保持稀疏：主要靠指针传递信息——spec、票、共享探索笔记、此前的 commit，指针里已有的信息不复制。好处很直接：子代理的 prompt 保持小，编排会话自己的窗口留给任务图。

| 概念 | 一句话 | 解决的问题 |
| --- | --- | --- |
| 任务图 | 带阻塞边的票，不是清单 | 决定谁能先开工 |
| 前沿 | 阻塞项全部落地的票的集合 | 决定这一刻放几个工人出去 |
| 集成分支 | 所有成果落地的唯一分支 | 让每次合并都是快进，冲突不堆积 |
| 上下文指针 | 对 spec、票、笔记、commit 的引用 | 子代理 prompt 够小，编排窗口留给图 |

## 03 使用场景：三条前置条件，一张选型表

官方文档给了一张选型表，四种情况各有着落，原样转写：

| 你的情况 | 用哪个 |
| --- | --- |
| spec 已拆成带阻塞边的票，想一次落地 | /implement-spec |
| 一次一张票，在自己的上下文窗口里跑，票与票之间清空 | /implement |
| spec 还没拆票 | 先 /to-tickets |
| 一小摊活，没有真正的图 | /implement 直接上 |

三条前置条件也值得逐条过：

- **有 issue tracker。**
	票从 tracker 上读、在 tracker 上关。没配置，技能会停下来让你先跑 /setup-matt-pocock-skills，而不是自己猜一个。
- **票带阻塞边**
	，to-tickets 写的那种。没有边，图是平的，所有票会在第一秒同时开跑。
- **harness 能后台跑子代理**
	，并且给每个子代理独立 worktree。在串行执行子代理的 harness 上，它只是一个更慢的 implement——这门技能存在的意义就是并发。

官方文档收录了一条用户反馈，解释了它为什么存在：有人想要 “subagents implement the tickets”（让子代理去实现这些票），而不是 “individually create new session and tell them to implement a ticket one by one, when a spec may contain over 5 tickets”（一个 spec 可能有 5 张以上的票时，逐个开会话、逐张告诉它去实现）。

另一个常见误解顺便澄清：它不依赖 GitHub。早期版本固定以 PR 收尾，有用户抱怨想离线停在分支上；现在只有你的 tracker 用 PR 收尾、或你主动要求时才会开 PR，否则跑完全部票、全部合并进集成分支即止——本地 markdown tracker 也能全程离线。

## 04 和 implement 的区别：调度权归谁

这是最容易被问混的一对。官方文档的定位是：implement-spec 就是「把 implement 逐票跑一遍」的并行替代版。差异可以压成一张表：

| 维度 | /implement | /implement-spec |
| --- | --- | --- |
| 输入 | 一个 spec 或一组票，习惯上一次一票 | 一份 spec 加它拆出的全部票 |
| 调度者 | 你自己：一票一个会话，票间清空上下文 | 一个编排会话，你只在开头指向 spec |
| 并发 | 不支持；同一 checkout 里并行开会话会互相踩——官方收录过 amend 落到别人 commit、stash 从 refs/stash 消失、commit 落错分支的报告 | 前沿上的票全部同时跑，各自 worktree，互不见面 |
| 分支 | 直接提交到当前分支，不建分支，也不问 | 建集成分支，工人分支快进合并进来 |
| 评审时机 | 每票提交前跑一次 code-review | 全部落地后，在集成分支上跑一次 |
| 收尾 | 到 commit 为止，不关票、不勾验收项，没有 completion step | 按 tracker 的方式逐张关票，或把 draft PR 标 ready |
| seam 约定 | 会话内可以现场约定，tdd 拒绝在不确认的 seam 上写测试 | 没有现场约定环节，想钉死就把 seam 写进 spec 或票 |

所以怎么选？记住那条分界就行：有图、票多、想一次落地，用 implement-spec；一次一票、小活、想逐票读代码，用 implement。代价也是明码标价：implement 之下，你亲自读每张票落地时的代码；implement-spec 把调度交给编排会话，你不再逐票读，最后评审集成分支。用官方文档的话说：你审查的是集成分支，在终点。

## 05 注意事项：官方 FAQ 里的六个坑

这份技能的 FAQ 密度极高，六个坑每一个都来自真实报告。

**坑一：code-review 只能跑一次，且必须在全部票落地之后。** 它拿全部 spec 做比对，中途跑会把还没建的票统统读成失败，agent 于是去建那张票，再触发下一轮评审。官方收录了一个五票功能的报告：评审与修复循环花了大约 4 个小时。对策：看到第二次大评审启动，让它只对已修发现做聚焦检查，然后停。第一轮评审一定会查出真问题——这个技能的产出是草稿，评审才完成它。

**坑二：并行冲突只是被推迟到合并时刻。** worktree 不消除碰撞，阻塞边本身也是从票的文本里猜的。官方收录的例子里，一个用户的 web 票和 mobile 票把同一个字符串分别写成了 blockedSince 和 blockedOn。对策：两张票共享文件时加一条阻塞边让它们串行，或者在共享探索笔记里钉死每个名字。

**坑三：在 GitHub 上，被阻塞的票可能永远不启动。** tracker 的 blocked-by 计数只在阻塞票关闭时才下降，而票通常在 PR 合并时才关闭——那已经是整场跑的终点。tracker 是起点的正确图源，却是中途的过期图源。对策：让编排者自己记录哪些票已合并进集成分支，据此计算前沿。

**坑四：worktree 里测试会静默跳过。** worktree 只装 git 跟踪的内容；读 gitignored 夹具、本地数据库、凭证的测试，会在那里静默跳过并报绿。验证依赖未跟踪材料的票，让编排者回主 checkout 跑。

**坑五：tdd 现在每个实现子代理都驱动，但 seam 不保。** 早期版本里子代理不继承 /tdd 指令，一从单票放大到整份 spec，红绿循环就停了——这是 in-progress 用户发现的，现在每个实现子代理建票时都会调 tdd。但没有 implement 会话里那种交互式 seam 约定；想钉死 seam，写在 spec 或票里。

**坑六：它不替代 Sandcastle，也不替代任何 AFK 脚本。** 真正无人值守的活，用确定性循环——一个 shell 脚本、一个 CI job——更快、更便宜、更可靠，因为没有 agent 在做编排决策。implement-spec 是把一个 agent 按在编排位上，好处是你能看、能随时纠偏。

## 结语

这个技能最值得学的其实不是并发，而是它把「调度」这件事从人手里拿走的方式：任务图定节奏，前沿定人数，集成分支定终点，指针代替寒暄。九个步骤里没有一步是写代码——它只决定谁在什么时候写什么。

但它交付的是草稿，不是成品。评审完成它，不是它完成自己。想清楚这件事，再决定要不要把下一份 spec 交给它。

💬 聊聊你的看法

你的 spec 现在拆成票了吗？带阻塞边的那种？手头如果有五张以上的票，你打算当一晚调度员，还是把调度交出去？

### 📌 数据来源说明

- mattpocock/skills 仓库 skills/engineering/implement-spec/SKILL.md 与 skills/engineering/implement/SKILL.md（本地克隆 main 分支，commit 49dd158，2026-10-09）
- 同仓库官方文档 docs/engineering/implement-spec.md 与 docs/engineering/implement.md
- CHANGELOG.md 与 GitHub Releases：v1.3.0（2026-10-04 发布）的 PR #1120 毕业记录，v1.3.1 为当前最新版本
- 仓库创建于 2026-02-03，MIT 协议；星标 290612、fork 24396、开放 issue 174，经 GitHub API 于 2026-10-14 查询确认
- 文中用户反馈引文均转写自官方文档 FAQ，为节译，未改动原意

### ⚠️ 免责声明

本文为技术工具分析类内容，所有事实均来自公开仓库源码与官方文档并经交叉核实，仅供参考，不构成任何技术选型或工程决策依据。文中涉及的仓库名称、作者信息归各自所有者所有。

[总结篇 | mattpocock/skills 全系列回顾与速查合集](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484175&idx=1&sn=3f436b6bfd417c40b3c5147c5a03b657&scene=21#wechat_redirect)

#mattpocock #skills #implement #spec #engineering

✨ 人工智能 · 目录