---
type: source
title: "MattPocock Skills v1.1 重磅更新｜重构AI编程全流程"
domain:
  - AI编程
  - AI工具
  - 软件工程
tags:
  - mattpocock-skills
  - v1.1
  - Wayfinder
  - AI编程工作流
sources: []
source_path: raw/articles/MattPocock Skills v1.1 重磅更新｜重构AI编程全流程.md
source_type: article
source_url: https://mp.weixin.qq.com/s?__biz=Mzk0MzE4MzY5MQ==&mid=2247483882&idx=1&sn=57a4f1a8b77629f38f766968833613b0
author: 小匠Skills
date_published: 2026-07-20
created: 2026-07-26
updated: 2026-07-26
status: active
aliases:
  - mattpocock skills v1.1 更新
  - v1.1 重磅更新
---

# MattPocock Skills v1.1 重磅更新｜重构AI编程全流程

> 一句话要点：[[小匠Skills]] 对 [[mattpocock skills]] v1.1 大版本的逐项拆解——核心命令更名（`/to-prd`→`/to-spec` 等）、Grill 盘问体系修复、补齐 SDLC 闭环、推出大型项目规划新工具 Wayfinder。

## 关键要点

- **核心命令更名**：`/to-prd`→`/to-spec`（产出含技术架构/领域模型，"Spec"比"PRD"更精准）；`/to-issues`→`/to-tickets`（合并原 to-plan，拆垂直切片工单）。旧指令废弃。
- **升级方式**：命令行 `npx skills@latest add mattpocock/skills` 覆盖；或 CCSwitch v3.13.0+（基于 SHA-256 哈希对比，卡片显示"有新版本"，支持单项/批量更新）。旧版技能缓存会冲突，需清空旧技能。
- **Grill 盘问体系修复**：修重复提问/一次抛多问/Agent 自问自答 bug；区分 **Facts**（代码可读的客观信息，Agent 自动查）vs **Decisions**（架构选型/业务规则，必须问用户）；新增**确认校验闸门**（完整规划生成后须用户确认才进实现）；优化领域模型同步（自动更新 CONTEXT.md、ADR）。
- **补齐 SDLC**：新增 `/implement`（除 TDD 外的标准化实现路径，适配快速迭代/小功能，遵循垂直切片独立可验证）；Code Review 升级（Martin Fowler code smells 检测 + 双轴：规范轴 + 需求轴，需求与代码双向绑定校验）。
- **Wayfinder（v1.1 王牌）**：解决大型项目超出单 Agent 会话上下文的痛点。自动拆巨型需求为多类型 ticket（调研/盘问/原型/开发），每张适配单会话，支持增量迭代、跨会话交接。内置子技能 `/research`（带引用 Markdown）、`/prototype`。适用：全新开源项目、多端联动大功能、大规模重构。
- **新版工作流**：
  - 基础（日常中小型需求）：见原文工作流图 `![[raw/assets/Image 3.webp]]`
  - 进阶 A（大型新项目/复杂功能）：`/wayfinder` → research/prototype → `/to-spec` → `/to-tickets` → 实现
  - 进阶 B（代码库重构）：`/improve-codebase-architecture` → prototype → `/to-spec` → `/to-tickets`
  - 进阶 C（Bug 修复闭环）：`/diagnosing-bugs` → `/tdd` 补回归测试
- **自动兜底质检**（无需手动调用）：`/code-review`、`/domain-modeling`、`/codebase-design`，代码提交/需求变更时自动触发，保障工程规范。
- **TDD 与路由**：TDD 循环规则微调，与 `/implement` 形成两条并行实现路径；`/ask-matt` 路由更新，自动推荐新版标准链路：需求盘问 → `/to-spec` → `/to-tickets` → `/implement`/`/tdd`。
- **数据**：160K★/750 万下载（本篇发布时），Claude Code 生态最主流 AI 工程化工具集。

## 详细笔记

v1.1 不是简单功能增补，而是"对整套 AI 编程工程体系的规范化重构"：统一术语、修复交互缺陷、补齐开发闭环、攻克大型项目规划难题。作者建议：旧版 to-prd/to-issues 用户尽快清空旧技能完成升级；中小型开发沿用基础流程，接手大型项目直接使用 Wayfinder。

## 与已有内容的关联

- **核心概念**：[[mattpocock skills]]（本篇是其 v1.1 演进的权威中文解读）。
- **相关实体**：[[小匠Skills]]（作者）、[[Matt Pocock]]（skills 作者）。
- **前作**：[[mattpocock skills 标准工作流]]（v1.0 总览，本篇是其更新；本篇开头链接前作）。

## ⚠️ 矛盾 / 待澄清

- 本篇 stars 数据 160K，与 [[Matt Pocock 完整工作流视频]] 录制时 162K、YouTube 描述 170K 略有差异——属时间点不同的正常增长（[[mattpocock skills]] concept 页取最新 170K）。

## 相关页面

- [[mattpocock skills]] · [[小匠Skills]] · [[Matt Pocock]] · [[mattpocock skills 标准工作流]]
