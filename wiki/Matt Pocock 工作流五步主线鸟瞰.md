---
type: source
title: "Matt Pocock 工作流五步主线鸟瞰"
domain: [AI编程]
tags: [Agent Skills, 工作流, grilling, TDD, code-review]
sources: []
source_path: "raw/articles/Matt Pocock 的工作流：从一个模糊想法到可交付代码.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/Fm42nn07D9D41UR1Vx9zng"
author: "[[鸟窝]]"
date_published: "2026-07-14"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [鸟窝解读 Matt Pocock 工作流, 从模糊想法到可交付代码]
---

# Matt Pocock 工作流五步主线鸟瞰

> 一句话要点：[[鸟窝]]（Go 圈作者）把 Matt Pocock skills 的主线概括成 5 个命令串联的"产线"，核心是把"想清楚"和"写代码"彻底分开。

## 关键要点

- 主线 5 步：`/grill-with-docs` → `/to-spec` → `/to-tickets` → `/implement` → `/code-review`——"先把想法拷问清楚 → 写成规范 → 拆成卡片 → 动手实现 → 双轴评审"。
- 工程类 skill 分两拨：User-invoked（用户手动 `/` 调，零 context load）和 Model-invoked（模型自己触发，description 占用每轮上下文）。
- `wayfinder` 在 `grill-with-docs` 上游：wayfinder 处理"又大又模糊、一个会话装不下"的项目，产出是决策而非交付物；grill-with-docs 处理"一个会话内、已明确"的想法。Matt 现主推 wayfinder，但两者互补不替代。
- `to-tickets` 引入"示踪弹（tracer-bullet）垂直切片"：一刀贯穿 schema/API/UI/测试，每片可独立演示；明确反对"水平横切"（先做完所有 schema 再做所有 API）。
- `code-review` 双轴并行（Standards 轴查编码规范+Fowler 异味基线，Spec 轴查是否忠于 PRD），两条作为并行子 Agent 跑、互不污染。
- productivity 类含 `grill-me`、`handoff`、`teach`、`writing-great-skills`；其中 `grilling` 是底层可复用循环，`grill-me`/`grill-with-docs` 都建在它之上——"拷问是 Matt 方法论的地基"。
- 安装命令：`npx skills add mattpocock/skills`。作者本人用 pigo（Go 版 pi agent）验证过这套流程，承认 Matt 的 skills "功能最好但文档跟不上"。

## 详细笔记

鸟窝把 engineering 大类做成两张表（user-invoked 9 个、model-invoked 8 个），明确"主线核心是 5 个 user-invoked 技能（外加 wayfinder）"。其余 skill（如 `research`、`resolving-merge-conflicts`、`improve-codebase-architecture`）属支撑类。`handoff` 与主线首尾呼应，被其他 skill 借用。`ask-matt` 是路由器，根据处境帮你挑工作流。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[mattpocock skills 推荐工作流速查]]
- 相关实体：[[Matt Pocock]]、[[鸟窝]]
- 相关源：[[mattpocock skills 标准工作流]]、[[Matt Pocock main flow 五环节]]、[[Matt Pocock skills Wayfinder 官方文档]]

## ⚠️ 矛盾 / 待澄清

- 本文将主线第一步描述为 `/grill-with-docs`（wayfinder 在其上游作为可选）；运维有术的 main flow 文章也持此观点，一致。本文写作时（2026-07-14）对 wayfinder 表述为"Matt 现主推"，与 v1.1.0 官方日志"wayfinder 正式化"时间线吻合，无明显矛盾。
- 注：本文为 [[鸟窝]] 视角（外部使用者实测，用 Go 版 agent 验证），与 [[运维有术]] 系列的源码解读视角互补。

## 相关页面

- [[Matt Pocock]]
- [[mattpocock skills]]
- [[鸟窝]]
- [[Matt Pocock main flow 五环节]]
