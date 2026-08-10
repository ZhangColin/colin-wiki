---
type: source
title: "mattpocock/skills 标准工作流：从需求到上线"
domain:
  - AI编程
  - AI工具
  - 软件工程
tags:
  - mattpocock-skills
  - v1.0
  - 工作流
  - AI编程工作流
sources: []
source_path: raw/articles/mattpocockskills 标准工作流：从需求到上线.md
source_type: article
source_url: https://mp.weixin.qq.com/s?__biz=Mzk0MzE4MzY5MQ==&mid=2247483838&idx=1&sn=5b84309ee90514cc1f45cc39854ce229
author: 小匠Skills
date_published: 2026-07-07
created: 2026-07-26
updated: 2026-08-10
status: active
aliases:
  - mattpocock skills 标准工作流
  - 从需求到上线
---

# mattpocock/skills 标准工作流：从需求到上线

> 一句话要点：[[小匠Skills]] 对 [[mattpocock skills]] v1.0 体系的全景总览——两类触发、四步主流程、三大变体、自动规范 skill、辅助工具，是中文圈最系统的入门解读。

> 🔖 **v1.1 升级注记**（2026-08-10）：本文为 **v1.0 视角**（忠实记录原文，正文不再改写）。v1.1 起：命令更名 `/to-prd`→`/to-spec`、`/to-issues`+`/to-plan`→`/to-tickets`；新增 `/wayfinder`（大型项目规划）与 `/implement`（非 TDD 实现路径）；grill 区分 Facts/Decisions + 确认闸门；code-review 双轴。**v1.1 新内容见** [[MattPocock Skills v1.1 重磅更新]]、[[Matt Pocock skills v1.1 官方更新日志]]、[[mattpocock skills]] 概念页「v1.1 演进」表，以及 v1.1 机制深读系列（[[Matt Pocock main flow 五环节]]、[[Matt Pocock skills Wayfinder 官方文档]] 等）。

## 关键要点

- **两类触发**：
  - **User-invoked**（手动 `/xxx`）：编排主流程。
  - **Model-invoked**（自动）：任务匹配时 agent 自动调用，承载可复用规范。
  - 规则：**user 可调 model，反之不行**。主流程你手动驱动；code review 等规范自动介入，不用你排程。
- **标准主流程（四步）**：`/grill-with-docs` → `/to-prd` → `/to-issues` → `/tdd`（或 `/implement`）
  1. `/grill-with-docs`：盘问 + 构建领域模型，锐化术语，内联更新 CONTEXT.md 和 ADR。对齐需求。
  2. `/to-prd`：把当前对话合成 PRD，发布到 issue tracker。不做访谈，只综合已讨论内容。固化共识。
  3. `/to-issues`：把计划/规格按**垂直切片**拆成可独立认领的 issues（一个 issue 跑通一条需求，从后端到前端，非按层横切）。
  4. `/tdd`（或 `/implement`）：红-绿-重构循环，一次啃一个切片；或 `/implement` 直接实现。每个切片能独立验证。
  - 经验：to-prd 与 to-issues 常成对，但小需求可跳过 PRD 直接 to-issues——官方把它们设计成两个独立 skill，拆开用有道理。
- **常见变体**：
  - **A 架构体检**：`/improve-codebase-architecture` → (prototype) → `/to-prd` → `/to-issues` → `/tdd`。扫描代码库找深化机会，生成可视化 HTML 报告，再盘问选定项。项目变"泥球"后定期跑。
  - **B 修 bug**：`/diagnosing-bugs` → `/tdd`。循环：reproduce → minimise → hypothesise → instrument → fix → regression-test。⚠️ 官方 diagnosing-bugs 本身含 fix + regression-test，别用魔改版砍成"只报原因"，那会把闭环丢了。修好后用 `/tdd` 补回归测试。
  - **C 原型（可选）**：`/prototype`。UI 类做多个激进变体；逻辑类做能跑的终端程序。原型不合适就扔，低成本测想法。一般新开 session + `/handoff` 交接上下文。
- **自动介入规范类**：`/code-review`（双轴：Standards 仓库规范 + Spec 忠实度，子 agent 互不污染）、`/domain-modeling`、`/codebase-design`、`/research`。
- **辅助与交接**：`/handoff`（压缩对话交另一 agent/session）、`/ask-matt`（不确定用哪个 skill 时路由）、`/grill-me`（盘问不写文档，纯对齐，非代码场景也能用）。
- **核心理念**："让 AI 编程从'想到哪写到哪'变成'有纪律的工程行为'。需求先对齐，计划先固化，任务先拆好，代码先测试——每一步都有 skill 兜着。"

## 详细笔记

完整标准工作流一图见原文：`![[raw/assets/Image 2.webp]]`

## 与已有内容的关联

- **核心概念**：[[mattpocock skills]]（本篇是其 v1.0 体系全景）。
- **相关实体**：[[小匠Skills]]（作者）、[[Matt Pocock]]（skills 作者）。
- **后续更新**：[[MattPocock Skills v1.1 重磅更新]]（本篇的 v1.1 升级版）。

## ⚠️ 矛盾 / 待澄清

- **本文用 v1.0 旧命令名**（`/to-prd`、`/to-issues`），在 v1.1 后**已被取代**为 `/to-spec`、`/to-tickets`（见 [[MattPocock Skills v1.1 重磅更新]]）。流程逻辑不变，仅命令名演进。阅读时请按 v1.1 名称对照。

## 相关页面

- [[mattpocock skills]] · [[小匠Skills]] · [[Matt Pocock]] · [[MattPocock Skills v1.1 重磅更新]]
