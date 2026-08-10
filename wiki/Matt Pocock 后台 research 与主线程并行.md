---
type: source
title: "Matt Pocock 后台 research 与主线程并行"
domain: [AI编程]
tags: [mattpocock-skills, research, AFK, HITL, 前台后台并行, 落盘契约]
sources: []
source_path: "raw/articles/别再一对一盯着 Agent 了：Matt Pocock 的后台 research，主线程继续干活.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491632&idx=1&sn=3aa920842652d1e9cdeb2bde850a84fb"
author: "[[运维有术]]"
date_published: "2026-08-07"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [后台 research, AFK 前台后台并行, research 落盘契约]
---

# Matt Pocock 后台 research 与主线程并行

> 一句话要点：[[运维有术]] 用"主线程审 PR + 后台查第三方 API"的连续案例，讲清 research/wayfinder/triage 怎么把活拆成前台（HITL）与后台（AFK）并行——后台铁律是追一手来源 + 落盘，主线程用 claim/分支/指针/契约四道边界防踩脚。

## 关键要点

- **错误分配**：大多数人"一对一盯着 Agent"——问一句等一句，人成 Agent 附属品。成熟工作流应拆前台（你专注做）+ 后台（Agent 并行跑）。
- **research skill 三条铁律**（全文仅 12 行）：(1) **只调研一手来源**（primary sources），每条声明须能回溯到拥有它的来源；(2) **产物是单个 Markdown 文件落仓库**；(3) 落盘是后台模式的必然要求——你不等它，结果只在会话里等于不存在。
- **社区批评（官方收录，未解决）**："five research subagents pointed at junk just gives you five confident wrong answers faster"——research **无 allowlist、无 domain gate、无 verification pass**，门控问题从提出起就有人反对，官方至今未正面回答。
- **wayfinder 的 HITL/AFK 划线**：Research 票（AFK）、Task 票（AFK 或 HITL）、Grilling 票（HITL 默认）、Prototype 票（HITL）。划线逻辑：**需做决定的 HITL，纯机械执行的 AFK**。
- **铁律**："a grilling agent that answers its own questions has broken this"——grilling 自问自答 = 协议已坏。
- **四道边界机制**（防并发踩脚）：(1) **claim 先认领再开工** + tracker native blocking 边；(2) **throwaway `research/<name>` 分支隔离**；(3) **context pointer 留在 ticket**；(4) **`ready-for-agent` + AGENT-BRIEF.md 契约**。
- **agent brief 四要点**：Durability over precision、Behavioral not procedural、Complete acceptance criteria、Explicit scope boundaries。
- **AFK/HITL 判断核心就一问**：这活需要做决定还是只做执行？读一手资料/搭分支/机械接入/跑验证 → AFK；需求对齐/设计取舍/prototype 评审/merge 冲突 → HITL。
- **`diagnosing-bugs` 也可部分后台化**：Phase 3 生成假设须展示给用户再测，但 "Don't block on it——proceed with your ranking if the user is AFK"。提假设是机械的，选假设才是人该干的。
- **handoff ≠ AFK**：handoff 是**串行接力**（文件是启动上下文），AFK 是**并行**（research 文件是参考资料）——接力棒 vs 路标。
- **四个反例**：(1) 把 grilling 丢后台；(2) 后台拿二手资料；(3) 主线程与后台同改一批文件；(4) research 结果没落盘。
- **官方收录的未解 bug**：research 嵌套 bug（issue #530，至今 open）——background agent 会再 spawn background agent，有人测到单个 research 烧约 **45 万 tokens**；结果不复用。
- 官方建议：**先用 HITL 跑**（running-your-afk-agent 文档）。作者结论：把后台 agent 当**需验收的临时同事**，不是免检的自动化流水线。

## 详细笔记

作者用支付回调重构 PR 审查案例贯穿全文：主线程逐行审 diff 时发现第三方回调 API 边界假设疑点，拉起 research 后台 Agent 查重试/签名/幂等行为，T0-T4 时间线显示两边无互相等待。findings 文件每行结论后跟官方文档章节链接 + 源码行号，体现"research 喂养思考但不替代思考"。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[HITL 与 AFK]]、[[smart zone]]
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[Matt Pocock skills Wayfinder 官方文档]]、[[Matt Pocock skills Handoff 官方文档]]、[[Matt Pocock 三条 on-ramp 分流]]

## ⚠️ 矛盾 / 待澄清

- 与 [[mattpocock skills]] 概念页的 `/research` 描述（"针对高可信一手来源调研"）**一致但补充**：本页补"一手来源无门控"的未解批评与嵌套 bug，是扩展非矛盾。
- "agent 在 wayfinder 会话中间开始写生产代码"的失败案例——对应 [[Matt Pocock wayfinder handoff 接力协议]] 提到的 issue #683（effort 字段可覆盖 plan-only），**两源互证**。
- 仓库 stars：本源记"截至 2026 年 7 月约 16 万"，与 [[mattpocock skills]] 概念页"170K（2026-07-26）"基本吻合，非冲突。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock skills Wayfinder 官方文档]] · [[Matt Pocock skills Handoff 官方文档]] · [[Matt Pocock 三条 on-ramp 分流]] · [[Matt Pocock]] · [[运维有术]] · [[HITL 与 AFK]]
