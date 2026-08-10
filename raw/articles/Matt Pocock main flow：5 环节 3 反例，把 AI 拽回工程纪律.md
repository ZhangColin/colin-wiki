---
title: "Matt Pocock main flow：5 环节 3 反例，把 AI 拽回工程纪律"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491540&idx=1&sn=6aefb6205d6c42a18907a7825ed502ce&chksm=cf43aa82f8342394eb3361307f02b42965f55931bbe7f9112ab430aa085a7e0b9cee805e85ba&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-06
description:
tags:
---
运维有术 术哥无界 *2026年7月29日 08:30*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *180* 篇，AI 编程最佳实战「2026」系列第 *62* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

![[Image 79.webp|main flow 五环节全景信息图封面]]

main flow 五环节全景信息图封面

读完 Matt Pocock 的 `skills` 仓库源码，让人真正意外的其实是这五件事硬被按顺序串在了一起： `/grill-with-docs → /to-spec → /to-tickets → /implement → /code-review` 。 `ask-matt` 的 SKILL.md 把它叫做 **main flow** ——所有正经工程任务都应该走这条路。

但这条链路上每一环真正在做什么、留下什么、什么时候算完成，并不是看 SKILL.md 标题就能猜到的。源码和 README 把 `to-spec` 的核心约束直接写在了第一行： **Do NOT interview the user — just synthesize what you already know** 。这一句决定了整个主链路的上下文管理逻辑。

这篇文章用一个连续案例（一个叫 `多格式导出` 的 feature）从左到右走完这五环。每一步都给出：这一步本身在做什么、留下什么文件或 ticket、什么时候算完成。再补一段 `链接断在中间` 反例和 `一次会话只做哪一段` 判断。

> **一行前提** ：这套 skill 还有一个真正的前置 skill `/setup-matt-pocock-skills` ——它配置 issue tracker、triage labels、domain docs 路径，后面的 `to-spec` / `to-tickets` 都假设这些已写好。跳过它直接用 `to-spec` / `to-tickets` 不会失败，但写出的产物格式不对。这篇不展开 setup，假设你的仓库已经跑过它。

## 1\. main flow 解决的真问题：会话是一次性的，工程不是

理解 main flow 之前要先承认一个前提： **session 是 disposable 的，但大块工作不是** 。Matt 在他自己的 dictionary-of-ai-coding 仓库里写过原话：

> A ticket should be completable before the session drifts out of the smart zone — and that constraint is testable.

也就是说，AI 编程的 **会话** 是会自己变笨的（ `ask-matt` 写 ~120k tokens， `dictionary-of-ai-coding` 写 125k–150k，YouTube 视频里他提的是 140k——三处数字不一致）。所以 main flow 在做的远不止让 AI 更聪明——它的真正目标是 **把工程纪律物化成文件，让纪律穿越会话边界** 。

## 2\. 五环节输入 / 产物 / 完成信号对照表

| 环节 | 入口触发 | 输入 | 产物 | 完成信号 | 失败症状 |
| --- | --- | --- | --- | --- | --- |
| `/grill-with-docs` | 用户手动 | 想法 + codebase | `CONTEXT.md`  \+ `docs/adr/*.md` + 精炼过的问题定义 | 每个分支决策解清，术语收敛 | 术语漂移、未澄清的假设进入下一环 |
| `/to-spec` | 用户手动 | 已被 grill 过的对话状态 | spec，发到 issue tracker，自动打 `ready-for-agent` 标签 | 所有 seams 与用户确认；spec 含 Problem / Solution / User Stories / Implementation / Testing / Out of Scope | spec 充满未验假设；用词不来自项目词表 |
| `/to-tickets` | 用户手动 | spec | 多个 tracer-bullet ticket，按 blocking edges 编号发布 | 任意无 blocker 的 ticket 可立即被领走 | ticket 横向分层；单 ticket 装多个 seam |
| `/implement` | 用户手动 | 单个 ticket（fresh context） | 通过 tdd 红绿的代码 + 测试 | tdd 红绿 + code-review 双轴通过 | 测了错的 seam；上下文污染；spec drift |
| `/code-review` | 用户手动（implement 末尾强制） | diff vs fixed point | Standards + Spec 两份独立报告 | 两轴分别判定后聚合 | 一轴掩盖另一轴；commit 时混进未经审查的代码 |

![[Image 80.webp|main flow 五环节流水线示意图]]

main flow 五环节流水线示意图

*main flow 五环节：从澄清访谈到代码审查，每环都把思考物化成文件*

## 3\. 贯穿案例：多格式导出 feature

我们用一个连续案例走完全程。场景：

- **产品** ：一个内部数据看板，原本只能导出 CSV。
- **需求** ：要加 JSON、Parquet、Excel 三种导出格式。
- **约束** ：导出要异步、支持取消、产物能直接给下游 ETL 消费。

这五环每一步都用到这个 feature 作为具体落点。

## 4\. 第一步：/grill-with-docs——在主链路中的位置

`grill-with-docs` 的内部细节（relentless interview、 `/domain-modeling` 怎么驱动、CONTEXT.md 怎么迭代）由同系列第 2 篇讲，这篇不复述。 **它只回答一件事：在主链路里它扮演什么角色？**

源码里 grill-with-docs 的 SKILL.md 全长只有 7 行：

> Run a `/grilling` session, using the `/domain-modeling` skill.

它是一个 **包装 skill** ，本身不写代码，只产出三类工件：

- `CONTEXT.md` ：项目共享词表——比如 **多格式导出** 里 `export job` 是不是同一个概念、 `cancel token` 是不是和 HTTP 取消混用。
- `docs/adr/*.md` ：关键决策记录——比如 **为什么不用同步导出** 。
- 精炼过的问题定义：哪些分支已经被问过、还剩哪些没问。

主链路对它的硬性要求是： **spec 之前必须已经被 grill 过一次** 。原因到 `to-spec` 那一节才看得到。

## 5\. 第二步：/to-spec——只综合，不访谈

源码里 `to-spec` 的核心约束是：

> Do NOT interview the user — just synthesize what you already know.

这一句反直觉。大部分 spec 工具的核心动作就是 **问用户** ，但 to-spec 的工作方式是 **反向的** ：它假设上游已经做完了访谈，它只把已有共识压缩成 spec 模板。

### 为什么不做访谈、只综合

这个设计有两个后果。

第一， **spec 的质量完全取决于上游对话质量** 。如果 grill 阶段没把 **导出取消语义** 问清，spec 里写出来的就是一句 **支持取消** ，下游 implement 阶段会按字面意思瞎补。

第二， **为什么这么设计** ——Matt 自己在 AIHero 的 `9 Things People Get Wrong With /grill-me and /grill-with-docs` 文章里直接解释过：

> By the time you finish grilling, you've made hundreds of tokens worth of choices about how your system should work. This is pure gold. Do not clear the context and start fresh just to write a PRD. That's throwing away all your design work.

这正是 main flow 强调 grill + spec + tickets 三步保持同一 context window 的原始理由——上游思考是金的，不能为了让 spec 看起来干净就把上下文清空重做。

### spec 模板的关键字段

`to-spec` 用一份固定模板（出处： `skills/engineering/to-spec/SKILL.md` ），字段如下：

- **Problem Statement** ：用户视角的痛点
- **Solution** ：用户视角的解决方式
- **User Stories** ：长列表，编号，As a / I want / so that 格式；to-spec 原文要求 `extremely extensive`
- **Implementation Decisions** ：模块、接口、架构、schema、API、交互。 **不写具体文件路径或代码片段** （会过时）
- **Testing Decisions** ：好测试的描述；测哪些模块；参考仓库已有同类测试
- **Out of Scope**
- **Further Notes**

Implementation Decisions 这一条很反直觉——它 **故意不写具体文件路径** 。原因 to-spec SKILL.md 也明说了：路径会过时，但模块边界不会。

应用到 `多格式导出` 时，spec 应当这样写（节选）：

> **Problem Statement** ：用户在使用数据看板时只能下载 CSV。下游 ETL 需要 JSON 和 Parquet 格式做流式消费，运营需要 Excel 做人工核对。
> 
> **Solution** ：在现有导出模块上扩展多格式输出。
> 
> **Implementation Decisions** ：
> 
> - 复用现有 export job 状态机；新增 `format` 字段而不是新模块
> - 复用现有 cancel token 协议
> - 新增格式注册表（FormatRegistry），每种格式注册一个 writer
> - 不要触碰现有 CSV 路径
> 
> **Testing Decisions** ：
> 
> - 测试公共格式输出契约，不测具体 writer 内部
> - 参考 `export-csv.test.ts` 的 seam 模式

源码 `to-spec` 的第二条硬规则：

> Use the highest seam possible. If new seams are needed, propose them at the highest point you can. The fewer seams across the codebase, the better - the ideal number is one.

seam 是测试的公共边界。在多格式导出里，seam 选得越高越好（理想是一个）—— `export job 产物契约` 就是这么一个 seam：加一个 format 字段就够，writer 实现是内部细节。 **这条 seam 必须在 spec 阶段就定下来** ，因为 implement 阶段没有权力决定在哪里测。

### 哪一类 ticket 必须重新走 spec

如果 spec 写完后，实施过程中发现原 spec 漏掉了一类边界——比如 **导出超过 10GB 时应该分片** —— **必须回去更新 spec** 。如果只更新 ticket 描述或直接 implement，spec 轴的 review 会判定 **实现偏离 spec** ，整段重做。

判断标准一句话： **任何影响 contract（对外接口、状态机、状态枚举）的实现** 必须回 spec；只影响 writer 内部的可以写进 ticket。

## 6\. 第三步：/to-tickets——tracer bullet 和 blocking edges

`to-tickets` 的核心创新只有两个概念：tracer bullet（贯穿弹）和 blocking edges（阻塞边）。

### tracer bullet 解决什么

源码原文：

> Each slice cuts a narrow but COMPLETE path through every layer (schema, API, UI, tests) — vertical, NOT a horizontal slice of one layer. A completed slice is demoable or verifiable on its own. Each slice is sized to fit in a single fresh context window.

`to-tickets` 禁止横向分层——比如 **先做完所有 schema、再做完所有 API、再做完所有 UI** 。每张 ticket 必须是 **窄但完整地穿过所有层** 。

**tracer bullet 解决的实际工程问题** ：

Matt 在 AIHero 的 `Tracer Bullets: Keeping AI Slop Under Control` 文章里直接引用了《The Pragmatic Programmer》原话：

> The pragmatic programmer calls this outrunning your headlights.

AI 写代码典型的失败模式是 **一口气写完整个 feature 再说** ——这就是 outrunning your headlights：跑得比车灯照得远。tracer bullet 强制 AI 每次只写一小段全栈走通， **写一段就停，等反馈** 。

应用到 `多格式导出` ，tracer bullet 切法：

- **Ticket 1** ：schema 加 `format` 枚举 + API 接 `format` 参数 + UI 暴露下拉框 + 一个端到端测试跑通（用 fake writer）。
- **Ticket 2** ：实现 JSON writer。schema/API/UI 不动。
- **Ticket 3** ：实现 Parquet writer。同样不动上层。
- **Ticket 4** ：实现 Excel writer。
- **Ticket 5** ：cancel token 在异步导出里的端到端覆盖。

每张 ticket 自己跑完就能 demo，没有 **写完 Ticket 3 但 Ticket 1 的测试挂了** 这种事。

### blocking edges 解决什么

`to-tickets` 给每张 ticket 显式声明它被哪些 ticket 阻塞：

> A ticket with no blockers can start immediately.

**blocking edges 解决的实际工程问题** ：让多张 ticket 可以并行。 **但这里要分清两个边界** —— `ask-matt` 源码侧只描述了 **串行** 模型（每个 ticket 一个新 session、worked blockers-first by hand）；字典（ `dictionary-of-ai-coding` ）里 Matt 承认这是官方认可的扩展：票图的叶子节点可以并发执行。读源码照搬会得出 **全串行** 的结论，读字典照搬会得出 **全并发** 的结论。文章这里 **严格按字典表述** ——叶子节点可以并发，但每个 implement 仍然各自一个 session。

在 `多格式导出` 例子里，Ticket 2/3/4 之间互相无依赖，可以并发。Ticket 5（cancel token 全链路）被所有 writer ticket 阻塞，因为它要测的是 **任何一个 writer 都能取消** 。

发布到 issue tracker 时：

- **Local files** ：`.scratch/<feature>/issues/NN-slug.md` ，每个 ticket 一个文件， **按依赖顺序编号** 。
- **Real tracker** ：每个 ticket 一个 issue，依赖顺序发布；用平台原生 blocking/sub-issue 关系。
- 全部打 `ready-for-agent` 标签——源码原话是 `agent-grabbable by construction` 。

### 一个 ticket 模板样例

```
# 02 — 实现 JSON writer

## 7. Depends on
- 01（schema + API + UI 的 tracer bullet）

## 8. Goal
让 export job 接受 \`format=json\` 后输出 JSON 文件。

## 9. Implementation Decisions
- 在 \`FormatRegistry\` 注册 \`json\` writer。
- 复用 01 已有的 schema 字段映射。
- 不动 cancel token（05 才统一处理）。

## 10. Testing Decisions
- seam 仍然是「export job 产物契约」。
- 测：给定 input data + format=json，产物的 schema 等于 JSON Schema fixture。
- 不测 writer 内部（那是 private impl detail）。
- 参考已有 test：\`packages/export/__tests__/csv.test.ts\` 的 seam 写法。

## 11. Out of Scope
- Parquet、Excel（03 / 04）。
- cancel token 全链路（05）。
```

这张 ticket 看着啰嗦，但每一段都有用：

- Implementation Decisions 不写文件路径，只写模块边界。
- Testing Decisions 显式声明 seam（与 spec 对齐）。
- Out of Scope 显式声明阻塞边——这就是 blocking edges 在 ticket 级别的体现。

## 12\. 第四步：/implement——三条隐含规则

`/implement` 的 SKILL.md 全长只有 15 行：

> Implement the work described by the user in the spec or tickets. Use /tdd where possible, at pre-agreed seams. Run typechecking regularly, single test files regularly, and the full test suite once at the end. Once done, use /code-review to review the work. Commit your work to the current branch.

字面意思不多，但拉通看，三条隐含规则贯穿：

### 规则 1：pre-agreed seams

测试只能在 spec 阶段就约定好的 seam 上做，不是 implement 阶段才决定。 `/tdd` 的 SKILL.md 把 seam 定义得很清楚—— **测试的公共边界，不是内部实现** 。配套的反面是三个 anti-pattern：

- Implementation-coupled（mock 内部协作者、测私有方法、通过侧通道验证）
- Tautological（断言重算实现达到相同结果）
- Horizontal slicing（先写所有测试再写实现——测试的是 **想象中的行为** ）

**反推到主链路** ：seam 如果没在 spec 里写明，implement 阶段只能瞎选。这正是为什么 spec 阶段那一句 `the fewer seams, the better` 是硬规则。

### 规则 2：between each ticket clear context

`ask-matt` 的 context hygiene 原话：

> Keep steps 1–3 in one unbroken context window — don't compact or clear until after /to-tickets — so the grilling, spec, and tickets all build on the same thinking. Each /implement then starts fresh, working from the ticket.

也就是说：

- **grill + spec + tickets** 共享一个长会话
- **每个 implement** 独立开新会话

`多格式导出` 里，Ticket 1（tracer bullet）做完后，开新会话做 Ticket 2。Ticket 1 跑过的中间讨论、错过的尝试、踩过的坑—— **全部不进入 Ticket 2 的上下文** 。Ticket 2 只读 ticket 文件本身。

为什么？Matt 在 dictionary 里写过原话：

> The smart zone is a budget, and unrelated work spends it.

在一个 session 里做第二个任务，等于从 dumb zone 起点开始。

### 规则 3：closing out with code-review

implement 末尾必须 review，不能写完直接 commit。这一条把 implement 和 code-review 锁成一对：implement 是 **完成 ticket** ，code-review 是 **评判 diff** 。

**哪一类 ticket 写完就算，不一定要 review？**

源码没有给出豁免—— `implement/SKILL.md` 最后一段明确写 `Once done, use /code-review to review the work` ，无例外。

但实践上可以做点折扣： **没有 contract 改动的纯内部重构** （比如把一个函数提取成 helper）可以走轻量 review。但不能跳过——review 不只是查 bug，更是查 spec drift 的最后一道闸。

## 13\. 第五步：/code-review——两轴并行，不顺手做完

`/code-review` 是 main flow 的关卡门。它必须在 commit 前跑完。

### 两轴并行

`code-review` 的核心机制是把 diff 喂给两个并行子代理：

```
┌────────────────────────┐
     │  diff between FIXED   │
     │  POINT and HEAD       │
     └──────────┬─────────────┘
                │
      ┌─────────┴─────────┐
      ▼                   ▼
┌─────────────┐     ┌─────────────┐
│  Standards  │     │    Spec     │
│   agent     │     │    agent    │
│ (parallel)  │     │ (parallel)  │
└──────┬──────┘     └──────┬──────┘
       │                   │
       └─────────┬─────────┘
                 ▼
          ┌─────────────┐
          │  Aggregate  │
          │  (separate) │
          └─────────────┘
```
- **Standards 轴** ：依据仓库文档化的编码标准 + Fowler smell baseline（12 个味道：Mysterious Name, Duplicated Code, Feature Envy, Data Clumps, Primitive Obsession, Repeated Switches, Shotgun Surgery, Divergent Change, Speculative Generality, Message Chains, Middle Man, Refused Bequest）。仓库自带文档就优先用自己的，不重复 baseline。
- **Spec 轴** ：依据 origin issue / PRD / spec。检查三件事：spec 要求的实施是否缺失；实现是否超出 spec；看起来实施了但实现错了。

源码原话：

> Two-axis review of the diff between HEAD and a fixed point the user supplies. Reporting them separately stops one axis from masking the other.

### 为什么并行而不是串行

如果串行做（先 Standards 再 Spec），有一个真实风险：Standards 那轴判了一堆 **实现有 bug** 的结论，会稀释 Spec 轴的发现——读者会下意识用 **代码不行** 去解释 **spec 不一致** ，把两个独立问题混在一起。 **两轴分别报告、各自完整** ，就是不让一个轴掩盖另一个。

### 为什么不顺手在 implement 里做完

这是 main flow 容易被质疑的设计之一。我的判断基于三个事实：

第一， **视角不同** 。implement 的视角是 **完成 ticket** ——目标是 **让 ticket 的 acceptance criteria 跑通** 。review 的视角是 **评判 diff** ——目标是 **diff 是不是符合仓库标准 + spec** 。两个目标在同一个思维里很难同时 hold。

第二， **确认偏误** 。implement 的人会偏好自己的实现—— **我刚写完，逻辑当然是对的** 。把 review 拆给独立子代理，至少切断了 **作者评自己** 这个偏误。

第三， **防止上下文污染** 。implement 的上下文里装满了 **为什么这么写** 的合理性（毕竟是你刚做的），这些理由在 review 时应该 **不存在** 。新会话/独立子代理强制这个切断。

`/code-review` 的双轴不是表演，是结构性地保护 review 的判断不被 implement 的判断稀释。

### 一个 code-review diff 片段样例

下面这段是模拟 Standards 轴命中 Shotgun Surgery 的 diff 片段。 **这是样式化样例** ，不是真实 PR——只为说明 review 报告长什么样。

```
// 改动：把格式枚举从三处分散的 if/else 合并到注册表

- if (format === 'csv') return renderCSV(data)
- if (format === 'json') return renderJSON(data)
- if (format === 'parquet') return renderParquet(data)
- if (format === 'excel') return renderExcel(data)
+ return FormatRegistry.lookup(format).render(data)
```

Standards 轴报告（节选）：

> **Hit — Repeated Switches**: 4 个 if/else 在 3 个不同文件（ExportPanel.tsx, ExportAPI.ts, ExportJob.ts）出现完全相同的形态。已被替换为 FormatRegistry.lookup。 **Verdict**: ✅ 通过。 **Note**: 同时建议给 FormatRegistry 加一个注册时类型断言（ `register(format: FormatKind, writer: Writer)` ），防止 runtime 写错。

Spec 轴报告（节选）：

> **Item 1 — 实现 JSON writer** ：spec 要求 format 字段接受 `json` ，产物 schema 等于 fixture。 **Implementation**: 实际产物在空数组时省略 `data` 字段（fixture 包含空数组但 `data: []` ）。Spec 没显式说空数组行为。 **Verdict**: ⚠️ 需要 spec 仲裁。建议在 spec 的 Further Notes 段补一句 **空输入时产物为 `{ data: [] }` 而非省略 data** 。

两个轴各自的判定互不污染，aggregator 把两份报告直接合并发给 implement 的人。

![[Image 81.webp|code-review 双轴并行架构图]]

code-review 双轴并行架构图

*code-review 双轴并行：Standards 与 Spec 各自独立判断，aggregator 不让一轴掩盖另一轴*

## 14\. 链接断在中间：三个反例

main flow 的代价是步骤多。问题常常藏在中间—— **跳过其中某一环** 。下面三个反例都来自 main flow 自身规则的逻辑反推。

### 反例 1：跳过 /to-spec，直接 /to-tickets

症状：

- `to-tickets` 没有 spec 可读，只能从对话里 **抽取** 任务——粒度完全取决于对话质量。
- 写出的 ticket 缺 Implementation Decisions 字段，implement 的人不知道 seam 在哪。
- 后续 code-review 的 Spec 轴判无可判——因为没有 spec。

具体到 `多格式导出` ：跳过 spec 之后，Ticket 2 很可能写成 **加 JSON 输出** ——没说 seam，没说 schema 边界，没说 cancel token 是否要兼容。implement 阶段会按字面意思实现，再让 code-review 来兜底，但 code-review 兜的是 spec drift，而 spec 本身不存在。

源码对应规则： `to-spec/SKILL.md` 第一行 + `to-tickets/SKILL.md` 中 `respect ADRs in the area you're touching` ——没有 spec 就没有 ADRs 可尊重。

### 反例 2：跳过 /to-tickets，直接 /implement

症状：

- 一个大 ticket 装多个 seam。任何 tdd 失败都得回滚整片。
- 一旦 context window 爆掉，handoff 不知道写到哪——因为没有 ticket 文件承载 **做到哪里了** 。

具体到 `多格式导出` ：直接 implement `加多格式导出` 这个大 ticket，会写出 schema、API、UI、3 个 writer、cancel token 的全栈改动——一票写完。某次 tdd 失败回滚时，会发现改动横跨 8 个文件，只能 `git reset --hard` ，连 **做到哪里了** 的痕迹都没留下。

源码对应规则： `to-tickets/SKILL.md` 中 `Each slice is sized to fit in a single fresh context window` 。

### 反例 3：跳过 /code-review，implement 后直接 commit

症状：

- Standards 漂移：仓库标准被绕过，没人守门。
- Spec drift：实现偏离原始 spec 但没人察觉。
- 这正是为什么 implement 末尾强制 code-review。

具体到 `多格式导出` ：直接 commit 后，代码里如果出现了 `writer 内部写死 schema` 、 `cancel token 协议悄悄变种` 等情况，没有 review 抓住这些，下一个 feature 在同一个 seam 上叠加，最终整段重写。

源码对应规则： `implement/SKILL.md` 最后一段 `Once done, use /code-review to review the work` 。

### 三个反例的共同模式

每个反例的本质都是 **把上一环的纪律扔掉，让下一环独自承担** 。main flow 看起来啰嗦，但每一环都在做 **上游做不了的事** ——grill 做澄清、spec 做综合、tickets 做切片、implement 做实现、review 做评判。 **没有一环是冗余的** 。

![[Image 82.webp|main flow 三个反例对比示意图]]

main flow 三个反例对比示意图

*三个反例的共同模式：把上一环的纪律扔掉，让下一环独自承担——main flow 没有一环是冗余的*

## 15\. 一次会话只做哪一段

main flow 真正反直觉的设计在 `ask-matt` 的 context hygiene。直接给原文：

> Keep steps 1–3 in one unbroken context window — don't compact or clear until after /to-tickets — so the grilling, spec, and tickets all build on the same thinking. Each /implement then starts fresh, working from the ticket.

**一次会话只做哪一段** 的具体判断：

- **grill-with-docs → to-spec → to-tickets** ：保持同一个会话，不要 `/clear` 、不要 `/compact` 。
- \*\*每个 `/implement` \*\*：开新会话，只读 ticket 文件。
- **smart zone 边界** （ `dictionary-of-ai-coding` ）：约 120k–150k tokens（三个来源数字不一致：ask-matt 写 120k，dictionary 写 125-150k，Matt YouTube 视频说 140k）。
- **跨会话桥梁** ：两种方式—— `/handoff` 把当前会话压缩为 markdown 文件，新会话引用它（ `forks` ，开新会话）；或 `/compact` 留在当前会话但压缩较早历史（ `continues` ，同会话）。 `ask-matt` 明确区分这两条： `/handoff` forks； `/compact` continues。

`多格式导出` 例子的会话切分：

1. **会话 1（长会话）** ：grill + spec + tickets。大约持续 1–2 小时，token 累计到 80k–100k。
2. **会话 2（短）** ：implement Ticket 1（tracer bullet）。约 30 分钟。
3. **会话 3/4/5（短，并发）** ：implement Ticket 2/3/4（三个 writer 并行开三个新会话）。
4. **会话 6（短）** ：implement Ticket 5（cancel token 全链路）。
5. **每个 implement 会话末尾** ：跑 `/code-review` 。要么开新会话，要么用同会话末尾但 **新开 review 子代理** ——避免 implement 的上下文污染 review 的判断。

### 与第 7 篇（wayfinder + handoff）的衔接

`ask-matt` 解决的是 **一次会话内的 scope 控制** ——长会话做规划，短会话做执行。

但任务 **跨多个会话也不够** （greenfield、大型 feature build、路径不清晰）时，main flow 就不够了。Matt 的解决方案在第 7 篇里：wayfinder 输出 **决策 ticket** （决策不是交付），雾散去后 handoff 而不是 build，最后把决策合并到 main flow 的 `/to-spec` 。

本文不复述 wayfinder/handoff 的内部细节—— `/handoff` skill、AIHero 的 `The /handoff Skill` 原文、与 wayfinder 的衔接模式都留给第 7 篇。这里只说一句衔接点： **main flow 内部其实默认假设你会用 `/handoff` 来切会话** ，但源码 README 没把它列进 main flow——这一点也是第 7 篇会展开的内容。

![[Image 83.webp|一次会话只做哪一段——main flow 会话语义切分图]]

一次会话只做哪一段——main flow 会话语义切分图

*main flow 会话切分：grill+spec+tickets 共享长会话，每个 implement 独立新会话，smart zone 是 token 预算*

## 16\. 三种规模下的判断

把 main flow 当成刻度尺而不是开关。 **强制走完全部 5 环不一定是正确选择** 。

- **小改动** （修一行、删一个文件、调一个文案）：可以跳过 grill 和 spec，直接 implement + code-review。tickets 也可以不开——单 ticket 够用。
- **中型 feature** （多格式导出、一个新的 API endpoint）：走 5 环，但 spec 可以简化。tracer bullet 严格走；ticket 数量控制在 3–5 张。
- **大型 feature / greenfield** ：走 5 环 + wayfinder（第 7 篇）+ 多次 handoff。

**判断的边界** ：

- 如果改动 **不引入新 contract** （接口、状态机、状态枚举），可以走轻量 spec。
- 如果改动 **影响多个模块** ，必须用 tracer bullet 切票。
- 如果改动的 blast radius 横跨整个 codebase（比如重命名共享列），to-tickets 显式给出了 expand–contract 模式——这种情况主链路不够，要加专门的 integration branch 模式。这条留给第 7 篇衔接。

## 17\. 几个常被误用的点

**1\. grill = 写 spec** ：错。grill 是访谈，spec 是综合。两者在主链路里分工不同——这也是为什么 grill-with-docs 的产物（CONTEXT.md + ADR）和 spec 是不同的文件。

**2\. to-spec 可以事后访谈补** ：错。源码原话 `Do NOT interview the user` ——如果 spec 阶段发现需要访谈，说明上游 grill 没做完，应该回 grill 而不是去 spec 里临时补问。

**3\. implement 内部可以顺手 review** ：错。两轴 review 的价值就在于独立判断。implement 的人评自己会被确认偏误污染。

**4\. tracer bullet = 小步快跑** ：不完全对。tracer bullet 强调 **窄但完整穿过所有层** ，不是 **小** 。一行代码改完不穿过所有层就不算 tracer bullet。

**5\. blocking edges 是项目管理工具** ：错。blocking edges 的目的是 **让无阻塞的 ticket 可以并发被领走** ，这和 **项目管理** 不是一回事——票图叶子节点可并行执行是 main flow 官方认可的扩展。

## 18\. 不承诺的结论

main flow 的源码、Matt 的 blog、Reddit / Hacker News 上的真实反馈，都没有承诺 ROI。Reddit 上有用户自报 **质量优先版本大约 1 小时/小 issue** ，但这是把 main flow 进一步工业化后的代价样本——Matt 自己没有要求这样串。

所以本文不写 **减少返工 / 减少 token / 减少事故** 。这些是 vibe coding 时代被反复证伪的承诺。main flow 卖的不是省时间，是 **让 AI 编程有工程纪律** ——而工程纪律的价值，取决于你愿不愿意用文件来承载它。

## 总结：main flow 的本质是文件即纪律

把这五环放到一起看，会发现一个共同特征：每一环都把当前思考 **物化成文件** 。

- grill → `CONTEXT.md` + ADR
- spec → spec 文档发到 issue tracker
- tickets → 编号的 ticket 文件，带 blocking edges
- implement → 通过 tdd 红绿的代码
- review → Standards + Spec 两份独立报告

**文件是 main flow 的真正产物** 。会话是一次性的（smart zone 会过期），但文件可以穿越会话。这就是为什么 main flow 强调 **每个 implement 一个新会话** ——下一个会话只需要读 ticket 文件就够了，根本不需要记住上一个会话的所有思考。

如果只记一句话：main flow 的设计不是 **让 AI 更聪明** ，是 **让工程纪律活在文件里，会话死了纪律还活着** 。这也是它和 vibe coding 工具之间一条清晰的分界线。

至于这个设计能不能撑住——再看半年：实际工程团队跑下来的 ticket 数量、code-review 命中率和 spec drift 频率会给出答案。这是一个开放的问题，不是已经验证的结论。

> **说明** ：本文内容基于 Matt Pocock Skills 源码和官方文档分析整理而成。 **文中关于 main flow 五环节的入口、出口、产物、门禁描述，三处反例的对照，会话语义边界判断，均来自源码 SKILL.md 原文引用，但本文未在生产环境中逐项跑通整套 main flow。** **文中的配置模板和参数建议仅供参考，实际效果请以你的业务数据和环境测试结果为准。** 如果你有实际使用经验，欢迎在评论区分享交流。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录