---
title: "Matt Pocock 三个 skill：4 个核心词 + 1 张决策表，让 Agent 先问对问题"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491498&idx=1&sn=6a25bfdfd85e0ceae41a45f21b81505c&chksm=cf43aafcf83423ead2d9637c81f0cbd6ee53f17dbfdac6092b53e27cad9ea3fc4e2e5f7c6fc3&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-06
description:
tags:
---
运维有术 术哥无界 *2026年7月23日 08:30*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *175* 篇，AI 编程最佳实战「2026」系列第 *59* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

![[Image 73.webp|Matt Pocock 三个 skill 全文信息图封面]]

Matt Pocock 三个 skill 全文信息图封面

你可能也遇过这种场景：一个写了半年的项目，最近三个月加了用户权限、订单状态、推送通知三个 feature。结果每次新需求进来，你都要在四五个文件之间反复跳转；每个文件单独看都不复杂，合在一起却谁也说不清 **这里到底在做什么** 。

更糟的是，AI Agent 加入之后，产出的速度上来了。但这种 **复杂度被分摊到无数小文件里** 的结构，反而被越冻越死。Agent 看到 pass-through 的函数就照着搬过去，看到 shallow module 就顺着手势给它加一层抽象。短期看，单次任务完成得很利索；长期看，结构已经悄悄固化。

这就是 Matt Pocock 那套 skills 想解决的事：不是给 Agent 一套 **重构 checklist** ，而是给一套共享词汇 + 决策流程，让 Agent 和人能用同一套语言讨论 **哪里是问题、哪个最值得修、该不该先做 prototype** 。

我花了几天把 `mattpocock/skills` 里 `codebase-design` 、 `prototype` 、 `improve-codebase-architecture` 三个 SKILL.md 通读了一遍（加上 `DEEPENING.md` 、 `DESIGN-IT-TWICE.md` 、 `LOGIC.md` 、 `UI.md` 、 `HTML-REPORT.md` ）。也翻了一圈 GitHub issue 里的社区反馈。下面把核心讲清楚。

## 1\. 这三个 skill 解决三种不同形状的问题

先放一张总览，省得你在后面混乱：

| Skill | 解决什么问题 | 不解决什么 |
| --- | --- | --- |
| `codebase-design` | 提供共享词汇和判断原则 | 不直接给你方案，不替你跑扫描 |
| `prototype` | 问题还没成型，用 throwaway code 验证一个具体问题 | 不帮你做架构改进，不写测试、不上线；验证后只 fold 决策进真模块 |
| `improve-codebase-architecture` | 模块已成形，想加深或扫全库时提出候选 | 先列候选让你挑，不直接画最终接口（可选 design-it-twice 跑多接口候选） |

记住这一层关系，下面才不会跑偏。

## 2\. 先把语言统一：4 个让你能讨论结构的词

`codebase-design` 的硬规则是 **Use these terms exactly — don't substitute** ——必须用 module、interface、seam、adapter 这些词，不能替换成 component、API、boundary、layer。

这种咬文嚼字看起来做作，实际是为了避免 Agent 冒出 **代码更干净** 、 **更容易维护** 这种空话。词汇对了，争论的基线才能对齐。

下面这四个词，是讨论结构时最常出场的：

**deep module** （深度模块）。一个模块的 interface 很小、很稳定，但 interface 后面藏着大量实现复杂度——这叫深。深度是 interface 的属性，不是 implementation 的属性。浅模块反过来：interface 大、说明文档长、调用者要懂一大堆事才能用。John Ousterhout 在《A Philosophy of Software Design》里把 deep module 当成好设计的目标——降低变更的认知成本，而不是机械增加抽象层数。

**seam** （接缝）。来自 Michael Feathers 的《Working Effectively with Legacy Code》，原意是 **在不修改该处的情况下改变其行为的位置** 。在 Agent 工作流里，seam 指的是 **行为可以在不改这一处的前提下被替换的位置** 。seam 的纪律很硬： \*\*One adapter means a hypothetical seam. Two adapters means a real one.\*\*（仓库原文大意）——单 adapter 是假想 seam，两个 adapter 才是真 seam。如果某个 interface 当前只有一个实现，那 seam 是过度设计。

**locality** （局部性）。一个改动落在多少个文件里？跨文件改动越多，locality 越差。 `improve-codebase-architecture` 的报告里， **Benefits** 那一栏写的就是 locality 怎么改善——把散落在 6 个文件的逻辑收敛到 1 个 module，认知负担立刻小一截。

**leverage** （杠杆）。一个模块被多少地方调用？leverage 高 = 这个 module 的 interface 设计好坏影响一大片人。设计 deepening 候选时，leverage 高值得重点评估；但 hot spot 之外的 leverage 高项要单独判断是否值得修——别在不需要修的地方花力气。

还有一组隐含原则藏在 `codebase-design` 的 testability 章节里——不写出来讨论结构时常常会漏。具体是这三条： **依赖注入而非 `new` **（Accept dependencies, don't create them）、** 返回结果而非副作用** （Return results, don't produce side effects）、 **小 surface area** （少方法、少参数）。这三条不是抽象口号，是判断 deep module 是否真深的硬指标——一个模块如果到处 `new` 、改 cart.total 而不是返回 Discount、方法列表长得像菜单，那它在词汇表上叫 deep，运行时仍然 shallow。 这四个词都不复杂，难的是在 prompt 里坚持用它们，而不是被 Agent 拐回 **更干净** 、 **更好维护** 。

![[Image 74.webp|四个核心词对照卡片：深度模块、接缝、局部性、杠杆]]

四个核心词对照卡片：深度模块、接缝、局部性、杠杆

## 3\. 问题还没成型之前：别动代码，先做 prototype

`prototype/SKILL.md` 开头一句话就把定义锁死了：

> A prototype is throwaway code that answers a question. The question decides the shape.

两个关键词： **throwaway** 和 **a question** 。

先说 a question。 `LOGIC.md` 顶部专门强调 **State the question** ，还给了判断标准—— **A logic prototype that answers the wrong question is pure waste** 。原型答错分支，整个 prototype 就白做。所以仓库把 **在 README 第一行写清楚要回答什么** 列为强制步骤。

再说 throwaway。 `LOGIC.md` 列了一堆 anti-patterns：

> Don't add tests. A prototype that needs tests is no longer a prototype. Don't wire it to the real database. Don't generalise. No "what if we wanted to support X later." Don't ship the TUI shell into production.

这几条不是建议，是硬约束。一旦你给 prototype 加测试、接真数据库、做 **未来可能用得上** 的抽象，它就不再是 prototype——它是个半成品。

那 prototype 该保留什么？ `LOGIC.md` 的标准是： **只把 TUI/UI shell 后面那个纯模块留下来** ——比如 reducer `(state, action) => state` 、state machine、一组纯函数。shell 本身扔到 throwaway branch，保留为 **primary source** 。 这里有个很容易掉的坑： **原型本来是验证问题形状的，结果做着做着变成了生产代码的草稿。** issue #384 反馈过，即使设置了禁止自动调用，Agent 仍可能在文字里 **按名字推荐** prototype skill，导致设计已经定了又被带进 prototype 歧路。

**Question/Run 模板** （基于 `LOGIC.md` 步骤整理的可复用形式，仓库原文是散文式步骤）：

```
# Question
（用一句话说清楚这个 prototype 要回答什么问题）

# Run
（一条命令直接跑起来，比如 \`pnpm run prototype/auth\`）

# State surface
（每次 action 之后，状态怎么打印 / 渲染——Surface the state 是仓库原话）
```

UI prototype 不一样，走 `UI.md` ：用 `?variant=` 在已有页面里切换 3 个 structurally different 的变体——不是颜色不同，是结构不同。底部居中浮动条左右切换，键盘 ← → 也支持，production build 必须隐藏。

![[Image 75.webp|prototype 四步流程：Question → Run → State surface → Capture]]

prototype 四步流程：Question → Run → State surface → Capture

## 4\. 问题成型之后：先扫哪里最疼，再谈加深

模块已经成形，你怀疑 **这块该加 seam 了** 或 **这块该加深了** ——这时候别上来就设计接口。

`improve-codebase-architecture/SKILL.md` 的第一阶段叫 Explore，仓库原话是 **Scope before you scan** ：

> 不要扫整个仓库，先看 `git log --oneline` 找 hot spots（最近反复改的区域）。

为什么？因为全仓库扫描效率低、噪声大。真正的架构问题，往往集中在那些 **被多个 feature 反复触碰** 的地方。你我开头举的 **用户权限 + 订单 + 通知** 就是典型——三个 feature 都在改。

找到 hot spots 之后，再用 `codebase-design` 那套词汇去问几个反问：

- Understanding one concept, do you have to bounce between many small modules?
- Are there modules that are obviously shallow?
- Were pure functions extracted just for testability, but the real bugs hide in how they're called?

然后在每个怀疑点跑一个 **deletion test** —— `codebase-design/SKILL.md` 给出的核心判断工具，原文是：

> Imagine deleting the module. If complexity vanishes, it was a pass-through. If complexity reappears across N callers, it was earning its keep.

翻译：想象把这个模块删掉。如果调用者变简单了，那它是 pass-through，直接删；如果复杂度被推回 N 个调用者，那它正在 **赚** 它的存在成本。

**deletion test 模板** （仓库原文是散文，我把它整理成可复用形式）：

```
# Module
（要测试的模块名 + 路径）

# Delete scenario
（如果删掉它，谁会变得更简单？谁会变得更复杂？）

# Verdict
- pass-through → 删
- earning its keep → 留 / 进入下一步：加深
- unclear → 先不做决定
```

这里反复出现的纪律是： **One adapter means a hypothetical seam. Two adapters means a real one.** 仓库原文大意。如果某个 interface 当前只有一个实现，那 seam 是过度设计——只有当第二个 adapter 真的存在（或真的会被需要），才值得造 seam。

另一个常被忽视的纪律： `DEEPENING.md` 明确说，加深之后旧的 unit test 必须删掉——"Old unit tests on shallow modules become waste once tests at the deepened module's interface exist — delete them."

## 5\. 为什么不让 Agent 直接给接口：报告先于设计

`improve-codebase-architecture` 第二个阶段的产物是一个 HTML 报告。每张候选卡片必须含：

- Files（涉及的文件）
- Problem（一句话）
- Solution（一句话）
- Benefits（locality + leverage + tests 怎么改善）
- Before / After 图
- Recommendation strength（Strong / Worth exploring / Speculative 三档徽章）

HTML 写到 OS temp dir，文件名带时间戳，进 repo 都不进——这是为了让候选清单可见，但又不污染仓库。报告末尾还有一个 **Top recommendation** 节，列出 **先做哪个 candidate 以及为什么** ，强制 Agent 给出强意见而不是把所有候选并列摆出来。 这里有个反直觉的设计： **报告里不允许直接给接口设计。** 仓库原话是 **Do NOT propose interfaces yet** 。Agent 必须先把候选列出来，然后问用户 \*\*Which of these would you like to explore?\*\*。

为什么这么做？因为一旦 Agent 直接给出接口设计，用户就会不自觉地 **评估那个设计好不好** ，而不是 **评估这个问题值不值得修** 。把候选列成问题而不是方案，可以强制重新对齐 **修不修、修哪个** 。

用户选定后，才进入 grilling loop——调 `/grilling` 走决策树、调 `/domain-modeling` 保持 CONTEXT.md 同步、想要多个接口候选再调 `codebase-design` 的 `DESIGN-IT-TWICE.md` 。

`DESIGN-IT-TWICE.md` 里有个值得抄的设计：并行 sub-agent 跑多个候选接口，每个 agent 不同约束——Agent 1 最小接口（最大 leverage）、Agent 2 最大灵活性、Agent 3 优化最常见 caller、Agent 4 ports & adapters（可选）。每个 agent 必须输出 interface、example、implementation 隐藏了什么、dependency strategy、trade-offs。最后由主 agent 对比 depth、locality、seam placement，给出强意见——原文是 **Be opinionated — the user wants a strong read, not a menu.**

![[Image 76.webp|HTML 候选报告 before/after 示意图]]

HTML 候选报告 before/after 示意图

## 6\. 决策表：先问什么，再看什么，何时动手

把前面几节串成一个判断流程。你拿到一个项目状态，先按这张表决定下一步：

| 当前状态 | 先做的事 | 触发 skill | 不要做的事 |
| --- | --- | --- | --- |
| 状态机 / state model 在纸上能写，跑起来才发现 bug | 写 Question/Run，做 LOGIC prototype | `prototype`  \+ `LOGIC.md` | 不要直接重构 |
| UI 长什么样没定 | 写 `?variant=A` 切换 3 个 structurally different 变体 | `prototype`  \+ `UI.md` | 不要单 variant 投入生产 |
| 模块反复被改，跨文件理解成本高 | 跑 `git log` 找 hot spots，列 candidates | `improve-codebase-architecture` | 不要扫整个仓库 |
| 怀疑某个模块是 pass-through | 跑 deletion test | `codebase-design` | 不要上来就加深 |
| 只有 1 个 adapter | 不造 seam | `codebase-design` | 不要为 **未来可能** 造抽象 |
| 选定一个 candidate，准备进入设计 | 跑 design-it-twice，并行 3 个接口候选 | `codebase-design`  \+ `DESIGN-IT-TWICE.md` | 不要让单 Agent 自评 |
| Agent 自己跑出去 100k token 不问你 | 强约束 `codebase-design` 是词汇表不是流程 | 反馈到 prompt 里 | 不要让 Agent 自行探索 |

最后一行特别值得说。issue #449 反馈了一个真实案例：用户用 `codebase-design` 时，Agent 没把它当成 **词汇参考** ，而是当成 **待执行的流程** ，自己跑出去重新探索、启动接口设计，烧了约 105k token 之后才回来问用户。 **词汇表和流程的边界，必须在 prompt 里讲清楚，否则 Agent 会替你决定。**

## 7\. 三个最容易踩的坑

**坑 1：过早抽象。** Agent 看见 **未来可能有第二个 adapter** ，就会立刻造 seam。 `codebase-design` 的纪律是 **先有第二个实现再说** ，但 Agent 没有这个纪律，需要你在 prompt 里加约束。假想 seam 比没有 seam 更糟——它把 **未来可能变化** 的隐含承诺写进了现在的代码里。

**坑 2：在错误的时机推荐 prototype。** Prototype 跑通了，你舍不得扔，这是另一个话题；更隐蔽的是反过来的情形——设计已经定下来了，Agent 看到 prototype 这个名字就在文字里推，把你重新拉回 prototype 歧路。issue #384 反馈的就是这种误推荐问题：即使 prompt 里禁止自动调用，Agent 仍然会在回复里按名字推荐 prototype skill，绕过限制把用户带回 throwaway 路径。 `LOGIC.md` 的解法很硬：shell 必须扔，纯模块才留——但前提是你真的在 prototype 阶段，不是被拐回来的。

**坑 3：用刚性启发式替代架构判断。** Deletion test 是判断工具，不是判断结果。删掉复杂度消失 ≠ 应该删——可能那个 pass-through 是当前架构故意保留的（比如它是个 external seam，故意把第三方服务的复杂度挡在内部）。词汇给你的是语言，判断还得你来。

## 8\. 这些 skill 不承诺什么

有几件事必须说清楚，免得有人看完觉得 **装上就灵** ：

- **开发速度我没用实测验证。** issue #458 反馈 `codebase-design` 在 TypeScript 大型代码库里只给出一些较小的抽象建议，没识别出真正的 shallow-module 结构问题。说明这些 skill 不是银弹。
- **Token 成本我没有数过。** 仓库没在 prompt 里承诺 **用这套流程能省 token** ——它要求 Agent 写 HTML 报告到 temp dir、并行 sub-agent 跑多个候选接口，本身就是 token 消耗型流程。
- **Agent 自执行风险是真实存在的。** `codebase-design` 没有 session-driver 边界， `prototype` 的名字太宽泛，都可能在 prompt 没约束好时被 Agent 误推荐或误执行。
- **中英文切换也是个坑。** issue #640 反馈这些 skills 倾向切到英文，破坏中文会话连续性。用中文 prompt 时得自己注意。

所以这套 skill 的真实价值，是给你一套 **让 Agent 停下来先想问题形状** 的约束。它不替你做架构决策，但能让 Agent 跟你讨论时用对词、问对问题。

至于这套工作流能不能撑住——半年后看 issue 里 **被采纳的接口建议** 涨到多少个、社区贡献者能不能跨语言写出更多例子，就知道了。

> **说明** ：本文基于 mattpocock/skills 仓库的 codebase-design / prototype / improve-codebase-architecture 三个 SKILL.md 源码通读整理。 **文中的术语定义、原则和流程描述对应仓库原文，但"开发速度收益"、"token 成本"等量化结论未经过实测，仅作设计意图解读。** 欢迎在评论区分享你的实际体验。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录