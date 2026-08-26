---
type: concept
title: "Agent 测评 Eval"
domain: [AI编程, 软件工程, 测试]
tags: [Eval, Agent 测评, LLM-as-Judge, pass^k, 回归测试, Trace, 基线, 质量门禁, holdout]
sources:
  - "[[腾讯 Agent 与 Skill 测评方案]]"
  - "[[Skills 设计方法论]]"
  - "[[Anthropic 与 Perplexity 的 Skills 设计方法论]]"
  - "[[Cole Medin 黑灯工厂实验]]"
  - "[[TAB 大仓 Harness 工程化实战]]"
created: 2026-08-25
updated: 2026-08-25
status: active
aliases:
  - Agent Eval
  - Agent 评估
  - LLM-as-Judge
  - 测评体系
---

# Agent 测评 Eval

> 一句话定义：对 AI Agent / Skill 的输出质量做**系统化、自动化、可回归**评测的工程体系——用三类评分器组合替代人肉验证，用可对比的分数替代"感觉变好了"。公式（腾讯）：**Eval = 输入 → 执行 → 捕获 Trace → 检查规则 → 可对比的分数**。

## 核心观点

### 为什么是独立主题

Agent 的**非确定性**（同 prompt 多次结果不同）、**黑盒化**（模型/Prompt/工具变更引发行为漂移）、**错误级联放大**（多步调用中小偏差逐级放大）使传统测试失效；没有 Eval，团队会"悄悄退化"且不敢升级模型。[[Skills 设计方法论]] 与腾讯实践从两个方向收敛到同一结论：**Eval 是 Skill/Agent 工程化的必要支柱，且应与开发同步启动而非事后补**。

### 三类评分器（谁来评）

**确定性评分器（脚本/断言/AST）> Rubric 评分器（LLM-as-Judge + Prompt + Schema）> 人工评分器（专家）**——能用代码判断的绝不用模型；人工主要做**校准**（与 LLM 评委对齐，一致率 ≥85% 才可用）、诊断异常（0%/100% 通过率通常是评分器坏了）、Ground Truth、高风险兜底。专家时间 60%+ 应花在校准与诊断。

### 评什么：五维度 + 四层映射

腾讯五维度：功能正确性(P0) / 过程质量(P1) / 效率与成本(P1) / 鲁棒性与安全(P0) / 体验与对齐(P2)。与 Perplexity 四层 Eval 可映射：Skill 加载 Eval ≈ 触发用例；端到端任务 Eval ≈ 功能正确性；跨模型 Eval ≈ 模型对比报告（pass@1/pass^5/token/费用）。两套框架同构互参。

### 关键机制

- **pass^k vs pass@k**：k 次全过（稳定性）vs 至少一次过（峰值）——评估 Agent 必须两者分开报。
- **用例四场景**：触发 → 核心逻辑 → 产物质量 → 异常容错，正负向都要有；**负向触发用例防"什么都触发"**（过度触发比不触发更隐蔽）。
- **基线机制**："先跑出来 → 人工确认 → 就是预期"——不手写预期，人工确认过的执行快照即基线；模型升级/逻辑变更/用例修改都要重建。
- **能力 vs 回归双轨**：能力套件起步 20–50% 通过率逐步提升；能力稳定后用例"毕业"进回归套件（**只增不减**，CI 每次跑）。线上 Bad Case 是最有价值的用例素材。
- **稳定性容忍阈值按类型**：关键决策类 0% / 辅助分析类 ≤10% / 创意生成类 ≤40%——阈值按业务风险调整，非机械套用。
- **Trace 是前置依赖**：被测 Agent 须输出结构化 Trace（JSONL），且格式跨版本稳定。

### holdout 盲测（第三种防自欺机制，2026-08 新增）

[[Cole Medin 黑灯工厂实验]] 的 holdout scenarios：开工前把"成功长什么样"写成一组测试场景，**builder 永远看不到**——看不到就无法为通过场景专门设计应用，无法"挟持验收标准"；validator 则不知道实现与计划。与腾讯基线机制形成互补对：

- **基线**（防漂移）：人确认过的快照做参照，评分器可见——回答"和以前一样好吗"。
- **holdout**（防挟持）：验收标准对构建者保密——回答"成功标准本身没被污染吗"。
- 两者与 Superpowers v6.0 "Do Not Trust the Report" 怀疑论 reviewer 同谱系：**验证者与被验证者的信息隔离**是防自欺的通用原则（详见 [[子代理驱动开发]]）。

### 基线对比反作弊（TAB 变体，2026-07）

[[TAB 大仓 Harness 工程化实战]] 把基线机制用作**反作弊**：开发前跑一次门禁"基线快照"（失败集合 A），开发后跑"差异"（失败集合 B），**只有 B−A 的新增失败才阻断**——AI 的"这是历史问题，不是我引入的"这把万能钥匙被整体拿走（"价值不在技术，在于剥夺 AI 的解释权"）。配套设计：

- **软门禁失败留"伤疤"**：不阻断但写警告 + 审查结论显式提及 + 度量报告沉淀——"你不能阻止 AI 偷懒，但可以让偷懒变得很醒目"（与 Perplexity"不应靠加长指令解决问题"同精神：改变激励结构而非说教）。
- 门禁哲学：**硬门禁宁少不多**——非质量红线不阻断，频繁打断会让团队退回 Vibe Coding。

## 证据 / 来源

- [[腾讯 Agent 与 Skill 测评方案]]（2026-06）— 工程化最完整版：三类评分器 + 五维度 + 四场景用例 + 基线 + 双轨 + TPerf 实战（LCS 步骤对齐、9 场景 30+ 用例 × 7 模型、0 容忍的风险等级上调）。
- [[Skills 设计方法论]] / [[Anthropic 与 Perplexity 的 Skills 设计方法论]]（2026-06）— Perplexity 四层 Eval 套件（加载→渐进加载→端到端→跨模型），Step 0 先写 Evals、负面示例比正面更重要；Gotchas 飞轮即持续 Eval。
- 腾讯文引用一手：Anthropic《Demystifying evals for AI agents》《Evaluating AI agents》、OpenAI《Eval skills》；Anthropic 推荐通过率：确定性任务 100%、非确定性 ≥95%。

## 与本 wiki 其他概念的关系

- [[Skills 设计方法论]]：Eval 是其四柱之一；本页是该柱的展开。
- [[Harness Engineering]]：对应六支柱之五"评估与观测"（L1 语法→L4 架构四层）的系统化实现；腾讯两篇（落地规范 + 测评方案）构成"建 harness + 验 harness"。
- [[Superpowers]] / [[mattpocock skills]]：三铁律与 verification-before-completion 是**单次执行内**的验证；本概念是**系统级回归**——两级互补，前者的红旗可反向沉淀为 Eval 用例。
- [[LLM Wiki]]（本 wiki 自身）：colin-wiki 的 Lint 是粗粒度人工近似，无自动 Eval、无 gotchas 飞轮——两条已登记的方法论缺口（见 [[Skills 设计方法论]] 矛盾区）。

## ⚠️ 矛盾 / 未解问题

- 具体数值（80 分达标线、≥85% 一致率、60% 专家时间、容忍阈值）均为团队经验值，未见实验依据，移植需自行校准。
- TPerf 案例显示同一框架内阈值可按风险上调（辅助分析类用 0 容忍）——框架给默认值，业务定最终值，引用时勿混淆两层。

## 相关页面

- [[腾讯 Agent 与 Skill 测评方案]] · [[Skills 设计方法论]] · [[Anthropic 与 Perplexity 的 Skills 设计方法论]] · [[Cole Medin 黑灯工厂实验]] · [[TAB 大仓 Harness 工程化实战]] · [[Harness Engineering]] · [[腾讯程序员]] · [[Cole Medin]] · [[Superpowers]] · [[LLM Wiki]]
