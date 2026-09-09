---
type: source
title: "mattpocock skills 完全入门指南"
domain: [AI编程, AI工具]
tags: [mattpocock-skills, 入门, SKILL.md, 失败模式, 插件安装, skills.sh]
sources: []
source_path: "raw/articles/01 - mattpocockskills 完全入门指南.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247483988&idx=1&sn=3847123a2641738d1f678490d22dbc48"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-03"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases: [mattpocock skills 入门, 01 - mattpocockskills 完全入门指南]
---

# mattpocock skills 完全入门指南

> 一句话要点：Skill = 可复用工作流文件而非一次性 prompt，用「AI 编码四类失败」划出它解决什么，给出两种安装方式与「何时不要用」清单。

## 关键要点

- **定义**：[[mattpocock skills]] 是 [[Matt Pocock]]（Total TypeScript 创办人）2026 年 2 月公开的个人 AI 技能集，很快冲到近 20 万星；每个技能就是一个 `SKILL.md`，是「他自己每天在用的工作流文件」。
- **prompt vs skill**：prompt = 一次性指令（说完就散）；skill = 可复用工作流（装进去持续生效）。比喻「开关」：`/grill-me` 手动开，`tdd` 写代码时自动亮。
- **AI 编码四类失败 → 解药**（全系列骨架，作者明示是 Matt 的 anecdotal evidence 非行业统计）：① 没做你要的事 → `grill-me` 追问式访谈；② 太啰嗦 → `CONTEXT.md` 共享语言；③ 代码不工作 → `tdd` + `diagnosing-bugs`；④ 项目变浆糊 → `codebase-design` + `improve-codebase-architecture`。
- **边界三不是**：不是能力插件包（不扩展能力只约束工作方式）、不是银弹（只降出错概率）、不替你做决定。金句：**约束 AI 怎么干活，不决定 AI 干什么活**。
- **两种安装，二选一勿都装**：① Claude Code 插件（订阅派，只读捆绑自动更新）：`claude plugins install mattpocock-skills`；② skills.sh（拥有派，复制成可编辑文件）：`npx skills@latest add mattpocock/skills`。建议先插件后文件版。
- **初始化**：`/setup-matt-pocock-skills` 每仓库只跑一次，三问：issue tracker（GitHub/Linear/本地文件）、triage 标签、文档目录。
- **触发规则**：user-invoked（`/grill-me`、`/to-spec` 等编排启动）与 model-invoked（tdd 等承载纪律）；唯一规则：**user-invoked 可调用 model-invoked，绝不调用另一个 user-invoked**（防流程套流程死循环）。
- **与 Trellis 对比**：Trellis = 完整编码框架（harness，绑平台）；mattpocock/skills = 可组合积木（直接改 SKILL.md，任何模型可用）。作者偏好积木，明言是口味非真理。
- **何时不要用**：一周才写一次代码的偶尔玩家；需求极简稳定；团队不想要流程（先让愿试的人用结果说服）；期待「一句话换完整交付物」魔法的人。

## 详细笔记

系列首篇定位是「摆开关」：先讲清 Skill 是什么/不是什么，再给四类失败的诊断框架（该框架贯穿整个系列，02 篇再展开设计逻辑），最后落到安装、setup 与边界清单。教学贯穿项目为 EShop（Java Spring Boot 3 + MyBatis-Plus + MySQL 电商系统）。

## 与已有内容的关联

- 与 [[mattpocock skills 标准工作流]] 同为入门层：本文是「为什么 + 怎么装 + 边界」，后者是 v1.0 主流程全景，互补。
- 四坑→解药框架与 [[MattPocock Skills 21 个 skill 工程纪律体系]] 主题重叠；「积木 vs 框架」叙事与 [[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]] 相合。
- setup 三问对应 [[Matt Pocock setup-matt-pocock-skills 配置落地]]。
- 相关概念：[[mattpocock skills]]、[[超级奶爸老谭]]；相关实体：[[Matt Pocock]]。

## ⚠️ 矛盾 / 待澄清

- **grill-me 定位与 wiki 取代口径冲突**：本文把 `/grill-me` 当「最有代表性的第一个技能」教新手入门，未提 grill-with-docs 及其取代关系——wiki 既有口径是 Matt 已公开声明 coding 场景不再推荐 `/grill-me`（见 [[grill-me 实战指南]]、[[还在用 grill-with-docs]]）。属老谭教学系列系统性滞后，不推翻既有结论。
- **v1.1 时代快照**：本文发布（08-03）恰在 v1.2（08-05）前两天，全文无 v1.2 内容（wizard/wait-what/round-by-round grilling 等）；「官方市场插件」默认路径与 v1.2 起 plugin 才被官方 marketplace 收录存在轻度时间差。
- star 数「近 20 万」与 wiki 增长曲线（7 月 170K → 8 月 212K）不冲突。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock]] · [[超级奶爸老谭]] · [[mattpocock skills 标准工作流]] · [[Matt Pocock setup-matt-pocock-skills 配置落地]] · [[grill-me 实战指南]] · [[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]]
