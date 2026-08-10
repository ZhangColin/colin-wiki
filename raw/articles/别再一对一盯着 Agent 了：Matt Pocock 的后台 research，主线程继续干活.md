---
title: "别再一对一盯着 Agent 了：Matt Pocock 的后台 research，主线程继续干活"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491632&idx=1&sn=3aa920842652d1e9cdeb2bde850a84fb&chksm=cf405566f837dc706168fca18e81c4f2213794e12593fd0ae0ad4af420a8f9f1cfd094fe4cdd&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-10
description:
tags:
---
运维有术 术哥无界 *2026年8月7日 08:30*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *189* 篇，AI 编程最佳实战「2026」系列第 *68* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

![[Image 101.webp|后台 Agent 并行工作法信息图封面：主线程继续干活]]

后台 Agent 并行工作法信息图封面：主线程继续干活

大多数人的 Agent 用法是 **一对一盯着** ：问一句，等一句。让它读文档，你就干瞪眼；让它搭分支，你盯着进度条；它一卡住，你马上切过去救。结果是人成了 Agent 的附属品，Agent 反而成了你工作的主线程。

这其实是个错误的分配方式。一套成熟的工作流里，Agent 是可以拆成前台和后台的：你写代码的时候，让另一个 Agent 去读文档；你评审的时候，让后台去跑验证。

Matt Pocock 的 `skills` 仓库里， `research` 、 `wayfinder` 、 `triage` 这几个 skill 把这件事讲得比较清楚，而且给出了可执行的机制。这个仓库在 GitHub 上 star 数很高，截至 2026 年 7 月约 16 万。

这篇就围绕一个问题展开： **怎么判断这活能不能丢给后台，以及后台结果回来后怎么安全地并进主线程。**

## 1\. 先看一个连续案例：主线程和后台怎么并行

假设你现在在主线程做一件需要专注的事：审查一个支付回调的重构 PR。逐行看 diff、核对状态机迁移、确认异常处理，这些事要求你全神贯注，中途插不进任何别的任务。

但审着审着，你发现一个疑点：PR 里对 **第三方支付回调 API 的边界行为** 做了假设，比如回调最多重试 3 次、签名过期时间 5 分钟、重复通知会被幂等处理。这些假设对不对，直接决定这次重构安不安全。

搁以前，你有两条路：要么切出主线程，自己去翻第三方 API 文档，看完再切回来接着审——上下文切换的代价你懂的。要么先记下疑点继续审，等审完再补查——那这个 PR 就没法一次审完。

现在你有了第三条路：拉起一个 `research` 后台 Agent，把问题丢给它， **然后继续审你的 PR** 。

时间线大概是这样的：

| 时刻 | 主线程（你） | 后台 Agent（research） |
| --- | --- | --- |
| T0 | 发现疑点：回调重试次数、签名过期、幂等行为 | 收到任务：查第三方回调 API 的一手资料 |
| T1 | 继续审查状态机迁移部分 | 拉取官方文档、源码、spec，逐条核对 |
| T2 | 审完异常处理，写了 2 条 review 意见 | 写出带引用的 findings 文件，落到仓库 |
| T3 | 顺手把另一个模块的命名问题也改了 | 在 ticket 上留下 context pointer |
| T4 | 切回来看 findings，补充 review 意见 | 任务结束，不占用主线程任何时间 |

两边没有互相等待。主线程从头到尾没有因为查资料这件事被阻塞，后台 Agent 也没有因为你还没看完就停下。这就是 research skill 开头就写明的语义： **spin up a background agent，so you keep working while it reads** ——拉起一个后台 Agent，它读的时候你继续干活。

这个案例后面的内容都围绕它展开：为什么后台必须追一手资料、为什么产物必须落盘、怎么防止两边互相踩脚。

![[Image 102.webp|前台主线程与后台 Agent 两条并行泳道概念图]]

前台主线程与后台 Agent 两条并行泳道概念图

## 2\. research：后台 Agent 的铁律是追一手来源

先看 `research` skill 本身。 `skills/engineering/research/SKILL.md` 全文只有 12 行，核心就三条。

**第一条：只对一手来源（primary sources）调研。** 官方文档、源码、spec、first-party API 算一手；二手转述（别人的教程、解读文章、博客复述）不算。而且每条声明都要能回溯到拥有它的来源—— **follow every claim back to the source that owns it** 。

这条铁律是有道理的。你让后台 Agent 去查东西，是因为你信不过自己记忆里的二手印象，想拿事实说话。但如果后台 Agent 自己也是从一篇二手博客里抄结论，那你就只是把错误信息包装得更像结论了。

官方文档里的 `research` 文档甚至收录了一条挺扎心的社区批评： **five research subagents pointed at junk just gives you five confident wrong answers faster** ——五个指向垃圾资料的 research 子代理，只会更快地给你五个自信的错误答案。skill 里没有 allowlist、没有 domain gate、没有 verification pass，这个门控问题从它提出起就有人反对，官方至今没有正面回答过。

**第二条：产物是单个 Markdown 文件，落到仓库里。** 找到什么，写成一个带引用的 Markdown 文件，保存到仓库已经保存这类笔记的位置，匹配现有惯例；如果没有惯例，放一个合理的地方并说明。

**第三条（其实是第一条的推论）：后台语义决定了必须落盘。** 你想想，为什么不能让它查完口头汇报一下？因为你自己根本不在等它。你继续干主线程的活了，它查完你根本不会立刻问它查到没。如果结果只在会话里，会话一结束，知识就丢了。

**落盘不是锦上添花，是后台模式的必然要求** ——这是异步的本质：我不在等你的输出，所以你的输出必须能在我需要的时候被找到。

一条后台跑完的 findings 大概是这样的：

```
# 第三方支付回调 API 边界行为调研

> 调研任务：确认支付回调的重试、签名过期、幂等行为
> 调研时间：2026-08-06

### 结论摘要

1. 回调最多重试 3 次，间隔递增（1s/5s/30s）
2. 签名有效期 5 分钟，超时后回调直接丢弃
3. 重复通知由 merchant_order_no 幂等处理，不重复入账

### 来源与证据

- 重试策略：官方文档「回调通知 - 重试机制」章节
  https://docs.example.com/api/callback#retry
- 签名过期：官方文档「安全规范 - 签名」章节
  https://docs.example.com/api/security#signature
- 幂等处理：官方源码 \`webhook/handler.go\` 第 88-95 行，
  按 merchant_order_no 查重后写库
```

注意每一行结论后面都跟着来源。这就是 research 和随便丢给聊天框一句 **帮我查查** 的区别： **research 喂养思考，但不替代思考** —— `ask-matt` 里原话是 **the file it produces is something to take into the main flow at /grill-with-docs** ，产出的文件是拿进主流程供你追问的，不是替你拍板的。

![[Image 103.webp|主线程审 PR 与后台查 API 的 T0-T4 并行时间线]]

主线程审 PR 与后台查 API 的 T0-T4 并行时间线

## 3\. wayfinder：把工作明确分成 HITL 和 AFK

`research` 管的是 **单个后台 Agent 怎么干活** ， `wayfinder` 管的是更大的问题： **一件大工作，哪些部分能交给 Agent 单独跑，哪些必须有你在场。**

`skills/engineering/wayfinder/SKILL.md` 把超过一个 session 能容纳的大工作画成一张 issue tracker 上的共享决策地图，一次解决一个 decision ticket。每个 ticket 要么 **HITL** （human in the loop，和你一起做），要么 **AFK** （agent 单独驱动）。分类不是随意的，每种 ticket 类型都写死了归属：

- **Research 票（AFK）** ：读文档、第三方 API、或本地知识库来浮现一个决策的事实。由 `/research` 子代理解决， **当需要工作目录之外的知识时使用** 。回到我们的案例，查支付回调 API 的边界行为就是典型的 Research 票——那些知识不在你仓库里，在第三方那边。
- **Task 票（AFK 或 HITL）** ：决策发生之前的机械前置工作——没有要决定、要原型、要调研的东西，但讨论被阻塞直到它完成。Agent 能独立跑的部分就 AFK，否则给人类一份精确的 checklist。它 **做而不决定** （does rather than decides）。
- **Grilling 票（HITL，默认）** ：通过对话一次一个问题地澄清需求。这是默认 case。
- **Prototype 票（HITL）** ：通过做一个便宜、粗糙、具体的 artifact 来提升讨论的保真度。

划线的逻辑值得停下来品一下： **凡是需要做决定的，HITL；凡是机械执行的，AFK。** Research 是读、是查、是收集事实，不需要拍板，AFK；Task 是铺路、是前置、是跑机械步骤，不需要拍板，能独立跑就 AFK；Grilling 和 Prototype 直接产出决定本身，必须人在场。

wayfinder 对这条边界说过一句很重的话： **a grilling agent that answers its own questions has broken this** ——一个自己回答自己问题的 grilling agent，已经坏了。因为 grilling 的意义就是你问、人来答，需求对齐发生在人和 Agent 的对话里。如果 Agent 自己出题自己答，对齐就变成了自嗨，最后做出来的东西没人负责。

![[Image 104.webp|research findings 文件的结论摘要与来源证据结构示意图]]

research findings 文件的结论摘要与来源证据结构示意图

## 4\. 后台和主线程的边界：认领、分支、指针、契约

并行不意味着自由发挥。同一张票、同一批文件，两边一起动，必然打架。wayfinder 和 triage 给了四道边界机制，前三个在 wayfinder，第四个在 triage。

**第一道：claim，先认领再开工。** 一个 session 在动手之前，先把 ticket assign 给自己，这样并发的 session 就会跳过它。assignee 就是 claim——一个 open 且 unassigned 的 ticket 就是 unclaimed。配套的还有一条 **blocking 边** ：wayfinder 用 tracker 的 native dependency 把票与票之间的阻塞关系可视化，frontier 一眼可见，任何 blocker 完成的票都可以被 grab。

这解决的是 **并发抢同一张票** 的问题：wayfinder 明确预期用户可能并行跑多个 unblocked tickets，所以其他 session 在并发编辑 tracker 是正常状态。没有认领机制，两个后台会同时开工同一件事，白干一份。

**第二道：throwaway 分支隔离。** 创建 research ticket 时，拉起 `/research` 子代理 **并行** 解决，在 throwaway `research/<name>` 分支上捕获 findings。

为什么是 throwaway 分支？因为主线程还在 main 上干活。后台的探索性改动、临时文件、半成品，都不该污染你正在工作的分支。它干完，findings 留在自己的分支里，主分支干干净净。

**第三道：context pointer。** 后台干完不是说一声就完，而是从 ticket 留一个 context pointer——指向 findings 文件在哪、分支叫什么。这样你切回来的时候，不用问上次查到哪了，ticket 上就有入口。知识不散落在会话记忆里，而是挂在 ticket 上。

**第四道： `ready-for-agent` + agent brief。** 这是 `triage` skill 的机制，它把 issue 移过一组状态角色，其中 `ready-for-agent` 的定义是 **fully specified, ready for an AFK agent** ——已完全说明，可交给 AFK Agent。这个状态下贴的 `AGENT-BRIEF.md` 是后台 Agent 工作的 **契约** ，四条要点：

- **Durability over precision** ：不引用文件路径、行号这种会过期的细节，写接口/类型/行为契约
- **Behavioral, not procedural** ：写系统应该做什么，不写怎么实现
- **Complete acceptance criteria** ：每个验收标准独立可测
- **Explicit scope boundaries** ：明确 out of scope，防止 Agent 自己加戏

一句话总结这四道边界： **用认领和 blocking 边防抢票，用分支防污染，用指针防丢失，用契约防跑偏。**

你注意一下，这套机制本身就是个诚实的声明——它默认并发是会出问题的，所以才需要这么多防线。这跟我们后面要说的别神化后台直接相关。

## 5\. 一张表判断：什么能丢后台，什么必须你在场

把前面几节压缩成一张判断表。判断的核心就一个问题： **这活需要做决定，还是只需要做执行？**

| 工作类型 | 归属 | 判断依据 |
| --- | --- | --- |
| 读一手资料 / 文档 / API / 知识库 | AFK（research 票） | 事实收集，不需要拍板 |
| 搭临时分支 / 机械接入 / 跑验证 | AFK（task 票） | 前置工作，做而不决定 |
| 可独立验收的实现 | AFK（ready-for-agent） | agent brief 有完整验收标准 |
| 需求对齐 / 设计取舍 | HITL（grilling，默认） | 一问一答，Agent 不能替人回答 |
| prototype 评审 | HITL | 讨论保真度需要人在场 |
| merge 冲突解决 | HITL | 按 intent 回溯，需要人判断哪侧意图 |
| 外部访问 / judgment call / 手动测试 | HITL（ready-for-human） | 无法委派的原因要写在 note 里 |

再补一个可以后台化的细节： `diagnosing-bugs` skill 的第三阶段要求生成 3-5 个可证伪的假设并 **展示给用户再测** （用户常能瞬间重排），但它明确写了： **Don't block on it——proceed with your ranking if the user is AFK** 。用户不在，就按你的排序继续。这也是机械执行可后台化的佐证：提假设是机械的，选假设才是人该干的。

![[Image 105.webp|AFK 与 HITL 判断表对比图：什么能丢后台，什么必须你在场]]

AFK 与 HITL 判断表对比图：什么能丢后台，什么必须你在场

## 6\. 反例：后台 Agent 不是银弹

说了这么多能后台化的，泼几盆冷水。以下反例来自源码逻辑和官方文档收录的社区报告，每一个都对应一条边界机制。

**反例一：把 grilling 票丢给后台。** 这是最典型的误用。grilling 是 HITL 默认，因为需求对齐必须发生在人和 Agent 的对话里。你让后台 Agent 自己 grill 自己，得到的就是一个自洽但没人对齐的方案。wayfinder 的原话已经说死了： **一个自己回答自己问题的 grilling agent 已经坏了。**

**反例二：后台 Agent 拿二手资料下结论。** primary sources 铁律不是装饰。后台 Agent 查东西本来就没人盯着，如果它再偷懒从二手博客抄，你拿到的就是一层层转述后的变形事实。而 skill 本身没有任何门控——官方文档收录的批评里， **primary source 无门控** 被反复提起：allowlist、domain gate、verification pass，一个都没有。

所以这条铁律的执行，靠的是你给任务时把范围说清楚。

**反例三：主线程和后台同时改同一批文件。** claim、throwaway 分支、blocking 边这三道防线就是为了防这个。

现实里的后果，官方 wayfinder 文档收录过一条很扎心的用户投诉： **我的 agent 在 wayfinder 会话中间开始写生产代码了** ——agent 往自己写的 Notes 里写了一句 `this map carries execution` ，当成授权自己动手了。这是官方文档里反复被报告的失败。后台擅自越过边界，改了你正在改的文件，你俩的改动直接冲突。

**反例四：research 结果没落盘。** 后台语义决定了你不在等它，所以结果只在会话里等于不存在。会话结束，知识跟着上下文一起蒸发。你重新查一遍的成本，比当时顺手落盘高得多。这就是为什么 research 的产物必须是 **写进仓库的单文件** ，而不是一句口头总结。

另外还有两个官方文档收录的、没解决的社区报告，值得你知道：

- **research 嵌套 bug** （GitHub issue #530，至今 open）：research 的 background agent 会再 spawn 一个 background agent。有人测到单个 research 任务烧了约 45 万 tokens，三个 run 重叠，重复的那个半小时后才结束，完全在视野外。
- **结果不复用** ：有社区批评说，写一次就死的 research 文件只是花哨的搜索，没有任何东西会自动加载过去的 research 文件。

所以关于后台 Agent，我的建议是： **把它当成一个需要验收的临时同事，不是免检的自动化流水线。** 后台化大概率省时间，但没人能打包票；冲突大概率能靠机制拦住，但不是不会发生。

官方的 `running-your-afk-agent` 文档给了一个很实在的建议： **先用 HITL 跑** ——全程人盯着，观察它怎么选任务、怎么写测试、哪里 struggle，再逐步放手。

## 7\. 后台化和 handoff 不是一回事

这里要澄清一个容易混的概念： **handoff 是接力，AFK 是并行。**

`handoff` skill 处理的是会话结束时的上下文交接：这个会话干到哪了、下一步是什么、踩过什么坑，写清楚交给下一个会话。它是 **串行** 的——一个会话结束，另一个会话接上。

research / AFK 是 **并行** 的——同一个时间点，主线程和后台 Agent 各干各的，主线程不阻塞。我们的案例里，你审 PR 和后台查 API 是同时进行的，不存在查完了我再看的等待。

两者容易混淆，是因为它们都用写文件、留指针作为交接手段，但语义完全不同：handoff 的文件是 **给下一个会话的启动上下文** ，research 的文件是 **给当前会话主线程的参考资料** 。前者是接力棒，后者是路标。一个串行、一个并行，别搞混。

## 总结

回到开头的问题：怎么判断这活能不能丢给后台，以及后台结果回来后怎么安全并进主线程。

答案分三层。 **第一层，判断准则** ：做决定的活（grilling、prototype 评审、merge 冲突）必须你在场；做执行的活（读一手资料、搭分支、机械接入、跑验证）可以丢后台。

**第二层，落盘约定** ：后台产物必须是带引用、落仓库、可回溯的文件，因为你不在等它，落盘就是它存在的方式。

**第三层，主线程边界** ：用 claim 和 blocking 边防抢票、用 throwaway 分支防污染、用 context pointer 防丢失、用 agent brief 防跑偏。

最后留一句实话：这套机制的源码里，一半以上的篇幅都在防并发出问题，这本身就是对后台 Agent 最诚实的评价——它不是用来替代你的判断的，是用来把你的时间从机械劳动里解放出来的。真正该你做的决定，一个都没少。

> **说明** ：本文内容基于 Matt Pocock `skills` 仓库源码（ `mattpocock/skills` ）和官方文档（aihero.dev）分析整理而成，属于源码与文档层面的机制解读，尚未在生产环境中完成全场景验证。 **文中的配置模板和参数建议仅供参考，实际效果请以你的业务数据和环境测试结果为准。** 如果有实际使用经验，欢迎在评论区分享交流。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录