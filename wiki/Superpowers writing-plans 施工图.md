---
type: source
title: "Superpowers writing-plans 施工图"
domain: [AI编程, 软件工程]
tags: [Superpowers, 老谭superpowers系列, writing-plans, 实施计划, 任务拆分, 验收标准]
sources: []
source_path: "raw/articles/03｜superpowerswriting-plans 把想法拆成步骤.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484240&idx=1&sn=cc5212d2a38e84ab9dfa6ed9e1478024"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-26"
created: 2026-09-10
updated: 2026-09-10
status: active
aliases: [superpowers writing-plans, /write-plan, 写计划技能]
---

# Superpowers writing-plans 施工图

> 一句话要点：`writing-plans` 把设计规格（What）翻译成实施计划（How）——带粒度、带验收、带依赖的施工图；粒度灵魂是"小到 AI 一次能做对，大到你还看得出它在干嘛"。

## 关键要点

- **规格 vs 计划**（艳姐的点破）："你这上面写的是要啥，没写先干啥后干啥"——规格是"终点在哪"的地图，计划是"第一步迈哪只脚"的施工图。
- **核心是粒度控制**，每步四要素：明确的任务内容（做什么）、验收标准（做完怎么算成）、依赖顺序（哪些必须在前）、粒度双向达标——**小到 AI 能一次性做对，大到你还看得出它在干嘛**。
- **验收必须可测试**："每步验收必须是能跑的命令或断言，不是'大概好了'"——这些验收是后面 executing-plans 与 verification-before-completion 拿去当尺子的。
- **计划不写代码**：计划只写"这一步要达成什么、怎么验证"；代码是执行阶段的事（示例 MoneyRules 金额不变式：非空 / >0 / 最多两位小数，返回 `Optional<String>`）。
- **实例（幸福账本第一批）**：阶段 A 后端领域与测试（A1 领域对象 → A2 预算服务 → A3 金额不变式，验收全是"哪个测试绿"）；阶段 B 接口化、阶段 C 前端留到后续批——"计划不是一次列完所有事"。
- **计划要进版本库**：跟代码一起提交，审查复盘有据可查。

## 详细笔记

注意事项：计划 ≠ 第二份需求文档（回答"先干什么、干到什么算完"）；粒度太粗 AI 自由发挥、太细人累死；没有验收的步骤等于没有终点的跑步。

## 与已有内容的关联

- 对应 [[Superpowers]] 7 步流程第 3 步；Qsuperpowers 企业实战四阶段闭环同样含 writing-plans（[[去哪儿网 AI Coding 研发平台实践]]）。
- 与 mattpocock 侧 to-spec→to-tickets 文件链（[[to-spec 流水线对话变代码]]）同位对照：Superpowers 用一份计划文档，mattpocock 用 spec/tickets 两级文件。
- **版本注记**：v6.0 起 writing-plans 每个 task 须带 Interfaces block（Consumes/Produces 精确签名，因 implementer 只看自己 brief，见 [[子代理驱动开发]] ⚠️ 区）——本源未提及该要求，系 2026-08 教学简化口径。

## ⚠️ 矛盾 / 待澄清

- 无直接矛盾；Interfaces block 缺失属版本口径差异（见上）。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers brainstorming 需求访谈]] · [[Superpowers executing-plans 一步一确认]] · [[子代理驱动开发]] · [[to-spec 流水线对话变代码]] · [[去哪儿网 AI Coding 研发平台实践]]
