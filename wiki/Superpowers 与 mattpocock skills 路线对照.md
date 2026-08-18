---
type: synthesis
title: "Superpowers 与 mattpocock skills 路线对照"
domain: [AI编程, AI工具, 软件工程]
tags: [Superpowers, mattpocock-skills, 路线对比, Claude Code, Agent Skills]
sources:
  - "[[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]]"
  - "[[2026 年再看 Superpowers：grill-me 场景选型]]"
  - "[[Anthropic 与 Perplexity 的 Skills 设计方法论]]"
  - "[[Superpowers]]"
  - "[[mattpocock skills]]"
created: 2026-08-10
updated: 2026-08-10
status: active
confidence: high
aliases:
  - Superpowers vs mattpocock skills
  - 两条 skills 路线对照
---

# Superpowers 与 mattpocock skills 路线对照

> 一句话结论：[[Superpowers]] 与 [[mattpocock skills]] 是 Claude Code 生态两条主流 skills 路线——前者用 hook 强制 + 红旗表把纪律焊死（重、自动触发），后者用最小锚点 + router 兜底 + user/model-invoked 分层保持轻量（轻、手动驱动）；理念同源、路线分歧，**可共存**，按团队/项目特征选。

## 问题

两套 skills 都把 skill 当"塑造 agent 行为的代码"、都源自经典软件工程、都强调 TDD/code review/规格化。根本分歧在哪？能否搭配？

## 对比 / 分析

| 维度 | [[Superpowers]]（obra） | [[mattpocock skills]]（Matt Pocock） |
| --- | --- | --- |
| **核心理念** | 工程纪律框架："不是更强，而是更稳" | skill 工程化标准：模型越强技能应越简单 |
| **触发机制** | hook 注入"哪怕 1% 可能适用也必须调用"，红旗表堵死借口，**自动触发** | 最小锚点 + `ask-matt` router 兜底，**user-invoked 手动驱动**为主（18 个里 12 个手动） |
| **体量代表** | `writing-skills` **689 行** | `grilling` **12 行** |
| **context 成本** | 描述多、token 消耗大（HN："consuming a stupid amount of tokens"，v6.0 主改进方向之一） | 全套 skills 只占 ~660 tokens，仅 6 个 description 进上下文 |
| **主流程** | 7 步闭环：brainstorming→worktree→writing-plans→subagent→TDD→code-review→finishing | main flow：grill-with-docs→to-spec→to-tickets→implement→code-review（+上游 wayfinder） |
| **会话/上下文** | worktree 隔离 + fresh subagent + Progress Ledger 对抗 compaction | smart zone + handoff + wayfinder"一 session 一 ticket" |
| **代码审查** | 独立 reviewer 两轮（v5.x）/ 单 reviewer 一次出两裁决（v6.0，只读怀疑论者） | 双轴并行子代理（Standards + Spec），"防一轴稀释另一轴" |
| **需求对齐** | `brainstorming`：规格生产流程（2-3 方案→设计文档→writing-plans） | `/grilling`：压力测试计划（沿决策树逐问，stop-gate 门禁） |
| **平台** | 多平台（Claude Code/Cursor/Codex/OpenCode/Gemini CLI/Copilot CLI） | 跨 agent（Claude Code/Codex/OpenCode） |
| **TDD** | RED→GREEN→REFACTOR 硬循环，铁律"没有失败测试就不写生产代码" | red→green loop，v1.1 移除 refactor（划给 code-review） |
| **设计哲学出处** | 94% PR 拒绝率的开源项目出身，极严格 | 批评 GSD/BMAD 太复杂、剥夺控制权 |

## 结论

1. **分歧点是"纪律怎么施加"**：Superpowers 用强制（hook + 红旗表 + hard gate），mattpocock 用最小化（紧凑 leading word + 路由器 + 让用户手动驱动）。两者背后的 [[Skills 设计方法论]] 共识一致——"Skills 是上下文工程，不是 prompt"（[[Anthropic]]/[[Perplexity]]）。
2. **目标产物差异决定选型**（[[2026 年再看 Superpowers：grill-me 场景选型]] 结论）：要规格文档→Superpowers brainstorming；要压力测试共享理解→grill-me。当前**无对照实验**证明谁更快/更准/更省 token。
3. **可共存**：mattpocock 大部分命令不参与自动触发，superpowers 的 hook 只强推自己那 14 个，互不抢匹配（[[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]]）。本 vault 自身就在用 superpowers，同时把 mattpocock skills 作为对照系研究。
4. **选型启发**：追求工程规范、团队可审计、多平台 → 偏 Superpowers；追求轻量灵活、单命令可组合、context 经济 → 偏 mattpocock。
5. **社区定位印证（2026-08 新增，v1.2 后）**：第三方深度对比（ryanuo.cc，经 [[Matt Skills v1.2 grilling 重构]] 转述）给出精准定位——**"grill-me is the pressure-test primitive; Superpowers is the default engineering OS"**（grill-me 是压测原语，Superpowers 是默认工程操作系统）；AI Coding Daily 称 grill-me "一直是 planning 领域的标准"。**社区共识是可组合而非互斥**：先用 grill-me 对齐，再用 Superpowers 的 plan/TDD/worktree 跑全流程，各取所长——与本页结论 3 一致。

## 引用

- [[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]] — 689 行 vs 12 行的源码级对比，路线分歧最系统陈述
- [[2026 年再看 Superpowers：grill-me 场景选型]] — grill-me vs brainstorming 按目标产物选工具
- [[Anthropic 与 Perplexity 的 Skills 设计方法论]] — 两者共同的方法论根基（上下文工程）
- [[Matt Skills v1.2 grilling 重构]] — v1.2 后的社区定位与"可组合"共识
- [[Superpowers]] · [[mattpocock skills]] — 两概念页

## 相关页面

- [[Superpowers]] · [[mattpocock skills]] · [[Jesse Vincent]] · [[Matt Pocock]] · [[Skills 设计方法论]] · [[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]] · [[2026 年再看 Superpowers：grill-me 场景选型]] · [[Anthropic 与 Perplexity 的 Skills 设计方法论]] · [[Superpowers 与 gstack 搭配（大脑与手脚）]]
