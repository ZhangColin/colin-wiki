---
title: "从一句\"认真 X\"到可执行 Agent Skill：Matt Pocock 的 8 个词帮你砍掉 prompt 里的 no-op"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491506&idx=1&sn=cbfadaa25b67ebc2d27638e6939a17da&chksm=cf43aae4f83423f20ffcbc361be0592c001aeb62dfcdb4dbd492cc3dcb3e8a7fc7816617d9ab&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-06
description:
tags:
---
运维有术 术哥无界 *2026年7月24日 08:30*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *176* 篇，AI 编程最佳实战「2026」系列第 *60* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

![[Image 30.png|封面图：从口号到 Agent Skill 的转换、信息图封面]]

封面图：从口号到 Agent Skill 的转换、信息图封面

> 这篇文章面向已经在写 Agent prompt / system instruction / skill 的开发者。读完你应该能：带着 8 个词、1 个最小模板、1 张判断表，回去对自己的 prompt 跑一遍诊断。

最近我在 Matt Pocock 的 skills 仓库里翻 `writing-great-skills` ，最直接的感受是：他那一套词汇体系不是用来背的，是用来诊断自己写的 skill 的。每一行都在回答一个问题——这条规则，会不会本来就该被删掉。

`writing-great-skills/SKILL.md` 那 83 行不是方法论手册，是一份去除手术清单。配合 `GLOSSARY.md` ，它把所有症状归到四个轴：Invocation（怎么触发）、Information Hierarchy（怎么编排）、Steering（怎么塑形）、Pruning（怎么精简）。每个症状都配了 cure。

我接下来会做三件事。先用一句团队口号改造成最小可执行 skill——这是最核心的可执行产物；再解释 8 个词，每个词配上 **在我的 prompt 里怎么检查它** ；最后给一张判断表——什么时候拆分、什么时候删除、什么时候推出去。

说一下边界。本文不展开 Superpowers 6.0 的微测试实验，不重复 writing-skills 方法论，也不展开 Matt Pocock 6 个 skill 的整体介绍——这三个都有专题。但所有引用都会带 `文件:行号` ，方便你自己去查。

## 1\. 为什么认真 X不是 skill

很多团队的 wiki 里都有这种话：

> "做 code review 的时候要认真一点。" "写测试要尽量覆盖全。" "重构的时候小心一点。"

把这类话直接照搬到 skill 里会发生什么？模型读了，模型点了头，然后继续按默认行为跑。

`SKILL.md:82` 把这种行称为 **no-op** ——一个并不能改变模型默认行为的指令。 `'be thorough'` 在模型已经足够 thorough 的情况下就是 no-op。

一行 no-op 看起来无害，但它挤占三样资源：description 的 context load、正文的注意力带宽、维护者的判断力。你以为是规则，其实是噪声。

什么样的口号值得改？我自己的判断标准是看两个信号：

- 团队里反复在说同样的口号（说明它背后有真实痛点）
- 同一个痛点反复出现（说明 model 默认行为没解决）

满足这两点，说明口号背后的 **行为缺口** 是实在的。剩下的就是把它从一行字扩成一个可执行单元。

![[Image 77.webp|模糊口号与 tight-review Skill 对比]]

模糊口号与 tight-review Skill 对比

## 2\. 把认真 review改造成 skill：完整示例

我故意拿 code review 做例子，因为它的反面例子最多：每个人都说要 review 好，但没几个人能说清楚什么叫 **review 好** 。

### 改造前

```
# Code Review

做 review 的时候要认真一点。
```

这种 skill 看起来 **有** ，但其实等于 **没有** ——它没有触发条件（什么时候必须跑）、没有步骤（怎么 review）、没有验收（什么叫 review 完）、没有失败处理（漏掉了怎么办）。

### 改造后

```
---
name: tight-review
description: "Run a tight code review on staged changes. Use when the user says 'review this' or has staged but uncommitted changes."
---

# Tight Code Review

### Phase 1 — Get the diff

- Run \`git diff --staged\` (or against the base branch if pushing).
- If empty, stop: "No changes to review."

### Phase 2 — Tight loop per change

For each changed file:

1. **Read the diff hunks once** — understand intent before commenting.
2. **Check three things, exhaustively**:
   - Does the change match the spec / ticket / stated goal?
   - Does it preserve the existing public interface (no breaking change without ADR)?
   - Does it introduce a \`TODO\` or commented-out code without a linked issue?
3. **Per check, write a verdict**: \`pass\` | \`fix-needed: <one-line reason>\` | \`discuss: <question>\`.

### Phase 3 — Completion

- Done when **every changed file has a verdict for all three checks**.
- If any verdict is \`fix-needed\` or \`discuss\`, return to the author with the list — do not approve.
```

### 前后对比

| 维度 | 改造前 | 改造后 |
| --- | --- | --- |
| 触发条件 | 无 | description 里写明（ `review this` 或 staged changes） |
| Leading word | **认真**  （模糊） | `tight`  （ `SKILL.md:69` 的范例：把 `fast, deterministic, low-overhead` 折叠成 `tight` ） |
| Completion criterion | 无 | 每个 phase 都有 `done when...` |
| Branching | 无 | Phase 2 是 **per-file 循环** |
| 失败处理 | 无 | Phase 3 的不通过条件 |
| 长度 | 5 行 | 30 行主体，无冗余 |

### 拆解一下

**Trigger 写在 description 里** 。这是 `SKILL.md:24-28` 的明确建议：description 同时做两件事—— **这是什么** 和 **什么分支会触发** 。 `tight-review` 的 description 用 `'review this'` 和 `staged but uncommitted changes` 钉死了触发分支，避免 description 自己就变成另一句口号。

**Leading word 替换模糊形容词** 。 `tight` 来自 `SKILL.md:69` 的范例——把 `fast, deterministic, low-overhead` 这种三联描述折叠成一个 pretrained 词。模型在 pretraining 里见过 `tight` 大量技术语境，知道它意味着 **无冗余、边界清晰、不放水** 。你不需要自己定义这个语义。

注意：原作者在 `SKILL.md:67-72` 列了一组 leading word 例子，本文没有把这些效果写成 **已验证的性能提升数据** ——它只是词汇替换的范例，验证它是否真能提升模型行为需要单独的实验，所以这里我只把它当 **词汇学方法论** 使用。

**每个 phase 都带 done when** 。这是 `GLOSSARY.md:137-141` 里 **completion criterion** 的两点之一——clarity（模型能不能分辨 done / not-done）。 `tight-review` 里 `every changed file has a verdict for all three checks` 是 checkable 的，因为模型可以遍历所有文件并统计 verdict。

**Branch 写在最前面** 。 `prototype` 的也是同样做法（ `prototype/SKILL.md:11-17` ）：在动手前先选 LOGIC 分支还是 UI 分支。 `tight-review` 的 Phase 2 是 **per-file 分支** ，每个 file 独立走完三件事。

**失败处理就是第三条指令** 。 `SKILL.md:78` 提到，模糊的 completion criterion 会让模型提前滑向 **完成** ——所以 `tight-review` 把 **不通过怎么办** 明文写出来（ `return to author with the list` ），不给模型发散空间。

## 3\. Skill 最小模板：下次直接套

把上面那个例子抽象出来，下次写任何 skill 都可以套这个骨架：

```
---
name: <skill-name>
description: "<一句话定义>. Use when <trigger 1>, <trigger 2>, or has <state 1>."
---

# <Skill Name>

![Agent Skill 八个诊断词检查轮](https://shugex-1258881081.cos.ap-beijing.myqcloud.com/images/2026072322/03-eight-word-diagnostic.png)

### Phase 1 — <动作>

- <具体动作>
- <如果某种情况，stop: "..." >

### Phase 2 — <动作>

For each <unit>:

1. <动作 1>
2. <动作 2：必查几件事>
3. <每件事输出一个 verdict>

### Phase 3 — Completion

- Done when **<可检查的、不变量>**。
- If <不通过条件>, <必须怎么做的指令>.
```

几个要点：

- description 里至少给 2 个 trigger 短语，方便模型匹配
- 至少有一个 Phase 带 **per- 循环**
- Completion criterion 用可数名词（ `every ...`, `every ...`, `exactly N ...`）而不是 **大致** 、 **尽量** 这类模糊词
- 失败处理用祈使句（ `return to author`, `stop`, `do not approve` ），别用 **建议**

## 4\. 8 个词汇：每个词配在我的 prompt 里怎么检查

下面是 `writing-great-skills/GLOSSARY.md` 抽出来的 8 个核心词。每个词讲三块内容：源码定义、在 prompt 里怎么检查（具体动作），以及我自己迁移到团队 wiki 时的建议。

来源标记规则：

- **【源码】** — 来自 `GLOSSARY.md` 或 `SKILL.md` 的原始定义
- **【分析】** — 基于源码做的逻辑推论
- **【迁移】** — 我个人的迁移建议，团队 wiki 里可以照搬

### 1\. Leading word

**【源码】** `GLOSSARY.md:129-135` ：一个 pretrained 词，靠 repetition 累积意义，单 token 锚定一个行为区域。 `GLOSSARY.md:131` 给的范例是 `lesson` 、 `proximal zone of development` 、 `fog of war` 、 `tracer bullets` ； `SKILL.md:69-72` 进一步给了 `tight` 、 `red` 这类作者自己造的范例（属于 **自造词要先定义清楚** 的特例）。

**【分析】** 作者在 `SKILL.md:69-72` 给的范例是 `fast, deterministic, low-overhead → tight` 和 `a loop you believe in → red` 。前者是 quality 折叠，后者是 gate 折叠（布尔可观测量）。

**【检查】** 把你的 prompt 里所有形容词挑出来，逐个问：

- 这个词是不是模型预训练里出现过的词？
- 同样的意思，是不是在 prompt 里出现了 3 次以上（比如 **认真** 、 **严密** 、 **不放过** ）？

如果两个问题都是 yes，把它们折叠成一个 pretrained 词。

**【迁移】** 团队 wiki 里可以用同样的 no-op test（见下文）来验证 leading word 是否有效——因为它是 model-relative，不是 reader-relative。

### 2\. Completion criterion

**【源码】** `GLOSSARY.md:137-141` ：一个 step 的 done 条件。两个 axis：

- **clarity** （能不能分辨 done / not-done）—— 抗 premature completion
- **demand** （要求多深）—— 决定 legwork 厚度

**【分析】** clarity 是 step-bound 的（需要 steps 才会咬），demand 不是（flat reference 上的 `every rule applied` 也能咬）。

**【检查】** 把每个 step 抽出来，看 done 条件：

- 它能不能被一个独立语句验证？（ `every X has Y` 可以， **理解 Y** 不行）
- 有没有出现 **大致 / 尽量 / 差不多** 这类模糊词？

模糊就 sharpen 一下。 **理解需求** 换成 **列出至少 3 个未决问题** 。

**【迁移】** 团队 wiki 里提 **验收标准** 时，文档里返回 `tight-review` 这一个例子，把 `done when` 模板搬过去。

### 3\. No-op

**【源码】** `SKILL.md:82` 、 `GLOSSARY.md:195-200` ：模型默认就会做的事，写了也是白写。

**【分析】** 判定方法是问 **vs default，这条有没有改变行为？** no-op 可以是 relevant 但无力——它跟这条规则有没有用无关，跟 **模型本身就在这样做** 有关。

**【检查】** 把 prompt 当作一段指令，删掉它，观察模型行为变化。如果删完没区别——no-op，删掉。注意：这是模型相对的概念，不是读者相对的。两个人看着同一行字吵架，跑一下就知道。

**【迁移】** 团队 wiki 里做 prompt audit 时，把 **测试过删除后是否改变行为** 作为唯一裁剪标准，比 **我认为这条有用** 靠谱。

### 4\. Duplication

**【源码】** `GLOSSARY.md:177-181` ：同一意思在多处出现。后果：维护难、token 浪费、权重被拉高。

**【分析】** `SKILL.md:79` 指出，重复一个 meaning 还会拉高它的 ladder 排名，让它看起来比实际重要。这是 accidental inverse of leading word。

**【检查】** 把 prompt/description 全文搜索某个意思，看是否在多个地方重写。如果是——折叠成一个 leading word。

**【迁移】** 描述里有这种典型场景：同一分支写两遍（"build features using TDD … asks for test-first development"）。 `SKILL.md:27` 把这种称为一分支两次。重写 triggers 列表时反复对自己说。

### 5\. Sediment

**【源码】** `GLOSSARY.md:189-193` ：失效内容堆积。 `SKILL.md:80` 说这是没有 pruning discipline 的 skill 的默认命运。

**【分析】** 和 duplication 的区别：duplication 是长度来自重复含义，sediment 是长度来自陈旧堆积（不是同一意思的多份）。

**【检查】** 跑一个 relevance audit：每行字写下它当前还在描述哪个行为。如果 5 秒内想不到——删了。

**【迁移】** 团队 skill 库应该每季度做一次 relevance audit。wiki 里贴一句 **每条规则问自己：现在还在管用吗？**

### 6\. Sprawl

**【源码】** `GLOSSARY.md:113-117` ：即使每行都活、每行都 unique，skill 还是太长。cure 是 progressive disclosure。

**【分析】** `SKILL.md:40` 的张力说得很清楚：push too little → 顶层臃肿；push too much → 隐藏了 agent 实际需要的材料。

**【检查】** 看 SKILL.md 行数。如果超过 100 行且还在涨，先看是否还能 progressive disclosure（参考下一节）。

**【迁移】** 团队 wiki 里写 **操作说明** 时，正文只保留主干，相关的检查表、规则库放到附件或链接页。

### 7\. Premature completion

**【源码】** `SKILL.md:78` 、 `GLOSSARY.md:155-159` ：当前 step 没真完成就滑向下一步。

**【分析】** `GLOSSARY.md:157` 给出两个 cure 的先后顺序：先 sharpen completion criterion（便宜、局部），不行再 hide post-completion steps（把后续步骤从视野里挪走，用 sequence split）。注意：hide 必须在真实 context boundary 上才有效（user-invoked hand-off 或 subagent dispatch），inline model-invoked call 是无效的。

**【检查】** 跑一下你的 skill，看模型是不是经常 **提前结束** 。如果有——先 sharpen criterion；如果 criterion 已经是 sharp 的，但模型还是提早——再考虑 sequence split。

**【迁移】** 团队做 code review skill 时，最容易出现 premature completion——模型看 3 个文件就准备收工。用 `tight-review` 的 `every changed file has a verdict` 作为 criterion，比 **基本看完了** 强。

### 8\. Negation

**【源码】** `SKILL.md:83` 、 `GLOSSARY.md:161-165` ：禁止式指令把被禁的行为召进上下文。"Don't think of an elephant" 就是这个原理。

**【分析】** `GLOSSARY.md:163` 给出 cure：写正面目标，让被禁的行为根本不出现。禁止式只在必须保留硬护栏时再用，且要配正面目标。

**【检查】** 在 prompt 里搜 `不要` 、 `别` 、 `never` 、 `禁止` 。每搜到一个问： **能不能改成正面目标？** 改得了就改。

**【迁移】** 团队 wiki 里写 **代码规范** 段落，常见的反例是 **不要写长函数** ——改成 **函数不超过 30 行** 更有效。

## 5\. Information Hierarchy：什么放正文、什么推 reference、什么时候拆

`SKILL.md:32-42` 给了一个三档阶梯：

1. **In-skill step** — 主体里的有序动作，每个都带 completion criterion
2. **In-skill reference** — 主体里的定义、规则、事实，可有可无
3. **External reference** — 推到外部文件，由 context pointer 触发加载

`tdd` 这个 skill 给了一个完整示范（ `tdd/SKILL.md:8-36` ）：

- 正文的 **Anti-patterns** 是 in-skill reference（peer set）
- 正文的 **Rules of the loop** 是 in-skill steps
- `tests.md` 和 `mocking.md` 是 external reference（带 context pointer）

判断标准只有一条：\*\* `SKILL.md:40` 那句张力\*\*——push too little → 顶层臃肿；push too much → 隐藏了 agent 实际需要的信息。

具体到我自己的判断：

- 每个分支都会读到的内容 → 留在正文
- 只有特定分支读到的 → 推外部 reference
- 永远在另一个 skill 里复用 → 推 external reference（GLOSSARY.md 里那种）
- 表格、清单、查得到的规范 → 推外部 reference

另一个相关词是 **branch** ： `classic skill` 里分支写在最前面（ `prototype/SKILL.md:11-17` 是范例， `LOGIC.md` vs `UI.md` ），让模型在动手前先选。 `SKILL.md:42` 给了 progressive disclosure 的依据：分支是 disclosure 的天然测试点。

## 6\. Invocation 决策：model-invoked 还是 user-invoked

`SKILL.md:13-18` 、README.md:171-209 都强调同一个选择：每个 skill 都要决定 model-invoked 还是 user-invoked。

两者资源消耗相反：

| 维度 | model-invoked | user-invoked |
| --- | --- | --- |
| 触发 | agent 自动 + 人工 | 仅人工 |
| description | 保留（带 trigger 关键词） | 移除 |
| Context load | 永远占 | 零 |
| Cognitive load | 零 | 人工负担 |
| frontmatter | 默认 | `disable-model-invocation: true` |

判断标准（ `SKILL.md:18` ）：只有 agent 必须自己 reach 时才用 model-invoked；否则 user-invoked。

**边界说明** ： `disable-model-invocation` 这个 frontmatter 字段并不是所有 Agent 平台都支持。Claude Code 是支持原文的，但 Codex、Cursor、Continue 等平台对 frontmatter 的支持程度不同（有的用一个等价字段，有的完全读取不到）。所以这段规则不应该写成 **必须如何** ——更稳妥的说法是 **先看 README 里的 frontmatter 说明** 。本文不重复 platform-specific 细节，因为它们每个季度都在迭代。

几个具体例子：

- `tight-review` （我举的例子）—— user-invoked。你不会希望模型在你每次提交时自动 **review 自己** —— review 必须你自己决定开始。注：description 里保留 `"review this"` 触发短语只是为了让用户自己想起来它存在，不是给 agent 看的（user-invoked 没人读 description 的 trigger）。
- `grilling` （ `grilling/SKILL.md` ）—— model-invoked。它要的是"在用户问'grill me'时自动接管"。
- `grill-me` （ `grill-me/SKILL.md` ）—— user-invoked，是 `/grilling` 的包装。是同一个 prompt 行为的两种触发方式。
- `implement` （ `implement/SKILL.md` ）—— user-invoked 的 orchestrator。它的工作是点流程大纲（"Use /tdd where possible... Run typechecking regularly..."），细节下钻到其他模型可触发的 skill。
- `handoff` （ `handoff/SKILL.md` ）—— user-invoked。保存对话到临时目录。

最后一招—— **router skill** 。 `SKILL.md:20` 说：user-invoked skill 多到记不住时，做一个 user-invoked 的索引 skill 列出其他的（ `grill-me` 就是这样一个实例）。

**Description 的 Context Load** ： `SKILL.md:24-28` 给了三条压榨 description 的方法：

- 把 leading word 放在最前面（这是 invocation work 的地方）
- 每个 trigger 分支只写一次（synonym 是 duplication）
- description 只保留 trigger，不重复 body 已经有的内容

每个 model-invoked 的 description 都在占 context window。占 100 字符就是 100 字符的永久消耗。

**触发分支问题** ：description 不是一两个字就够的。要列出用户可能说的关键字（ `review this` / `staged changes` ）、可能出现的语境（ `has staged but uncommitted changes` ）。漏一个分支，模型就不触发；多一个 synonym，weight 就被拉高。

![[Image 78.webp|信息阶梯与 Skill 触发方式]]

信息阶梯与 Skill 触发方式

## 7\. 改造诊断表：什么时候拆、删、外置

下面是按调研报告整理的判断表。我把它做成 **症状 → 判断 → 处理** 的格式，方便对着自己的 skill 跑一遍：

| 现象 | 判断 | 处理方式 |
| --- | --- | --- |
| 同一意思在多个句子/章节出现 | Duplication | 折叠成 leading word，或砍掉重复处 |
| 行已经无力改变模型默认行为 | No-op | 删整句，别修剪 |
| 写于早期但已不再描述当前行为 | Sediment | 删，定期 relevance audit |
| skill 整体很长，即使每行都活 | Sprawl | progressive disclosure 推 reference |
| 只有某些分支读到的内容 | Branch-specific reference | 用 context pointer 按需加载 |
| 负面写"不要 X"，但能正面写 | Negation | 改写正面目标 |
| 模糊规则但 agent 已经按合理方式完成 | Premature completion 风险低 | 不动，或 sharpen criterion |
| 模糊规则但 agent 经常提前完成 | Premature completion 风险高 | sharpen criterion；不行再 sequence split |
| 多个 user-invoked skill 记不住 | Cognitive load 过高 | 加 router skill |
| 团队 wiki 里有一句"认真 X" | 候选 skill | 走 leading word + completion criterion 改造流程 |

`SKILL.md:46-58` 给两个 split 的 cut：

- **By invocation** ：拆出 model-invoked skill 当有一个独立 leading word 触发它或别处必须 reach
- **By sequence** ：拆 steps 当 step 的 post-completion steps 在诱导 premature completion

`SKILL.md:50-51` 提醒：每个 cut 都要付一种 load。拆 model-invoked skill 就要付 description 的 context load，拆出 user-invoked 就要付 cognitive load。如果一个 cut 不省任何 load——别拆。

![[Image 31.png|Skill 拆分删除外置判断表]]

Skill 拆分删除外置判断表

## 8\. 六个真实的小 skill 当例子

按用户要求，在这 7 个 source files 里挑 6 个最有结构感的，提取 **词、门、动作、产物** 四元素。

### grilling（12 行，model-invoked）

`grilling/SKILL.md:6-12` 的核心：

- **Leading word** ： `relentlessly` （一次）
- **Completion criterion** ： `shared understanding` + 用户确认
- **动作** ：按决策树分支走，每个 question 一次
- **失败处理** ： `Asking multiple questions at once is bewildering` ——用负面形态描述失败状态

可学结构：12 行主体，每一行都直接改变行为。 **Ask the questions one at a time** 如果改成 **按顺序提问** 就会变成 no-op。

### tdd（36 行，model-invoked）

`tdd/SKILL.md:8` 的开头： `TDD is the red → green loop` ——leading word 是 `red` 。

- **Leading word** ： `red` （ `SKILL.md:70` 把它作为范例）
- **Completion criterion** ：每个 cycle 都要 red 才能 green
- **动作** ：red → green → refactor（refactor 不在 loop 里）
- **Anti-patterns** ：implementation-coupled、tautological、horizontal slicing

可学结构：anti-patterns 用名词化短语（一个短语覆盖一类），加上 **tell** ——比如 **mocking internal collaborators, tests private methods, or verifies through a side channel** 是 anti-pattern 的特征。

### prototype（26 行，model-invoked）

`prototype/SKILL.md:8` 一句话定义： `throwaway code that answers a question`.

- **Leading word** ： `throwaway`
- **Branch selection** ：LOGIC.md vs UI.md（不同问题走不同路径）
- **动作** ：6 条 rules that apply to both
- **完成** ：第 6 条 `Capture it when done` ——提交到 throwaway branch

可学结构：分支选择写在正文最前面（ `prototype/SKILL.md:11-17` ），让 agent 在动手前先选。

### handoff（16 行，user-invoked）

`handoff/SKILL.md:8-16` 的核心：

- **动作** ：写 handoff 文档
- **关键规则** ： `Do not duplicate content already captured in other artifacts`
- **single source of truth** ：specs、plans、ADRs、issues、commits、diffs 都不要重复

可学结构：单点真值用一条禁止 duplicate 的指令 + 替代做法（ `Reference them by path or URL instead` ）来强制。

### implement（15 行，user-invoked）

`implement/SKILL.md:7-15` 极简：

- **动作** ：实现 spec/tickets
- **下钻** ：Use `/tdd` where possible, at pre-agreed seams
- **节奏** ：typechecking regularly, single test files regularly, full test suite once at the end
- **完成** ：用 `/code-review` review，commit 到当前分支

可学结构：orchestrator skill 只点流程大纲，细节（怎么 TDD、怎么 review）下钻到其他 model-invoked skill。这正是 `SKILL.md:50-58` 说的 **split by invocation** ——把可被触发的部分做成 model-invoked，orchestrator 本身 user-invoked。

### grill-me（7 行，user-invoked）

`grill-me/SKILL.md:7` 整文件只有一行命令： `Run a ` /grilling ` session.`

- **动作** ：几乎为零
- **价值** ：提供从一个 user-invoked skill 触发另一个 user-invoked skill 的唯一办法（因为 user-invoked 没有 description 不会被自动触发）

可学结构：纯路由 skill，把自己压缩到极致。它存在的意义不是做事，而是给你一个容易记住的入口。

## 9\. 一些边界提醒

写完前面这些，我想留几条不能踩的边界。

**Leading word 效果是机制，不是性能数据** 。本文里我用 `tight` 和 `red` 举例，并不意味着只要换 leading word 就有 X% 提升。 `SKILL.md:67-72` 和 `GLOSSARY.md:129-135` 都是说 **用 leading word 替换 repetition 更省 token 也能让模型挂上先验** ，这话是机制描述，不是量化承诺。要验证是否真有改进，需要单独跑实验。

**每条 frontmatter 不是全平台通用** 。 `disable-model-invocation` 是 Claude Code 的实现，Codex、Cursor、Continue 各自有相似但不完全相同的字段。写 skill 时先看你的目标平台的 README——不在你 README 里的字段写了也不会生效，反而会让 skill 出错。

**三类内容要分清楚** 。本文每次引用 `GLOSSARY.md` / `SKILL.md` 都是原文事实（ `【源码】` ）； **这两个 axis 对应 cure 是 X 和 Y** 是基于源码的推理（ `【分析】` ）； **我建议团队 wiki 里也用 no-op test** 是我自己的迁移建议（ `【迁移】` ）。读者看到一句话时能立刻判断它的来源，不会把别人的个人建议当成共识。

**`tight-review` 这个例子是作者迁移产物** 。 `tight-review` 整体结构和 `prototype` 等同源 skill 一致，但不是我从 mattpocock 仓库找出来的——是按 `SKILL.md:24-42` 的方法论套出来的。读者拿去用没问题，但要知道它不是 **官方范例** 。

## 10\. 走到这里该做什么

最后给你一个立刻能跑的诊断清单——不是诊断这个 skill，是诊断你自己的。

1. **挑一条 prompt rule** ：大概率是 wiki 里那句 **认真 X**
2. **跑 no-op test** ：删掉它，看模型行为有没有变。如果没变——恭喜，这条是 no-op，可以删
3. **如果它有用，把它展开** ：用第三节的最小模板
4. **写完跑一遍 8 词诊断** ：每个词用第四节的检查那一行问一遍
5. **看 Invocation** ：这条是 model-invoked 还是 user-invoked？资源消耗对吗？
6. **看 Information Hierarchy** ：哪些推 reference？哪些拆 skill？

不一定每次都要做得完美。把口号当成 skill 雏形这件事，比 **skill 是否够标准** 重要。skill 的目的是让 agent 跑得稳，不是给团队一个填空模板。 写 skill 的时候，最容易跑偏的不是写错，而是写多。 `SKILL.md:59` 给了最直接的判断标准： `most prose that fails should go, not be rewritten` 。删一句比改一句更值。

**相关资源**

- writing-great-skills 完整的词汇定义：GLOSSARY.md（仓库路径 `skills/productivity/writing-great-skills/GLOSSARY.md` ）
- 本文讨论的 7 个参考 skill 全部在 `mattpocock/skills` 仓库（github.com/matt-pocock/skills）
- 想进一步读方法论：参考仓库 README.md 第 171-209 行的 invocation 设计原则

> **说明** ：本文内容基于 Matt Pocock 的 `mattpocock/skills` 仓库的 `writing-great-skills` 系列源码分析整理而成， `tight-review` 是作者构造的迁移示例而非官方范例，源码基于本地仓库版本尚未在生产环境中逐项验证。 **文中的 prompt 模板和判断标准仅供参考，实际效果请以你的业务场景和模型版本测试结果为准。** 如果有不同视角或更好的诊断方法，欢迎在评论区分享交流。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录