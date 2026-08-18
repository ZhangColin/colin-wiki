---
type: entity
title: "AgentScope Java"
domain: [AI编程, AI工具, Java]
tags: [AgentScope, Java Agent, HarnessAgent, ReActAgent, Toolkit, Agent 运行时]
sources:
  - "[[AgentScope Java 2.0 正式版]]"
  - "[[AgentScope Java 2.0 上手 Web 接口]]"
  - "[[AgentScope Java 2.0 核心架构拆解]]"
created: 2026-08-17
updated: 2026-08-17
status: active
aliases:
  - agentscope-java
  - AgentScope Java 2.0
---

# AgentScope Java

> 一句话定义：开源 Java Agent 运行时框架（`github.com/agentscope-ai/agentscope-java`，Maven `io.agentscope:agentscope-harness`），主张 Agent 不只要会做事，还得能稳定运行——把状态、权限、事件流、沙箱、执行环境等 Agent 运行时工程做成一等公民。

## 概述

AgentScope Java 2.0.0 于 2026-07-10 发布首个 GA（此前 RC1-RC5）。Java 基线 17，推荐 Maven 3.9+。它盯的不是"怎么调模型"（Spring AI）也不是"怎么建模任务"（Embabel 的 Goal/Action/GOAP），而是 **Agent 开始行动以后怎么长期、安全地跑**——即 [[Harness Engineering]] 在 Java 侧的框架实现（`HarnessAgent` 之名直接来自该概念）。

## 核心架构（三层）

- **HarnessAgent（整车）**：应用入口与工程化包装，内部持有 `delegate` ReActAgent；组织 workspace、filesystem、sandbox、subagent、skill、plan-mode、MCP 等能力。`streamEvents()` 先 `ensureSessionDefaults` 补会话默认值，再管理沙箱生命周期。
- **ReActAgent（发动机）**：无会话状态的推理-行动循环执行核心（2.0 起），`reasoning()` 调模型、`acting()` 执行工具写回上下文；同一实例可服务多会话。
- **Toolkit**：工具注册、Schema 暴露、参数转换、执行。**模型只生成工具调用请求，执行发生在应用侧**。

## 关键属性 / 事实

- **2.0 四大变化**：① ReActAgent 去会话状态 + HarnessAgent 管运行设施；② Memory/SessionManager/StatePersistence 收敛为 `AgentState` + `AgentStateStore`（按 `(userId, sessionId)` 寻址）；③ 流式统一为类型化 `streamEvents()`，Hook 迁移到五阶段 Middleware（onAgent/onReasoning/onActing/onModelCall/onSystemPrompt）；④ 新增 PermissionEngine（ALLOW/DENY/ASK/PASSTHROUGH，进入工具执行链路）、ModelRegistry、模型重试与备用切换。
- **类型化事件流**：AgentStartEvent / ModelCallStartEvent / TextBlockDeltaEvent / ToolCallStart/EndEvent / ToolResultStart/EndEvent / AgentResultEvent / AgentEndEvent，含 RequireUserConfirmEvent（人工确认）——前端可分别展示工具状态与审批请求。
- **RuntimeContext vs AgentState**：前者是本次调用的上下文标记（userId/sessionId），后者是会话执行状态（summary/context/curIter/permission/tool/task 状态）。
- **上手哲学**：先搭最小闭环（HTTP 入口/参数校验/会话/流式），主动关掉一切（disableFilesystemTools/disableShellTool/…）——"入口先别做太满"，排查问题按层定位而非改 Prompt。
- **状态存储**：InMemory（重启即失）→ JsonFileAgentStateStore（单机）→ Redis 等（多副本）。
- **选型对照**：Spring AI=应用集成；Embabel=任务建模（GOAP）；AgentScope Java=执行运行时。多数项目先选一个主框架。

## 演变 / 时间线

- 2026-07-10：v2.0.0 首个 GA（`2.0 first GA release`）。坑：README 残留 1.0.12 旧依赖示例，以 v2 文档与根 POM 为准。
- 1.0 已有多模型、ReActAgent、注解式工具、MCP、多模态、RAG、长期记忆、多 Agent Pipeline、Hook；2.0 旧 Pipeline/PlanNotebook 被替换，旧 RAG/长期记忆接口弃用或重写。

## 相关页面

- [[AI实战有术]] · [[Harness Engineering]] · [[AgentScope Java 2.0 正式版]] · [[AgentScope Java 2.0 上手 Web 接口]] · [[AgentScope Java 2.0 核心架构拆解]] · [[mattpocock skills]] · [[Superpowers]]
