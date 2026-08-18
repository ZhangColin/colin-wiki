---
type: concept
title: mattpocock skills
domain:
  - AI编程
  - AI工具
  - 软件工程
tags:
  - mattpocock-skills
  - AI编程工作流
  - Claude Code
  - Skills
  - TDD
  - 工程纪律
sources:
  - "[[Matt Pocock 完整工作流视频]]"
  - "[[mattpocock skills 标准工作流]]"
  - "[[MattPocock Skills v1.1 重磅更新]]"
  - "[[mattpocock skills 秒杀系统实战]]"
  - "[[Matt Pocock skills v1.1 官方更新日志]]"
  - "[[Matt Pocock skills Wayfinder 官方文档]]"
  - "[[Matt Pocock skills Handoff 官方文档]]"
  - "[[MattPocock Skills 21 个 skill 工程纪律体系]]"
  - "[[Matt Pocock main flow 五环节]]"
  - "[[Matt Pocock 6 个 skill 工程师本能]]"
  - "[[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]]"
  - "[[Matt Pocock 后台 research 与主线程并行]]"
  - "[[Matt Pocock Skills v1.2 更新全景]]"
  - "[[Matt Skills v1.2 grilling 重构]]"
  - "[[Matt Pocock wizard 人墙自动化]]"
  - "[[Matt Pocock 会话边界五问决策树]]"
created: 2026-07-26
updated: 2026-08-17
status: active
aliases:
  - mattpocock/skills
  - Matt Pocock skills
  - Matt Pocock Skills
---

# mattpocock skills

> 一句话定义：[[Matt Pocock]] 开源的一套 Agent skills（GitHub 212K★/skills.sh 13.5M 下载，2026-08），给 AI 编程加上工程纪律——用 grill 盘问对齐需求、用 spec/tickets 拆分跨 session 的工作、用 implement + 内置 code review 兜底质量。是 Claude Code 生态最主流的 AI 工程化工具集，v1.2 起朝通用 agent 技能标准走（Claude + Codex 双 harness）。

## 核心观点

### 1. 定位：把"想到哪写到哪"变成"有纪律的工程"

vibe coding 的痛点是需求跑偏、上下文散掉、改到一半忘了目标。mattpocock skills 给 AI 编程加一条"标准流水线"——每一步都有 skill 兜着。对标 GSD/BMAD/Spec-Kit，但更强调**简单、自由、好组合**（[[Matt Pocock]] 批评 GSD 等"太复杂、剥夺控制权"），理念是"模型越强，技能应越简单"。

### 2. 两类 skill：user-invoked vs model-invoked

- **user-invoked**：你输入 `/xxx` 手动触发，负责编排主流程。
- **model-invoked**：任务匹配时 agent 自动调用，承载可复用规范（code review、domain modeling 等）。
- 规则：**user-invoked 可调用 model-invoked，反之不行**。主流程由你手动驱动；规范类在合适时机自动介入，不用排程。
- **轻量设计**：skills 多为 user-invoked，descriptions 短而精——全套 skills 只占 **660 tokens**（对比其他 repo 会把大量描述 leech 进 context）。

### 3. 主流程（main flow）

```
/grill-with-docs →（分岔）→ /implement  或  /to-spec → /to-tickets → /implement
```

- **/grill-with-docs**：基于一个 idea（可极模糊）盘问，探索代码，把"我想改 X"变成 crisp defensible plan；同时把学到的写入 `context.md` 与 ADR。一次约 20 问（v1.2 起因 grilling round-by-round 重构，13 问约 3 轮问完，见 [[Matt Skills v1.2 grilling 重构]]）。
- **分岔点**：工作量 ≤ 单 session smart zone → 直接 `/implement`；> 单 session → `/to-spec` → `/to-tickets`。
- **/to-spec**：把整段讨论压缩成 spec（目的地/终态描述：problem statement、solution、user stories、implementation/testing decisions），发布到 issue tracker，用于最终对照验收。
- **/to-tickets**：把 spec 拆成 implementation plan，每个 ticket = 一个 smart zone 的**垂直切片**工作量。
- **/implement**：清 context，逐个 ticket 实现；内置 typecheck/build/verification + code review，最后 commit。

### 4. 核心概念

- **vertical slice（垂直切片）**：一个 ticket 跑通一条需求（后端到前端），而非按层横切。AI 默认爱"横向"（先把所有 DB 层做完），vertical slice 强制纵向贯通。
- **smart zone（约 120k–150k token）**：[[Matt Pocock]] 的经验法则——LLM 在一定 token 预算内是"聪明区"，超过会出现 attention degradation、幻觉。判断工作要否拆分，就看会不会撑过这条线。**阈值有争议**（不同一手源给 120k/125-150k/140k），详见 [[smart zone]]。
- **sub-agent 做 code review**：main agent 写完代码会护短（"我写的，挺好"）；拆给 sub-agents，它们有清晰 context window，审查更客观。这是 code review 必须用 sub-agent 的根本理由。
- **issue tracker 无关**：skills 读本地配置——GitHub Issues、本地 markdown、Jira、Linear、Beads 都支持，setup 时告诉 agent 即可。

### 5. 自动介入的规范类 skill（model-invoked）

- **/code-review**：双轴审查——**Spec 轴**（对照原 spec 防漏/未specified）+ **Standards 轴**（仓库编码标准；无则回落 Martin Fowler code smells）。
- **/domain-modeling**：主动构建/锐化领域模型，对照术语表挑刺，更新 context.md/ADR。
- **/codebase-design**：设计"深模块"的共同规范（小接口后面藏大量行为）。
- **/research**：针对高可信一手来源调研，输出带引用的 Markdown。

### 6. 辅助与交接

- **/ask-matt**：作者把自己做成 skill——知道整个 repo 怎么用，推荐 main flow。
- **/grill-me**：和 grill-with-docs 一样盘问但不写文档（stateless），纯对齐。⚠️ **Matt 本人已在 AI Hero 官方页声明 coding 场景不再推荐 `/grill-me`，改推 `/grill-with-docs`**（`/grill-me` 退为"非编码压力测试/临时轻量对齐"）。v1.2 起 grill-me 是 7 行薄封装（正文仅 "Run a /grilling session."），核心逻辑全在 `/grilling` 原语（**只装 grill-me 不装 grilling 会 nothing happens**）。演进链详见 [[grill-me 实战指南]]、[[还在用 grill-with-docs]]；grilling v1.2 重构见 [[Matt Skills v1.2 grilling 重构]]。
- **/handoff**：把当前对话**压缩**（compaction）成交接文档，让 fresh agent 跨 session/agent 接手。**何时用**：接近 context limit、收工、或交给另一个 agent。**产出**：只载"活的线索"（在飞的事+为什么）+ suggested skills + references（spec/ADR/issue 路径，不复制）+ redacted secrets；存 OS tmp。**续接**：新 session 把 handoff.md 贴进去当 bootstrap prompt，告诉新 agent read 什么 / skip 什么 / not touch 什么。详见 [[Matt Pocock skills Handoff 官方文档]]。
- **/prototype**：构建可丢弃原型回答设计问题（UI 类做多个激进变体；逻辑类做能跑的终端程序）。
- **/diagnosing-bugs**：纪律化诊断循环（reproduce → minimise → hypothesise → instrument → fix → regression-test）。
- **/teach**：生成可交互教学课程，拉权威资料（不靠模型知识库，联网搜官方文档）。

## ⚠️ v1.1 演进（2026-07）

v1.1 是对整套体系的规范化重构，旧用户需注意：

| 变化 | v1.0（旧） | v1.1（新） |
| --- | --- | --- |
| 命令更名 | `/to-prd` | `/to-spec`（产出含技术架构/领域模型，叫 Spec 更精准） |
| 命令更名 | `/to-issues` | `/to-tickets`（合并原 to-plan，拆垂直切片工单） |
| 实现路径 | 仅 `/tdd` 单路径 | + `/implement`（非 TDD，适配快速迭代/小功能） |
| Grill 盘问 | 有交互 bug | 修复 + 区分 Facts（代码可查）vs Decisions（须问用户）+ 确认闸门 |
| Code Review | 基础 | Martin Fowler code smells 体系 + 双轴 |
| **新工具** | — | **/wayfinder**（大型项目规划，配 /research、/prototype） |

→ **影响**：[[mattpocock skills 标准工作流]]、[[mattpocock skills 秒杀系统实战]] 两篇 source 用的是 v1.0 旧命令名（`/to-prd`、`/to-issues`），在 v1.1 后已被取代——读到那里请按 v1.1 名称对照。

### Wayfinder（v1.1 重点）

专门解决**大型项目超出单 Agent 会话上下文**的痛点：把巨型活画成 issue tracker 上的一张 map（`wayfinder:map`），下挂 **decision tickets**，**一个 session 只解一个 ticket**，逐个攻坚直到 "the way is clear"。

- **两种模式**：Chart the map（画地图，6 步）+ Work through the map（走地图，5 步）。
- **四种 ticket**：Research（AFK 查文档）/ Prototype（HITL 提保真，前端几乎必用）/ Grilling（HITL 一次一问，默认）/ Task（决策前的手动活）。
- **铁律**：一 session 一 ticket（research 例外）；**Plan, don't do**——每票解一个决策而非一段构建。
- **判据**：能精准**提问**就是 ticket；还问不精准就是 fog of war（写进 Not yet specified，等雾散再 graduate）。
- 适用：全新开源项目、多端联动大功能、大规模重构。基础小需求仍沿用经典四步，不强制替换。

完整流程与概念体系见 [[Matt Pocock skills Wayfinder 官方文档]]；按场景选流程见 [[mattpocock skills 推荐工作流速查]]。

## ⚠️ v1.2 演进（2026-08-05 发布，3 天 3 版至 1.2.3）

v1.2 的主线是 **4 处人机分工边界的位移 + grilling 重构 + 双平台**，详见 [[Matt Pocock Skills v1.2 更新全景]]：

| 变化 | v1.1 | v1.2 |
| --- | --- | --- |
| **grilling 推进** | 一次一问（13 问 13 轮） | **round-by-round frontier**（13 问约 3 轮，每题带推荐答案；争议设计可 opt-out 回一次一问） |
| **wizard** | in-progress | 毕业进 Engineering、转 model-invoked——人墙步骤（配密钥/开面板/一次性迁移）agent 自动伸手，产物为交互式 bash 向导（agent 只写不跑） |
| **to-questionnaire** | in-progress | 毕业进 Productivity——"grill the send, not the subject"，挖别人脑子里的知识 |
| **wait-what** | 不存在 | 新增 7 行——没听懂时按 CONTEXT.md 领域语言重讲，非压缩 |
| **prototype** | throwaway 用完删除（TUI shell） | **留档**：`prototype/<name>` 分支 + context pointer；logic 分支改单文件可分享 HTML |
| **writing-great-skills** | 旧名 | 改名 **writing-for-agents**，转 model-invoked |
| **平台** | 只有 Claude | Claude plugin（官方 marketplace 收录，订阅制，24 个 promoted skill）+ Codex 元数据（openai.yaml）+ skills.sh（fork 制）三态 |
| **减法** | — | 删 6 个旧 skill；wayfinder 引入 decision ticket |

→ grilling 重构机制与社区定位（"grill-me is the pressure-test primitive; Superpowers is the default engineering OS"）见 [[Matt Skills v1.2 grilling 重构]]；wizard 深拆见 [[Matt Pocock wizard 人墙自动化]]；会话边界管理（Continue/clear/handoff/subagent/compact 五问决策树）见 [[Matt Pocock 会话边界五问决策树]]。

## 本地化对照：与本 vault 自用 skills 的关系

本 wiki（colin-wiki）自己运行的 **superpowers / pm-execution** skills 与 mattpocock skills 是同一类东西——都是给 LLM agent 加工程纪律的 skill 集，且都通过 SKILL.md + user/model-invoked 机制工作。侧重不同：

- **mattpocock skills**：偏 **AI 编程工程纪律**（需求 → spec → ticket → 实现 → review 的软件交付链）。
- **superpowers**（本 vault 用）：偏**通用 agent 纪律**（brainstorming、systematic-debugging、TDD、verification 等跨领域方法论）。

两者在 TDD、code review、计划/规格化等理念上高度同源，可互相参照。一个具体差异：mattpocock 的 `/grill-*` 强调"把问题问清楚、追问到底"（[[mattpocock skills 秒杀系统实战]] 认为比一般 brainstorming/prime 模式更深），与本 vault 的 superpowers brainstorming 形成对照。

## 证据 / 来源

- [[Matt Pocock skills v1.1 官方更新日志]] — [[Matt Pocock]] 官方博客 v1.1 changelog（英文一手）：main flow 官方图、改名理由、implement/code-review/tdd 原话。
- [[Matt Pocock skills Wayfinder 官方文档]] — `wayfinder/SKILL.md` 原文（英文一手）：Wayfinder chart/work 流程与 map/ticket/fog 概念体系，操作细节权威源。
- [[Matt Pocock 完整工作流视频]] — [[Matt Pocock]] 本人教程（一手源，权威）：安装/setup/grill/spec/tickets/implement 全流程 + smart zone + sub-agent review。
- [[mattpocock skills 标准工作流]] — [[小匠Skills]] 的 v1.0 工作流总览（中文圈系统解读）。
- [[MattPocock Skills v1.1 重磅更新]] — [[小匠Skills]] 的 v1.1 更新日志解读（命令更名、Wayfinder、SDLC 闭环）。
- [[mattpocock skills 秒杀系统实战]] — [[AI随风随风]] 的实战 demo（第二视角）。

## ⚠️ 矛盾 / 未解问题

- **仓库 stars 数据各源不一致**：视频录制时 162K、YouTube 描述 170K；7 月各源 150K-170K；[[Matt Skills v1.2 grilling 重构]] 记 **212.2K**（2026-08-10 检索，全站第 19 名，skills.sh 13.5M 下载）；[[Matt Pocock Skills v1.2 更新全景]] 记 **213.4K**（2026-08-14）。属时间点正常增长，非矛盾——本页取**最新 212K+**（2026-08）。增长曲线：5 月 77K → 7 月 170K → 8 月 212K。
- **skill 数量口径随版本演进（重要）**：v1.1.0 源码核对为 **18 个**（12 user-invoked + 6 model-invoked，"21 是早期流传数"）；v1.2 plugin 显式列出 **24 个 promoted skill**——引用 skill 数必须带版本号。
- **smart zone 阈值未收敛**：ask-matt ~120k / dictionary 125-150k / YouTube ~140k 三处不一，已沉淀到 [[smart zone]] 概念页统一标注为"约 120k–150k（有争议）"。
- **/grill-me 已被作者公开取代**：coding 场景 Matt 改推 `/grill-with-docs`/`domain-model`，新链路 `/grill-with-docs → /to-prd → /to-issues → tdd`。引用 `/grill-me` 时须标注此演进（详见 [[grill-me 实战指南]]、[[还在用 grill-with-docs]]）。
- **Grill 与 brainstorming 的边界**：[[mattpocock skills 秒杀系统实战]] 认为 grill 比一般 brainstorming/prime 模式问得更深、更追问；这是设计意图差异，非冲突。
- *未解*：Wayfinder 是否会逐步取代基础四步流程用于中型项目，待后续版本观察。

## 相关页面

- **来源**：[[Matt Pocock 完整工作流视频]] · [[mattpocock skills 标准工作流]] · [[MattPocock Skills v1.1 重磅更新]] · [[mattpocock skills 秒杀系统实战]] · [[Matt Pocock skills v1.1 官方更新日志]] · [[Matt Pocock skills Wayfinder 官方文档]] · [[Matt Pocock skills Handoff 官方文档]] · [[MattPocock Skills 21 个 skill 工程纪律体系]] · [[Matt Pocock main flow 五环节]] · [[Matt Pocock 6 个 skill 工程师本能]] · [[Matt Pocock 三个 skill 结构词汇]] · [[Matt Pocock writing-great-skills 八词诊断]] · [[Matt Pocock setup-matt-pocock-skills 配置落地]] · [[Matt Pocock wayfinder handoff 接力协议]] · [[Matt Pocock 三条 on-ramp 分流]] · [[Matt Pocock 后台 research 与主线程并行]] · [[Matt Pocock Prototype 保真度方法论]] · [[grill-me 实战指南]] · [[还在用 grill-with-docs]] · [[diagnosing-bugs 与 tdd]] · [[Matt Pocock 的 Git 护栏]] · [[Matt Pocock 工作流五步主线鸟瞰]] · [[Matt Pocock Skills v1.2 更新全景]] · [[Matt Skills v1.2 grilling 重构]] · [[Matt Pocock wizard 人墙自动化]] · [[Matt Pocock 会话边界五问决策树]]
- **综合**：[[mattpocock skills 推荐工作流速查]] — 按场景选流程的速查页（新手入口）
- **子概念**：[[smart zone]] · [[HITL 与 AFK]] · [[Skills 设计方法论]]
- **实体**：[[Matt Pocock]] · [[小匠Skills]] · [[运维有术]] · [[鸟窝]]
- **对照**：[[Superpowers]]（本 vault 自用，路线之争见 [[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]]）· [[Jesse Vincent]]
- *pending（待独立建）*：vertical slice · Claude Code · leading word · deletion test（已覆盖于本页与相关 source，待多源触发再独立建）
