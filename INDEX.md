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
- [[AgentScope Java]] — 开源 Java Agent 运行时框架（HarnessAgent/ReActAgent/Toolkit 三层）
- [[AI实战有术]] — Dilee（11 年 Java 老兵），AgentScope Java 系列解读作者
- [[腾讯程序员]] — 公众号（atreusliu/martinskxu/fitchzheng/leoshli），Harness 三部曲：落地规范 + 测评方案 + TAB 大仓实战
- [[ConardLi]] — 公众号「code秘密花园」，Harness 三次演进理论科普（腾讯落地文引用的理论源）
- [[白墨]] — 公众号「白哥Agent落地局」，企业 Agent 十二大工程框架作者
- [[SinKris]] — 公众号「Kris产品成长之路」，产品经理 8 个 AI 工作流（个人级 AI 应用视角）
- [[去哪儿网]] — 大型 OTA，AI Coding 组织级平台实践（QunarDevCenter / 天弦 QDO / Qsuperpowers）
- [[爱海贼的无处不在]] — 公众号「无处不在的技术」，业界实践深度解读（去哪儿李佳奇分享）
- [[Cole Medin]] — coleam00，30+ 开源 skills / Archon / 黑灯工厂实验作者
- [[老丁888]] — 公众号「钉子之家」，AI 观点解读（Cole Medin 黑灯工厂）

## 概念 (concept)

- [[LLM Wiki]] — 人 sourcing、LLM 记账的持久复利知识库模式（本 wiki 核心主题）
- [[mattpocock skills]] — Matt Pocock 的 Claude Code skills 集，给 AI 编程加工程纪律
- [[Superpowers]] — Jesse Vincent 的 Claude Code skills 集（本 vault 自用），7 步强制流程 + 14 技能 + 3 铁律
- [[smart zone]] — LLM 会话 token 阈值（约 120k–150k，有争议），决定何时拆 session/handoff
- [[HITL 与 AFK]] — 人机分工判据：做决定→人在环，机械执行→agent 自主；含前台后台并行四道边界
- [[子代理驱动开发]] — Superpowers 核心执行模式：fresh subagent per task + 独立审查 + 上下文隔离
- [[Skills 设计方法论]] — Skills 即上下文工程（Anthropic/Perplexity 共识）：三层成本 + Hub-and-Spoke + Gotchas + 四层 Eval
- [[智能问数平台]] — NL2SQL 生产级形态：五层架构 + 语义层先行 + 权限贯穿（新领域种子概念）
- [[Harness Engineering]] — Agent = Model + Harness：6 大支柱的运行环境工程学（OpenAI 提出，腾讯/AgentScope/Matt skills 多路落地）
- [[Agent 测评 Eval]] — 三类评分器 + 五维度 + 基线机制 + 能力/回归双轨（Perplexity 四层 Eval 与腾讯测评实践合成）
- [[AI Coding 自主度分级]] — 去哪儿 L0-L5 与 Dan Shapiro 五级双框架对照，L3 分水岭共识
- [[Data Agent]] — 数据智能消费形态：≠NL2SQL、三层次、事实-推测-行动信任链（白墨 + 智能问数平台双源）

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

### mattpocock skills — grill / 调试 / Git 护栏- [[grill-me 实战指南]] — grill-me(入口)/grilling(原语)分层 + 五规则（已被 grill-with-docs 取代） · 2026-07-20
- [[还在用 grill-with-docs]] — grill-with-docs = grilling + domain-modeling，Matt 推荐 coding 入口 · 2026-07-21
- [[diagnosing-bugs 与 tdd]] — red-capable command 硬约束 + 6 阶段诊断 vs Superpowers 4 阶段 · 2026-07-22
- [[Matt Pocock 的 Git 护栏]] — 命令前/提交前/冲突时三道护栏 + PreToolUse exit 2 · 2026-08-03

### mattpocock skills — v1.2 更新（2026-08）
- [[Matt Skills v1.2 grilling 重构]] — 一次一问→round-by-round frontier，13 问约 3 轮 + opt-out · 2026-08-11
- [[Matt Pocock wizard 人墙自动化]] — 人墙步骤→交互式 bash 向导（agent 只写不跑）· 2026-08-12
- [[Matt Pocock 会话边界五问决策树]] — PHASE-BOUNDARIES：Continue/clear/handoff/subagent/compact，compact 垫底 · 2026-08-13
- [[Matt Pocock Skills v1.2 更新全景]] — 4 处人机边界位移 + 双平台三态 + 24 promoted skills · 2026-08-14

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

### 企业级 AI Coding 与 Agent 落地实践
- [[Harness Engineering 团队落地规范]] — 腾讯团队级落地：6 支柱 + 3 阶段路线图 + harness-audit 审计 · 2026-07-17
- [[腾讯 Agent 与 Skill 测评方案]] — 腾讯测评体系：三类评分器 + 五维度 + 基线 + TPerf 实战 · 2026-06-16
- [[去哪儿网 AI Coding 研发平台实践]] — 组织级平台：L0-L5 分级 + Harness 四把锁 + 天弦/Qsuperpowers + Skills 治理 + 双估时 · 2026-06-24
- [[TAB 大仓 Harness 工程化实战]] — 腾讯 TAB 大仓：六层资产 + 13 棒接力 + 4 Agent 契约 + 基线反作弊 + Team Mode 撞墙复盘 · 2026-07-07
- [[Harness Engineering 三次演进与头部实践]] — ConardLi 理论科普：Prompt→Context→Harness 三次迁移 + Anthropic/OpenAI/LangChain 实践 · 2026-04-03
- [[企业 Agent 落地十二大工程框架]] — 白墨：六纵六横十二工程 + Data Agent/Ontology/Action 语义模型（上篇）· 2026-07-30

### 个人 AI 工作流（产品管理）
- [[产品经理的 AI 工作流]] — SinKris：8 个工作流（工具+Prompt+SOP）+ 80/20 分工公式 · 2026-08-03

### AI Coding 自主化（个人实验与分级框架）
- [[Cole Medin 黑灯工厂实验]] — Dark Factory：spec 进可上线代码出，治理文件宪法 + holdout 盲测 + 蓝绿部署 + 两盏灯 · 2026-08-19

### Java Agent 框架（AgentScope）
- [[AgentScope Java 2.0 正式版]] — v2.0.0 GA：运行时工程定位 + 三框架选型对照 · 2026-07-11
- [[AgentScope Java 2.0 上手 Web 接口]] — HarnessAgent 接 Spring Boot 最小闭环（关掉一切再逐步加）· 2026-07-14
- [[AgentScope Java 2.0 核心架构拆解]] — HarnessAgent/ReActAgent/Toolkit 三层 + 按层排查表 · 2026-07-15

### mattpocock skills 实战
- [[mattpocock skills 秒杀系统实战]] — AI随风随风 的秒杀系统 demo 实战（第二视角） · 2026-07-01

### 数据 / 架构（新领域）
- [[智能问数平台设计：五层架构与十步落地]] — NL2SQL 生产级平台：语义层先行 + 权限贯穿 · 2026-07-17

## 综合 (synthesis)

- [[mattpocock skills 推荐工作流速查]] — 按"活的大小/类型"给推荐路径（主线四步 / wayfinder 超大活 / 重构 / bug 修复），新手入口、常查导向
- [[Superpowers 与 mattpocock skills 路线对照]] — 两条 Claude Code skills 路线分歧（强制 vs 最小化）与共存结论
- [[Superpowers 与 gstack 搭配（大脑与手脚）]] — 双插件搭配范式，飞哥+运维有术独立得出一致结论
- [[MCP 与直连凭证对比]] — 为什么配置 MCP 而非直接给 agent 数据库凭证：结构性约束 + 凭证不进上下文

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
- 会话边界管理（五问决策树：Continue/clear/handoff/subagent/compact）— 已覆盖于 [[Matt Pocock 会话边界五问决策树]] 与 [[smart zone]]，机制已跨多源出现，待独立建页
- round-by-round frontier — grilling v1.2 推进机制（已覆盖于 [[Matt Skills v1.2 grilling 重构]]）
- Claude Code — 本 vault 实际使用的 LLM agent
- 语义层 / Schema Linking — [[智能问数平台]] 核心子机制，白墨 Data Agent 章节第二次出现（指标口径、数据源路由），已并入 [[Data Agent]]；是否独立建页待观察
- Action 语义模型 / Ontology 五阶段 — 白墨十二工程的本体工程核心（1 源，已覆盖于 [[企业 Agent 落地十二大工程框架]]），待第二次出现
- 企业 Agent 成熟度模型（L1-L5）— 白墨十二工程框架下篇将展开（上篇仅预告）
- 个人 Prompt 模板库 ≈ 个人版 Skills — SinKris 工作流与 Skills 的映射（推论，1 源，已覆盖于 [[产品经理的 AI 工作流]]）
- OpenAI — Harness Engineering 概念出处（2026-02 文章），暂在 [[Harness Engineering]] 内提及
- 双估时 / AI 估时压缩率与兑现率 — AI 提效进入排期计划的度量机制（1 源，已覆盖于 [[去哪儿网 AI Coding 研发平台实践]]）
- T/M/C 指标 / 出码率 Git Blame 法 — AI 参与度三指标与"人写 vs AI 写"区分方法（1 源，同上）
- Dan Shapiro — Glowforge 创始人，五级自主框架与"黑灯工厂"命名出处（暂在 [[AI Coding 自主度分级]] / [[Cole Medin 黑灯工厂实验]] 内提及）
- Archon / StrongDM holdout / Spotify Honk / Gas Town — 黑灯工厂谱系工具与前人尝试（暂在 [[Cole Medin 黑灯工厂实验]] 内提及）
- "复杂度自给自足"陷阱 / Harness 设计中的 YAGNI — TAB 撞墙复盘提炼（1 源，已覆盖于 [[TAB 大仓 Harness 工程化实战]]）
- 仓库文档 vs Memory（团队共识落仓库）— 已在 [[LLM Wiki]] §8 与 [[TAB 大仓 Harness 工程化实战]] 双向印证，是否独立建页待观察

**实体**（被提及但非专段讨论）：
- Codex / Cursor / OpenCode — 其他可用 LLM agent
- NotebookLM — RAG 代表，作为对照提及
- qmd — 本地 markdown 搜索引擎（规模大时引入）
- Thariq Shihipar（Anthropic）/ Henry Modisett（Perplexity）— 已在各自实体页内提及
