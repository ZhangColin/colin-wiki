---
type: source
title: "12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争"
domain: [AI编程]
tags: [Agent Skills, 源码解读, 路线对比, superpowers]
sources: []
source_path: "raw/articles/12 行 vs 689 行：mattpocockskills 与 superpowers 的路线之争.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491359&idx=1&sn=a47c8a78d7d915f79698dbb264a8caf9"
author: "[[运维有术]]"
date_published: "2026-07-09"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [mattpocock skills vs superpowers 路线之争]
---

# 12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争

> 一句话要点：[[运维有术]] 对 mattpocock/skills v1.1.0 做源码深度解读，提炼其与 [[Superpowers]] 的根本路线分歧——前者用最小锚点 + router 兜底，后者用 hook 强制 + 红旗表堵死退路。

## 关键要点

- 定位反差：`grilling` 的 SKILL.md 连 frontmatter 共 12 行；[[Superpowers]] 的 `writing-skills` 写了 689 行。前者不是敷衍，是"一套完整工程哲学的压缩结果"。
- 数量对比（写作时 GitHub API）：mattpocock/skills star **160,750**，superpowers star **249,522**；mattpocock/skills 仓库 2026 年 2 月才建，4 个月出头。
- v1.1.0 合计 **18 个 promoted skill**（12 user-invoked + 6 model-invoked），**不是早期流传的 21**；主流程已改名/合并：`to-prd`→`to-spec`，`to-issues` 并入 `to-tickets`，`wayfinder` 和 `code-review` 从 in-progress 毕业到 engineering。
- 唯一划分轴：`user-invoked` 设 `disable-model-invocation: true`，模型永远不能自动触发；18 个里 12 个是 user-invoked，意味着只有 6 个 skill 的 description 进入模型上下文。依赖单向：user-invoked 可调 model-invoked，**user-invoked 之间不能互相调用**。
- `ask-matt`（76 行路由中枢）是权威路由：与其让 18 个 skill 全塞进上下文互相干扰，不如只露几个给模型、剩下靠 router 兜底。
- 对照 [[Superpowers]]：用 hook 注入"哪怕只有 1% 的可能某个 skill 适用也必须调用"，红旗表逐条堵死模型借口，代价是 token 消耗大（HN 单一样本："aside from consuming a stupid amount of tokens"）。
- 四大支柱全源自经典软件工程书：Grilling（《The Pragmatic Programmer》）、CONTEXT.md / ubiquitous language（Eric Evans《DDD》）、TDD + tracer bullet、深模块（Kent Beck + Ousterhout）。
- writing-great-skills 的核心美德是 **predictability（过程一致而非输出一致）**；关键词汇：leading words、progressive disclosure、completion criterion、sediment、no-op、negation。
- 两条路线共存可行：mattpocock 大部分命令不参与自动触发，superpowers 的 hook 只强推自己那 14 个，互不冲突。

## 详细笔记

- skill 演化证据（`deprecated/` 目录）：`design-an-interface` 被吸收进 `codebase-design`；`qa` 重构进 `triage`；`ubiquitous-language` 并入 `domain-modeling`。信号：skill 数量会精简，不会膨胀。
- Context hygiene 铁律：grilling → spec → tickets 必须保持在同一未打断 context window（不 compact、不清空），上限约 smart zone，逼近就 `/handoff` 转新会话。
- `to-tickets`（114 行）对 wide refactor 不用 tracer bullet，改用 expand-contract。
- v1.1.0 修复点：`grilling` 区分 fact 与 decision，堵住 agent 在 resolve-ticket 框架里自己回答自己的决策问题。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[Skills 设计方法论]]、[[mattpocock skills 推荐工作流速查]]
- 相关实体：[[运维有术]]、[[Matt Pocock]]、[[Superpowers]]
- 相关源：[[mattpocock skills 标准工作流]]、[[Matt Pocock skills v1.1 官方更新日志]]、[[Matt Pocock skills Wayfinder 官方文档]]、[[Matt Pocock skills Handoff 官方文档]]、[[MattPocock Skills v1.1 重磅更新]]、[[2026 年再看 Superpowers：grill-me 场景选型]]

## ⚠️ 矛盾 / 待澄清

- **promoted skill 数量**：本源明确为 v1.1.0 的 **18 个**（12+6），并指出"不是早期流传的 21"。若 wiki 已有页面仍写 21（如 [[MattPocock Skills 21 个 skill 工程纪律体系]] 的早期口径），应同步修正——详见 [[mattpocock skills]] 概念页口径说明。
- **star 数据**：160,750 / 249,522 为写作时 API 快照，会持续变动；引用时需注明时点。
- 与 [[Superpowers]] 的对照是作者个人观察，**不构成推荐或否定**（原文声明）；HN 评价为单一样本，不可外推。

## 相关页面

- [[mattpocock skills]] · [[Skills 设计方法论]] · [[mattpocock skills 推荐工作流速查]] · [[运维有术]] · [[Matt Pocock]] · [[Superpowers]] · [[Matt Pocock skills v1.1 官方更新日志]] · [[Matt Pocock skills Wayfinder 官方文档]] · [[Matt Pocock skills Handoff 官方文档]] · [[mattpocock skills 标准工作流]] · [[2026 年再看 Superpowers：grill-me 场景选型]]
