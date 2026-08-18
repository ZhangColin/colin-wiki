---
type: source
title: "Matt Pocock 会话边界五问决策树"
domain: [AI编程]
tags: [mattpocock-skills, PHASE-BOUNDARIES, 上下文管理, compact, clear, handoff, subagent]
sources: []
source_path: "raw/articles/AI 编码会话的 5 次边界抉择：为什么 compact 垫底.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491677&idx=1&sn=828c1d04d4c5f552433920be83172d18"
author: "[[运维有术]]"
date_published: "2026-08-13"
created: 2026-08-17
updated: 2026-08-17
status: active
aliases: [PHASE-BOUNDARIES, 五问决策树, compact 垫底, 会话边界]
---

# Matt Pocock 会话边界五问决策树

> 一句话要点：[[运维有术]] 解读 [[mattpocock skills]] 仓库 `ask-matt` 下的 PHASE-BOUNDARIES.md——阶段交界处的五问串行决策树（Continue → /clear → /handoff → subagent → /compact），核心模型是"**上下文是一次性资产，除 Continue 外每个边界动作都是把一手压成二手的损耗交换**"，所以 compact 垫底。

## 关键要点

- **五问成立条件（先碰到的 yes 赢）**：①**Continue**——下一阶段需要本阶段一手来源（grilling→implementation 要推理原文），或 smart zone 预算还够；②**/clear**——上下文与下一步完全无关（典型：ticket 之间），代价单向（丢"为什么"）；③**/handoff**——很窄，四选一（换 harness/换目录/交给同事/mid-phase 叉支线），买到可移植性；④**subagent**——任务紧到能 AFK、不阻塞主会话（典型：自动评审）；⑤**/compact**——兜底，其余四问都不成立才落这里，且要**带指令**（`/compact we're going to QA this area`）。
- **计费接入案例 6 处边界**：grilling→spec 继续；spec→tickets 继续（官方：grill→spec→tickets 保持一个不中断窗口，**到 to-tickets 之后才允许 compact/clear**）；tickets→implement **/clear**（需求已落盘，实现只认 ticket）；ticket 间 **/clear**；评审 **subagent**（双轴并行 AFK）；评审→QA **/compact**（结论散在 diff 会话里、同 harness 同目录、需留在循环）。
- **一手 vs 二手模型**：一手（Continue）信息全/噪音多/活动空间小；二手（/compact、/handoff）有损/干净/活动空间大。**subagent 特殊：把损耗"转移"而不是"支付"**——主会话窗口不动，token 密集活在子窗口完成，还你一份二手报告。
- **Context Rot 论文结论（反直觉）**：**退化是输入长度本身造成的，不是内容脏**——实验把非目标 token 全换成空格，退化照样发生；没法靠"把上下文收拾干净"省钱，长度本身就在花钱。
- **smart zone 新口径**：[[Matt Pocock]] 经验法则——**约 40% context 处开始退化，前 40% 是 smart zone、后 60% 是 dumb zone**；边界在哪有争议，但人人都同意边界存在。他还吐槽 Anthropic 的 Ralph 插件：bash 循环迭代累积在一个会话，三到四次迭代后 agent 完全在 dumb zone。
- **会话预算纪律**：一次会话一个任务，每个任务给它会话最锐利的部分；单个任务超过一个 smart zone 就拆，在自然边界 handoff 或 compact。
- **顺序即逻辑**：Continue 先问（零成本 rule it out first）；/clear 第二（代价单向）；/compact 垫底（从它开始按的失败模式=新会话对被压扁的决策**"自信地错"**——每步看起来都对，最难发现）。/clear vs /handoff：handoff 写文件可恢复（可逆），clear 什么都不留（单向）。
- **这是判断题不是客观题**：同一处边界两天可能走两条路都合理；价值不在"选对"，在**"在边界问、按顺序问"**。mid-phase 没有决策可做，只有继续或拆 subagent 两条出路。
- **四反例**：上下文很热就 /clear（why 丢了）；stage 中间 /compact（丢 thread 只能从摘要猜）；本可 Continue 却 /handoff（推理原文被压成摘要）；万事皆 compact（压三次后自信地错，全程无报错）。
- 平台注记：以 Claude Code / Codex 为默认环境，`/compact`、`/clear`、subagent 不是所有平台都有等价物。
- Medium 评价：别把这仓库当提示词抄，**"最短的那个 skill 文件才是关键"**——很可能指的就是它。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[smart zone]]（40% 口径 + Context Rot 机制）、[[HITL 与 AFK]]（subagent 一档）
- 相关实体：[[Matt Pocock]]、[[运维有术]]、[[Anthropic]]（Ralph 插件吐槽对象）
- 相关源：[[Matt Pocock main flow 五环节]]（"每个 implement 开新会话"的边界依据）、[[Matt Pocock wayfinder handoff 接力协议]]（/handoff forks, /compact continues）、[[Matt Pocock skills Handoff 官方文档]]、[[Matt Pocock 后台 research 与主线程并行]]

## ⚠️ 矛盾 / 待澄清

- **smart zone 再添新口径**：本文给"**约 40% context 处开始退化**"（相对比例）；此前口径为绝对值 ~120K-150K。两者不必然矛盾（长上下文模型下 40% ≈ 绝对值区间），但属又一种说法——已并入 [[smart zone]] 口径表。
- 与 [[Matt Pocock wayfinder handoff 接力协议]] 的 "/handoff forks，/compact continues" 一致；本文补决策顺序与成立条件。
- Context Rot 论文为转述（未给原始出处链接），引用论文结论时建议回查原文（推论提示）。

## 相关页面

- [[mattpocock skills]] · [[smart zone]] · [[HITL 与 AFK]] · [[Matt Pocock main flow 五环节]] · [[Matt Pocock wayfinder handoff 接力协议]] · [[Matt Pocock skills Handoff 官方文档]] · [[Matt Pocock]] · [[运维有术]]
