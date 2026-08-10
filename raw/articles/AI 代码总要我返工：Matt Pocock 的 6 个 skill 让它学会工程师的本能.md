---
title: "AI 代码总要我返工：Matt Pocock 的 6 个 skill 让它学会工程师的本能"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491433&idx=1&sn=10ee5393ccab9ea5a4105082823027db&chksm=cf43aa3ff8342329cac2f4f38c3ef44f908dbcb605b1f39f682f221c15251b5a1a0d4815cb73&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-04
description:
tags:
---
运维有术 术哥无界 *2026年7月17日 07:38*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *169* 篇，AI 编程最佳实战「2026」系列第 *55* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

![[Image 56.webp|封面图：Matt Pocock 的 6 个 skill 把工程纪律补回 AI 编程]]

封面图：Matt Pocock 的 6 个 skill 把工程纪律补回 AI 编程

Matt Pocock 把他的 skills 仓库定位为 **Skills For Real Engineers** ，README 开篇就说："My agent skills that I use every day to do real engineering - not vibe coding."（ `README.md:11-19` ）。

如果只看名字，这只是一个挂在 GitHub 上的 Claude Code 插件集合。

但翻完所有 promoted skill 之后，我更愿意把它读成一份工程纪律的样张：作者把软件工程里那些"早该做但没人真做"的检查点，写成了 agent 能反复跑的 workflow artifact。

这不是一个新工具，但它值得被读一遍。下面是 6 个我读完最想抄走纪律的工程类 skill，按"它管什么 - 它的核心词 - 它点名要避开的失败 - 你能立刻抄走的动作"四个角度展开。

`writing-great-skills` 不属于 engineering bucket，它是 promoted 的 `productivity/` 成员，但因为它定义了整套共享词汇，我把它放在第一个。

## 1\. 这套 skills 在解决什么

仓库 README 不从安装教程开始，而是先列出四类 agent 常见失败（ `README.md:69-167` ）：

- **意图错位** ：开发者以为需求已经说清，看到结果才发现 agent 理解错了。
- **语言冗长** ：agent 进入项目后缺少共享语言，只能用许多词绕着描述。
- **代码不能工作** ：agent 缺少能看到真实运行结果的反馈回路。
- **代码变成 ball of mud** ：agent 加速写代码，也会加速软件熵。

读完之后我的判断是：仓库真正要说的是 \*\*"Software engineering fundamentals matter more than ever"\*\*，而不是"更聪明地提示 AI"（ `README.md:165-167` ）。

Skill 是这些 fundamentals 的可执行载体：每个 skill 都给自己设定一个不可跳过的门，并指定门没通过时禁止进入下一步。

下面六个 skill 就是这套思路的样本。

![[Image 57.webp|图 1：四类 agent 失败模式——意图错位、语言冗长、代码不能工作、ball of mud]]

图 1：四类 agent 失败模式——意图错位、语言冗长、代码不能工作、ball of mud

## 2\. writing-great-skills：从一组随机过程里挤出确定性

> 位于 `skills/productivity/writing-great-skills/` ，不是 engineering bucket。

这个 skill 是其余所有 skill 的元规范。

它给出一句话定义（AI Hero 写作 great-skills 页面与仓库 SKILL.md 同义收录，加粗部分为页面表述）：\*\*A skill's job is to wrangle determinism out of a stochastic system. The goal is not the same output on every run, but the same process.\*\*（ `aihero.dev/skills-writing-great-skills` ； `writing-great-skills/SKILL.md:7-9` 同义）

所谓确定性，是每次跑过同一组检查点——产出是不是完全一样反而是次要的。 `Predictability` 被列为根本品质，cleverness、completeness、exhaustiveness 都排在它之后（ `GLOSSARY.md:9-13` ）。

### 一个被反复复用的核心词：leading word

`Leading word` 指的是已经存在于模型预训练中的紧凑概念。

它以很少的 token 调动模型既有先验，在 SKILL 正文里锚定执行，在 description 里锚定调用（ `writing-great-skills/SKILL.md:61-72` ； `GLOSSARY.md:129-135` ）。

原文举的两个例子很值得抄：

- 把"fast, deterministic, low-overhead"折叠成 **tight** 。
- 把"a loop you believe in"折叠成可观测的 **red** 。

一个 `tight` 顶得上一段三行的说明，因为模型在 pretraining 里已经见过这个词承载过的工程语义。

### 它点名的失败模式

同一节列出了六种典型失败模式：duplication、sediment、sprawl、negation、premature completion、no-op。

后两条尤其值得单独记下来（ `writing-great-skills/SKILL.md:74-83` ； `GLOSSARY.md:155-201` ）：

- **Premature completion** ：当前步骤还没真正完成，注意力已经滑向下一步。读代码时很常见的现象：commit 一发就觉得修完了。
- **No-op** ：指令没改变模型相对默认行为。花了上下文，却没有带来行为差异。

### 你能立刻抄走的动作

挑一条团队 wiki、issue 模板或 PR 描述里常写的模糊要求，例如 **认真排查** 、 **保持测试稳定** 。问自己两个问题：

1. 它能不能换成一个模型已有先验的 leading word？比如 `tight` 、 `red` 、 `tracer bullet` 。
2. 它有没有可检查、尽可能穷尽的 completion criterion？

这不是 agent skill 才用得上的标准。Issue 模板、PR checklist、runbook 都吃这套：把形容词换成 leading word，把"完成感"换成验收条件。

## 3\. codebase-design：seam 是可替换的位置，深度是可压缩的行为

> 位于 `skills/engineering/codebase-design/` 。

这个 skill 围绕两个词展开： **deep module** 和 **seam** 。

- **Depth** ：调用者每学习一单位 interface，能获得多少行为。深模块用小 interface 提供大行为；浅模块的 interface 与 implementation 几乎一样复杂（ `codebase-design/SKILL.md:14-20` ）。
- **Seam** （引用 Michael Feathers）：无需在该位置直接编辑，就能改变行为的地方。seam 是 interface 所在的位置，放在哪里与放什么进去是两个不同决策（ `codebase-design/SKILL.md:20-24` ）。
- **Leverage** 面向调用者， **locality** 面向维护者（ `codebase-design/SKILL.md:24-28` ）。

### 它点名的失败模式

- **Shallow module** ：interface 与 implementation 几乎一样复杂，只是 pass-through（ `codebase-design/SKILL.md:30-52` ）。
- **Hypothetical seam** ：只有一个 adapter 就提前引入抽象。原文给出的判定标准很硬： **One adapter means a hypothetical seam. Two adapters means a real one** （ `codebase-design/SKILL.md:60-65` ）。

### 你能立刻抄走的动作

对最近频繁修改的一个模块做一次 **deletion test** ：想象把它删掉。如果复杂度只是散回 N 个调用点，它大概率真的提供了 locality；如果复杂度随它一起消失，它基本就是 pass-through（ `codebase-design/SKILL.md:62-64` ）。

再问一个相关问题：测试能不能只通过 interface 完成？如果必须越过 interface 测内部实现，模块形状可能就不对（ `codebase-design/SKILL.md:64-65` ）。这个检查放进 PR 描述或架构评审的 checklist 里很合适。

![[Image 58.webp|图 2：deep module 用小 interface 提供大行为；seam 是接口所在的可替换位置]]

图 2：deep module 用小 interface 提供大行为；seam 是接口所在的可替换位置

## 4\. tdd：先把 seam 谈妥，再开枪

> 位于 `skills/engineering/tdd/` 。

这个 skill 把 TDD 收得很窄，只剩一个动作： **red → green loop** ，并且要求测试必须验证 public interface 上的行为，不是实现细节（ `tdd/SKILL.md:6-16` ）。

它的两个核心词：

- **Pre-agreed seam** ：写测试前先写下要测的 seam 并与用户确认，避免把测试预算平均撒在所有内部边缘（ `tdd/SKILL.md:18-24` ）。
- **Tracer bullet** ：垂直切片里的每一个测试是一条 tracer bullet，一条测试、一个最小实现，让上一轮学到的东西影响下一轮（ `tdd/SKILL.md:26-35` ）。

### 它点名的失败模式

- **Implementation-coupled** ：mock 内部协作者、测试私有方法，重构未改变行为却导致测试失败（ `tdd/SKILL.md:26-29` ）。
- **Tautological** ：断言用与实现相同的方法重新计算预期值，天然无法与实现产生分歧（ `tdd/SKILL.md:28-30` ）。
- **Horizontal slicing** ：先写完所有测试，再写全部实现，测试的是想象出来的形状而非真实行为（ `tdd/SKILL.md:29-30` ）。

horizontal slicing 是我读到这里最想提醒同事的一种失败：看起来"很 TDD"，其实是在对一个还没存在的实现下注。

### 你能立刻抄走的动作

下一个 feature 开工前，不要先列测试文件，而是写一句："public interface 是什么？这次只确认哪一个 seam？"

然后只做一轮：一个会红的行为测试 → 够它变绿的最小实现。 **禁止预判下一轮需求** （ `tdd/SKILL.md:32-36` ）。这是 `tracer bullet` 一词的字面要求：每一轮必须靠真实反馈驱动，而不是靠想象。

## 5\. diagnosing-bugs：没有 red-capable loop，就没有诊断

> 位于 `skills/engineering/diagnosing-bugs/` 。

AI Hero 站点在 diagnosing-bugs 页面把这一句概括为 **The tight loop is the skill** ——SKILL.md 第 12 行用的是更朴素的 **This is the skill** 。

只要存在一个针对该 bug 的 tight pass/fail signal，二分、假设和 instrumentation 都只是消费这个信号。

没有它，盯代码解决不了问题（ `diagnosing-bugs/SKILL.md:12-16` ； `aihero.dev/skills-diagnosing-bugs` 页面小节标题）。

原文把第一阶段定义得非常严格：必须产出一个 **已经至少运行过一次的命令** ，并同时满足 red-capable、deterministic、fast、agent-runnable。命令不存在，就不能进入假设阶段（ `diagnosing-bugs/SKILL.md:47-60` ）。

### 它点名的失败模式

- **Jumping straight to a hypothesis** ：还没构造出能准确捕获用户症状的 red-capable command，就开读代码建理论。原文直接称这是该 skill 要防止的 **exact failure** （ `diagnosing-bugs/SKILL.md:51-60` ）。
- **Single-hypothesis anchoring** ：只生成一个假设，会被第一个看似合理的解释锚定。原文要求先生成 **3-5 个排序且可证伪的假设** （ `diagnosing-bugs/SKILL.md:82-92` ）。
- **Wrong bug = wrong fix** ：反馈回路捕获的是附近另一个失败，而不是用户报告的精确症状（ `diagnosing-bugs/SKILL.md:62-80` ）。

### 你能立刻抄走的动作

面对下一个 bug，先在 issue 顶部留一个字段： `One command:`。在这里放入能捕获精确症状的测试、curl、CLI、回放脚本或最小 harness，并实际运行一次。

命令不存在时，禁止填写"根因猜测"。这一条可以直接复制进团队的 bug report 模板，比让值班同学自由发挥要可控得多。

![[Image 59.webp|图 3：诊断门控——没有 red-capable command 就不能进入假设与 instrumentation]]

图 3：诊断门控——没有 red-capable command 就不能进入假设与 instrumentation

## 6\. improve-codebase-architecture：先扫热路径，再选 deepening opportunity

> 位于 `skills/engineering/improve-codebase-architecture/` 。

这个 skill 用的是 `codebase-design` 的严格词汇，但目标不同：把 shallow module 变成 deep module。

它给这种重构机会起了一个专门名字： **deepening opportunity** ，目标是 testability 与 AI-navigability（ `improve-codebase-architecture/SKILL.md:7-14` ）。

它要求复用 `codebase-design` 的词汇：module、interface、depth、seam、adapter、leverage、locality。不允许漂移回 component、service、API、boundary 之类泛词。

### 它点名的失败模式

- **Rigid heuristics** ：原文要求 **organically explore** ，不用刚性静态规则假装在做架构判断（ `improve-codebase-architecture/SKILL.md:27-35` ）。
- **Premature interface design** ：报告阶段只展示候选、问题、收益和 before/after，不立刻设计 interface。要先让用户选择，再进入 grilling loop（ `improve-codebase-architecture/SKILL.md:37-64` ）。
- **Generic architecture language** ：HTML 报告要求每条收益必须回到 locality / leverage，拒绝用 **cleaner code** 、 **easier to maintain** 等没有定义的泛词（ `HTML-REPORT.md:106-123` ）。

### 你能立刻抄走的动作

从最近 20-50 个 commit 中找一个反复变化的区域，只做一张 **deepening candidate** 卡片：Files、Problem、Solution、Locality/Leverage、Before/After。

先不要写 interface，也不要马上改代码。先让团队选"这是不是现在最值得处理的摩擦"。这一步比直接动手重要的多，因为它把"我们现在最痛的是什么"这个判断权交回给人。

## 7\. prototype：问题决定形状，不是反过来

> 位于 `skills/engineering/prototype/` 。

最后看 prototype。它给的定义非常窄： **throwaway code that answers a question** ，问题决定原型形状（ `prototype/SKILL.md:6-15` ）。

逻辑或状态问题做可交互终端应用；UI 问题做多个差异足够大的变体。分支选错会浪费整个原型（ `prototype/SKILL.md:10-17` ）。

### 它点名的失败模式

- **把原型做成半成品生产代码** ：从第一天起就要明确标记 throwaway，不做默认持久化，不写测试，不补与可运行无关的错误处理，也不做抽象（ `prototype/SKILL.md:19-25` ）。
- **Question/branch mismatch** ：没先明确"要回答哪个问题"，就选了错误的逻辑或 UI 产物。

### 你能立刻抄走的动作

在原型目录或 issue 顶部写两行：

- `Question:` 这个原型只回答什么？
- `Run:` 用哪一个命令启动？

完成后把已验证的决定迁入正式代码，把原型作为 primary source 放到 throwaway branch，主分支只保留最终决定（ `prototype/SKILL.md:19-26` ）。

这两行比"先写个 POC 看看"这种口头约定强很多。Question/Run 写成团队约定后，下次有人要 demo 一个新方案，至少先写清楚他打算验证什么。

## 8\. 把六个 skill 放在一起看

这六个 skill 单独读是各自的工作流，放在一起读能看出四种共同模式。

**1\. 用 completion criterion 卡门，不是用形容词劝告。** debugging 要求一个已经跑过的 red-capable command，TDD 要求预先确认 seam，prototype 要求写 Question/Run。它们在流程节点上设置能检查的门，而不是写一段 **请认真一点** 。

这种写法对 agent 重要，对人也重要：人写得越像 **认真** 、 **稳定** 、 **清晰** ，团队就越难复盘到底做到了没有。

**2\. 用共享词汇压缩决策空间。** `tight` 、 `red` 、 `tracer bullet` 、 `seam` 、 `deepening opportunity` 都不是装饰术语。每个词压缩的是一组行为，能让团队和 agent 用一个词完成对齐。

`writing-great-skills` 反复强调这一点，是因为这些词的力量来自它们已经在模型 pretraining 里占有语义：换成一个团队自造的合成词，力量立刻打折扣。

**3\. 把失败模式直接写进流程。** 仓库不是只描述理想路径，还点名 `premature completion` 、 `no-op` 、 `shallow module` 、 `horizontal slicing` 、 `jumping straight to a hypothesis` 等偏差，并为每种偏差设置对应动作。

这与 README 里那句 `Software engineering fundamentals matter more than ever` 直接呼应（ `README.md:165-167` ）。把失败模式写进流程，等于把团队的口头"我们应该……"变成可触发的判定条件。

**4\. 人的控制权没有被吞掉。** pre-agreed seam 要求先与用户确认、debugging 要求先有可运行的 red-capable command、architecture 要求先展示候选再让用户选择、user/model-invoked 区分要求显式选择触发方式。

流程越严格，人的低成本决策点反而越多。

这与 GSD、BMAD、Spec-Kit 等"owning the process"的方法形成对比：后者把流程交给一个 harness，前者把流程做成可审阅的 Markdown。

把这四点压成一句话：这些 skill 把工程纪律做成可审阅、可 fork、可在 PR 上讨论的 workflow artifact，而不让 agent 替你全自动跑工程。

仓库 README 直接点出了这一立场："These skills are designed to be small, easy to adapt, and composable"（ `README.md:14-19` ）。

附带一个维护层面的观察：仓库的 v1.1 变更说明明确写出了三类由用户报告推动的修改，包括 **模型一次问多个问题** 、 **grilling 结束后未经确认直接实施** 、 **模型自己探索并自我 grilling** ——分别对应 **单问题指导、实施前确认门、区分 Facts 和 Decisions** 三类修复（ `research-web-01.md` 第 1.3 节）。

这些 workflow artifact 按真实使用反馈迭代，并不一锤定音。

这与上面四种共同模式是同一个思路的不同切面：纪律要可被修改，才值得被遵守。

![[Image 27.png|图 4：六个 skill 的核心词、点名失败模式、可抄动作汇总]]

图 4：六个 skill 的核心词、点名失败模式、可抄动作汇总

## 9\. 下班前能做的三件小事

文章不打算收在"AI 时代程序员应当如何转型"这种拔高结论上。比起抽象口号，下面三件小事更值得在今天之内做掉。

**1\. 在 bug issue 模板里加一行 `One command:`。** 抄自 diagnosing-bugs。下一次有人报 bug，让他先填这一行；填不出来就先一起构造 red-capable command，再聊根因。这一行的成本是改一次模板，收益是少开几次"我觉得大概是 X"的会议。

**2\. 选一个最近 20-50 个 commit 里反复改动的模块，跑一次 deletion test。** 抄自 codebase-design + improve-codebase-architecture。

删得掉 vs 散得开，结论会很不一样；测试能不能只走 interface 也是一个独立信号。

如果团队不熟 deletion test，可以先从最不重要的工具模块做起——失败成本低，方法可以练手。

**3\. 在 PR 模板或 demo 文档里加两行 `Question:` 和 `Run:`。** 抄自 prototype。任何原型/POC 在写代码前先回答这两个字段，能挡掉很多"先做做看"的项目。

如果想再保守一点，可以再加一行 `Throwaway? Y/N` ，对应 `prototype/SKILL.md:19-25` 的硬性要求。

这三件事都不需要装任何 skill，不需要换 IDE，也不需要会写 agent prompt。它们抄走的是这套仓库的最小可执行单位：词、门、动作。等团队用熟这三件事，再回头读 `writing-great-skills` 里关于 leading word 的章节，会有完全不同的体感。

> **说明** ：本文内容基于 Matt Pocock 在 GitHub 上开源的 `skills` 仓库（ `mattpocock/skills` 和 AI Hero 官方页面 `aihero.dev` 的同源介绍整理而成。文中六个 promoted skill 的 SKILL.md 正文、bucket 分类、调用模型与维护约束均来自仓库 `engineering/` 与 `productivity/` 下的原始文件，未把任何仓库的二次解读当事实依据。 **文中的概念拆解、方法论迁移建议和 workflow 改造方案仅供参考，实际落地请以你团队当前的语言、测试与代码评审习惯为准。** 任何不准确或过时的细节，欢迎在评论区指出，我会回到原仓库核对修正。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录