---
title: "12 行 vs 689 行：mattpocock/skills 与 superpowers 的路线之争"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491359&idx=1&sn=a47c8a78d7d915f79698dbb264a8caf9&chksm=cf43aa49f834235fedd5b6c128e1c732ece0bea6662c6f4f054937257c599511283c5e10dcda&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-03
description:
tags:
---
运维有术 术哥无界 *2026年7月9日 08:30*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *161* 篇，AI 编程最佳实战「2026」系列第 *50* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

![[Image 19.png|12行 vs 689行 路线之争信息图封面]]

12行 vs 689行 路线之争信息图封面

第一次翻开 `grilling` 的 SKILL.md，我以为漏掉了什么。整个仓库居中位置、被两个上层 skill 反复委托的拷问原语，连 frontmatter 带正文一共 **12 行** 。同一件事，隔壁 superpowers 的 `writing-skills` 写了 **689 行** 。

这 12 行不是敷衍，是一套完整工程哲学的压缩结果。

Matt Pocock 这套 `mattpocock/skills` （ `package.json` 里叫 `mattpocock-skills` ，版本 `1.1.0` ，MIT 协议）定位很直白：Skills for Real Engineers，给真正做工程的人用的 skill。写前端的朋友对 Pocock 这个名字不陌生，Total TypeScript 就是他做的，TS 圈里排得上号的佬；这两年转做 AI Hero，newsletter 攒了约 6 万订阅。

这个 skills 仓库 2026 年 2 月才建，到现在 4 个月出头，GitHub API 查到的 star 已经到 **160,750** ，对标的 superpowers 是 **249,522** 。

有意思的不是数字，是路线。GSD、BMAD、Spec-Kit 这类框架走的是 owning the process（接管流程），代价是把开发者的控制权收走，流程本身出了 bug 还难查。

Matt 在 README 里摆明车马走相反方向：small、easy to adapt、composable、work with any model、based on decades of engineering experience。skill 不接管流程，只在关键处给一个最小的锚点，模型自己跑。

本文走源码深度解读路线，不跑流程，不复用任何外部参考的实战案例。所有技术事实以仓库 v1.1.0 源码为准。

> **说明** ：本文基于 `mattpocock/skills` 仓库 v1.1.0 源码（ `github.com/mattpocock/skills` ）分析整理。源码行数、skill 命名、配置项、演化轨迹等技术事实均以 v1.1.0 为准，仓库后续版本可能发生变化。star 数（160,750 / 249,522）为写作时 GitHub API 查询结果，会持续变动。 **文中的架构判断和路线对比仅代表个人观察，不构成对任何一套框架的推荐或否定——选哪条路线，请结合你自己的项目特征和工程约束判断。** 如果有实际使用经验或不同观点，欢迎在评论区交流。

## 1\. 18 个 promoted skill 和一条已经改名的链路

先说清楚仓库长什么样。

`skills/` 下按 bucket 分桶， `CLAUDE.md` 里写得明白：只有 `engineering/` 和 `productivity/` 两个桶的 skill 才算 promoted，才会进 top-level README、`.claude-plugin/plugin.json` 和 docs 页面； `misc/` 、 `personal/` 、 `in-progress/` 、 `deprecated/` 都不算。

**当前 v1.1.0 合计 promoted 是 18 个** （12 个 user-invoked + 6 个 model-invoked），不是早期流传的 21。

这里有个演化点要说。半年前关于这个仓库的外部讨论写的是 21 个 skill，主流程是 `/grill-with-docs → /to-prd → /to-issues → /implement → /code-review` 。源码已经变了： `to-prd` 改名 `to-spec` ， `to-issues` 被合并进 `to-tickets` （顺手吞掉了旧的 `to-plan` ）， `wayfinder` 和 `code-review` 都是从 `in-progress` 刚毕业到 `engineering` 的， `code-review` 还改了名。

这套 skill 自己也在新陈代谢， `deprecated/` 目录是证据： `design-an-interface` 被 `codebase-design` 的 design-it-twice 吸收， `qa` 重构进 `triage` ， `request-refactor-plan` 并进 `improve-codebase-architecture` ， `ubiquitous-language` 并进 `domain-modeling` 。做得好的逻辑下沉成 model-invoked 的词汇层，冗余的合并掉。

这条路线的第一个信号就在这：skill 数量会精简，不会膨胀。和 superpowers 的 14 个 skill 合计 3300+ 行、单篇 writing-skills 689 行的体积感，是两种完全不同的假设。

## 2\. disable-model-invocation：一条约束决定整个仓库的形状

仓库只有一条切分 skill 的轴，定义在 `.agents/invocation.md` （全文 18 行）。

`user-invoked` 在 frontmatter 里写 `disable-model-invocation: true` ，description 是给人看的（剥掉 trigger list），模型永远不能自动触发它，其他 skill 也不能调用它。 `model-invoked` 是默认，description 保留 rich trigger phrasing 给模型读。

依赖关系只能单向：user-invoked 可以调 model-invoked， **user-invoked 之间不能互相调用** 。

18 个里有 **12 个是 user-invoked** 。这意味着模型能自动看到的 skill 只有 6 个，剩下 12 个的 description 根本不进模型的上下文。

装好之后敲 `/skills` 一看，列表比预期少很多，第一反应是漏装了，查一圈才发现是故意的。

隐藏是故意的。理由写在 `ask-matt` （76 行的路由中枢）的定位里：与其让 18 个 skill 全塞进模型上下文互相干扰，不如只露几个给模型，剩下的靠 router 兜底。

记不住当前情境该走哪条流，敲 `/ask-matt` ，它会告诉你。

这条约束的代价是用户得记住 12 个斜杠命令，收益是模型上下文干净、触发可预测。

对照 superpowers 的做法：用 hook 往每个会话开头注入一段指令，大意是哪怕只有 1% 的可能某个 skill 适用也必须调用，没有商量余地。一边假设模型记不住命令要 router 兜底，一边假设模型会偷懒要把退路堵死。

两种假设都成立，问题是你信哪一种。

![[Image 20.png|skill 调用拓扑示意图]]

skill 调用拓扑示意图

*图 1：user-invoked 之间相互隔离，只能单向调用 model-invoked，ask-matt 作为路由中枢*

## 3\. 四大支柱：把几本经典软件工程书压成最小锚点

README 用四段 failure mode 对应四段引言把仓库的哲学立住。这四根支柱的共同点是： **源头全在经典软件工程书上** ，不是 AI 时代的新发明。

支柱一叫 Grilling，解决 **Agent 没做我想要的** 这个失败模式。引用《Pragmatic Programmer》那句 No-one knows exactly what they want，解法是反过来让 agent 拷问开发者，一次一个问题，每个带推荐答案。

底层原语就是开头那 12 行的 `grilling` ， `grill-me` （无 codebase）和 `grill-with-docs` （有 codebase）都委托给它。

v1.1.0 打磨出一个关键区分：能查代码查到的 fact 由 agent 自己查，需要拍板的 decision 必须等用户回答。这条修复专门堵一个漏洞：agent 在 resolve-ticket 框架里自己回答自己的决策问题。

支柱二叫 CONTEXT.md，解决 **Agent 太啰嗦** 这个失败模式。agent 说话啰嗦的根源是不掌握项目黑话，用 20 个词讲 1 个词能讲完的事。

解法是在 grilling 过程中顺手编一本项目词典，落成 `CONTEXT.md` 一个文件。这套思路直接来自 Eric Evans《Domain-Driven Design》的 ubiquitous language。

仓库根目录的 `CONTEXT.md` 就是范例，定义了 Issue tracker、Issue、Triage role 三个词，还标注了 resolved ambiguities（比如 backlog 这个词以前既指工具又指工作本体，已禁用）。配套的 `domain-modeling` （74 行）是主动维护这门语言的 model-invoked skill，规则极克制：词第一次确定时才写 CONTEXT.md（懒创建），ADR 只在三条全满足时才建议（hard to reverse + 没上下文会让人困惑 + 真有 trade-off）。

支柱三叫 TDD + 反馈回路，解决 **代码不 work** 这个失败模式。 `tdd` skill 36 行，核心是红绿循环加三个反模式：Implementation-coupled（mock 内部协作者或测私有方法，一重构就挂）、Tautological（断言用代码自己的算法重算期望值，永远过）、Horizontal slicing（先写完所有测试再写实现，测的是想象的行为）。

解法是 vertical slice，即 tracer bullet 模式：一个测试接一个实现地循环。

配套的 `diagnosing-bugs` （134 行）把硬 bug 诊断拆成 6 个 phase，核心论点很硬：Phase 1 先构造一个 tight + red-capable 的反馈回路，bug 就 90% 修好了，没这个回路不准进假设阶段。

支柱四叫深模块 / 架构，解决 **代码成了泥球** 这个失败模式。引用 Kent Beck 的 Invest in the design of the system every day 和 Ousterhout 的 The best modules are deep。 `codebase-design` 是 model-invoked 的词汇层， **全仓库教科书气息浓的一篇 SKILL.md，114 行** ，定义了 Module、Interface、Implementation、Depth、Seam（引 Michael Feathers）、Adapter、Leverage、Locality 一整套术语。

一个细节值得单独说：它明确拒绝 Ousterhout 原版把 depth 定义成 `实现行数 / 接口行数` 的比值，改用 depth-as-leverage。深不是行数多，是杠杆大。

四根支柱全是老书老概念，只不过压成了 agent 能执行的最小形态。Matt 这套 skill 的功夫不在发明，在搬运——能把经典方法论压进几十行让模型照着跑，这本身是门手艺。

![[Image 21.png|四大支柱结构图]]

四大支柱结构图

*图 2：四根支柱对应四种失败模式，源头全在经典软件工程书，压成最小锚点*

## 4\. 主流程：从 idea 到 ship，ask-matt 是权威路由

把 18 个 skill 串成一条工程链，权威源是 `ask-matt` ，不是 README。主流程长这样：

`/grill-with-docs` 是有 codebase 的入口（没有 codebase 用 `/grill-me` ）。从这里判断设计问题是否需要 runnable 答案：需要就走 `/handoff` 出去跑 `/prototype` 再 `/handoff` 回来，不需要直接往下。

判断是否多 session 才能 build：是就走 `/to-spec → /to-tickets` （切成 tracer-bullet 票，带 blocking edges），每个 ticket 开新 session 跑 `/implement` ， `/implement` 内部驱动 `/tdd` ，收尾跑 `/code-review` ；否则当前 session 直接 `/implement` 。

Context hygiene 是这条链的铁律。1 到 3 步必须保持在一个未打断的 context window 里（不 compact、不清空），直到 `/to-tickets` 完成后每个 `/implement` 才开新窗口。理由是 grilling、spec、tickets 必须共享同一个思考。上限叫 **smart zone** ，大约 120k token，逼近就 `/handoff` 转新会话。

三个 on-ramp 汇入主流程。一堆 bug 和请求堆积走 `/triage` （注意 `/to-tickets` 产出的票已经 agent-ready，不能再 triage）。

东西坏了走 `/diagnosing-bugs` ，修完发现没有好的 seam 锁 bug 就 handoff 给 `/improve-codebase-architecture` 。巨大模糊的努力（greenfield 或超大 feature）走 `/wayfinder` 。

`wayfinder` 是 v1.1.0 刚毕业的重量级 skill（127 行），用了一整套游戏化隐喻。destination 是目的地，第一个动作就是命名它来固定 scope。map 是 issue tracker 上的一个 `wayfinder:map` issue，是索引不是仓库，决策只活在它的 ticket 里。

**fog of war** 是暂时无法精确表述的决策区域，写在 map 的 `## Not yet specified` 。frontier 是所有 blocker 已 close 的 open ticket，即可拿的。out of scope 是目的地之外的工作，永不 graduate。

![[Image 22.png|wayfinder 的战争迷雾隐喻图]]

wayfinder 的战争迷雾隐喻图

*图 4：wayfinder 用游戏化隐喻管理超大块工作的不确定性*

四种 ticket 类型对应四种工作模式：research（AFK，agent 后台跑）、prototype（HITL，需人在环）、grilling（HITL）、task（HITL 或 AFK， **做而非决策的类型，只有它动手干活** ）。

一条关键约束： **一个 session 只解一个 ticket** 。HITL ticket 必须 live exchange，agent 绝不能替人回答。这条约束也是修复来的，用户反馈说 wayfinder 会自己拷问自己，v1.1.0 把它焊死了。

`code-review` （89 行）的收尾设计也值得单独拆。它把评审拆成两条 **并行 sub-agent** ，避免互相污染 context。Standards 轴查是否符合仓库文档化的编码规范，外加一份固定的 Fowler smell baseline（12 个：Mysterious Name、Duplicated Code、Feature Envy、Data Clumps、Primitive Obsession、Repeated Switches、Shotgun Surgery、Divergent Change、Speculative Generality、Message Chains、Middle Man、Refused Bequest）。

Spec 轴查是否忠实实现了 originating issue / PRD。两条铁律：仓库文档标准覆盖 baseline；每个 smell 都是 judgement call 而非 hard violation。最后 **不合并 rerank** ，两条轴各自报告各自轴上的严重项。

![[Image 23.png|code-review 双轴并行示意图]]

code-review 双轴并行示意图

*图 5：Standards 和 Spec 双轴并行评审，context 隔离不互相污染*

![[Image 24.png|主流程链路图]]

主流程链路图

*图 3：从 idea 到 ship 的流水线，前三步必须保持在 smart zone 同一上下文窗口内*

## 5\. writing-great-skills：skill 写作的元方法论

整套仓库 self-aware 程度高的一块，是 `writing-great-skills` （83 行正文 + 独立 GLOSSARY.md）。它定义了一整套 skill 写作词汇，本身又是一份软件工程方法论的迁移。

核心美德叫 **predictability** ，注意不是输出一致，是过程一致。skill 的存在是为了从随机系统里 wrestle 出确定性，每一行都要为 predictability 服务。下面几个概念用得最多。

**Leading words** 指利用模型预训练知识的词，比如 fog of war、tracer bullet、prototype，这些词模型本来就有概念，skill 不用重新教，只用把它锚到具体动作上。 **Progressive disclosure** 指 skill 不把所有细节一次性塞进 frontmatter，正文按需加载。 **Completion criterion** 是每个 skill 必须定义 **什么时候算完** ，对应的反模式叫 **premature completion** （提前宣布完成）。

**Sediment** 指 skill 里那些沉淀下来、每次都执行但用户察觉不到的默认行为。 **No-op** 指故意设计成什么都不做的分支，用来兜底边界情况。 **Negation** 是一条禁令：不要写 **别想大象** 这种否定式指令，模型会先想大象。要写应该做什么，不写不该做什么。

这套词汇本身就是一份微型方法论。它解释了为什么 `grilling` 只有 12 行能工作：leading words（grill 这个词模型懂）、progressive disclosure（细节委托给上层）、明确的 completion criterion（问到设计树没有悬而未决的分支为止）。

也解释了为什么 superpowers 要写 689 行：它不信任 leading words，要把每条退路用红旗表逐条堵死。

## 6\. 一条 to-tickets 的工程权衡：什么时候不用 tracer bullet

`to-tickets` （114 行）处理 **wide refactor** 时有一个反直觉的设计。

Wide refactor 指一个机械改动但 blast radius 覆盖全代码库，比如改一列的列名。直觉做法是切片迁移，但 `to-tickets` 在这里 **不用 tracer bullet** ，改用 expand-contract：先在旧形式旁边扩出新形式（不破坏旧代码），再按 blast radius 分批迁移（每批一个 ticket，CI 全程保持绿），最后 contract 删掉旧形式。

为什么不用 tracer bullet？因为 tracer bullet 的前提是 **一个测试验证一个实现** ，而 wide refactor 的每一步都是机械替换，没有设计决策可验证。强行用 tracer bullet 会造出无意义的测试。

这个设计选择背后是一句话： **方法论是工具不是信仰** ，TDD 不是所有场景的答案。

这条取舍在 `diagnosing-bugs` 里也有呼应。Phase 1 要求构造 tight + red-capable 的反馈回路，但如果 bug 是环境问题（依赖版本、配置、权限），反馈回路本身就是问题。

这时 `diagnosing-bugs` 的 6 个 phase 会跳过假设阶段，直接走环境排查分支。skill 不是铁板一块的流程，是有分支判断的工具。

## 7\. 两条路线的克制对照

写到这该收一收。把两条路线摆在一起，尽量克制地对比。

superpowers 假设模型会偷懒、会给自己找理由，所以把每条退路堵死，靠 hook 强制触发，用红旗表逐条反驳模型的借口。代价是 token 消耗大。

HN 上有条评价说用了一周，aside from consuming a stupid amount of tokens，在所有个人 benchmark 上都明显比裸跑 Codex/Claude 差。这个评价是单一样本，但 superpowers 6 的一个主要改进方向确实是 reduce token spend。

mattpocock/skills 假设模型大概率做对，skill 只在关键处给锚点，用 router 兜底记不住命令的情况，用 disable-model-invocation 把不该自动触发的 skill 藏起来。

代价是用户得记 12 个斜杠命令，得在 grilling 阶段真坐下来逐个拍板决策而不是把活儿甩给 agent 跑几小时。社区目前没看到对这套仓库本身的负面评价，要么推荐要么拿它做对照基准。

选哪条，取决于你的项目值不值得每个决策都过自己的手。

如果你的活儿是机械替换、批量生成、跑通就行，superpowers 的流程接管更省心；如果你的活儿每个决策都有长期后果、错了难回滚，Matt 这套的握方向盘手感更实在。

两条路线能共存。Matt 这套大部分命令不参与自动触发，superpowers 的 hook 只强推自己那 14 个，相互之间不冲突。

两个都留着，同一条链各跑一遍，看交付质量和效果差在哪，这大概是当前够诚实的对比方式。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录