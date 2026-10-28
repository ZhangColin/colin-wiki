---
type: source
title: "Superpowers requesting-code-review 主动求审"
domain: [AI编程, 软件工程, 协作]
tags: [Superpowers, 老谭superpowers系列, requesting-code-review, 代码审查, diff自查, 审查说明]
sources: []
source_path: "raw/articles/18｜superpowersrequesting-code-review 提交前主动审查.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/wohrEQsUYGQAlSNaOO9RoA"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-16"
created: 2026-10-22
updated: 2026-10-22
status: active
aliases: [requesting-code-review, 主动求审, 提交前主动审查]
---

# Superpowers requesting-code-review 主动求审

> 一句话要点：`requesting-code-review` 把「请求审查」变成一套**提交前自检**流程——你交出去的不是「我的代码」，而是「**一份别人能高效审查的变更**」：自看 diff → 自跑测试 → 自查清单 → 自写说明。

## 关键要点

- **场景病**：写完就提交、提交就丢给人「帮我看看」——diff 一半讲不清为什么改、测试没跑就求审，是浪费审查者的时间。**代码审查不是「别人替你把关」，而是「你把自己的活干到最好，然后请别人帮你看看还有没有漏」**。
- **准备四步**：
  1. **自看差异**：`git diff` 摊开，自己能讲明白每一行为什么改（「把『我大概改了啥』变成『我清清楚楚改了每一行』」）；
  2. **自跑测试**：一条铁律——**测试不绿，别求审**（「测试是求审的敲门砖，不绿就求审等于没敲门直接闯」）；
  3. **自查清单**：命名（`amnt`→`amount`，「缩写就是给自己埋坑」）、遗留 TODO/死代码、边界条件（空数据/空值/超预算==预算）、夹带的无关改动（要拆出来）、金额走 `BigDecimal` + `compareTo`——**消灭低级问题，让审查者把精力花在真问题上**；
  4. **自写说明**：变更做了什么 + 为什么 + **重点想请你审哪**——「这步最关键，它把审查者的注意力引导到你心里没底的几处，比『你随便看看』高效十倍」。
- **心态**：求审是「请帮我找问题」，不是「请夸我」——准备工作做足，审查者才能专注找真问题。

## 详细笔记

对应 [[Superpowers]] 7 步流程第 6 环 `requesting-code-review` 的单技能篇（18 技能协作类，[[Superpowers 安装与技能全景]]）。案例：「预算页加超支提醒」变更（后端补 `spent` 聚合 + 前端标红）。教学叙事中审查者是人（老王），审查对象是 17 篇 defense-in-depth 的变更——**口径注记**：Superpowers 原版设定是 AI 审查（v6.0 已两 reviewer 合一、只读怀疑论者，见 [[Superpowers 6.0 reviewer 只读重写]]），老谭用「人审人」做教学化演绎，四步准备对 AI 审查同样适用（结构化审查说明 = 给 reviewer 的输入质量）。结尾老王「被挑毛病别上头」为 19 篇埋线。

## 与已有内容的关联

- **前篇**：[[Superpowers defense-in-depth 纵深防御]]（本篇审查的就是它的变更）。
- **与 v6.0 reviewer 同构**：[[Superpowers 6.0 reviewer 只读重写]] 的「文件替代粘贴」（审查输入结构化）与本篇「自写审查说明」同一上下文经济学；「想请你审哪」即 reviewer 的 spec 合规聚焦点。
- **与 verification 衔接**：第 2 步「测试不绿别求审」是 [[Superpowers verification-before-completion 验证再收工]] 三问清单的前两问前置到求审环节。
- **跨路线**：与 mattpocock `/code-review` 的 Standards/Spec 双轴互参——本篇「重点想请你审哪」相当于 Spec 轴（对照验收）的发起侧（推论，见 [[diagnosing-bugs 与 tdd]] 同家族页）。

## ⚠️ 矛盾 / 待澄清

- 与 wiki 既有记载无冲突。教学叙事（人审人）与 Superpowers v6.0（AI reviewer）的场景差异已在详细笔记标注，非矛盾。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers defense-in-depth 纵深防御]] · [[Superpowers receiving-code-review 接受审查]] · [[Superpowers verification-before-completion 验证再收工]] · [[Superpowers 6.0 reviewer 只读重写]] · [[Superpowers subagent-driven-development AI 项目经理]]
