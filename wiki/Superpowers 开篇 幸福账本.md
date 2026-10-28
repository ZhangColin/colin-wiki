---
type: source
title: "Superpowers 开篇 幸福账本"
domain: [AI编程, 软件工程]
tags: [Superpowers, 老谭superpowers系列, 入门, 系列开篇, 教学项目]
sources: []
source_path: "raw/articles/00｜superpowers 开篇：艳姐说，我们家钱算不清了.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484223&idx=1&sn=da68f8d58934055a5ffac2166ba99884"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-21"
created: 2026-09-10
updated: 2026-09-10
status: active
aliases: [superpowers 开篇, 幸福账本开篇, 00｜superpowers 开篇]
---

# Superpowers 开篇 幸福账本

> 一句话要点：[[超级奶爸老谭]] 第二套教学系列的开篇——用「幸福账本」家庭记账全栈项目贯穿 Superpowers 全部技能，讲清 superpowers 是什么（把"怎么用 AI 把软件做靠谱"拆成可照做的技能）、系列写法四规矩与技术栈约定。

## 关键要点

- **系列缘起与项目**：艳姐（妻子）抱怨"家里的账算不清"→ 老谭用 AI + obra/superpowers 写「幸福账本」——网页记账系统，从一句抱怨长成前后端完整应用，"像追剧一样学技术"。验收标准就一条：每月工资到账那天账能对平，艳姐网页点几下能看到花销与预算。
- **superpowers 定位（老谭口径）**：[[Jesse Vincent]]（obra，"老欧"）的开源项目，把"怎么用 AI 把软件做靠谱"拆成一整套可照做的技能。一句话总结：**让 AI 从"会写代码"变成"会跟人一起把事干成"**。对照痛点：无规矩的 AI = 需求没聊清、代码没测试、bug 乱改（"三千行代码花三个晚上看不懂，最后启动都启动不了"）。
- **运作机制**：Claude Code 装插件后注入入口技能 `using-superpowers`，按场景自动激活对应技能；也可 `/brainstorm`、`/write-plan`、`/execute-plan` 主动调用。
- **系列四规矩**：① 示例代码用 Java 且必须能跑（仓库原生 TypeScript 例子翻译成 Java 生态）；② 同一完整项目从第一篇看到最后一篇（前后端完整，非单个后端库）；③ 一篇讲一个技能（使用场景→技能是什么→怎么用→注意事项）；④ 用 Obsidian 管理笔记，固定标签 `superpowers`。
- **技术栈约定（全系列不变）**：后端 Java 21 + Spring Boot 3 + Maven + H2 文件库（落盘重启不丢）；前端 Vue 3 + Vite + Element Plus（`/api` 代理）；测试 JUnit 5 + Mockito + AssertJ（异步用 Awaitility）/ Vitest；**金额一律 `BigDecimal` 禁止 `double`**。
- **技能路线图**：按仓库分类列全技能树（先聊清楚→写下来→写对代码→并行不打架→出 bug 不慌→审好代码→沉淀自己），中间穿插两篇基建篇（REST API / Vue 前端），最后两篇组合实战——系列规划二十多篇，**本 wiki 收录时（09-10）连载至第 14 篇**。

## 详细笔记

老谭上一系列教的是 [[mattpocock skills]]（EShop 案例 12 篇，v1.1 口径快照），本系列转向 [[Superpowers]]——同一作者先后系统教学"路线之争"的两条路线，本身是两条路线面向不同读者、可共存的注脚（详见 [[Superpowers 与 mattpocock skills 路线对照]]）。角色阵容：艳姐（需求方/验收人）、乐乐（零花钱子账本，ownerId=2）、老王（邻居，小白提问者）。文末"源码后台私信获取"。

## 与已有内容的关联

- 相关概念：[[Superpowers]] · [[mattpocock skills]] · [[Superpowers 与 mattpocock skills 路线对照]]
- 相关实体：[[超级奶爸老谭]] · [[Jesse Vincent]]
- 技能树已预告、待收录的篇目：root-cause-tracing / verification-before-completion / defense-in-depth / requesting-code-review / receiving-code-review / writing-skills / 组合实战 2 篇

## ⚠️ 矛盾 / 待澄清

- **版本时点**：系列为 2026-08 仓库快照（18 技能口径，见 [[Superpowers 安装与技能全景]]），早于本系列的 v6.0（2026-06）reviewer 重写未被提及——subagent-driven-development 篇的两级审查口径与 v6.0 单 reviewer 框架不同，已在 [[Superpowers subagent-driven-development AI 项目经理]] 标注。
- 无与 wiki 既有声明的直接冲突。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Jesse Vincent]] · [[Superpowers 安装与技能全景]] · [[Superpowers 与 mattpocock skills 路线对照]] · [[mattpocock skills 完全入门指南]]
