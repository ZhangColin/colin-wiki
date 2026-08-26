---
type: source
title: "企业 Agent 落地十二大工程框架"
domain: [AI编程, 企业应用, 软件工程]
tags: [十二大工程, Harness Engineering, Skill Engineering, Ontology, Data Agent, Loop Engineering, 企业 Agent]
sources: []
source_path: "raw/articles/6.6万字一篇文章讲清楚：企业 Agent 落地十二大工程框架（上篇）.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/0dtMBG5Xfu1t4Mu7C9I3Dg"
author: "[[白墨]]"
date_published: "2026-07-30"
created: 2026-08-26
updated: 2026-08-26
status: active
aliases: [十二大工程框架, 企业 Agent 十二大工程]
---

# 企业 Agent 落地十二大工程框架

> 一句话要点：[[白墨]]（白哥Agent落地局）6.6 万字长文（本文为上篇约 3.5 万字，169 幅配图）首次提出「**企业 Agent 落地十二大工程框架**」——六纵（Prompt → Context → Skill → Agent → Harness → Loop，能力递进）+ 六横（Data / Ontology / Process / Evaluation / Safety-Governance / Product-UX，托底支撑）；上篇详述前八个工程，下篇将覆盖 Evaluation/Safety/Process/Product-UX 与 L1-L5 成熟度模型。

## 关键要点

- **总框架**：六纵让 Agent 从「会回答」走向「会复用、会做事、会协同、会上线、会进化」；六横保证「可信（Data）、可懂（Ontology）、可流转（Process）、可测（Evaluation）、可控（Safety-Governance）、可用（Product-UX）」。示例贯穿全文：员工说「帮我提交一笔差旅报销」。
- **三个易混淆工程的边界（独创）**：Process 管**业务怎么运转**（业务定义）、Agent 管**任务怎么完成**（动态执行）、Harness 管**系统怎么稳定运行**（技术执行）——「Process 定义业务流转，Agent 决定任务完成，Harness 保证系统运行，谁也替代不了谁」。合同审查例：Process 定义销售发起→法务审查→签署路径，Agent 在风险节点读合同生成建议，Harness 调度解析工具/模型服务/审批系统并处理失败重试。
- **Prompt Engineering**（让模型听懂）：七项建设（任务指令/角色设定/输出格式/Few-shot/风格语气/安全拒答/工具与流程感知）；「Prompt 是业务与模型的第一层控制面」；生产 Prompt 需企业资产仓库（ID/Owner/版本/评测集/回滚）；**Prompt 里的安全规则只是第一道约束，不能代替真正的身份认证、权限校验、流程控制和工具拦截**；Loop 层警示——「Prompt 不能成为所有问题的万能修复层」。
- **Context Engineering**（让模型知道）：五个问题（给什么/何时给/什么结构给/给多少/哪些不能给）；上下文压缩漏斗（摘要/去重/排序/分层/截断/重要性评分）；记忆三层（短期/中期/长期）与任务状态/流程状态区分；**零信任权限拦截**——过滤在信息进入模型上下文之前完成，「无权限数据不进推理链路」。
- **Skill Engineering**（让能力复用）：Skill = 能力包（Prompt 模板 + I/O Schema + 上下文需求 + 本体约束 + 工具逻辑 + 业务规则 + 权限 + 风险等级 + 评估 + 版本）；**三层结构**：原子 Skill（单一动作）→ 复合 Skill（多原子组合）→ 业务 Skill（完整业务能力）；Skill Registry/Marketplace（含适用流程、SLA、Owner）；八阶段生命周期（设计到废弃）；七概念边界：Prompt 是语言指令 / Context 是知识状态 / Ontology 是业务语义 / Process 是业务流转 / Tool 是外部接口 / Skill 是能力封装 / Agent 是任务执行者。
- **Agent Engineering**（让系统行动）：规划—行动—观察—调整循环 vs 固定工作流；**「多 Agent 不是默认优于单 Agent」**——「如果任务可以通过一个 Agent + 多个 Skill 稳定完成，绝不人为引入多体协同的复杂性」；七种专业 Agent 角色（Planner/Research/Data/Skill/Writer/Reviewer/Executor）；「真正的系统自治是给定边界内的最优解，绝非漫无目的的自由发挥」。
- **Harness Engineering**（让系统稳定上线）：定义=「企业 Agent 的**运行控制面**」，**不是简单的工作流配置，也不是单一 Agent 框架**；15 步运行编排（接收请求→识别意图→判断权限→检索上下文→…→安全审核→流程流转→返回→日志，权限判断与安全审核嵌在执行链里）；模型路由映射（任务类型/风险/成本综合考量）；Skill/工具编排（注册/发现/路由/超时重试/降级/结果标准化）；**成本控制因果回环**——「便宜模型省下的调用费，常常被错误、重试和人工接管吃回去」，真正降本靠缓存/降级/批处理/请求合并。
- **Loop Engineering**（让系统持续进化）：反馈来源远不止点赞点踩（用户追问/放弃/改写/审批驳回/流程超时/业务指标变化）；**错误归因是核心**——一次合同风险误判可能来自七个不同工程层，归因准优化才不瞎改；全要素版本快照（12 类组件版本都记录才能复现问题）；A/B 测试引擎；**生产安全闸门**——持续进化≠不受控自动改生产，变更必须可评估/可审批/可灰度/可审计/可回滚。
- **Data Engineering**（让数据可信可用）：数据源地图 + **权威来源判断**（多源冲突时知道以谁为准）；可信数据转化管道；主数据管理（同一客户多个编号的实体对齐）；**Data Agent 三层次**：查询型 → 分析型 → 行动型（「查询数据→分析数据→解释数据→提出建议→触发行动」）；「Data Engineering 让数据准备好，Data Agent 让数据用起来」；Data Agent ≠ NL2SQL——还需指标识别/数据源路由/语义理解/权限控制/结果验证；**事实-推测-行动信任链**：数据事实/统计发现/业务推测（待验证假设）/因果结论（需更强证据）/行动建议（需人工复核），「越靠近行动，越需要证据和人工复核」；高风险分析「不应把相关性包装成因果关系」。
- **Ontology Engineering**（让业务语义可理解）：业务语义底座（概念/实体关系/属性字段映射/业务规则/约束/状态机/**Action 语义模型**）；**不能过早重型建设**——五阶段渐进（术语表→场景本体→轻量语义模型→正式领域本体→平台级本体服务），「围绕一个报销 Agent 开始，别一上来定义全企业概念」；**Action 是连接各工程的桥梁**——实体回答有什么/关系回答怎么关联/状态回答在哪阶段/规则回答判断依据/Action 回答能发生什么变化/约束回答什么不允许；Action 与状态机区别（状态机画边界，Action 给依据：谁、什么条件、触发什么迁移）；高风险 Action 分级（查询订单低风险/修改记录发邮件高风险/支付退款转账极高风险）——「理解 Action、选择 Action、执行 Action 是三个不同层次的能力」；指标口径建模（「客户数」可能指注册/下单/付费/活跃）。
- 金句：「模型能力越强，越需要可信数据，否则模型只会把不准确的数据组织成一个更加完整、更有说服力的错误答案」；「Data Agent 的成熟不是 SQL 越复杂越好，而是能否在正确权限、可信数据和统一口径下稳定回答」。

## 与已有内容的关联

- [[Harness Engineering]]：白墨的 Harness 是「运行控制面」窄定义（十二工程之一），与 OpenAI「Harness = Agent - Model」宽定义构成**定义谱系**，详见概念页矛盾区。
- [[智能问数平台]]：Data Agent 三层次、语义层、指标口径、零信任权限过滤与该页五层架构高度同构，跨源印证。
- [[Skills 设计方法论]]：Skill 三层（原子/复合/业务）与 Perplexity 三层成本/渐进式披露呼应；Skill Registry/Marketplace 是 Skill 治理的企业级形态。
- [[Agent 测评 Eval]]：Evaluation 是十二工程横切之一（下篇展开），Loop 的失败案例归因与腾讯基线机制同向。
- [[HITL 与 AFK]]：「多 Agent 不默认优于单 Agent」、高危 Action 人工确认、生产安全闸门都是人机边界设计。

## ⚠️ 矛盾 / 待澄清

- **Harness 定义窄化**：白墨将 Harness 定位为十二工程之一（运行/编排层），而 OpenAI 原文 Harness = Agent - Model（除模型外一切，Prompt/Context/Skill/Agent 全算 Harness）。腾讯 6 支柱介于两者之间。属定义谱系而非矛盾，已在 [[Harness Engineering]] 标注。
- 本文为下篇待续（Evaluation/Safety-Governance/Process/Product-UX 与 L1-L5 成熟度模型未出），评价框架完整性需等全篇。
- 文中大量示例（报销/合同审查）为教学案例，非真实落地数据。

## 相关页面

- [[白墨]] · [[Harness Engineering]] · [[智能问数平台]] · [[Skills 设计方法论]] · [[Agent 测评 Eval]] · [[HITL 与 AFK]]
