---
type: concept
title: HITL 与 AFK
domain: [AI编程, 软件工程]
tags: [HITL, AFK, 人机协作, 前台后台并行, 人机分工]
sources:
  - "[[Matt Pocock 后台 research 与主线程并行]]"
  - "[[Matt Pocock skills Wayfinder 官方文档]]"
  - "[[Matt Pocock wayfinder handoff 接力协议]]"
  - "[[Matt Pocock 三条 on-ramp 分流]]"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases:
  - HITL / AFK
  - 前台后台并行
  - human in the loop / away from keyboard
---

# HITL 与 AFK

> 一句话定义：[[mattpocock skills]] 划分人机分工的二元判据——需做决定的事 HITL（human in the loop，人在环），纯机械执行的事 AFK（away from keyboard，agent 自主跑）；核心判据就一问："这活需要做决定，还是只做执行？"

## 核心观点

### 划线准则

- **AFK（agent 自主）**：读一手资料/文档/API、搭临时分支、机械接入、跑验证——事实收集与机械执行，不需要拍板。
- **HITL（人在场）**：需求对齐、设计取舍、prototype 评审、merge 冲突解决、judgment call——产出"决定"本身，必须人在场。

### wayfinder 四类 ticket 的归属

- **Research 票（AFK）**：读文档/查事实。
- **Task 票（AFK 或 HITL）**：决策前的机械前置工作，"做而不决定"。
- **Grilling 票（HITL，默认）**：一问一答对齐需求。
- **Prototype 票（HITL）**：做廉价粗糙 artifact 提升保真度。

### 核心铁律

"a grilling agent that answers its own questions has broken this"——grilling 自问自答 = 协议已坏，对齐变自嗨。需求对齐必须发生在人和 Agent 的对话里。

## 四道边界机制（防并发踩脚）

前台（HITL 主线程）与后台（AFK agent）并行时，用四道边界防互相踩脚：

1. **claim 先认领再开工** + tracker native blocking 边（防抢同一张票）。
2. **throwaway `research/<name>` 分支隔离**（防污染主分支）。
3. **context pointer 留在 ticket**（指向 findings 文件/分支，防知识散落会话记忆）。
4. **`ready-for-agent` + AGENT-BRIEF.md 契约**（防 agent 跑偏）。agent brief 四要点：Durability over precision、Behavioral not procedural、Complete acceptance criteria、Explicit scope boundaries。

## wizard：脚本化 HITL（v1.2 新形态，2026-08）

v1.2 毕业的 `wizard` 补上了 HITL 谱系的第三种形态——**脚本化 HITL**：

- **运行时 HITL**（主流框架）：agent 运行中暂停等确认（Haystack confirmation policy、LangGraph halt/resume、ag2 human_input_mode）。
- **脚本化 HITL**（wizard）：agent 撞到"只有人能做的步骤"（配密钥/开面板/一次性迁移）时，生成交互式 bash 向导，**agent 只写脚本、从不运行它；脚本由人在自己的机器上运行**——"Nothing is sent to an agent while it runs"（密钥不经 agent）。产物可复用/可提交/可版本化，代价是无即时反馈，用静态检查+逐值流向追踪补。
- 与 grilling 的分工：**wizard=执行型人机交接（人怎么参与），grilling=决策型对齐（做什么、为什么）**。官方定位："sitting at the line where automation stops and a human has to click"——不扩大自动化范围。
- 姊妹机制：`to-questionnaire`（v1.2）——"grill the send, not the subject"，答案只在别人脑子里时生成问卷而非问 agent。

详见 [[Matt Pocock wizard 人墙自动化]]。

## handoff ≠ AFK（易混点澄清）

两者都用写文件、留指针作为交接手段，但语义完全不同：

- **handoff = 串行接力**：会话结束→新会话接上；handoff 文件是**给下一个会话的启动上下文**（接力棒）。
- **AFK = 并行**：同一时间主线程与后台 agent 各干各的，主线程不阻塞；research 文件是**给当前会话主线程的参考资料**（路标）。

## research 落盘契约（AFK 的必然要求）

后台 agent 查完你不会立刻问它（你不在等它），所以结果必须**落盘成带引用的单个 Markdown 文件**——"我不在等你的输出，所以你的输出必须能在我需要的时候被找到"。这是异步的本质。

## ⚠️ 矛盾 / 未解问题

- **官方未收敛的边界争议**：issue #667（`wayfinder:task` 类型在 HITL/AFK 间摇摆）；issue #683（Notes 的 `effort` 字段可覆盖 `Plan, don't do` 默认，留越权执行口子，社区 pushback 但官方未改）。
- **real-world 越权**：官方收录投诉"agent 在 wayfinder 会话中间开始写生产代码"（往 Notes 写 `this map carries execution` 当授权）——正是 effort 口子的现实后果。
- **research 无门控**（官方收录未解批评）：research skill 无 allowlist、无 domain gate、无 verification pass——"five research subagents pointed at junk just gives you five confident wrong answers faster"。
- **research 嵌套 bug**（issue #530，至今 open）：background agent 会再 spawn background agent，有人测到单个 research 烧约 45 万 tokens。
- 官方建议：**先用 HITL 跑**（running-your-afk-agent 文档），观察 agent 行为再逐步放手。把后台 agent 当**需验收的临时同事**，不是免检的自动化流水线。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock 后台 research 与主线程并行]] · [[Matt Pocock skills Wayfinder 官方文档]] · [[Matt Pocock wayfinder handoff 接力协议]] · [[Matt Pocock 三条 on-ramp 分流]] · [[Matt Pocock skills Handoff 官方文档]] · [[Matt Pocock wizard 人墙自动化]] · [[Matt Pocock 会话边界五问决策树]] · [[smart zone]]
