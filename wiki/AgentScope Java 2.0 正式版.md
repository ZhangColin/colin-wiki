---
type: source
title: "AgentScope Java 2.0 正式版"
domain: [AI编程, Java]
tags: [AgentScope, Java, v2.0.0, GA, ReActAgent, HarnessAgent, Agent 运行时, 框架选型]
sources: []
source_path: "raw/articles/1. AgentScope Java 2.0 正式版来了：Java Agent 不只要会做事，还得能稳定运行.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzcwMjA0Njk3Nw==&mid=2247484197&idx=1&sn=f749c6a90912433198b64be6adad49d8"
author: "[[AI实战有术]]"
date_published: "2026-07-11"
created: 2026-08-17
updated: 2026-08-17
status: active
aliases: [AgentScope Java 2.0 GA, AgentScope 正式版]
---

# AgentScope Java 2.0 正式版

> 一句话要点：[[AI实战有术]]（Dilee）解读 [[AgentScope Java]] v2.0.0 首个 GA（2026-07-10）——它盯的不是"怎么调模型"（Spring AI）也不是"怎么建模任务"（Embabel），而是 **Agent 开始行动以后怎么长期、安全地跑**（运行时工程）。

## 关键要点

- v2.0.0（tag "2.0 first GA release"，此前 RC1-RC5）：Java 基线 17、Maven 3.9+，`io.agentscope:agentscope-harness:2.0.0` 已上 Maven Central。坑：README 残留 `1.0.12` 旧依赖示例，以 v2 文档与根 POM 为准。
- **2.0 四大变化**：① `ReActAgent` 改**无会话状态**执行核心（同一实例服务多会话），`HarnessAgent` 负责工作区/状态恢复/上下文压缩/沙箱/Skills/Plan Mode；② Memory/SessionManager/StatePersistence 收敛为 `AgentState` + `AgentStateStore`；③ 流式统一为类型化 `streamEvents()`，Hook 迁移到五阶段 Middleware；④ 新增 `PermissionEngine`、`ModelRegistry`、模型重试与备用模型切换。
- **三框架选型对照**：Spring AI=把模型/AI 能力接进 Spring 应用（ChatClient/Tool Calling/Advisor/VectorStore/MCP）；Embabel=围绕目标组织动作、按状态规划路径（Agent/Action/Goal/Condition/GOAP）；AgentScope Java=管理循环/状态/权限/事件/运行环境。多数项目先选一个主框架；跨框架组合需验证依赖与运行时边界。
- **五个值得看的点**：①类型化事件流（`streamEvents()` 返回 `AgentEvent`：模型开始调用/文本增量/工具调用开始/工具结果/等待用户确认/最终结果——前端按事件类型展示，不是"一直转圈的页面"）；②权限进入工具执行链路（ALLOW/DENY/ASK，自定义检查可 PASSTHROUTU 交回引擎；**"权限不是 Prompt 里的一句'请谨慎操作'"**）；③状态不绑死实例（`AgentState` 按 `(userId, sessionId)` 寻址，相同串行/不同并行；裸 ReActAgent 不配 store 不持久化，HarnessAgent 默认 `JsonFileAgentStateStore`，可换 Redis——但接 Redis ≠ 自动生产级，隔离/过期/幂等/合规仍要自设计）；④Workspace 把任务逻辑和执行地点分开（本地/容器/云沙箱）；⑤Middleware 留扩展点（onAgent/onReasoning/onActing/onModelCall/onSystemPrompt——横切能力集中处理，类似 Spring 拦截器）。
- **与 Embabel 最易混**：Embabel 偏任务模型（先描述 Action/Goal 让规划器找路径）；AgentScope 偏执行运行时（循环怎么跑稳、权限怎么管、中断怎么恢复）。选型看你在为"路径怎么规划"发愁还是"Agent 怎么长期安全跑"发愁。
- 整车/发动机比喻：ReActAgent 是发动机（推理与工具调用），HarnessAgent 是整辆车（工作区/记忆/沙箱/子 Agent/技能）。

## 与已有内容的关联

- 相关概念：[[AgentScope Java]]、[[Harness Engineering]]（HarnessAgent 即该概念的框架化）
- 相关实体：[[AI实战有术]]（作者）
- 相关源：[[AgentScope Java 2.0 上手 Web 接口]]、[[AgentScope Java 2.0 核心架构拆解]]（系列第 2/3 篇）

## ⚠️ 矛盾 / 待澄清

- 无源间矛盾。作者明确区分源码事实与工程判断（如 Spring Boot 4.1.0 能编译运行 ≠ 官方"最高支持/完整认证"）。
- 1.0→2.0 有破坏性变更（旧 Pipeline/PlanNotebook 替换，旧 RAG/长期记忆接口弃用或重写），老项目升级需注意。

## 相关页面

- [[AgentScope Java]] · [[Harness Engineering]] · [[AI实战有术]] · [[AgentScope Java 2.0 上手 Web 接口]] · [[AgentScope Java 2.0 核心架构拆解]]
