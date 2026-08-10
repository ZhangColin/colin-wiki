# 索引

> 内容目录。按类别组织，每项一行 `[[链接]] — 一句话描述`。每次 ingest 后由 LLM 维护。
> 自动聚合见 `bases/全部页面.base`。

## 实体 (entity)

- [[Andrej Karpathy]] — AI 研究者，LLM Wiki 方法论提出者
- [[Obsidian]] — LLM Wiki 的落地载体/"IDE"，本地 markdown 知识管理软件
- [[Vannevar Bush]] — 1945 Memex 提出者，LLM Wiki 思想源头
- [[Dataview]] — Obsidian 插件，基于 frontmatter 做动态聚合（本 vault 实际用原生 Bases）
- [[运维有术]] — 公众号「术哥无界」，本 wiki 被引用最多的作者（LLM Wiki + mattpocock/Superpowers/gstack 三大系列源码解读）
- [[Matt Pocock]] — TypeScript 教育者/Total TypeScript 作者，mattpocock skills 作者
- [[小匠Skills]] — 公众号，中文圈 mattpocock skills 主要解读者（v1.0 总览/v1.1 更新/视频搬运系列）
- [[AI随风随风]] — B 站 UP，mattpocock skills 秒杀系统实战 demo 作者（第三方第二视角）
- [[鸟窝]] — Go 圈博主（smallnest），用 pigo 实测 Matt Pocock skills，提供外部使用者视角
- [[飞哥]] — 公众号「刷屏AI」/Vibe Coding，最早（2026-04-07）提出 superpowers+gstack 搭配
- [[Jesse Vincent]] — 开源老兵（obra），[[Superpowers]] 作者、RT 作者
- [[Garry Tan]] — YC 总裁，[[gstack]] 作者，投资 Coinbase/Instacart
- [[Anthropic]] — Claude/Claude Code 公司，Agent Skills 开放标准发起方（2025-12 捐 AAIF）
- [[Perplexity]] — Skills 设计方法论提出方（三层成本/Hub-and-Spoke/Gotchas 飞轮/四层 Eval）
- [[gstack]] — Garry Tan 的 AI 工程工具集（23 命令 + Sprint 工作流 + 无头浏览器）

## 概念 (concept)

- [[LLM Wiki]] — 人 sourcing、LLM 记账的持久复利知识库模式（本 wiki 核心主题）
- [[mattpocock skills]] — Matt Pocock 的 Claude Code skills 集，给 AI 编程加工程纪律
- [[Superpowers]] — Jesse Vincent 的 Claude Code skills 集（本 vault 自用），7 步强制流程 + 14 技能 + 3 铁律
- [[smart zone]] — LLM 会话 token 阈值（约 120k–150k，有争议），决定何时拆 session/handoff
- [[HITL 与 AFK]] — 人机分工判据：做决定→人在环，机械执行→agent 自主；含前台后台并行四道边界
- [[子代理驱动开发]] — Superpowers 核心执行模式：fresh subagent per task + 独立审查 + 上下文隔离
- [[Skills 设计方法论]] — Skills 即上下文工程（Anthropic/Perplexity 共识）：三层成本 + Hub-and-Spoke + Gotchas + 四层 Eval
- [[智能问数平台]] — NL2SQL 生产级形态：五层架构 + 语义层先行 + 权限贯穿（新领域种子概念）

## 源 (source)

### LLM Wiki 方法论
- [[LLM Wiki 方法论 gist]] — Karpathy 方法论原文（本 wiki 宪法） · 原文日期不详
- [[Karpathy 的 LLM Wiki 搭建实战]] — Obsidian 落地 LLM Wiki 的中文教程 · 2026-07-03

### mattpocock skills — 总览与主线
- [[mattpocock skills 标准工作流]] — 小匠Skills 的 v1.0 主流程与变体总览（中文入门） · 2026-07-07
- [[Matt Pocock 完整工作流视频]] — Matt 本人演示 skills main flow（一手源，含脏数据修复说明） · 2026-07-16
- [[MattPocock Skills v1.1 重磅更新]] — v1.1 更新：命令更名/Wayfinder/SDLC 闭环 · 2026-07-20
- [[Matt Pocock skills v1.1 官方更新日志]] — v1.1 官方英文一手 · 2026-07-08
- [[Matt Pocock 工作流五步主线鸟瞰]] — 鸟窝用 pigo 实测的五步主线概括（外部视角） · 2026-07-14
- [[MattPocock Skills 21 个 skill 工程纪律体系]] — 术哥 v1.1.0 全景总览（注：skill 数早期口径 21，源码核对为 18） · 2026-07-13
- [[Matt Pocock main flow 五环节]] — 多格式导出案例走完五环，"文件即纪律" · 2026-07-29
- [[Matt Pocock 6 个 skill 工程师本能]] — 6 个 promoted skill 纪律样张 · 2026-07-17
- [[Matt Pocock 三个 skill 结构词汇]] — codebase-design/prototype/improve + 4 核心词 + deletion test · 2026-07-23
- [[Matt Pocock writing-great-skills 八词诊断]] — skill 元设计 8 诊断词 + tight-review 改造示例 · 2026-07-24
- [[Matt Pocock setup-matt-pocock-skills 配置落地]] — setup 是 prompt-driven 对话不是脚本 · 2026-08-06

### mattpocock skills — wayfinder / handoff / 分流 / 并行 / 原型
- [[Matt Pocock skills Wayfinder 官方文档]] — Wayfinder SKILL 原文，操作细节权威源 · 2026-07-08
- [[Matt Pocock skills Handoff 官方文档]] — handoff 官方文档 · 早于 v1.1
- [[Matt Pocock wayfinder handoff 接力协议]] — 两种"装不下"区别 + 5 次会话接力案例 · 2026-07-30
- [[Matt Pocock 三条 on-ramp 分流]] — ask-matt 三入口（triage/diagnosing-bugs/wayfinder） · 2026-07-31
- [[Matt Pocock 后台 research 与主线程并行]] — HITL/AFK 前台后台并行 + 四道边界 + handoff≠AFK · 2026-08-07
- [[Matt Pocock Prototype 保真度方法论]] — UI/Logic 双分支 + 保真度光谱 · 2026-08-04

### mattpocock skills — grill / 调试 / Git 护栏
- [[grill-me 实战指南]] — grill-me(入口)/grilling(原语)分层 + 五规则（已被 grill-with-docs 取代） · 2026-07-20
- [[还在用 grill-with-docs]] — grill-with-docs = grilling + domain-modeling，Matt 推荐 coding 入口 · 2026-07-21
- [[diagnosing-bugs 与 tdd]] — red-capable command 硬约束 + 6 阶段诊断 vs Superpowers 4 阶段 · 2026-07-22
- [[Matt Pocock 的 Git 护栏]] — 命令前/提交前/冲突时三道护栏 + PreToolUse exit 2 · 2026-08-03

### Superpowers
- [[Superpowers 5万 Star 工程纪律框架]] — 7 阶段强制流程概览（v4.3） · 2026-02-15
- [[Superpowers 实战指南 7步流程14技能]] — 14 技能四分类 + 3 铁律（v5.0.7） · 2026-04-09
- [[Superpowers 7步闭环工作流深度指南]] — 优惠券核销实战端到端 · 2026-04-13
- [[Superpowers 7阶段交付SKU库存扣减]] — Python 电商库存扣减实战 · 2026-04-14
- [[Superpowers 6.0 reviewer 只读重写]] — v6.0 reviewer 结构性重写（两 reviewer 合一等） · 2026-06-22

### gstack 与插件搭配
- [[gstack 最佳实战]] — 23 命令 Sprint 工作流 + Bun/CDP 无头浏览器架构 · 2026-04-11
- [[Superpowers + gstack 搭配实战]] — 大脑/手脚三层不冲突 + 5 交接点 · 2026-04-12
- [[Claude Code 双插件搭配 superpowers 与 gstack]] — 飞哥最早提出搭配 + CLAUDE.md 五原则 · 2026-04-07

### 方法论对比
- [[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]] — 最小锚点+router vs hook 强制+红旗表 · 2026-07-09
- [[2026 年再看 Superpowers：grill-me 场景选型]] — grill-me vs Superpowers brainstorming 按目标产物选 · 2026-07-16
- [[Anthropic 与 Perplexity 的 Skills 设计方法论]] — 两家官方博客综述：9 类/3 层成本/4 层 Eval · 2026-06-12

### mattpocock skills 实战
- [[mattpocock skills 秒杀系统实战]] — AI随风随风 的秒杀系统 demo 实战（第二视角） · 2026-07-01

### 数据 / 架构（新领域）
- [[智能问数平台设计：五层架构与十步落地]] — NL2SQL 生产级平台：语义层先行 + 权限贯穿 · 2026-07-17

## 综合 (synthesis)

- [[mattpocock skills 推荐工作流速查]] — 按"活的大小/类型"给推荐路径（主线四步 / wayfinder 超大活 / 重构 / bug 修复），新手入口、常查导向
- [[Superpowers 与 mattpocock skills 路线对照]] — 两条 Claude Code skills 路线分歧（强制 vs 最小化）与共存结论
- [[Superpowers 与 gstack 搭配（大脑与手脚）]] — 双插件搭配范式，飞哥+运维有术独立得出一致结论

---

## 🅿️ Pending（观察中，达门槛再正式建）

> 被源提及但尚未独立建页。概念需 ≥2 篇来源、实体需专段讨论。

**概念**（多已覆盖于父概念页，待多源触发再独立建）：
- RAG — 主流检索增强生成范式，作为 LLM Wiki 的对照面
- Ingest / Query / Lint — LLM Wiki 三大操作（已覆盖于 [[LLM Wiki]]）
- Memex — Bush 1945 设想（绑定于 [[Vannevar Bush]]）
- Schema / AGENTS.md — 告诉 LLM 如何维护 wiki 的行为配置
- 复利产物 (persistent compounding artifact) — LLM Wiki 的核心产物特性
- vertical slice / tracer bullet — mattpocock skills 核心切片机制（已覆盖于 [[mattpocock skills]]）
- leading word / deletion test / red-capable command — mattpocock skills 子机制（已覆盖于相关 source）
- Claude Code — 本 vault 实际使用的 LLM agent
- 语义层 / Schema Linking — [[智能问数平台]] 核心子机制（1 源，待第二次出现）

**实体**（被提及但非专段讨论）：
- Codex / Cursor / OpenCode — 其他可用 LLM agent
- NotebookLM — RAG 代表，作为对照提及
- qmd — 本地 markdown 搜索引擎（规模大时引入）
- Thariq Shihipar（Anthropic）/ Henry Modisett（Perplexity）— 已在各自实体页内提及
