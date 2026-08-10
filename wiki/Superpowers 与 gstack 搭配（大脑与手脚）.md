---
type: synthesis
title: "Superpowers 与 gstack 搭配（大脑与手脚）"
domain: [AI编程, AI工具]
tags: [Superpowers, gstack, 插件搭配, 大脑手脚, CLAUDE.md, 技能路由]
sources:
  - "[[gstack 最佳实战]]"
  - "[[Superpowers + gstack 搭配实战]]"
  - "[[Claude Code 双插件搭配 superpowers 与 gstack]]"
created: 2026-08-10
updated: 2026-08-10
status: active
confidence: high
aliases:
  - superpowers + gstack 搭配
  - 大脑与手脚
  - 双插件搭配
---

# Superpowers 与 gstack 搭配（大脑与手脚）

> 一句话结论：把 Claude Code 插件砍到只剩 [[Superpowers]]（大脑：思考与流程）+ [[gstack]]（手脚：执行与外部世界），二者能力边界几乎无重叠，在安装路径/命名空间/触发机制三层互不冲突，通过 4-5 个交接点串成从想法到上线的闭环。**两位独立作者（[[飞哥]] 与 [[运维有术]]）相隔数日得出高度一致结论**，互为佐证。

## 问题

Claude Code 插件多到 skill 抢匹配、行为不可复现，怎么搭最简？

## 对比 / 分析

### 边界划分（核心比喻）

| | [[Superpowers]]（大脑） | [[gstack]]（手脚） |
| --- | --- | --- |
| **管什么** | 怎么想：拆需求/规划/系统化排查/自检审查 | 真的动起来：开浏览器/跑端到端/ship/上线监控 |
| **作者背景** | [[Jesse Vincent]]（开源老兵，重工程规范） | [[Garry Tan]]（YC 总裁，重全生命周期交付） |
| **触发** | 自动（hook + 红旗表） | 手动（斜杠命令） |
| **能力** | brainstorming/writing-plans/TDD/systematic-debugging/code-review/verification | `/browse`/`/qa`/`/ship`/`/land-and-deploy`/`/canary`/`/careful`/`/freeze` |

关键论断（[[Claude Code 双插件搭配 superpowers 与 gstack|飞哥]]）："gstack 这些能力 superpowers 一个都没有"——二者无功能重叠、不抢 skill 匹配。

### 三层不冲突

1. **安装路径隔离**：superpowers 落 `~/.claude/skills/superpowers-*`，gstack 落 `~/.claude/skills/gstack/`。
2. **命名空间隔离**：可用 `./setup --prefix` 给 gstack 加 `/gstack-` 前缀避免重叠。
3. **触发机制互补**：superpowers 处理编码过程决策点，gstack 处理产品流程决策点，不抢同一触发点。

### 交接点（两作者口径差一）

| 飞哥（4 个） | 运维有术（5 个，多调试交接） |
| --- | --- |
| writing-plans → executing-plans | brainstorming → /autoplan |
| executing-plans → /browse 或 /qa | writing-plans → /plan-eng-review |
| verification → requesting-code-review | test-driven-development → /qa |
| finishing-a-development-branch → /ship | systematic-debugging → /investigate |
|  | finishing-a-development-branch → /ship |

差异源于飞哥未单列调试交接。标准开发闭环约 13 步，superpowers 处理编码质量环节、gstack 处理外部世界环节、交替工作。

### CLAUDE.md 裁决规则（让 skill 匹配可预测）

五条核心原则（[[Claude Code 双插件搭配 superpowers 与 gstack|飞哥]]）：① 流程归 superpowers；② 执行归 gstack；③ 独立 reviewer 通道（作者与审查者不同上下文）；④ 证据优先（"没有证据的完成=没完成"）；⑤ 遇到歧义先 brainstorm。配套一张"默认 skill 路由表"把模糊指令映射到确定 skill。浏览器规则：`/browse` 是唯一入口，禁用 `mcp__claude-in-chrome__*`。

## 结论

1. **结论稳健**：两位独立作者（[[飞哥]] 2026-04-07 最早、[[运维有术]] 04-11/04-12 源码级深化）得出一致搭配结论——superpowers 大脑 + gstack 手脚。运维有术在飞哥基础上补了源码级差异表与 5 交接点。
2. **两种推荐方案**：方案 A（superpowers 为主、gstack 为辅）适合追求工程规范的团队；方案 B（gstack 为主、superpowers 为辅）适合个人开发者/快速原型。
3. **注意点**：CLAUDE.md 分区管理、命令前缀、两套技能同占上下文的 token 消耗（用 `/freeze` 限制编辑范围）。

## ⚠️ 矛盾 / 待澄清

- **安装 marketplace 名不一**：`superpowers@superpowers-marketplace`（飞哥）vs `superpowers@claude-plugins-official`（运维有术）。
- **gstack 安装两路径并存**：`claude plugin marketplace add gstack`（飞哥）vs `git clone + ./setup`（运维有术）。
- **superpowers 技能数**：14（运维有术明确）vs ~11（飞哥列举）。
- gstack 自述产能数据（60 万行/压缩比 100x 等）出自项目 README，未独立核实。

## 引用

- [[Claude Code 双插件搭配 superpowers 与 gstack]] — [[飞哥]] 2026-04-07，最早提出搭配 + CLAUDE.md 五原则 + 路由表
- [[gstack 最佳实战]] — [[运维有术]]，gstack 架构/Sprint/安全机制全貌
- [[Superpowers + gstack 搭配实战]] — [[运维有术]]，源码级差异表 + 5 交接点 + 13 步闭环

## 相关页面

- [[Superpowers]] · [[gstack]] · [[Jesse Vincent]] · [[Garry Tan]] · [[飞哥]] · [[运维有术]] · [[Claude Code 双插件搭配 superpowers 与 gstack]] · [[gstack 最佳实战]] · [[Superpowers + gstack 搭配实战]] · [[Superpowers 与 mattpocock skills 路线对照]]
