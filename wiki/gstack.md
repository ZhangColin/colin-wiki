---
type: entity
title: gstack
domain: [AI编程, AI工具]
tags: [gstack, Claude Code 插件, Sprint, 无头浏览器, Garry Tan]
sources:
  - "[[gstack 最佳实战]]"
  - "[[Superpowers + gstack 搭配实战]]"
  - "[[Claude Code 双插件搭配 superpowers 与 gstack]]"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases:
  - gstack
  - garrytan/gstack
---

# gstack

> 一句话定义：[[Garry Tan]]（YC 总裁）开源的 AI 工程工作流工具集（MIT，仓库 `github.com/garrytan/gstack`），用 23 个斜杠命令 + 8 个工具把 Claude Code 武装成一支虚拟工程团队，核心是一套 Think → Plan → Build → Review → Test → Ship → Reflect 的 Sprint 工作流。

## 概述

gstack 不是框架/SDK/插件，而是一套结构化的 Sprint 工作流工具集。每个阶段（Think/Plan/Build/Review/Test/Ship/Reflect）都有对应 AI 技能执行。在本 wiki 中，gstack 与 [[Superpowers]] 构成"手脚 vs 大脑"的经典搭配——superpowers 管思考与流程，gstack 管执行与外部世界（浏览器/QA/ship/deploy/canary），二者无功能重叠。

## 关键属性 / 事实

- **作者**：[[Garry Tan]]（Y Combinator 总裁）。
- **技术架构**：`Claude Code → CLI(编译二进制) → HTTP POST → Bun Server → CDP → Chromium(无头)`。选 Bun 而非 Node.js。
- **守护进程**：首次调用约 3 秒启动，之后 100-200ms，30 分钟空闲自动关闭；随机端口支持多工作空间并行（可同时开 10-15 个 Sprint）。
- **Ref 系统**：`@e1`/`@e2`/`@c1` 引用页面元素，基于 Playwright 无障碍树，不修改 DOM。
- **Sprint 七阶段技能**：`/office-hours`（Think）→ `/autoplan`（Plan）→ `/review`/`/codex`（Review）→ `/qa`/`/benchmark`（Test）→ `/ship`/`/land-and-deploy`（Ship）→ `/retro`/`/learn`（Reflect）。
- **安全机制（v0.15.12+）**：四层提示注入防御；`/careful`、`/freeze`、`/guard`。
- **设计哲学（ETHOS.md）**：Boil the Lake / Search Before Building（三层知识体系）/ User Sovereignty（"AI 模型推荐，用户决策"）。
- **支持 8 个 AI 编程 Agent**，但功能完整体验仅在 Claude Code 上。

## ⚠️ 矛盾 / 待澄清

- **安装方式两路径并存**：`claude plugin marketplace add gstack && claude plugin install gstack`（[[Claude Code 双插件搭配 superpowers 与 gstack]]）vs `git clone ... && ./setup`（[[gstack 最佳实战]]、[[Superpowers + gstack 搭配实战]]）。
- **自述数据未独立核实**：60 天 60 万行/1237 贡献/压缩比 100x 等出自 gstack README/ETHOS.md，属营销性自报。
- **设计审查技能名不一**：`/qa-design-review`（飞哥）vs `/plan-design-review`（运维有术路由表）。

## 演变 / 时间线

- 当前版本 v0.16.2.0（截至 [[gstack 最佳实战]] 写作）。
- v0.16.0：浏览器从"点击工具"升级为"数据提取平台"（新增 media/data/scrape/archive 等）。
- v0.15.12+：引入四层提示注入防御。

## 相关页面

- [[Garry Tan]] · [[Superpowers]] · [[运维有术]] · [[飞哥]] · [[gstack 最佳实战]] · [[Superpowers + gstack 搭配实战]] · [[Claude Code 双插件搭配 superpowers 与 gstack]] · [[Superpowers 与 gstack 搭配（大脑与手脚）]] · [[mattpocock skills]]
