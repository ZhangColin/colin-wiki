---
type: concept
title: Session Event Log
domain:
  - AI编程
  - 软件工程
tags:
  - 事件溯源
  - 会话持久化
  - Agent运行时
  - 前端架构
  - 可观测性
sources:
  - "[[DeepSeek Harness 源码拆解]]"
  - "[[从 Chat UI 到 Agent UI]]"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases:
  - 事件溯源式会话
  - 事实流与会话投影
  - Snapshot + Event Log
---

# Session Event Log

> 一句话定义：Agent 会话持久化的架构模式——**先保存不可变的事实事件流，消息数组、UI 状态、恢复、遥测都从同一事件流投影**；反对把 messages 数组当唯一数据库。后端（[[DeepSeek Harness 源码拆解]]）与前端（[[从 Chat UI 到 Agent UI]]）两个独立源各自得出同构结论。

## 核心观点

### 后端形态：事实流 + 多投影（DSH）

- 会话先落事实事件流：`turn/start`、`assistant/chunk`、`tool/call` 等；`deriveMessages()`、Web Transcript、恢复、Fork、遥测都从同一流投影——「谁也不该偷偷维护第二真相」。
- **铁律：模型可见的东西必须能被日志重建**。
- 工具调用**先记 `tool/call` 再执行**——进程崩在中间，恢复语义是「这件事开始过、结果未知、需检查现场」，不删不重跑。
- Compaction 是 Surface Replacement：旧表面被摘要替换，但原始事实仍在日志。
- 工程细节：JSONL/SQLite 双 Provider 共用写入协调器；事件进批次，`flush` 是耐久屏障；日志中间损坏直接拒绝，只可丢未写成的尾部碎片。

### 前端形态：Snapshot + Event Log + Live Stream

- 进会话流程：加载 Snapshot（恢复消息/计划/工具/Artifact）→ 识别 activeRun → 读 lastEventId → 重连 SSE 补发遗漏事件 → 继续实时。
- 所有事件交给 `applyAgentEvent(state, event)` 纯函数归约——实时与补发共用一套逻辑、按 eventId 去重、可重放可测试。
- **Run 是独立于页面连接存在的后台执行对象**：取消页面订阅 ≠ 取消 Agent 执行；这是 Event Log 模式对「用户切走/断网/多标签页」的自然支撑。

### 跨源映射（三组同构）

| DSH（后端） | Agent UI（前端） |
| --- | --- |
| Session Event Log 事实流 + 多投影 | Snapshot + Event Log + Live Stream |
| `presentCall`/`presentResult`（执行与展示解耦） | Tool Renderer Registry（两层化工具展示） |
| Approval 链 + 单调 Guard | Approval Card + `waiting_user` 状态 |

## 证据 / 来源

- [[DeepSeek Harness 源码拆解]] — DSH 的 Session Event Log 实现细节（事实流、tool/call 先行、Compaction=Surface Replacement、耐久屏障）。
- [[从 Chat UI 到 Agent UI]] — 前端架构「不能只靠 SSE」与统一事件 Reducer、状态恢复五步流程。

## ⚠️ 矛盾 / 未解问题

- **理想与实现的差距**：Agent UI 篇假设「从暂停位置继续」是可行协议；DSH 篇显示跨进程续跑（消息已收未落盘即崩溃、缺耐久邮箱与任务租约）仍是行业未解难题——两篇合读可知该模式为演进方向而非普遍已实现能力。
- 与多数现存 Chat UI（以整条 string 消息为主、无事件流）实现口径不同。

## 相关页面

- [[DeepSeek Harness]] · [[从 Chat UI 到 Agent UI]] · [[Harness Engineering]] · [[HITL 与 AFK]] · [[DeepSeek Harness 源码拆解]] · [[炼丹上岸]] · [[翟星人的实验室]]
