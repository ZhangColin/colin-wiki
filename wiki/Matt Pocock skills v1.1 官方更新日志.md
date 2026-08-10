---
type: source
title: "Matt Pocock skills v1.1 官方更新日志"
domain:
  - AI编程
  - AI工具
  - 软件工程
tags:
  - mattpocock-skills
  - v1.1
  - AI编程工作流
sources: []
source_path: "raw/articles/Matt Pocock skills v1.1 官方更新日志（aihero.dev）.md"
source_type: article
source_url: "https://www.aihero.dev/skills/skills-changelog-v1-1-wayfinder-to-spec-to-tickets-grilling-improvements"
author: "Matt Pocock"
date_published: 2026-07-08
created: 2026-07-27
updated: 2026-07-27
status: active
aliases:
  - v1.1 官方 changelog
  - skills v1.1 更新日志
  - aihero v1.1 changelog
---

# Matt Pocock skills v1.1 官方更新日志

> 一句话要点：[[Matt Pocock]] 官方博客 v1.1 changelog 一手英文原文。与 [[小匠Skills]] 中文解读 [[MattPocock Skills v1.1 重磅更新]] 同源对照——本页提供**官方原话**，并补几处中文页未逐字强调的要点（implement 全文、Fowler code smells 清单、tdd 改动理由、改名理由）。

## 关键要点

### 迁移

`npx skills add mattpocock/skills`（**不会自动拾取 rename/merge**，需手动走一遍 skills 文件夹清旧技能）。新公开文档：`docs/engineering/to-spec.md`、`docs/engineering/to-tickets.md`。

### 改名理由（官方原话）

- **`/to-prd` → `/to-spec`**：产出其实不是 PRD（描述产品本身），而是 specification——更广义，可技术/非技术/混合。**"spec" 成整套 skills 的统一贯穿术语**。
- **`/to-plan` + `/to-issues` → `/to-tickets`**：旧名偏 GitHub/Linear 的 "issues" 叫法；本质是 spec 下的 **tickets**——actuate spec 的旅程。`/to-tickets` 产出 tracer-bullet 垂直切片，每个声明 blocking 边。两种模式：本地 `tickets.md`（手撸 top-to-bottom）或真 tracker（blocking 变原生链接，多 agent 并行）。

### Grilling 三处修复（官方原话）

1. **更明确"为何一次只问一问"**——原指引"一次多问令人困惑"会被模型忽略，所以讲清 _why_。
2. **末尾确认门**："达成共识前别执行计划。" 很多人反映 grilling 一结束模型就跳去实现，此门拦住它。
3. **防 self-grilling**（尤其 Fable 上发生）：区分 **Facts**（探代码可查：代码模式、既有实现）vs **Decisions**（须用户定：架构选型、功能范围）。

### 新版 main flow（官方图）

**Grilling → Spec → Tickets → Implement → Code Review**。不再直接用 plan mode，而是让 agent grill 你。两份支持文档：glossary（术语表）+ ADR（架构决策记录）。

### `/implement`（原文几乎全文）

> Implement the work described by the user in the spec or tickets. Use TDD where possible at pre-agreed seams. Run type checking regularly. Single test files regularly. Full test sweep once at the end.

主要靠 agent priors + `agents.md`。Matt 说**几乎不想做这个 skill，因为太简单**——但人们老问"flow 是啥"，于是给了个明确终点。

### `/code-review`：双轴 + Fowler code smells

双轴**并行 sub-agent**：**Standards 轴**（读 `codingstandards.md` 或同类）+ **Spec 轴**（对照原 spec/ticket 的正确性）。

新增 **Martin Fowler《Refactoring》code smells 体系**——Matt 重读发现模型训练里已深植，只需 invoke 术语，模型就会认：mysterious name、duplicated code、feature envy、data clumps、primitive obsession、repeated switches、divergent change、speculative generality、message chains、middleman……Matt 测了两周，**"提升代码质量出奇有用"，成本极低（~10 行指引）**。

### `/wayfinder`

v1.1 王牌，**在很多场景替代 `/grill-with-docs`**——当规划大到撑过 smart zone / 撞 context window 时用。把活画成 issue tracker 上的 map（决策 sized 到单 session），blocking 关系让依赖决策在前置解决前不可动。配 `/research`、`/prototype` 两个新 skill。完整定义见 [[Matt Pocock skills Wayfinder 官方文档]]。

### `/research`

小而顺手：后台 subagent 调研（**不中断你的流程**）、对照一手源、写简单 markdown、存仓库既定位置。任何不想打断节奏的调研都用它。

### `/prototype`

改为 **model-invoked**（Wayfinder 可自调）。两种：**Logic prototype**（后端 API/业务逻辑）、**UI prototype**（前端组件/用户流），探索设计空间后再 commit spec。前端工作几乎必用。

### `/tdd` 改 reference only

旧 tdd 让 agent 每步求确认，与多数人实际用法（要 agent 自主）不符。新版**只给核心顺序，不规定步骤**：

```
1. Red - 写失败测试
2. Green - 让测试过
（Refactor 移出循环）
```

关键变化：**Refactor 不再在 TDD 循环内**，移到 code review 阶段——保持实现 session 聚焦，不把重构负担压进实现。可放心传给 AFK agent。

## 详细笔记

- v1.1 不是功能增补，是"对整套 AI 编程工程体系的**规范化重构**"：统一术语、修交互缺陷、补开发闭环、攻大型项目规划。
- main flow 图中，Wayfinder 是"超大活"的入口；中小活仍走经典 grilling 起步。
- 本日志未强调 stars 数字；[[MattPocock Skills v1.1 重磅更新]] 记 160K（发布时），[[mattpocock skills]] concept 页统一取最新 170K。

## 与已有内容的关联

- **核心概念**：[[mattpocock skills]]（本页是其 v1.1 演进的**官方一手源**）。
- **同批官方源**：[[Matt Pocock skills Wayfinder 官方文档]]（Wayfinder 操作细节）。
- **中文二手解读**：[[MattPocock Skills v1.1 重磅更新]]（[[小匠Skills]] 概括版，本官方页与其同源对照、补官方原话）。
- **相关实体**：[[Matt Pocock]]（作者）、[[小匠Skills]]（中文解读者）。

## ⚠️ 矛盾 / 待澄清

- 与 [[MattPocock Skills v1.1 重磅更新]] 一致，无矛盾；本页为官方一手，中文页为二手概括。
- stars 数字各源时间点不同（非矛盾），[[mattpocock skills]] 取最新 170K。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock skills Wayfinder 官方文档]] · [[MattPocock Skills v1.1 重磅更新]] · [[Matt Pocock]]
