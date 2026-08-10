---
type: source
title: "Claude Code 双插件搭配 superpowers 与 gstack"
domain: [AI编程]
tags: [superpowers, gstack, Claude Code 插件, 插件搭配, CLAUDE.md 配置, skill 路由]
sources: []
source_path: "raw/articles/Claude Code 双插件最佳搭配：superpowers 当大脑，gstack 当手脚.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/ShJ6ogkcI-6qZtFY--XcTA"
author: "[[飞哥]]"
date_published: "2026-04-07"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [superpowers 当大脑 gstack 当手脚, 双插件最佳搭配, 飞哥 superpowers gstack]
---

# Claude Code 双插件搭配 superpowers 与 gstack

> 一句话要点：把 Claude Code 插件砍到只剩 [[Superpowers]]（大脑，思考与流程）和 [[gstack]]（手脚，执行与外部世界）两个，无重叠无冲突，配合 CLAUDE.md 裁决规则即可让 skill 匹配变得可预测。

## 关键要点

- **核心痛点**：oh-my-claudecode、feature-dev、code-review、ralph-loop、document-skills 等插件功能严重重叠，Claude Code 启动时随机匹配、行为不可复现。
- **核心比喻**：**superpowers 是大脑，gstack 是手脚**。前者教 Claude Code 怎么想（拆解需求/规划/系统化排查/自检审查），后者让 Claude Code 真的动起来（开浏览器/跑端到端/ship 到生产）。二者无功能重叠、不抢 skill 匹配。
- **superpowers 是什么**：[[Jesse Vincent]] 团队做的 skill 集合。最关键理念："作者和审查者绝不在同一个上下文里互评"——写完代码不能立刻自我审查，必须新开 reviewer 通道。
- **superpowers 管的三层**：① 计划与思考层 ② 编码与调试层 ③ 审查与验证层。
- **gstack 管的四块**：① 浏览器与 QA ② Ship 与 Deploy ③ 上线后监控 ④ 安全护栏；外加多视角 plan-review。作者强调"gstack 这些能力 superpowers 一个都没有"。
- **CLAUDE.md 五条核心原则**：① 流程归 superpowers；② 执行归 gstack；③ 独立 reviewer 通道；④ 证据优先（"没有证据的完成=没完成"）；⑤ 遇到歧义先 brainstorm。
- **浏览器规则**：`/browse` 是唯一浏览器入口，禁止 `mcp__claude-in-chrome__*` 和 `mcp__computer-use__*`。
- **4 个关键交接点**（本文口径，比 [[Superpowers + gstack 搭配实战]] 少 1 个）：① writing-plans → executing-plans；② executing-plans → /browse 或 /qa；③ verification → requesting-code-review；④ finishing-a-development-branch → /ship。
- **安装四步**：装 superpowers → 装 gstack → 清掉冲突旧插件 → 配置 CLAUDE.md。

## 详细笔记

文章给出可直接抄进 `~/.claude/CLAUDE.md` 的完整配置模板，以及一张覆盖 16 类日常任务的"默认 skill 路由表"。结语点出普适哲学："更少的组件，更清晰的边界，更明确的交接"。

## 与已有内容的关联

- 相关概念：[[Superpowers]]、[[mattpocock skills]]（对照）、[[LLM Wiki]]
- 相关实体：[[飞哥]]（作者）、[[Jesse Vincent]]、[[gstack]]
- 相关源：[[gstack 最佳实战]]、[[Superpowers + gstack 搭配实战]]（本文是该系列最早一篇 2026-04-07，比另两篇早 4-5 天）

## ⚠️ 矛盾 / 待澄清

- **作者不同**：本文作者为 [[飞哥]]（公众号"刷屏AI"/栏目"Vibe Coding"），同簇另两篇为 [[运维有术]]。两作者独立得出高度一致的"superpowers 大脑 + gstack 手脚"搭配结论，互为佐证。
- **superpowers 安装 marketplace 不一致**：本文用 `superpowers@superpowers-marketplace`；[[Superpowers + gstack 搭配实战]] 用 `superpowers@claude-plugins-official`。待澄清。
- **gstack 安装方式不一致**：本文用 `claude plugin marketplace add gstack && claude plugin install gstack`；[[gstack 最佳实战]] 与 [[Superpowers + gstack 搭配实战]] 用 `git clone ... && ./setup`。两种安装路径需在 [[gstack]] 实体页说明并存。
- **交接点数量 4 vs 5**：本文 4 个（缺调试交接）；[[Superpowers + gstack 搭配实战]] 5 个（含 systematic-debugging → /investigate）。
- **superpowers 技能数**：本文列出约 11 个，未给总数；搭配实战文明确为 14 个。差额待核。

## 相关页面

- [[Superpowers]] · [[gstack]] · [[Jesse Vincent]] · [[飞哥]] · [[gstack 最佳实战]] · [[Superpowers + gstack 搭配实战]] · [[mattpocock skills]] · [[LLM Wiki]]
