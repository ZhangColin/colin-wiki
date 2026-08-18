---
type: source
title: "超火AI编程工作流mattpocock-skills，保姆级教程详细讲解"
domain:
  - AI编程
  - AI工具
  - 软件工程
tags:
  - mattpocock-skills
  - 实战demo
  - 秒杀系统
  - AI编程工作流
sources: []
source_path: raw/articles/超火AI编程工作流mattpocock-skills，保姆级教程详细讲解.md
source_type: article
source_url: https://www.bilibili.com/video/BV1K6Te6bET4
author: AI随风随风
date_published: 2026-07-01
created: 2026-07-26
updated: 2026-08-10
status: active
aliases:
  - mattpocock skills 秒杀系统实战
  - 保姆级教程
---

# 超火AI编程工作流mattpocock-skills，保姆级教程详细讲解

> 一句话要点：[[AI随风随风]]（B 站）用一个「秒杀系统 Demo」把 [[mattpocock skills]] 全套技能串起来实战演示——从初始化、需求访谈、PRD、原型、开发、Review、Bug 调试到交接与教学文档，第三方第二视角。

> 🔖 **v1.1 升级注记**（2026-08-10）：本视频为 **v1.0 视角**（忠实记录原 demo，正文不再改写）。v1.1 起命令更名 `/to-prd`→`/to-spec`、`/to-issues`→`/to-tickets`，并新增 `/wayfinder`（大型规划）、`/implement`（非 TDD 路径）。**v1.1 新内容见** [[MattPocock Skills v1.1 重磅更新]]、[[Matt Pocock skills v1.1 官方更新日志]]、[[mattpocock skills]] 概念页。
>
> 🔖 **v1.2 追记**（2026-08-17）：v1.2（2026-08-05）grilling 重构（13 问约 3 轮）、wizard/to-questionnaire/wait-what 毕业、prototype 留档、双平台。见 [[Matt Pocock Skills v1.2 更新全景]]。另：本 wiki 另有同主题"电商高并发扣减"实战 [[Superpowers 7阶段交付SKU库存扣减]]（用 Superpowers 框架，可对照）。

## 视频信息

- **UP 主**：AI随风随风 · **发布**：2026-07-01 · **平台**：B 站
- **URL**：https://www.bilibili.com/video/BV1K6Te6bET4

## 关键要点

- **定位判断**：作者认为这组 skills 最大特点不是复杂，而是**简单、自由、好组合**——不强行规定每步怎么做，更像可灵活插入开发流程的"AI 编程工具箱"。对比 GSD 等太复杂、剥夺控制权的方案。观点："模型越强，技能应越简单。" 适合 Claude Code / Codex / OpenCode 用户。
- **安装与初始化**：`npx skills add`；选 official 组、global scope。第一步 `/setup`（即 `/setup-mattpocock-skills`）：
  - **issue tracker**（GitHub/GitLab/Jira/本地，demo 选本地管控状态）
  - **triage labels**（任务状态标签：需要分类/需提供信息/可进入开发/需人工实现等——AI 读标签决定是否往下走；可自定义如"初始化/进行中/可进入开发/需 review/bug"）
  - **context.md + ADR**（单上下文：单体仓库/前后端合一如 Next.js；多上下文：前端+Java+Python 分语言分仓）
  - 创建 `CLAUDE.md`（全局约束，每次对话加载，非常关键）vs `AGENTS.md`（看用的 agent）
- **/grill-me vs /grill-with-docs**：grill-me 只采访不生成文档；grill-with-docs 采访 + 落地文档（记录歧义、约定、决议）。想编码用 grill-with-docs；只探讨想法靠不靠谱用 grill-me。
- **grill vs 头脑风暴（重要对比）**：grill 更强调"把问题问清楚"——从多维度深问，**根据回答产生新问题**，直到双方搞懂（复杂问题可能 10-20 轮）。头脑风暴/prime 模式问题更浅、目的更偏"尽快生成文档进下一步"。建议：想法模糊时先用 grill 深聊 → 生成详细文档 → 再用头脑风暴/prime 补充 → 进开发。
- **/to-prd**：总结对话形成 PRD 文档（user stories、实现决策、测试边界、技术栈、数据库设计、API 路由）。小改动用 `/to-issues`，大需求用 `/to-prd`。可推送到 GitHub Issues。
- **实现路径**：① 让 agent 读 PRD 直接开发；② 进 Claude Code plan 模式按 PRD 生成计划再执行；③ TDD 方式测试驱动开发。
- **/prototype（原型设计）**：纠结某页面怎么做时，生成 **1-3 个风格方案**（A/B/C）让你选，基于已做内容 + 模型设计规范推荐。不是文字描述，是**可预览的界面**。选定后转化成正式界面。非专业设计师拿不准时很有用。
- **/code-review**：扫描整个代码库找可优化点，输出 **HTML 报告**（含改善方向、原因、预期效果、推荐强度）。给 AI 生成代码加一层保障。
- **/diagnosing-bugs**：从 **10 个方向**测试找 bug，一种不奏效换另一种。适合顽固性、不易找原因的 bug。
- **/handoff**：切换 agent（如 Claude Code → Claude App）或中断时，生成交接文档让别的工具读取续做。
- **/teach（亮点）**：生成可交互教学课程。**联网搜权威资料**（如 Anthropic 官方文档）——不用模型知识库。生成课件 + 测验 + 练习，可交互讨论、逐节生成。适合学开源项目、最新概念（demo 学 Claude Code worktree）。
- **数据**：发布时仓库接近 150K★。作者强调："这组技能不仅技能写得好，流程也非常自由，可下载来学习人家怎么写技能。"

## 详细笔记

demo 完整链路：初始化 `/setup` → `/grill-with-docs`（秒杀系统需求，8 轮问答搞清）→ `/to-prd`（16 条 user story、8 项实现决策）→ 开发实现（TDD）→ 原型设计（登录注册 3 方案 A/B/C，选 B 左右分栏）→ `/code-review`（找到 3 个可优化点，输出 HTML 报告）→ `/diagnosing-bugs`（10 法找 bug）→ `/handoff`（交接到 Claude App）→ `/teach`（生成 Claude Code worktree 交互课程）。

## 与已有内容的关联

- **核心概念**：[[mattpocock skills]]（本视频是其第三方实战 demo）。
- **相关实体**：[[AI随风随风]]（作者，*pending，待独立建*）。
- **互补**：[[Matt Pocock 完整工作流视频]]（作者本人一手演示）、[[mattpocock skills 标准工作流]]（体系总览）、[[MattPocock Skills v1.1 重磅更新]]（v1.1 新内容）。

## ⚠️ 矛盾 / 待澄清

- **本文用 v1.0 旧命令名**（`/to-prd`、`/to-issues`），v1.1 后已被 `/to-spec`、`/to-tickets` 取代（见 [[MattPocock Skills v1.1 重磅更新]]）。
- **grill 与 brainstorming 对比**：本文认为 grill 比一般头脑风暴更深追问——与本 vault 使用的 superpowers brainstorming skill 形成对照（设计意图差异，非冲突）。详见 [[mattpocock skills]] concept 页"本地化对照"。
- 作者把初始化命令称为 `/setup`，[[Matt Pocock 完整工作流视频]] 里全名是 `/setup-mattpocock-skills`——同一命令的简称。
- 发布时仓库"接近 150K★"，与其他源的时间点数据有差异（[[mattpocock skills]] concept 页取最新 170K）。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock 完整工作流视频]] · [[mattpocock skills 标准工作流]] · [[MattPocock Skills v1.1 重磅更新]]
