---
type: source
title: "Matt Skills v1.2 grilling 重构"
domain: [AI编程]
tags: [mattpocock-skills, v1.2, grilling, round-by-round, frontier, 需求对齐]
sources: []
source_path: "raw/articles/Matt Skills v1.2：13 问从 13 轮压到 3 轮，AI 编码对齐不再龟速.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491659&idx=1&sn=d0909b183f526cc897f59f86907e54c9"
author: "[[运维有术]]"
date_published: "2026-08-11"
created: 2026-08-17
updated: 2026-08-17
status: active
aliases: [grilling v1.2, round-by-round frontier, 13 问 3 轮]
---

# Matt Skills v1.2 grilling 重构

> 一句话要点：[[运维有术]] 深拆 v1.2 对 `/grilling` 面试原语的彻底重构（PR #593）——从 v1.1"一次一问"升级为 **round-by-round frontier 推进**，13 个问题约 3 轮问完，把对齐从"正确但慢"推向"又快又准"。

## 关键要点

- **v1.1 的功与过**：一次一问 + 确认门控 + Facts/Decisions 分离救回了对齐质量，但代价是效率——13 问 13 轮、"对齐比写代码还累"。
- **v1.2 三概念**：①**设计树（design tree）**——把待对齐的事映射成决策树，父决策没定子决策没法问；②**frontier**——所有前置条件已满足的问题集合（"当前这一刻，能问出口的问题"）；③**round**——一轮问完整个 frontier，每题带编号 + AI 推荐答案，用户按编号批量作答。答案重塑决策树，frontier 外扩，再进下一轮。
- 官方数据：**13 个问题约 3 轮问完**（v1.1 是 13 轮）。
- round-based 曾短暂作为独立技能 **batch-grill-me** 发布，随后并入 grilling 本体，所有依赖它的技能一次获得该能力。
- **Facts vs Decisions 保留并强化**：facts 派 sub-agent 并行查、不阻塞轮次；decisions 必须逐一提给用户等待回答——"AI 查事实的同时，用户已经在回答决策了"。已知 bug：另一技能在 resolve-ticket 框架内运行 grilling 时，周边任务易被误读为可自主回答决策。
- **确认门控保留**：frontier 为空 ≠ 结束，须用户确认 shared understanding 才可行动。
- **opt-out（争议设计）**：round-based 是官方标注的 **contested design**；读得慢的人/二语使用者/需要顺序脚手架的用户，在全局 CLAUDE.md 加一行 `When grilling, ask one question at a time.` 回到一次一问——官方强调这是 **"supported rather than tolerated"**。
- **两个已知边界**：弱模型仍可能失效（低 effort 模型把 interview 压缩成几个问题+大纲，护栏得自己上）；frontier 是判断不是计算图（一轮内两问题可能实际有依赖，靠用户指出后下一轮重开分支）。
- v1.2 全家桶：Claude 官方插件、三新技能（wait-what/to-questionnaire/wizard）、prototype 重构、writing-for-agents 改名、Codex 双 harness、删 6 技能、to-prd→to-spec 改名彻底完成。
- 三大 Grilling 入口：/grill-with-docs（日常推荐，写 CONTEXT.md/ADR）、/grill-me（7 行薄封装，轻量/无代码库）、/wayfinder（大型项目，v1.2 引入 decision ticket）。**坑：grill-me 依赖 grilling，只装 grill-me 会 nothing happens**。
- **社区数据（截至 2026-08-10）**：GitHub **212.2K stars**（全站第 19 名）、skills.sh 累计 **13.5M 下载**；5 月 77K → 7 月 170K → 8 月 212K。
- **第三方定位（重要）**：ryanuo.cc——**"grill-me is the pressure-test primitive; Superpowers is the default engineering OS"**；社区共识**可组合非互斥**：先用 grill-me 对齐，再用 Superpowers 的 plan/TDD/worktree 跑全流程。
- GitHub Discussions #214 用户反馈："试了一次 /grill-with-docs 后，现在大部分时候只需要说 yes"。
- 进阶：improve-codebase-architecture v1.2 加 YAGNI 过滤（Explore 只读最近约 20 条 commit）；/wait-what 配合 /caveman 压输出。

## 详细笔记

五维对比表（v1.1 vs v1.2）：提问方式（逐个 vs 一轮 frontier）、问题数量（13 问 13 轮 vs 约 3 轮）、推荐答案（无 vs 每题附）、推进方式（线性 vs 树状外扩）、用户负担（高 vs 低）。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[Superpowers 与 mattpocock skills 路线对照]]（第三方定位直接印证）
- 相关实体：[[Matt Pocock]]、[[运维有术]]、[[Superpowers]]
- 相关源：[[Matt Pocock Skills v1.2 更新全景]]（同日姊妹篇）、[[grill-me 实战指南]]（v1.1 一次一问时代记录，本文为其演进源）、[[还在用 grill-with-docs]]、[[2026 年再看 Superpowers：grill-me 场景选型]]

## ⚠️ 矛盾 / 待澄清

- **grilling 行为代际变更**：[[grill-me 实战指南]] 与 [[还在用 grill-with-docs]] 记录的"一次一问"是 v1.1 行为，v1.2 默认已变 round-based（可 opt-out 回退）——演进非矛盾，概念页已标。
- 与 [[mattpocock skills]] 概念页记录的 grilling 五规则一致，本文补"一轮问 frontier"的推进机制。
- star 口径：212.2K（08-10 检索）vs 213.4K（[[Matt Pocock Skills v1.2 更新全景]] 08-14 写作）——正常增长。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock Skills v1.2 更新全景]] · [[grill-me 实战指南]] · [[还在用 grill-with-docs]] · [[Superpowers 与 mattpocock skills 路线对照]] · [[Superpowers]] · [[Matt Pocock]] · [[运维有术]]
