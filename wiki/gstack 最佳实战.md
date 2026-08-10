---
type: source
title: "gstack 最佳实战"
domain: [AI编程]
tags: [gstack, Claude Code 插件, AI 工作流, Sprint, 无头浏览器, Garry Tan]
sources: []
source_path: "raw/articles/gstack 最佳实战：23 个 AI 技能，7 步工作流，让一个人跑出完整工程团队的工作量.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247490390&idx=1&sn=566229d5af2f0b899e48fcd25bcd82df"
author: "[[运维有术]]"
date_published: "2026-04-11"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [gstack 23 个 AI 技能, gstack Sprint 工作流]
---

# gstack 最佳实战

> 一句话要点：[[gstack]] 是 [[Garry Tan]]（YC 总裁）开源的 AI 工程工具集，用 23 个斜杠命令 + 8 个工具把 Claude Code 武装成一支虚拟工程团队，核心是一套 Think → Plan → Build → Review → Test → Ship → Reflect 的 Sprint 工作流。

## 关键要点

- **定位**：[[gstack]] 不是框架/SDK/插件，而是一套结构化的 Sprint 工作流工具集，把 Claude Code 变成有完整流程的工程团队。
- **来源数据**（gstack README 自述）：Garry Tan 60 天生成 600,000+ 行生产代码，测试代码占比 35%，一周产出 140,751 行添加/362 次提交；当前版本 v0.16.2.0，MIT 开源。
- **三个设计哲学**（ETHOS.md）：① Boil the Lake（煮沸湖泊）——AI 让"做完整"边际成本接近零，但区分可煮沸的"湖泊"与不可完成的"海洋"；② Search Before Building——三层知识体系（验证过的标准模式 / 新兴社区实践 / 第一性原理推导）；③ User Sovereignty——"AI 模型推荐，用户决策"。
- **技术架构**：`Claude Code → CLI(编译二进制) → HTTP POST → Bun Server → CDP → Chromium(无头)`。选 Bun 而非 Node.js：编译单文件二进制、原生 TypeScript、内置 HTTP 服务器。
- **守护进程模型**：首次调用浏览器约 3 秒启动，之后每次 100-200ms，30 分钟空闲自动关闭；用随机端口（10000-60000）支持多工作空间并行。
- **Ref 系统**：用 `@e1`/`@e2`/`@c1` 引用页面元素，基于 Playwright 无障碍树定位且不修改 DOM。安全模型：只绑定 localhost、Bearer token 认证、Shell 注入防护。
- **Sprint 七阶段技能**：Think `/office-hours`（YC 合伙人角色，强制六个问题）→ Plan `/autoplan`/`/plan-eng-review` → Review `/review`/`/codex`（调 OpenAI Codex 跨模型独立审查）→ Test `/qa`/`/benchmark` → Ship `/ship`/`/land-and-deploy` → Reflect `/retro`/`/learn`。
- **效率压缩比**（ETHOS.md 自述）：样板代码 ~100x、测试编写 ~50x、功能实现 ~30x、Bug 修复+回归 ~20x。作者注明实际效果取决于项目复杂度和模型能力。
- **并行 Sprint 模式**：利用守护进程随机端口设计可同时开 10-15 个 Sprint 会话，这是 Garry Tan 日产万行代码的机制。
- 支持 8 个 AI 编程 Agent，但功能完整体验仅在 Claude Code 上。
- **安全机制（v0.15.12+）**：四层提示注入防御；工具 `/careful`（破坏性命令警告）、`/freeze`（锁定目录）、`/guard`（二者组合）。

## 详细笔记

v0.16.0 起 gstack 浏览器从"点击工具"升级为"数据提取平台"，新增 `media`/`data`/`download`/`scrape`/`archive` 等命令。作者明确指出 gstack 适合独立开发者/3-5 人小团队/开源维护者，不太适合纯新手、已有完善 CI/CD 的大团队、非 Web 开发项目。

## 与已有内容的关联

- 相关概念：[[LLM Wiki]]、[[mattpocock skills]]（另一套 Claude Code skills 体系，对照）
- 相关实体：[[运维有术]]（作者）、[[Garry Tan]]（gstack 作者）、[[gstack]]、[[Matt Pocock]]（对照：另一派 skills 路线）
- 相关源：[[Superpowers + gstack 搭配实战]]、[[Claude Code 双插件搭配 superpowers 与 gstack]]（同簇）

## ⚠️ 矛盾 / 待澄清

- **README 数据可信度**：60 万行/1237 次贡献等数据均来自 gstack 项目自述 README，作者已注明"实际效果取决于项目复杂度和模型能力"，属营销性自报数据，应标注来源。
- 技能数量口径：本文称"23 个 AI 技能 + 8 个工具"，与 [[Superpowers + gstack 搭配实战]] 一致；汇总数"37 个技能（14+23）"依赖 [[Superpowers]] 侧的 14 个计数。

## 相关页面

- [[gstack]] · [[Garry Tan]] · [[运维有术]] · [[Superpowers + gstack 搭配实战]] · [[Claude Code 双插件搭配 superpowers 与 gstack]] · [[Superpowers]] · [[mattpocock skills]] · [[LLM Wiki]]
