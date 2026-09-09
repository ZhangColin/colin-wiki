---
type: concept
title: "Harness Engineering"
domain: [AI编程, 软件工程]
tags: [Harness Engineering, Agent Harness, 驾驭工程, 上下文管理, 团队规范]
sources:
  - "[[Harness Engineering 团队落地规范]]"
  - "[[Harness Engineering 三次演进与头部实践]]"
  - "[[AgentScope Java 2.0 正式版]]"
  - "[[Matt Pocock Skills v1.2 更新全景]]"
  - "[[去哪儿网 AI Coding 研发平台实践]]"
  - "[[腾讯 Agent 与 Skill 测评方案]]"
  - "[[Cole Medin 黑灯工厂实验]]"
  - "[[TAB 大仓 Harness 工程化实战]]"
  - "[[企业 Agent 落地十二大工程框架]]"
  - "[[DeepSeek Harness 源码拆解]]"
created: 2026-08-17
updated: 2026-09-09
status: active
aliases:
  - 驾驭工程
  - Agent Harness
  - Harness 工程
---

# Harness Engineering

> 一句话定义：把 Agent 的运行环境当工程系统来设计的学科——**Agent = Model + Harness**，模型提供智能，Harness（缰绳/马具，引申为"驾驭"）让智能变成生产力：工具调用、上下文管理、权限校验、状态持久化、执行编排、评估验证、约束恢复、记忆系统。

## 核心观点

### 出处与证据

OpenAI 2026-02 文章《Harness Engineering: Leveraging Codex in an Agent-First World》：一个 3 人（后扩 7 人）工程师团队，**完全禁止手写代码**，用 AI Agent 5 个月写了 100 万+ 行代码、合并 1500 个 PR、效率约 10 倍。核心公式：**Agent = Model + Harness**——LLM 本身无状态、无工具、无记忆，Harness 层是给模型装上"手脚和记忆"的工程基础设施；你写的所有代码、配的所有规则，都是 Harness 的一部分。

### 演进视角：Prompt → Context → Harness（三次重心迁移）

[[ConardLi]]（[[Harness Engineering 三次演进与头部实践]]，2026-04，腾讯落地文所引的理论源）给出概念史框架——三层**层层递进、后者包含前者**，不是取代关系：

| 阶段 | 解决的问题 | 核心命题 | 天花板 |
| --- | --- | --- | --- |
| Prompt Engineering | 表达问题（怎么说） | 模型不是不会，是没讲清楚 | 不能替代事实本身，解决不了「信息问题」 |
| Context Engineering | 信息问题（给什么） | 对的信息、对的形式、对的时刻 | 只作用在输入侧，解决不了执行偏航 |
| Harness Engineering | 过程问题（怎么持续做对） | 持续监督、约束、纠偏、收敛 | —（当前前沿） |

LangChain 工程师定义：**Harness = Agent - Model**。「模型的能力上限由 OpenAI、Anthropic 决定，Harness 由工程师决定」；LangChain 仅改造 Harness 不动模型，Terminal Bench 2.0 得分 52.8 → 66.5（Top 30 外 → Top 5）——Harness 质变的硬证据。

### 头部公司实践（ConardLi 汇总）

- **Anthropic** 两个失败模式：①「上下文焦虑」（接近窗口极限模型想赶紧收尾）→ 解法 **Context Reset**（换干净上下文新 Agent + 交班交接，而非 Compaction 压缩——像进程重启 vs 清缓存）；②「自评失真」（自己评自己偏乐观）→ 解法 **generator + evaluator 分离**（后扩展为 planner + generator + evaluator），「生产与验收必须分离」，Evaluator 实际操作页面而非读代码打分。
- **OpenAI**：「修复方案几乎从来不是'更努力'，而是'缺了什么结构性的能力'」；~100 行 AGENTS.md 目录页 + CI 校验文档新鲜度 + 「文档园丁」Agent；架构约束写进系统、检查结果自带修复提示；后台 Agent 持续扫描打质量分。
- **三家同构**：都在回答六问——模型看到什么/能做什么/下一步做什么/如何连续工作/怎么知道对不对/出错怎么拉回来。

### 为什么需要：Vibe Coding 三致命问题

| 问题 | 现象 | 后果 |
| --- | --- | --- |
| 架构混乱 | Agent 走捷径，功能 A 用库 X、功能 B 用库 Y，无分层 | 换底层逻辑整个项目大改 |
| 上下文雪崩 | 项目超 50 文件后 Agent "忘事"（user_id → uid） | 越大越蠢，修一冒二 |
| 可维护性丧失 | 开发黑盒，只有 Agent 知道代码怎么来的 | 人接手不如重写 |

### 6 大支柱（及工具映射）

1. **上下文管理**：渐进式披露（AGENTS.md ~100 行目录索引，OpenAI 自己踩过"巨型 AGENTS.md"坑）、Spec 进 Git 当长期记忆、changes/ 变更隔离、Skills 三层按需加载、知识库挂载、AI Wiki 代码知识化。
2. **工具系统**：MCP（连接外部世界）+ Skills（封装专家经验）+ 知识库（注入业务上下文）——比喻：**MCP 是开门的钥匙，Skills 是开门后做的事，知识库是进门前读的说明书**。
3. **执行编排与多 Agent 协作**："3+1 Phase"（计划→编码→交付→沉淀）；四角色 Planner/Generator/Evaluator/Archiver；SDD 工作流 `requirements.md → 人工审核 → task.md → 执行 → 归档`。
4. **状态与记忆**：短期（会话）/ 中期（Memories）/ 长期（Git 里的 Spec 文件）/ 变更记忆（Spec Deltas）。
5. **评估与观测**：四层——L1 语法（编译/Lint）→ L2 逻辑（单测）→ L3 规范（Rules 合规）→ L4 架构（不破坏现有设计）。
6. **约束与恢复**：三级约束（Rules 硬性红线 / Skills 软性约束 / Safety 兜底）+ Git 回滚、编译失败自动回退。

### 团队落地要点（腾讯样本）

- **3 阶段路线图**：基础建设（1-2 周：工具+基础 Rules+知识库）→ 工具接入（2-4 周：MCP+Skills+SDD）→ 持续优化（度量看板+知识飞轮）。
- **team-harness 仓库**：团队规范唯一真实来源，脚本/CI 自动同步到各业务项目——"让工具适配规范，而不是人适配工具"。
- **harness-audit Skill**：把整套规范固化成可执行的合规性审计（7 维度打分 S/A/B/C/D）——**"Skill 就是规范的可执行版本"**；审计报告是体检结果不是 KPI。
- 团队红线 5 条：先 Spec 后 Code / Rules 共享 / Skill 沉淀 / MCP 优先 / 变更可追溯。
- 8 反模式：巨型 Prompt、跳过审核、Rules 不维护、MCP 过度接入、Skill 不原子化、盲信 AI 输出、Chat 历史当文档、一个 PR 改所有。

### 大仓实操复盘（TAB 样本，2026-07）

[[TAB 大仓 Harness 工程化实战]]（腾讯 A/B 实验平台，30+ 微服务大仓）给出目前**颗粒度最细的落地复盘**，六层资产：Rule / Skill / Sub Agent / Workflow / Scripts / MCP：

- **"能判定的就别留在 Rule 里"**——自然语言天然被解释性执行，规则全部下沉 lint/脚本；比腾讯规范篇的 Rules 红线更进一步（起步先有规则共识，成熟态规则脚本化）。
- **"能写成 bash 的就别让 Agent 跑"**——4 个认知 Agent（需求/方案/开发/审查）+ 总控，纯确定性工作（调度/门禁/测试/MR）全部脚本化。
- 13 棒接力 Workflow + **最重纪律"下游不可改上游产物"** + 7 道门禁（基线对比剥夺"历史问题"解释权、软门禁留伤疤）+ 5 个人工关卡（"模型对自己生成的东西天然没有否决欲望"）。
- 撞墙方法论："**复杂度自给自足**陷阱"（机制主要为 fix 自己引入的副作用时 80% 该删）+ YAGNI。
- 金句："AI 的瓶颈早就不是模型，是协作、流程、信任"；"半自动比全自动更难，但更值得做"。

### 个人/小团队极致实践（Cole Medin 黑灯工厂，2026-08）

[[Cole Medin 黑灯工厂实验]] 把 Agent = Model + Harness 落到**个人可复现**的形态，与腾讯/去哪儿的大厂平台路线互补：

- **三层 = 公式具象化**：Archon（harness 管道，不可替换）+ Claude Code（agent，可换）+ Sonnet/Haiku（模型，逐节点可换）——"模型路由是全系统成本最低的杠杆"，可替换零件与不可替换管道分清。
- **约束支柱的极端形式**：三份治理文件是"宪法"，铁律**工厂永远不能改自己的宪法**；禁区（认证/流量控制/部署配置/治理文件）任何 PR 一碰先自动打回——"自主系统有多可信，取决于它对自己有哪些事永远改不了"。
- **评估支柱的新变体**：holdout 盲测场景——验收标准 builder 永远看不到，防"挟持验收标准"（见 [[Agent 测评 Eval]]）。
- 反面教训：调度器曾让 LLM 决定派发任务→"幻听"出不存在的任务→改纯 bash——**机械执行归自动化，决策不轻信模型**。

### 企业级平台实践（去哪儿样本，2026-06）

[[去哪儿网]]（李佳奇团队）给出目前**最操作化的 Harness 定义**：**Harness = AI 研发过程控制能力**——衡量 AI 在流程中是否被稳定触发、被约束、被隔离、被审查（可控、可复用、可审计、可规模化）。落地为两件套：

- **12 环节控制点**：需求文档→需求评审→方案设计→代码编写→代码测试→Checklist→开发自测→Code Review→QA 测试→自动化回归→灰度验证→生产发布，每环节拆成"AI 动作 + 约束 + 人工节点"三段式。
- **四把锁**：① AI 触发机制（Skills/Workflow/Agent 流程化调用）② 约束与门禁（模板/规范/准入/失败拦截）③ 安全隔离环境（沙箱，AI 不直接进生产链路）④ 人工审查节点（需求/设计/合入/灰度/发布保留确认权）。
- 金句："**决定 AI Coding 上限的不是模型，是 Harness**——没有 Harness 的 AI Coding 系统，就像没有方向盘的车"。
- 与腾讯样本互补：腾讯走"规范 + harness-audit 审计"路线，去哪儿走"平台 + 数据度量（Harness 等级作为质的指标之一）"路线。

### 企业 Agent 方法论综合（白墨十二大工程，2026-07）

[[白墨]]（[[企业 Agent 落地十二大工程框架]]）把企业 Agent 落地拆为**十二大工程**——六纵（Prompt → Context → Skill → Agent → Harness → Loop，能力递进）+ 六横（Data / Ontology / Process / Evaluation / Safety-Governance / Product-UX，托底支撑）：

- **白墨的 Harness 是"窄定义"**：十二工程之一，定位「企业 Agent 的**运行控制面**」——把模型、Agent、Skill、工具、数据、本体、流程、权限、监控、日志和成本控制整合成可运行系统；15 步运行编排（权限判断与安全审核嵌在执行链）、模型路由、成本控制因果回环（「便宜模型省下的调用费被错误、重试和人工接管吃回去」）。
- **三方边界**：Process 管业务怎么运转 / Agent 管任务怎么完成 / Harness 管系统怎么稳定运行。
- **对 6 支柱的补全**：白墨把 OpenAI 支柱里较隐性的部分独立成工程——Ontology（业务语义底座，含 Action 语义模型、状态机、指标口径）、Data（数据源权威性、可信数据管道）、Process（端到端业务流转）、Loop（错误归因到具体工程层 + 全要素版本快照）、Safety-Governance、Product-UX——是企业级落地的「工程清单化」。
- 关键取舍观：「多 Agent 不是默认优于单 Agent」；本体「不能过早重型建设」；「持续进化≠不受控自动改生产」（五道生产安全闸门）。

### 开源运行时底座（DeepSeek Harness，2026-08）

[[DeepSeek Harness 源码拆解]]（[[翟星人的实验室]]）给出「Agent = Model + Harness」公式的**开源框架级实现**（MIT，约 80k stars，Developer Preview），与前面的大厂平台/方法论样本互补：

- **Everything is a plugin**：基于 [[Cordis]] 插件运行时，模型适配、工具、会话、Loop、沙箱、子 Agent、MCP Client、Skill Provider、Hook Bridge 全部插件化——Hook Bridge 把 Claude Code/Codex 的 hooks.json 翻译成 Cordis 拦截点，三套机制一套生命周期。
- **Harness 六个关切的具体实现**：上下文管理（System Prompt 插件化 Section 注册 + 前缀稳定复用 KV Cache + AGENTS.md 字节预算）、状态与记忆（Session Event Log 事实流 + 多投影，见 [[Session Event Log]]）、权限（软 System Prompt / 硬 Guard+文件策略两层 + 单调 Guard + SandboxMode 三档仅约束文件副作用）、约束恢复（工具调用先记 `tool/call` 再执行、崩溃恢复语义「开始过、结果未知」）、执行编排（Turn/Step 两级循环 + 子 Agent Spawn/Fork/composeFrom）。
- **缺口印证 Eval 支柱**：BENCHMARK.md 只讲怎么跑、无公开结果/轨迹/权限/Token 预算/失败口径——正是 [[腾讯 Agent 与 Skill 测评方案]]「基线反作弊」要解决的框架层问题。
- **范式对比**：LangChain 向上封装（降开发门槛）vs DSH 向下拆开（框架层透明）；与 [[AgentScope Java]] 形成「Java 轻量 vs 插件化重量」对照。

## 与本 wiki 其他概念的关系（跨源同构）

- **[[mattpocock skills]] / [[Superpowers]] 本质都是 harness**：SKILL.md + 触发机制 + 工程纪律 = 给模型装的"马具"；Matt v1.2 的 harness-neutral（#781 去专有工具名）与双 harness 支持（Claude/Codex）正是"同一 harness 跨模型"的方向。
- **Anthropic 的 Context Reset 与 [[smart zone]] / [[Matt Pocock 会话边界五问决策树]] 同题异构**：都是解决"上下文快满时怎么办"——Anthropic 主张换干净上下文新 Agent + 交班（进程重启），Matt 用五问决策树（handoff 接力 / compact 垫底），Anthropic 证据显示压缩（compaction）不能解决"上下文焦虑"，为 Matt 把 compact 垫底提供了头部公司同源印证；「自评失真」亦是 [[Agent 测评 Eval]] 与 [[Cole Medin 黑灯工厂实验]] holdout 盲测的理论根基（推论，跨源连接）。
- **[[AgentScope Java]] 是 harness 的 Java 框架实现**：`HarnessAgent` 直接以 harness 命名，6 大支柱中的状态、权限、事件流、沙箱全部框架化。
- **[[Skills 设计方法论]]（Anthropic/Perplexity）** 对应支柱一（上下文管理）与支柱二（Skills）的理论化；其 Eval 柱已独立展开为 [[Agent 测评 Eval]]——腾讯两篇（落地规范 + 测评方案）分别对应支柱三（执行编排）与支柱五（评估与观测）的"建 + 验"闭环。
- **[[去哪儿网]] 是 harness 的大厂平台化样本**：四把锁与 6 大支柱同构（触发↔工具系统、门禁↔约束、隔离↔恢复、审查↔评估观测），并把"Harness 等级"本身做成可度量的质量指标；[[Cole Medin]] 是个人/小团队样本——同一公式（Agent+Model+Harness）在两个尺度上都成立。
- **[[LLM Wiki]]（本 wiki 自身）**：CLAUDE.md/AGENTS.md + templates + INDEX/LOG 也是一种 harness——规则即马具；TAB 样本"团队要对齐的必须落到仓库、Memory 只放个人偏好"从 Harness 视角独立印证了这一主张。
- **[[DeepSeek Harness]] 与 [[从 Chat UI 到 Agent UI]] 是同一事实流的后端与前端**：DSH 的 Session Event Log（事实流+多投影、presentCall/presentResult）与 Agent UI 的 Snapshot+Event Log+Live Stream、Tool Renderer Registry 三组同构——「执行状态可解释、可恢复」是 harness 从后端到前端的一致目标，已沉淀 [[Session Event Log]]。

## ⚠️ 矛盾 / 未解问题

- "100 万行/10 倍效率"出自 OpenAI 文章自述，无第三方复核；腾讯规范的落地效果数据未见（审计示例是内部项目）；去哪儿成果数字（75%+ 出码率等）同样为大会分享自述口径。
- **Harness 定义谱系（四方，互补非矛盾）**：
  - **最宽**（[[ConardLi]]/LangChain 口径）：Harness = Agent - Model，除模型外的一切——Prompt/Context/Skill 全部算 Harness；腾讯 6 支柱（上下文管理/工具系统/执行编排/状态记忆/评估观测/约束恢复）取此口径。
  - **中宽**（[[去哪儿网]]）：Harness = AI 研发**过程控制能力**（触发/约束/隔离/审查），偏"控制"。
  - **最窄**（[[白墨]]）：Harness = 十二工程之一的**运行控制面**（调度/路由/监控/恢复），Prompt/Context/Skill/Agent/Loop 被拆为独立工程——白墨十二工程整体≈最宽口径的 Harness 工程清单化。
  - 引用时需注明口径；OpenAI/腾讯"赋能型基础设施"与去哪儿"控制型能力"组合起来才是完整定义。
- 工具链细节（CodeBuddy/Knot/工蜂/TAPD）为腾讯内网生态，外部读者只能借鉴方法论骨架（6 支柱/SOP/审计思路），不能照抄配置。
- DSH 的「约 80k stars」为 2026-08-14 时点数据；其缓存优势归因（一半模型、一半 Harness 前缀稳定）纠正了网上「全归功于 DSH」口径；Developer Preview 阶段官方明示兼容性破坏，架构细节可能随版本变化。

## 相关页面

- [[腾讯程序员]] · [[ConardLi]] · [[白墨]] · [[去哪儿网]] · [[Cole Medin]] · [[Harness Engineering 团队落地规范]] · [[Harness Engineering 三次演进与头部实践]] · [[企业 Agent 落地十二大工程框架]] · [[TAB 大仓 Harness 工程化实战]] · [[去哪儿网 AI Coding 研发平台实践]] · [[Cole Medin 黑灯工厂实验]] · [[AgentScope Java]] · [[mattpocock skills]] · [[Superpowers]] · [[Skills 设计方法论]] · [[LLM Wiki]] · [[DeepSeek Harness]] · [[DeepSeek Harness 源码拆解]] · [[Session Event Log]] · [[从 Chat UI 到 Agent UI]]
