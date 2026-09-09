---
type: source
title: "从 Chat UI 到 Agent UI"
domain: [AI产品, 软件工程]
tags: [Agent-UI, Agentic-UI, Message-Part, 工具调用展示, HITL, 审批, Artifact, Run状态机, 事件协议, Generative-UI]
sources: []
source_path: "raw/articles/从 Chat UI 到 Agent UI：如何设计一个真正可用的智能体交互界面.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/p34PXv5ZFT0ie_aiLI0WQQ"
author: "[[炼丹上岸]]"
date_published: "2026-08-30"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases: [Agent UI 设计, Chat UI 到 Agent UI]
---

# 从 Chat UI 到 Agent UI

> 一句话要点：Agent 产品的界面不该是"更漂亮的聊天框"，而应围绕持续运行的执行过程（Run）设计——用结构化 Message Part、Run 状态、计划、工具调用、审批、Artifact 工作区与状态恢复，把用户从"读对话"升级为"理解、控制并参与一个持续运行的智能执行系统"。

## 关键要点

- **本质区别**：Chatbot 只需三类状态（用户消息/Assistant 消息/正在生成）；Agent 的执行近似后台任务系统（创建 Run → 分析 → 计划 → 调工具 → 处理结果 → 更新计划 → 等确认 → 出结果），持续几十秒到几十分钟，页面会关、会话会切、网络会断。因此 Agent UI 的设计对象从 Message 扩展为 **Conversation / Message / Run / Step / Tool Call / Approval / Artifact / Event** 八类对象。
- **第一原则**：把 Agent 的内部执行状态转换为用户可理解、可判断、可操作的界面状态——展示可解释的执行状态（✓ 文件读取完成 / ● 正在分析评分办法 / ○ 等待匹配历史案例），不是暴露模型隐藏推理。中心是"执行过程"而非"消息"。
- **核心升级：Message → 多个 Message Part**（Text/Plan/Tool/Approval/Artifact/Source/Error 七种 Part），前端按 part.type 渲染专用组件（`MessagePartRenderer` switch），不再把所有内容当字符串。聊天区负责沟通解释，工作区承载实际成果（5000 字报告不塞进气泡而是"已生成报告 + [打开] [导出] [继续修改]"）。
- **工具调用 UI 两层化 + Tool Renderer Registry**：默认层展示业务含义（"✓ 已完成案例检索，找到 12 个相关项目，耗时 1.8 秒"），展开层才给技术详情（工具名/参数 JSON/Trace ID）；不同工具配不同渲染器（搜索→结果列表、数据库→表格、图表→图表组件、发邮件→邮件预览与确认按钮），**绝不给普通用户直接显示工具调用 JSON**。
- **Plan 与 Todo 必须分离**：Plan 是给用户看的执行计划 UI（StepStatus: pending/running/completed/failed/skipped，显示 3/5 进度）；Todo Tool 是 Agent 自己可调用更新的任务管理工具。Todo 变化可投影成 Plan UI，但架构上是两个概念。
- **Human-in-the-loop 是真暂停**：后端协议 `approval.required/approved/rejected`；前端收 approval.required 后显示审批卡片并把 Run 置为 `waiting_user`。关键点：Agent 必须真正暂停（不能只是给两个按钮），确认后**从暂停位置继续而非重跑整个任务**。这是 Agent 从"自动生成内容"走向"参与真实业务操作"的门槛。
- **Artifact 工作区**：`interface Artifact {id, type: document|table|chart|code|html, title, status: creating|ready|failed, version}`，支持版本迭代（v1 → 用户要求补充 → v2 → 加图表 → v3）；"对话描述修改意图，Artifact 保存真实成果"。
- **Run 状态机**：queued/running/waiting_tool/waiting_user/paused/completed/failed/cancelled（普通聊天只有 idle/streaming/completed）。**取消页面订阅 ≠ 取消 Agent 执行**——Run 是独立于页面连接存在的后台执行对象，用户切走时 SSE 可关、Run 不停，回来时恢复消息/Run/Plan/工具/Artifact 状态而非重跑。
- **不能只靠 SSE**：架构应为 **Snapshot + Event Log + Live Stream**；进会话流程：加载 Snapshot → 恢复消息/计划/工具/Artifact → 识别 activeRun → 读 lastEventId → 重连 SSE → 补发遗漏事件 → 继续实时。需处理刷新、切换会话、断网、多标签页、后台执行、事件补发与去重。
- **统一事件 Reducer + 协议层**：所有事件（run.started/assistant.delta/plan.updated/tool.started/tool.completed/approval.required/artifact.created/run.completed）交给 `applyAgentEvent(state, event)` 纯函数归约，实时与补发共用一套逻辑、按 eventId 去重、可重放可测试。**AG-UI 的价值在于定义 Agent 与前端间的统一事件协议**——前端不直接理解 LangGraph/OpenAI Agents/自研 Runtime 私有事件，经 Adapter 转统一 UI Event。
- **Generative UI 两种企业做法**：受控组件注册表（模型只能从 `componentRegistry` 选组件传 props）或声明式 UI Schema；**不推荐直接执行模型生成的任意 HTML/React**（XSS、样式与性能不可控、组件质量不稳、破坏设计系统）。
- **主流项目借鉴矩阵**：ChatUI（基础聊天层）、assistant-ui（组件 Primitive 化：Thread/Message/Composer/Message Part/Attachment/Action Bar/Branch Picker——别做一个巨型 `<AgentChat />`）、Vercel AI SDK UI（UIMessage/Message Parts/工具流/Generative UI）、CopilotKit（**深度嵌入业务页面**：Shared State/HITL/Agent 读写页面状态）、LangChain Agent Chat UI（LangGraph 场景的 Run 中断/状态恢复/Time Travel）、AG-UI（协议层，自研平台比选组件库更重要）。
- **推荐五层前端架构**：页面组件层 → 状态层（Conversation/Run/Artifact State）→ Event Reducer → Transport（SSE/Fetch Stream/WebSocket）→ Agent Protocol Adapter → 底层 Runtime。演进路线：**Chat UI → Tool-aware Chat UI → Agent Runtime UI → Agentic Application UI**，四阶段落地（基础聊天体验 → Agent 执行展示 → 可靠状态恢复【决定能否上生产的阶段】→ Agentic UI）。

## 与已有内容的关联

- 与 [[DeepSeek Harness 源码拆解]] 强呼应：DSH 的 Session Event Log（事实流+多端投影）正是"Snapshot+Event Log"的后端实现；presentCall/presentResult（执行与展示解耦）正是 Tool Renderer Registry 的服务端设计；Approval/审批卡片对应 DSH Approval 链与 [[HITL 与 AFK]]。三组跨源映射可沉淀 [[Session Event Log]] 等概念。
- 与 [[AI Coding 自主度分级]] 可关联：waiting_user/审批能力对应人去环程度。
- Plan≠Todo 与 Claude Code 的 todo 工具可互证。
- 本文是纯前端视角，恰好补 wiki 目前后端/工程视角的空缺。

## ⚠️ 矛盾 / 待澄清

- "Agent UI 的核心不是 Message 而是 Message Part"与 DSH"Session 先保存事实、Messages 只是投影"属于同一思想的两端，可互证；但与多数现存 Chat UI（仍以整条 string 消息为主）实现口径不同——宜作为演进方向而非现况描述。
- 文中举例 Run 需"等待用户确认后从暂停位置继续"——理想协议；DSH 源码拆解恰恰说明跨进程续跑（暂停恢复、消息已收未落盘即崩溃）仍是行业未解难题，两篇合读可避免把 UI 规范当作已普遍实现的能力。
- 文章对 AG-UI 评价正面但未提及任何实践代价/缺陷，属单方来源。
- 作者明确站在"企业系统不要执行模型生成 HTML"一侧，与某些激进 Generative UI 实践有张力——文中观点本身自洽。

## 相关页面

- [[炼丹上岸]] · [[DeepSeek Harness 源码拆解]] · [[Session Event Log]] · [[HITL 与 AFK]] · [[AI Coding 自主度分级]] · [[Harness Engineering]] · [[翟星人的实验室]]
