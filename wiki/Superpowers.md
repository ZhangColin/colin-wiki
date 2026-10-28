---
type: concept
title: Superpowers
domain: [AI编程, AI工具, 软件工程]
tags: [Superpowers, Claude Code, Agent Skills, TDD, 工程纪律, 强制触发]
sources:
  - "[[Superpowers 5万 Star 工程纪律框架]]"
  - "[[Superpowers 实战指南 7步流程14技能]]"
  - "[[Superpowers 7步闭环工作流深度指南]]"
  - "[[Superpowers 7阶段交付SKU库存扣减]]"
  - "[[Superpowers 6.0 reviewer 只读重写]]"
  - "[[Superpowers + gstack 搭配实战]]"
  - "[[Claude Code 双插件搭配 superpowers 与 gstack]]"
  - "[[去哪儿网 AI Coding 研发平台实践]]"
  - "[[Superpowers 开篇 幸福账本]]"
  - "[[Superpowers 安装与技能全景]]"
  - "[[Superpowers brainstorming 需求访谈]]"
  - "[[Superpowers writing-plans 施工图]]"
  - "[[Superpowers executing-plans 一步一确认]]"
  - "[[Superpowers TDD 红绿循环]]"
  - "[[Superpowers condition-based-waiting 条件等待]]"
  - "[[Superpowers testing-anti-patterns 测试反模式]]"
  - "[[幸福账本 REST API 基建]]"
  - "[[幸福账本 Vue 前端起步]]"
  - "[[Superpowers using-git-worktrees 工作树隔离]]"
  - "[[Superpowers finishing-a-development-branch 分支收尾]]"
  - "[[Superpowers dispatching-parallel-agents 并行派发]]"
  - "[[Superpowers subagent-driven-development AI 项目经理]]"
  - "[[Superpowers systematic-debugging 四阶段调试]]"
  - "[[Superpowers root-cause-tracing 根因追踪]]"
  - "[[Superpowers verification-before-completion 验证再收工]]"
  - "[[Superpowers defense-in-depth 纵深防御]]"
  - "[[Superpowers requesting-code-review 主动求审]]"
  - "[[Superpowers receiving-code-review 接受审查]]"
  - "[[Superpowers writing-skills 规矩固化成技能]]"
  - "[[Superpowers 组合实战1 从一句话到上线]]"
  - "[[Superpowers 组合实战2 线上大排查]]"
  - "[[Superpowers 结语 AI 技能树]]"
  - "[[Superpowers 全系列速查合集]]"
created: 2026-08-10
updated: 2026-10-22
status: active
aliases:
  - obra/superpowers
  - Superpowers skills
---

# Superpowers

> 一句话定义：[[Jesse Vincent]]（obra）开源的 Claude Code skill 集合（`github.com/obra/superpowers`），给 AI 编程代理套上"工程纪律框架"——用 7 阶段强制流程把 AI 从"急于写代码"变成"三思而后行"。是 [[mattpocock skills]] 的主要对照路线，也是本 vault 自身使用的 skills。

## 核心观点

### 1. 定位：不是更强，而是更稳

核心理念八字："不是更强，而是更稳"。每个技能都是一个强制性检查点——"不让 AI 想跳就跳"。Process over Prompt：与其调教提示词，不如让 AI 走固定的工程流程。

### 2. 7 步工作流

`brainstorming → using-git-worktrees → writing-plans → subagent-driven-development → test-driven-development → requesting-code-review → finishing-a-development-branch`

### 3. 14 个 Skills 四分类

协作类 9 个；测试类 1 个（test-driven-development）；调试类 2 个（systematic-debugging、verification-before-completion）；元类 2 个（writing-skills、using-superpowers）。

### 4. 强制触发机制

存在适用技能则 Agent 必须使用、无选择余地。设计坦承借用 Cialdini 说服学原理——权威性（提示词写"技能是强制性的"）、承诺（让 Agent 主动宣布使用）、社会证明（描述"始终"会发生什么）。详见 [[Anthropic 与 Perplexity 的 Skills 设计方法论]] 中"advisory vs hard gate"讨论。

### 5. 三条铁律（Iron Laws）

① 没有失败测试就不写生产代码（TDD）；② 不做根因调查就不修 bug（systematic-debugging）；③ 没有新鲜验证证据就不做完成声明（verification-before-completion）。三条对抗三种典型偷懒。老谭系列已将三铁律全部技能化展开：①→05 篇 [[Superpowers TDD 红绿循环]]、②→14 篇 [[Superpowers systematic-debugging 四阶段调试]]、③→16 篇 [[Superpowers verification-before-completion 验证再收工]]。

### 6. v6.0 reviewer 只读重写（2026-06）

表面是"约 2 倍速、近 50% 省 token"，实质是围绕 reviewer 角色的结构性重写：① 两 reviewer 合一（spec + quality 单 reviewer 一次 diff 出两裁决）；② reviewer 变只读怀疑论者（"Do Not Trust the Report"）；③ 文件替代粘贴（上下文经济学）；④ Progress Ledger 对抗 compaction 失忆；⑤ Model 强制声明。详见 [[Superpowers 6.0 reviewer 只读重写]]。

### 7. 企业级定制印证：Qsuperpowers（去哪儿，2026-06）

[[去哪儿网]] 在社区版 Superpowers 之上构建企业定制框架 **Qsuperpowers**——目前已知最完整的大厂生产级采用案例：

- **skill 名原样复用**：四阶段闭环直接使用 brainstorming / writing-plans / **subagent-driven-development** / executing-plans / **test-driven-development** / requesting-code-review（双轮审查）/ finishing-a-development-branch——印证 7 步流程在企业环境有效。
- **定位边界被明确表述**："通用 superpowers 提供流程标准化能力，但**不包含企业个性化需求、内部系统集成、工程规范落地**"——通用纪律层 + 企业集成层的分层结构。
- 企业增量在集成而非流程：飞书文档（需求解析）、AST（代码理解）、CI/CD（Noah 部署）、灰霸测试（质量验证）四大接入点。
- 效果：2pd+ 复杂需求 3pd→1pd、提效 66%、无 bug 上线（自述口径）。详见 [[去哪儿网 AI Coding 研发平台实践]]。

## 子代理驱动开发

核心公式 "Fresh subagent per task + two-stage review"。把大任务拆成独立子任务，每个子任务过两轮审查（规格合规 + 代码质量），审查者是独立 AI 代理。模型选择原则：机械实现用快速/廉价模型、集成判断用标准模型、架构/审查用最强模型。详见 [[子代理驱动开发]]。

### 8. 中文教学化传播：老谭幸福账本系列（2026-08～09，连载中）

[[超级奶爸老谭]] 继 mattpocock skills 教学系列后，于 2026-08-21 开启第二套系列——用「幸福账本」（家庭记账 Java 21 + Spring Boot 3 + H2 / Vue 3 + Element Plus 全栈项目）逐篇教 Superpowers 技能树，日更 08:05，**已完结**（2026-09-23 总结篇收官）：00–23 共 24 篇正文 + 1 篇总结篇，本 wiki 25 源全部收录。价值：① 首个把技能树系统翻译成 **Java 生态**的中文系列（BigDecimal/YearMonth/Awaitility 等对应写法）；② 两篇基建篇补齐"领域 → REST → Vue"全栈落地，技能教学有完整宿主项目；③ 每技能配"何时不要用"边界与真实踩坑叙事。注意：同一作者先后系统教学"路线之争"的两条路线（mattpocock 12 篇 → Superpowers 15+ 篇），本身是两路线面向不同读者、可共存的作者级注脚。入口 [[Superpowers 开篇 幸福账本]]。

## ⚠️ Star 数溯源（数据冲突，须以 GitHub 实时为准）

| 来源 | 日期 | star 数 |
| --- | --- | --- |
| [[Superpowers 5万 Star 工程纪律框架]] | 2026-02 | 51,400+ |
| [[Superpowers 实战指南 7步流程14技能]] | 2026-04-09 | 36.6K |
| [[Superpowers 7步闭环工作流深度指南]] | 2026-04-13 | 147,000+ |
| [[Superpowers + gstack 搭配实战]] | 2026-04-12 | 145K |

时间递增但数字先降后暴涨 4 倍——**高度存疑**（疑 147K 为笔误/夸大，36.6K 与 51,400+ 也倒挂）。引用须注明时点并以 GitHub 实时核对。

## ⚠️ 安装命令不一致

- `/plugin install superpowers@claude-plugins-official`（[[Superpowers 实战指南 7步流程14技能]]、[[Superpowers + gstack 搭配实战]]）
- `/plugin marketplace add obra/superpowers-marketplace` + `install superpowers@superpowers-marketplace`（[[Superpowers 5万 Star 工程纪律框架]]、[[Claude Code 双插件搭配 superpowers 与 gstack]]）

marketplace 名称不一，以官方 README 为准。

## ⚠️ 技能数口径随版本演进

v5.0.7（2026-04）**14 个**（协作 9 + 测试 1 + 调试 2 + 元 2，[[Superpowers 实战指南 7步流程14技能]]）→ 2026-08 仓库 **18 个**（协作 9 + 测试 3 + 调试 4 + 元 2，[[Superpowers 安装与技能全景]]）：testing 新增 condition-based-waiting / testing-anti-patterns，debugging 新增 root-cause-tracing / defense-in-depth。判为版本演进非矛盾；与 mattpocock skills 的 18→24 演进同构——**引用技能数量必须带版本/时点**。

## 与 [[mattpocock skills]] 的对照

两者都把 skill 当"塑造 agent 行为的代码"，但路线不同——Superpowers 重"流程纪律 + 强制触发 + 多平台"，mattpocock skills 重"skill 工程化标准（SKILL.md frontmatter 规范 + Wayfinder/Handoff 交接机制）"。详见 [[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]]。两路线共存可行：superpowers 的 hook 只强推自己那 14 个，与 mattpocock 互不冲突。

## 演变 / 时间线

- v4.3（2026-02）→ v5.0.7（2026-03-31）→ v6.0/v6.0.3（2026-06）。v6.0 是对 v5.x"两阶段审查"的结构性变更。
- 4 月那波"slow me down"社区批评针对 5.x；[[Jesse Vincent]] 在 6.0 公告明确"slow isn't fun"是改进动机。

## 相关页面

- **实体**：[[Jesse Vincent]] · [[gstack]] · [[Garry Tan]] · [[去哪儿网]] · [[超级奶爸老谭]]
- **来源**：[[Superpowers 5万 Star 工程纪律框架]] · [[Superpowers 实战指南 7步流程14技能]] · [[Superpowers 7步闭环工作流深度指南]] · [[Superpowers 7阶段交付SKU库存扣减]] · [[Superpowers 6.0 reviewer 只读重写]] · [[Superpowers + gstack 搭配实战]] · [[Claude Code 双插件搭配 superpowers 与 gstack]] · [[去哪儿网 AI Coding 研发平台实践]]
- **老谭教学系列（幸福账本，25 源·全系列完结）**：[[Superpowers 开篇 幸福账本]] · [[Superpowers 安装与技能全景]] · [[Superpowers brainstorming 需求访谈]] · [[Superpowers writing-plans 施工图]] · [[Superpowers executing-plans 一步一确认]] · [[Superpowers TDD 红绿循环]] · [[Superpowers condition-based-waiting 条件等待]] · [[Superpowers testing-anti-patterns 测试反模式]] · [[幸福账本 REST API 基建]] · [[幸福账本 Vue 前端起步]] · [[Superpowers using-git-worktrees 工作树隔离]] · [[Superpowers finishing-a-development-branch 分支收尾]] · [[Superpowers dispatching-parallel-agents 并行派发]] · [[Superpowers subagent-driven-development AI 项目经理]] · [[Superpowers systematic-debugging 四阶段调试]] · [[Superpowers root-cause-tracing 根因追踪]] · [[Superpowers verification-before-completion 验证再收工]] · [[Superpowers defense-in-depth 纵深防御]] · [[Superpowers requesting-code-review 主动求审]] · [[Superpowers receiving-code-review 接受审查]] · [[Superpowers writing-skills 规矩固化成技能]] · [[Superpowers 组合实战1 从一句话到上线]] · [[Superpowers 组合实战2 线上大排查]] · [[Superpowers 结语 AI 技能树]] · [[Superpowers 全系列速查合集]]
- **相关概念**：[[子代理驱动开发]] · [[mattpocock skills]] · [[Skills 设计方法论]]
- **综合**：[[Superpowers 与 mattpocock skills 路线对照]] · [[Superpowers 与 gstack 搭配（大脑与手脚）]]
- **对照**：[[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]] · [[2026 年再看 Superpowers：grill-me 场景选型]]
