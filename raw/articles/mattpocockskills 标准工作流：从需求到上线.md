---
title: "mattpocock/skills 标准工作流：从需求到上线"
source: "https://mp.weixin.qq.com/s?__biz=Mzk0MzE4MzY5MQ==&mid=2247483838&idx=1&sn=5b84309ee90514cc1f45cc39854ce229&chksm=c336839af4410a8cb554d68da69663207c788dc532263f8e2ceb21b16ca400dd79ab05815b99&cur_album_id=4613068008494923776&scene=190#rd"
author:
  - "[[小匠]]"
published:
created: 2026-07-26
description: "用 AI 编程最怕需求跑偏、对话散掉。mattpocock/skills 这套标准工作流，从盘问需求到拆任务、写代码、做 review，每一步都有 skill 兜着。附完整流程图。"
tags:
---
小匠 小匠Skills *2026年7月7日 08:00*

## mattpocock/skills 标准工作流

## AI 编程从需求到上线的完整流程

用 AI 写代码，最怕的不是写不出来，而是"对话散掉、需求跑偏、改到一半忘了最初要干嘛"。

mattpocock/skills 这套开源 skill 体系，本质上就是给 AI 编程加上一条"标准流水线"——从盘问需求，到拆任务、写代码、做 review，每一步都有对应的 skill 兜着。

这篇只讲一件事：这套流程怎么用。

### 两类触发方式

先理解这个，后面的流程才看得懂。skill 分两种：

• **User-invoked（手动触发）** ：你输入 `/xxx` 才跑，负责编排主流程。

• **Model-invoked（自动触发）** ：任务匹配时 agent 自动调用，承载可复用规范。

一条规则记住：user-invoked 可以调用 model-invoked，反过来不行。所以下面讲的主流程，全是你手动驱动的；查 bug、code review 这类规范，会在合适时机自动介入，不用你排程。

### 标准主流程

四步走，这是整套体系的骨架：

`/grill-with-docs` → `/to-prd` → `/to-issues` → `/tdd` （或 `/implement` ）

**第一步 /grill-with-docs** ：盘问会话，同时构建项目领域模型，锐化术语，内联更新 CONTEXT.md 和 ADR。说白了，这一步是在"对齐需求"——把模糊的东西逼清楚，和 agent 先达成共识再往下走。

**第二步 /to-prd** ：把当前对话直接合成 PRD，发布到 issue tracker。不做访谈，只综合已经讨论过的内容。作用是把共识固化成文档，别让对话散掉、后续遗忘。

**第三步 /to-issues** ：把计划/规格/PRD 按垂直切片拆成可独立认领的 issues。注意是"垂直切片"——一个 issue 跑通一个需求，从后端到前端，而不是按层横切。

**第四步 /tdd（或 /implement）** ：TDD 的红-绿-重构循环，一次啃一个垂直切片；或者用 /implement 直接实现。每个切片能独立验证。

补一个经验点：to-prd 和 to-issues 经常成对出现，但如果是明确的小需求，可以跳过 PRD 直接 to-issues——官方本就把它们设计成两个独立 skill，拆开用是有道理的。

### 常见变体

主流程不是唯一路径，看情况走分支。

**变体 A：从架构体检出发**

`/improve-codebase-architecture` → (prototype) → `/to-prd` → `/to-issues` → `/tdd`

扫描代码库找深化机会，生成可视化 HTML 报告，再陪你盘问选定项。项目变"泥球"之后用它来重构，建议定期跑。

**变体 B：修 bug**

`/diagnosing-bugs` → `/tdd`

纪律化诊断循环：reproduce → minimise → hypothesise → instrument → fix → regression-test。

提醒一句：官方的 diagnosing-bugs 本身就包含 fix 和 regression-test，别像某些魔改版那样把它砍成"只报原因"——那会把闭环丢了。修好后用 /tdd 补回归测试。

**变体 C：视觉/交互对齐（可选）**

`/prototype`

构建可丢弃原型来回答设计问题。UI 类需求做多个激进变体；逻辑类做能跑的终端程序。原型不合适就扔，低成本测想法。一般新开 session，用 /handoff 把上下文交接过来。

### 自动介入的规范类 skill

这些不用你手动排程，任务匹配时 agent 会自动触发，是主流程质量的兜底：

• **/code-review** ：对 diff 做双轴审查。Standards 轴看是否遵循仓库规范和坏味道基线；Spec 轴看是否忠实实现来源 issue/PRD。两条轴并行跑，子 agent 互不污染。

• **/domain-modeling** ：主动构建和锐化领域模型，对照术语表挑刺，更新 CONTEXT.md/ADR。

• **/codebase-design** ：设计深模块的共同规范——小接口后面藏大量行为。

• **/research** ：针对高可信一手来源调研，结果写成带引用的 Markdown。

### 一图看懂

完整标准工作流（一图看懂）

![[Image 2.webp|图片]]

### 辅助与交接

按需用，不是必经流程：

• **/handoff** ：把当前对话压缩成交接文档，交给另一个 agent 或 session 继续。

• **/ask-matt** ：不确定该用哪个 skill 时，让它帮你路由到合适的 user-invoked skill。

• **/grill-me** ：和 grill-with-docs 一样盘问，但不写文档，纯对齐。非代码场景也能用。

### 写在最后

这套流程的价值，不在于"多了一个工具"，而在于让 AI 编程从"想到哪写到哪"变成"有纪律的工程行为"。

需求先对齐，计划先固化，任务先拆好，代码先测试——每一步都有 skill 兜着，跑偏的概率就小很多。

建议从标准主流程那四步开始上手，跑顺了再加变体。

— END —

mattpocock/skills · 目录