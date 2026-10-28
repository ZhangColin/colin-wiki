---
type: source
title: "Superpowers writing-skills 规矩固化成技能"
domain: [AI编程, AI工具, 软件工程]
tags: [Superpowers, 老谭superpowers系列, writing-skills, SKILL.md, 元技能, 规矩固化, 触发条件]
sources: []
source_path: "raw/articles/20｜superpowerswriting-skills 把自己的规矩固化成技能.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/btihnCdaj9yQRQLk44waRw"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-18"
created: 2026-10-22
updated: 2026-10-22
status: active
aliases: [writing-skills, 规矩固化成技能, 把自己的规矩固化成技能]
---

# Superpowers writing-skills 规矩固化成技能

> 一句话要点：`writing-skills` 是元技能——**把一套做法写成一个 AI 也能读、能照着执行的 SKILL.md**。「description 管触发、正文管可执行、反模式管避坑、验证管生效。**记性靠不住，文件靠得住**。」

## 关键要点

- **动机**：二十篇攒的规矩（金额 BigDecimal / compareTo、isEqualByComparingTo、验证再收工、纵深防御、中文注释……）散落各篇、烙在脑子里——「脑子这东西」靠不住；AI 要照规矩写代码，得让它**能读到**，而不是靠它猜。
- **SKILL.md 三部分各有讲究**：frontmatter 的 `name` + `description`（**description 决定 AI 什么时候「想起来」读它**，是整个技能的门面）；正文（规矩写成一二三条、具体到能照着做）；目录位置（项目 `skills/` 下，AI 干活时能读到）。
- **写法四步**：
  1. **先想清楚什么时候该被读**：description 写成触发条件（「写 Java/Vue 代码前先读它，避免金额、命名、测试层面的低级错误」）——规矩从「散落各篇」变成「开工即读」；
  2. **规矩要可执行，不要正确的废话**：「注意金额精度」是废话，「金额一律 `BigDecimal`；比较用 `compareTo`；断言用 `isEqualByComparingTo`；超过两位小数用 `MoneyRules.check` 拦下」是指令——**AI 不需要提醒，需要指令**；
  3. **把反模式也写进去**：踩过的坑固化进「已踩过的坑（不要再踩）」区块（BigDecimal 转 double 差一分钱 → 哨兵测试；报表漏 ownerId 过滤 → 防回归）——「让 AI 不重蹈覆辙的最省力方式」；
  4. **验证技能真的会被用上**：让 AI 照规范写一个方法，逐条对照（BigDecimal？isEqualByComparingTo？驼峰？中文注释？）——全中技能生效，漏了说明规范没写清楚，回去补（把 verification-before-completion 用于元技能）。
- **案例**：`happy-ledger-coding-standards`——金额（红线）/ 命名 / 验证再收工 / 纵深防御四区块的项目级代码规范技能。

## 详细笔记

对应 [[Superpowers]] 18 技能元类（writing-skills / using-superpowers 之一，[[Superpowers 安装与技能全景]]）——Superpowers 用自己教「怎么写 Superpowers 技能」的自举点。位置在系列第 20 篇：技能教学全部完成后、组合实战开始前——叙事上「把 20 篇规矩收拢成文件」再「拿这套技能真刀真枪做一票大的」（21/22 篇）。老王「规矩这么多，要是你自己忘了呢」之问（17 篇埋线）由本篇收束。

**跨路线同构**（详见 [[writing-skills 跨路线对照]]）：「正确的废话 vs 可执行指令」即 mattpocock writing-great-skills 的 no-op 判定（「删掉它模型行为变没变」，[[Matt Pocock writing-great-skills 八词诊断]]）；「description 管触发」与 [[Skills 设计方法论]]「description 是路由触发器不是功能文档」共识；「把踩过的坑写进去」与 Perplexity Gotchas 飞轮同构；「验证 AI 真会用」是端到端任务 Eval 的雏形。

## 与已有内容的关联

- **前篇**：[[Superpowers receiving-code-review 接受审查]]（老王「固化下来让 AI 照着来」提议的落地）。
- **系列内引用**：技能区块即前 20 篇的浓缩——05 TDD / 16 verification / 17 defense-in-depth（[[Superpowers TDD 红绿循环]] · [[Superpowers verification-before-completion 验证再收工]] · [[Superpowers defense-in-depth 纵深防御]]）。
- **跨路线**：[[Matt Pocock writing-great-skills 八词诊断]]（做减法的诊断手术）vs 本篇（做加法的规矩固化）——同一 SKILL.md 工程的两种姿态，见 [[writing-skills 跨路线对照]]。
- **方法论锚点**：[[Skills 设计方法论]]（三层成本 / Gotchas / 四层 Eval 的官方共识底座）。
- **本 wiki 自反**：老谭「每个方法配中文注释 + 规矩写进文件」与 colin-wiki 的 CLAUDE.md/AGENTS.md schema 同构——「仓库文档 vs Memory」命题又一例证（见 [[LLM Wiki]] §8）。

## ⚠️ 矛盾 / 待澄清

- 与 wiki 既有记载无冲突。
- 口径注记（推论）：老谭演示的是**项目级**技能（规范绑定单一项目），未涉及跨项目复用与发布（mattpocock 路线的 skill 工程化标准面向复用）；两条路线的 writing-skills 适用域不同，非矛盾。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers receiving-code-review 接受审查]] · [[Superpowers defense-in-depth 纵深防御]] · [[Superpowers verification-before-completion 验证再收工]] · [[Superpowers TDD 红绿循环]] · [[Matt Pocock writing-great-skills 八词诊断]] · [[Skills 设计方法论]] · [[writing-skills 跨路线对照]]
