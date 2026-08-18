---
type: source
title: "AgentScope Java 2.0 上手 Web 接口"
domain: [AI编程, Java]
tags: [AgentScope, Java, HarnessAgent, RuntimeContext, SSE, Spring Boot, 上手实战]
sources: []
source_path: "raw/articles/2. AgentScope Java 2.0 上手：用 HarnessAgent 跑通第一个 Web 接口.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzcwMjA0Njk3Nw==&mid=2247484222&idx=1&sn=9933ee4e87eadc6e2f36268a761e1121"
author: "[[AI实战有术]]"
date_published: "2026-07-14"
created: 2026-08-17
updated: 2026-08-17
status: active
aliases: [AgentScope 上手, HarnessAgent Web 接口]
---

# AgentScope Java 2.0 上手 Web 接口

> 一句话要点：[[AI实战有术]] 的 [[AgentScope Java]] 第一课上手——先把 HarnessAgent 接进 Spring Boot 跑通最小闭环（HTTP 入口/参数校验/会话边界/SSE 流式），**主动关掉一切工具**；"入口先别做太满，不是为了写弱 Agent，而是让每层能力都有地方落、都有办法测试"。

## 关键要点

- **链路**：HTTP → Controller → Service → HarnessAgent → `RuntimeContext(userId, sessionId)` → DeepSeek → SSE 流式返回。先不暴露工具、不给文件权限、状态放当前进程。
- 环境：JDK 17、Maven 3.9+、Spring Boot 4.1.0（官方 BOM 是 4.0.4——能跑 ≠ 官方认证）、`deepseek-v4-flash`。
- 依赖三件：`agentscope-harness`（带核心运行时）+ `agentscope-extensions-model-openai`（`OpenAIChatModel` + `DeepSeekFormatter`）+ spring-boot-starter-web/validation。
- **克制的 HarnessAgent**：`.stream(true)` 开流式后，builder 里连开十几个 disable（disableFilesystemTools/disableShellTool/disableMemoryTools/disableMemoryHooks/disableCompaction/disableSubagents/disableWorkspaceContext/disableAtPathExpansion/disableDynamicSkills/disableDefaultWorkspaceSkills/disableToolsConfig）+ `removeTool("wait_async_results")`——即使关掉子 Agent，默认消息总线仍会注册该工具。启动后 Toolkit 里没有可供模型调用的工具。
- **System Prompt 写边界**："当前没有接入项目文件/日志/数据库/外部工具，不要声称已经完成查询、修改或保存"——**模型不知道自己有没有能力，应用要告诉它**；接工具以后这条也一样重要。
- **RuntimeContext 是关键**：同一 HarnessAgent Bean 服务多用户多会话，按 `(userId, sessionId)` 找状态，不需要每个用户 new 一个 Agent。userId 别信前端（从登录态/网关取）。
- `streamEvents()` 返回 `Flux<AgentEvent>`，本例只取 `TextBlockDeltaEvent` 转业务 SSE 事件（SESSION/MESSAGE/DONE）。Spring MVC + Flux 作流式返回值即可，**不必为一个接口把整个项目切成 WebFlux**；但不是全链路非阻塞，生产要配异步执行器/超时/SSE 心跳。
- **两个测试比输出更关键**：同 sessionId 再问"我刚才让你整理什么"→应记得；换 sessionId 问→应不知道。这才能证明 RuntimeContext 和 AgentStateStore 真在参与执行。
- `InMemoryAgentStateStore` 进程重启即失；跨进程换 `JsonFileAgentStateStore`/Redis 扩展/自实现。
- 反模式警示：Demo 一上来把工具全打开，一旦回答不符合预期，分不清是模型没听懂、Prompt 没写清楚、工具返回脏数据，还是状态串了。

## 与已有内容的关联

- 相关概念：[[AgentScope Java]]、[[Harness Engineering]]
- 相关实体：[[AI实战有术]]（作者）
- 相关源：[[AgentScope Java 2.0 正式版]]（系列第 1 篇）、[[AgentScope Java 2.0 核心架构拆解]]（第 3 篇）

## ⚠️ 矛盾 / 待澄清

- 无源间矛盾；Spring Boot 版本（4.1.0 vs 官方 BOM 4.0.4）为作者实测口径，以项目依赖验证为准。

## 相关页面

- [[AgentScope Java]] · [[Harness Engineering]] · [[AI实战有术]] · [[AgentScope Java 2.0 正式版]] · [[AgentScope Java 2.0 核心架构拆解]]
