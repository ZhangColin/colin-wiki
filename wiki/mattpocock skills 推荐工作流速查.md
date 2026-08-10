---
type: synthesis
title: "mattpocock skills 推荐工作流速查"
domain:
  - AI编程
  - AI工具
  - 软件工程
tags:
  - mattpocock-skills
  - AI编程工作流
  - 速查
  - v1.1
sources:
  - "[[Matt Pocock skills v1.1 官方更新日志]]"
  - "[[Matt Pocock skills Wayfinder 官方文档]]"
  - "[[Matt Pocock 完整工作流视频]]"
  - "[[MattPocock Skills v1.1 重磅更新]]"
  - "[[mattpocock skills 标准工作流]]"
  - "[[mattpocock skills 秒杀系统实战]]"
created: 2026-07-27
updated: 2026-07-27
status: active
confidence: high
aliases:
  - mattpocock skills 工作流
  - 推荐工作流速查
  - skills 速查表
  - mattpocock skills cheat sheet
---

# mattpocock skills 推荐工作流速查

> 一句话结论：**按"活的大小"选入口**——日常中小活走主线四步（grilling → spec → tickets → implement + review）；超大活（撑过 smart zone、多 session、被雾包裹）从 `/wayfinder` 起步画地图；另有重构、bug 修复两条专用支线。

## 问题

刚接触 [[mattpocock skills]]，看到一堆 `/` 命令不知道该用哪个、按什么顺序。本页综合官方 v1.1 文档与多源，按**活的大小和类型**给推荐路径，给新手做常查速查。

## 第一步：项目初始化（任何 repo 第一次用）

```
/setup-matt-pocock-skills   → 选 issue tracker（GitHub/Linear/本地 md/Jira…）、
                              triage labels、写 context.md + ADR、灌进 CLAUDE.md
/ask-matt                   → 入口向导，问 "how do I get started" 会推荐 main flow
```

## 核心：按活选路径（决策树）

```
你的活有多大 / 什么类型？
│
├─ 日常中小需求（单 session 装得下，~140k token 内）
│     → 主线四步：/grilling → /to-spec → /to-tickets → /implement（内置 /code-review）
│
├─ 超大活（撑过 smart zone、撞 context window、被雾包裹、要多 session）
│     → /wayfinder 画地图 → 逐票攻坚 → 收敛后回主线 /to-spec
│
├─ 代码库重构
│     → /improve-codebase-architecture → /prototype → /to-spec → /to-tickets
│
└─ Bug 修复
      → /diagnosing-bugs → /tdd 补回归测试
```

> 三条进阶变体（大型/重构/bug）来自 [[MattPocock Skills v1.1 重磅更新]]（[[小匠Skills]] 概括）；主线与 wayfinder 由 [[Matt Pocock skills v1.1 官方更新日志]] 官方图确认。

## 主线四步（最常用）

| 步 | 命令 | 做什么 | 关键纪律 |
| --- | --- | --- | --- |
| ① 对齐 | `/grilling` | agent 盘问你目标、探索代码，产出落 `context.md` + ADR | 一次一问；**Facts**（代码可查）vs **Decisions**（须你定）；末尾确认门才进实现 |
| ② 出规格 | `/to-spec` | 压成 spec（目的地/终态描述） | "spec" 是统一术语（旧叫 `/to-prd`） |
| ③ 拆票 | `/to-tickets` | 拆成 tracer-bullet 垂直切片，每票声明 blocking 边 | 每票 = 一个 smart zone 工作量；本地 `tickets.md` 或真 tracker |
| ④ 实现 | `/implement` | 逐票清 context 实现 | TDD where possible、定期 typecheck、单文件测试、末尾全 sweep |
| ⑤ 审查 | `/code-review`（implement 内置） | 双轴**并行 sub-agent** | **Standards 轴**（`codingstandards.md`）+ **Spec 轴**（对照原票）；Fowler code smells |

## 超大活：`/wayfinder`（v1.1 重点）

**什么时候用**：活大到单 session 装不下、被雾包裹（看不到从这儿到目的地的路）。很多场景**替代 `/grill-with-docs`**。

**核心心智**：把活画成 issue tracker 上的一张 **map**（`wayfinder:map`），下挂 **decision tickets**，**一个 session 只解一个 ticket**，逐个攻坚直到 "the way is clear"。

- 两种模式：**Chart the map**（画地图，6 步）+ **Work through the map**（走地图，5 步）。
- 四种 ticket：**Research**（AFK 查文档）/ **Prototype**（HITL 提保真，前端几乎必用）/ **Grilling**（HITL 一次一问，默认）/ **Task**（决策前的手动活）。
- 铁律：**一 session 一 ticket**（research 例外）。
- 完成后：map 走 `/to-spec` 进主线。

完整流程与 map/ticket/frontier/fog-of-war 概念见 [[Matt Pocock skills Wayfinder 官方文档]]。

## 必备概念（速记）

| 概念 | 一句话 |
| --- | --- |
| **smart zone** | ~140k token 内是 LLM 聪明区，超过出现退化/幻觉。判断工作要否拆分就看这条线。 |
| **vertical slice** | 一个 ticket 纵向跑通一条需求（后端到前端），不按层横切。 |
| **spec** | 目的地/终态描述；统一术语（替代旧 PRD 叫法）。 |
| **context.md + ADR** | 领域文档 + 架构决策记录，grilling 产出落这里，跨 session 复利。 |
| **sub-agent 做 review** | main agent 会护短，拆给 sub-agent 审查更客观狠。 |
| **Plan, don't do** | Wayfinder 默认只规划不执行，每张票解一个**决策**而非一段构建。 |

## 命令速查表

**user-invoked**（手动 `/触发`，编排主流程）：

| 命令 | 用途 |
| --- | --- |
| `/setup-matt-pocock-skills` | 项目初始化（一次性） |
| `/ask-matt` | 入口向导，推荐 main flow |
| `/grilling`（旧 `/grill-with-docs`） | 盘问对齐需求 |
| `/grill-me` | 盘问但不写文档（非代码场景也能用） |
| `/to-spec`（旧 `/to-prd`） | 产出 spec |
| `/to-tickets`（旧 `/to-plan` + `/to-issues`） | spec 拆票 |
| `/wayfinder` | 超大活画地图（v1.1） |
| `/implement` | 逐票实现 |
| `/tdd` | TDD 实现路径（reference only：Red → Green） |
| `/prototype` | 廉价原型提保真（Logic / UI） |
| `/research` | 后台 subagent 调研，不打断节奏 |
| `/handoff` | 跨 session/agent 交接 |
| `/diagnosing-bugs` | 纪律化诊断循环（reproduce → minimise → hypothesise → instrument → fix → regression） |
| `/teach` | 生成可交互教学课程 |

**model-invoked**（自动介入，承载规范，无需手动调）：

| 命令 | 触发时机 |
| --- | --- |
| `/code-review` | implement 内置；提交前双轴审查 + Fowler smells |
| `/domain-modeling` | 构建领域模型、更新 context.md / ADR |
| `/codebase-design` | 设计"深模块"规范 |

## v1.0 → v1.1 命令对照

| v1.0（旧） | v1.1（新） | 说明 |
| --- | --- | --- |
| `/to-prd` | `/to-spec` | 产出更广义，叫 spec 更精准 |
| `/to-plan` + `/to-issues` | `/to-tickets` | 合并；拆垂直切片票 |
| `/grill-with-docs` | `/grilling`（超大活改用 `/wayfinder`） | grilling 修了 3 处 bug |
| 仅 `/tdd` | + `/implement` | 增加非 TDD 实现路径 |

## 本地化提示

本 vault（colin-wiki）自用的是 **superpowers** 体系（`brainstorming` / `systematic-debugging` / `tdd` 等），**没有** mattpocock 的 `/` 命令。要用真版需在目标工程 repo 跑 `npx skills add mattpocock/skills`。两者理念同源（grilling 对应 brainstorming 的深化版），差异详见 [[mattpocock skills]] 的"本地化对照"段。

## 结论

新手记住三件事：

1. **第一次**先 `/setup-matt-pocock-skills` + `/ask-matt`；
2. **默认**走主线四步（grilling → spec → tickets → implement + review）；
3. **活大到单 session 装不下**就用 `/wayfinder`。

其余命令按场景查上方速查表。

## 引用

- [[Matt Pocock skills v1.1 官方更新日志]] — 官方 main flow 图、改名理由、implement/review/tdd 原话
- [[Matt Pocock skills Wayfinder 官方文档]] — Wayfinder chart/work 流程与概念体系
- [[Matt Pocock 完整工作流视频]] — [[Matt Pocock]] 本人 v1.0 main flow 一手演示（smart zone、sub-agent review）
- [[MattPocock Skills v1.1 重磅更新]] — [[小匠Skills]] 中文 v1.1 概括，含四条工作流变体
- [[mattpocock skills 标准工作流]] — [[小匠Skills]] 中文 v1.0 总览
- [[mattpocock skills 秒杀系统实战]] — [[AI随风随风]] 实战第二视角

## 相关页面

- [[mattpocock skills]]（concept 统领，百科式） · [[Matt Pocock]] · [[小匠Skills]]
