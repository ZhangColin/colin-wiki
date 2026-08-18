---
type: concept
title: "Harness Engineering"
domain: [AI编程, 软件工程]
tags: [Harness Engineering, Agent Harness, 驾驭工程, 上下文管理, 团队规范]
sources:
  - "[[Harness Engineering 团队落地规范]]"
  - "[[AgentScope Java 2.0 正式版]]"
  - "[[Matt Pocock Skills v1.2 更新全景]]"
created: 2026-08-17
updated: 2026-08-17
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

## 与本 wiki 其他概念的关系（跨源同构）

- **[[mattpocock skills]] / [[Superpowers]] 本质都是 harness**：SKILL.md + 触发机制 + 工程纪律 = 给模型装的"马具"；Matt v1.2 的 harness-neutral（#781 去专有工具名）与双 harness 支持（Claude/Codex）正是"同一 harness 跨模型"的方向。
- **[[AgentScope Java]] 是 harness 的 Java 框架实现**：`HarnessAgent` 直接以 harness 命名，6 大支柱中的状态、权限、事件流、沙箱全部框架化。
- **[[Skills 设计方法论]]（Anthropic/Perplexity）** 对应支柱一（上下文管理）与支柱二（Skills）的理论化。
- **[[LLM Wiki]]（本 wiki 自身）**：CLAUDE.md/AGENTS.md + templates + INDEX/LOG 也是一种 harness——规则即马具。

## ⚠️ 矛盾 / 未解问题

- "100 万行/10 倍效率"出自 OpenAI 文章自述，无第三方复核；腾讯规范的落地效果数据未见（审计示例是内部项目）。
- 工具链细节（CodeBuddy/Knot/工蜂/TAPD）为腾讯内网生态，外部读者只能借鉴方法论骨架（6 支柱/SOP/审计思路），不能照抄配置。

## 相关页面

- [[腾讯程序员]] · [[Harness Engineering 团队落地规范]] · [[AgentScope Java]] · [[mattpocock skills]] · [[Superpowers]] · [[Skills 设计方法论]] · [[LLM Wiki]]
