---
type: source
title: "AgentScope Java 2.0 核心架构拆解"
domain: [AI编程, Java]
tags: [AgentScope, Java, HarnessAgent, ReActAgent, Toolkit, AgentState, AgentEvent, 架构]
sources: []
source_path: "raw/articles/3. AgentScope Java 2.0 核心架构拆解：HarnessAgent、ReActAgent、Toolkit 到底是什么.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzcwMjA0Njk3Nw==&mid=2247484249&idx=1&sn=f9efae2f894e4ec0275042bde56255d2"
author: "[[AI实战有术]]"
date_published: "2026-07-15"
created: 2026-08-17
updated: 2026-08-17
status: active
aliases: [AgentScope 架构, HarnessAgent ReActAgent Toolkit]
---

# AgentScope Java 2.0 核心架构拆解

> 一句话要点：[[AI实战有术]] 拆开 [[AgentScope Java]] 的运行链路——**HarnessAgent 管工程包装、ReActAgent 管推理-行动循环、Toolkit 管工具执行**；理解三层后，就不会一遇到 Agent 行为不符预期就只盯着 Prompt 改。

## 关键要点

- **执行主链路**：Controller → Service → HarnessAgent → ReActAgent → Model 流式返回 ChatResponse → 响应中出现 `ToolUseBlock` → Toolkit 执行工具 → `ToolResultBlock` 写回上下文 → ReActAgent 再调模型 → 最终回答。
- **HarnessAgent（外层）**：`implements Agent, AutoCloseable`，内部持有 `delegate` ReActAgent；`streamEvents()` 先 `ensureSessionDefaults`（补默认 sessionId + 注入运行上下文），再 `wrappedStreamEvents`（配置沙箱时管理调用前后沙箱生命周期），才进 `delegate.streamEvents`。组织 workspace/filesystem/sandbox/subagent/skill/plan-mode/MCP——排查时先把文件系统/Shell/记忆/子 Agent 关掉收窄链路，稳定后再逐步加。
- **ReActAgent（内层）**：`reasoning()` 调模型处理回复、`acting()` 执行工具写回上下文。ReAct 不等于"暴露完整思维链"，是一种运行节奏。不绑模型服务，依赖 `Model` 接口（`Flux<ChatResponse> stream(messages, tools, options)`）。
- **Toolkit**：注册工具/生成 Schema/分组/参数转换/执行/包装结果。**边界：模型只根据 Schema 生成调用请求（JSON），执行发生在应用侧**——模型选工具和生成参数，应用负责校验、权限、执行、返回结果（`@Tool`/`@ToolParam` 注解，`readOnly` 标记）。
- **RuntimeContext vs AgentState（别混）**：前者是本次调用携带的上下文（userId/sessionId）；后者是会话执行状态——字段含 summary、context（消息列表）、curIter、permissionContext、toolContext、tasksContext。状态槽位由 `slotKey(uid, sid)` 定位。
- **AgentEvent 事件清单**：AgentStartEvent / ModelCallStartEvent / TextBlockDeltaEvent / ToolCallStart/EndEvent / ToolResultStart/EndEvent / AgentResultEvent / AgentEndEvent + 人工确认时 RequireUserConfirmEvent、RequestStopEvent。前端别只当打字机效果，应映射为 MESSAGE / TOOL_CALL_START / TOOL_RESULT_END / REQUIRE_USER_CONFIRM（弹确认框）/ AGENT_RESULT / DONE。
- **排查表（现象→先看哪层）**：参数不对→Controller/DTO；鉴权失败→Model 配置；不调工具→Prompt/工具 Schema/Toolkit 注册；参数错→Schema 与 ToolParam 描述；执行报错→Toolkit 实现；工具被拒→PermissionEngine；多用户上下文串→RuntimeContext/AgentStateStore；重启丢会话→还在用 InMemory；后端有事件前端看不到→AgentEvent 到 SSE 的映射。
- 比喻：ReActAgent 是发动机，HarnessAgent 是整车。

## 与已有内容的关联

- 相关概念：[[AgentScope Java]]、[[Harness Engineering]]、[[HITL 与 AFK]]（RequireUserConfirmEvent 即运行时 HITL）
- 相关实体：[[AI实战有术]]（作者）
- 相关源：[[AgentScope Java 2.0 正式版]]、[[AgentScope Java 2.0 上手 Web 接口]]（系列第 1/2 篇）

## ⚠️ 矛盾 / 待澄清

- 无源间矛盾；与前两篇（GA 解读、上手）构成完整系列，口径一致。

## 相关页面

- [[AgentScope Java]] · [[Harness Engineering]] · [[HITL 与 AFK]] · [[AI实战有术]] · [[AgentScope Java 2.0 正式版]] · [[AgentScope Java 2.0 上手 Web 接口]]
