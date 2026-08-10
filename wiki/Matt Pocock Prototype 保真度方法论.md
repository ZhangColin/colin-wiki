---
type: source
title: "Matt Pocock Prototype 保真度方法论"
domain: [AI编程]
tags: [mattpocock-skills, prototype, 保真度, UI原型, 逻辑原型]
sources: []
source_path: "raw/articles/别等 Spec 写完才发现不对：AI 编程时代，一文讲透如何用 Prototype 直接看效果.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491601&idx=1&sn=9c1746e2271297eb7ab8f3375775a84f"
author: "[[运维有术]]"
date_published: "2026-08-04"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [prototype 方法论, 保真度方法论, UI 原型 vs Logic 原型]
---

# Matt Pocock Prototype 保真度方法论

> 一句话要点：[[运维有术]] 详解 `/prototype` skill——"throwaway code that answers a question"，用可运行代码代替文字描述讨论设计；分 UI 分支（结构不同变体 + `?variant=` 切换）与 Logic 分支（纯 reducer + TUI shell），核心是当原型成本趋零时讨论保真度就该更高。

## 关键要点

- **prototype 定义**（原文）："A prototype is throwaway code that answers a question"。解决"还没法用语言精确描述需求时，先用可运行代码把问题可视化"——用眼睛做评审而非用文字做翻译。
- **AI 改变原型成本等式**：传统 prototype 要半天搭环境+半天做交互；AI 下原型三约束（一条命令跑、默认无持久化、跳过打磨）使其几分钟生成。**当原型成本趋近零，讨论保真度就该更高。**
- **保真度光谱**：低保真问题（架构选择、模块划分）→ grilling/to-spec；中低（接口设计）→ grilling + spec；高保真问题（布局/交互节奏→ prototype UI；状态机/业务逻辑边界→ prototype Logic）。
- **切换信号**：当关键问题变成 "how should it look" 或 "how should it behave" 时切 prototype；还在"要不要做/接口怎么设计"时继续 grilling。
- **UI 分支**：同一路由生成**结构不同**变体（`?variant=` 切换），三约束——(1) 变体须结构不同（"not just different colours"，只差颜色是 tweak 非 prototype）；(2) 优先嵌入已有页面 sub-shape A；(3) 新路由兜底 sub-shape B（路径含 `prototype` 字样）。
- **Matt tldraw demo**：生成 A/B/C 三变体，约 **10 万 token** 后 compact，口述偏好后 AI 生成融合版 D。关键：原型**集成在 live page 上非独立路由**。
- **Logic 分支**：纯逻辑终端小程序 + TUI shell，约束"Keep it pure: no I/O, no terminal code, no console.log for control flow"。逻辑模块（reducer `(state,event)=>state`）可移植进生产代码，TUI shell 是 throwaway。
- **分支选错代价**（原文 "Getting this wrong wastes the whole prototype"）：判断——**问题关键词是 "look" 还是 "behave"**。
- **六条通用规则**：(1) Throwaway from day one 明确标记；(2) 一条命令跑；(3) 默认无持久化；(4) 跳过打磨；(5) Surface the state；(6) 完成后 capture answer（验证过的决定迁真实代码，原型本体作 primary source 提交 throwaway branch 非 main）。
- **进出主链路**：grilling 到"需看运行效果"时 `/handoff` 压成文档 → 新会话加载跑 `/prototype`；满意后 `/handoff` 回原始会话；实施交给 AFK agent。
- **三个反例**：(1) 把原型直接 promote 成生产代码（"Rewrite it properly when you fold it in"）；(2) 变体只差颜色不差结构（"三个不同色卡片是 wallpaper 不是 prototype"）；(3) UI 问题走 Logic 分支或反之。

## 详细笔记

作者用订单状态追踪页加搜索的 Spec 驱动 4 轮失败案例对照 prototype 价值：每轮纯文字→凭空想象→拿到代码→发现不对→重新描述，信息损耗越滚越大。可运行代码是"最高精度的需求文档"。prototype 的"答案"不是代码本身，而是代码验证过的那个设计决定。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[mattpocock skills 推荐工作流速查]]、[[HITL 与 AFK]]
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[Matt Pocock skills Wayfinder 官方文档]]、[[Matt Pocock skills Handoff 官方文档]]、[[Matt Pocock wayfinder handoff 接力协议]]、[[Matt Pocock 三个 skill 结构词汇]]

## ⚠️ 矛盾 / 待澄清

- 与 [[mattpocock skills]] 概念页的 `/prototype` 描述**一致**，本页补 UI/Logic 双分支与保真度光谱，是操作细节的展开，无矛盾。
- 与 [[Matt Pocock skills Wayfinder 官方文档]] 的 Prototype ticket 定义（HITL）**完全一致**；本页把"保真度"显式概念化。
- 待澄清：tldraw demo 约 10 万 token 后 compact——这个 token 量与 smart zone（见 [[smart zone]]）的关系提示 prototype 会话也可能逼近 dumb zone。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock skills Wayfinder 官方文档]] · [[Matt Pocock skills Handoff 官方文档]] · [[Matt Pocock wayfinder handoff 接力协议]] · [[Matt Pocock]] · [[运维有术]]
