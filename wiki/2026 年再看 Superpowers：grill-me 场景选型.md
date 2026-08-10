---
type: source
title: "2026 年再看 Superpowers：grill-me 场景选型"
domain: [AI编程]
tags: [Agent Skills, 需求对齐, grill-me, superpowers, 源码解读]
sources: []
source_path: "raw/articles/2026 年再看 Superpowers：轻量需求对齐，grill-me 或许更合适.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491419&idx=1&sn=b700cbfb0d4c1093bab1d68bcd858f8d"
author: "[[运维有术]]"
date_published: "2026-07-16"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [grill-me vs Superpowers brainstorming, Superpowers 与 grill-me 场景选型]
---

# 2026 年再看 Superpowers：grill-me 场景选型

> 一句话要点：[[运维有术]] 拆解 `/grill-me` + `/grilling` 的轻量访谈原语，对照 [[Superpowers]] `brainstorming` 规格生产流程，给出"按目标产物选工具"的边界判断。

## 关键要点

- `/grill-me` 正文只有一行：`Run a \`/grilling\` session.`，设 `disable-model-invocation: true`，是用户主动触发入口；真正定义访谈纪律的是可被其他 Skill 调用的 `/grilling`（12 行）。分层像 CLI（入口面向人）与库函数（原语面向 Skill）。
- `/grilling` 是一台轻量状态机：探索事实 → 找到当前依赖链上靠前的决策 → 一次提一个问题（带推荐答案）→ 等待用户回答 → 更新双方理解 → 用户确认理解完整 → 解锁行动。五条规则卡五种跑偏。
- 关键规则①沿决策树推进；②一次只问一个；③问题必须带推荐答案；④事实与决策分流（CHANGELOG 显示 2026-07-06 提交把两类问题拆开）；⑤共享理解是行动门禁（2026-07-03 提交把完成条件收紧为显式 stop-gate）。
- `/grill-me` 默认追求共享理解但不保证落盘规格；文档能力由组合入口 `/grill-with-docs` 叠加。
- 作者公开课程页建议：编码规划里已不优先 `/grill-me`；需与代码库对齐用 `/grill-with-docs`，`/grill-me` 仍适合做轻量计划压力测试。
- 对照 [[Superpowers]] `brainstorming`：两者都先理解上下文、一次只问一个、给具体建议、人确认前禁实施；分开在**目标产物**——`/grill-me` 压力测试计划，`brainstorming` 是规格生产流程（比较 2-3 种方案 → 写设计文档 → 交 `writing-plans`）。Superpowers 写得重的原因：发布说明记录早期规格审阅循环没进 checklist/流程图时模型会跳过。
- 边界声明（重要）：**当前公开材料没有可复核对照实验**，源码能证明流程差异，证明不了开发速度/成功率/返工率/耗时/token 消耗上的固定收益。

## 详细笔记

- Superpowers Issue #939 记录的摩擦：`brainstorming` 默认输出路径压过项目 `CLAUDE.md` 中的路径偏好——提示"规则越密协调面越大"，但单 Issue 不足以证明整个项目不遵循用户配置。
- `disable-model-invocation` 是具体运行环境的 frontmatter 能力，不同 Agent 工具是否支持要看各自实现。
- 安装：`npx skills@latest add mattpocock/skills`。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[mattpocock skills 推荐工作流速查]]
- 相关实体：[[运维有术]]、[[Matt Pocock]]、[[Superpowers]]
- 相关源：[[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]]、[[mattpocock skills 标准工作流]]、[[grill-me 实战指南]]、[[还在用 grill-with-docs]]

## ⚠️ 矛盾 / 待澄清

- 与本 wiki [[LLM Wiki 方法论 gist]]/CLAUDE.md 规则对照（推论）：colin-wiki 的 ingest/query 流程是"固定步骤 + 人在环判断"，更接近 [[Superpowers]] 的 checklist 风格；而 `/grill-me` 式"少量纪律集中成可嵌入原语 + stop-gate"是另一种思路。两者不冲突，但 colin-wiki 当前未采用 stop-gate 式门禁——值得在方法论语录里记一笔取舍。
- `/grill-me` 7 行 vs 第 1 篇说 12 行：**不矛盾**——7 行指 `grill-me` 入口，12 行指底层 `grilling` 原语；两源一致。
- 行数短 ≠ 更省 token / 效果更好——作者明确声明无对照实验，引用时勿越界。

## 相关页面

- [[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]] · [[mattpocock skills]] · [[mattpocock skills 推荐工作流速查]] · [[运维有术]] · [[Matt Pocock]] · [[Superpowers]] · [[mattpocock skills 标准工作流]] · [[grill-me 实战指南]] · [[还在用 grill-with-docs]]
