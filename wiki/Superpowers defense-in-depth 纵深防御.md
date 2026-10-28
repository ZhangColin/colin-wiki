---
type: source
title: "Superpowers defense-in-depth 纵深防御"
domain: [AI编程, 软件工程, 安全]
tags: [Superpowers, 老谭superpowers系列, defense-in-depth, 纵深防御, Bean Validation, MoneyRules, 防回归测试]
sources: []
source_path: "raw/articles/17｜superpowersdefense-in-depth 给 API 多上几道保障.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/b22LES2veuZApZqgIKKcgg"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-15"
created: 2026-10-22
updated: 2026-10-22
status: active
aliases: [defense-in-depth, 纵深防御]
---

# Superpowers defense-in-depth 纵深防御

> 一句话要点：`defense-in-depth` 把「一道锁」换成「纵深防线」——不指望任何一道防线万无一失，把防线一层层叠起来，**一处失守，下一层还在**。「单点防御赌『拦得住』，纵深防御赌『就算漏了，下面还有』」。

## 关键要点

- **「一道锁」的致命弱点**：只在**前端**限制「金额必须大于 0」，有人绕过前端直接 `curl` 打后端传 `amount: -5`——后端照单全收，负数账进库。**前端那道锁，对 curl 形同虚设**。
- **核心思想来自安全领域**：系统每一层都有独立防御，攻击者要层层突破而不是一层就到底。每层**独立工作、不依赖上一层「已经拦过」**。
- **四道锁**（「记一笔」接口案例）：
  1. **前端表单校验**：提交前拦（价值是**体验**，挡不住绕过它的请求）；
  2. **后端参数校验**：`@Valid` + Bean Validation（`@NotNull` / `@Positive`），接口层格式兜底，绕过前端直接 400；
  3. **后端业务校验**：`MoneyRules` 守注解表达不了的业务不变式（如「金额最多两位小数」——`@Positive` 管「大于 0」管不了小数位）；
  4. **防回归测试**：「防线的防线」——把「金额非法必须被拦」写成测试，防止哪天有人把防线拆了。
- **注意事项四条**：每层防御要独立（别写「因为前端校验了后端就不管」）；前端校验管体验、**后端校验管安全**（真正的安全边界在后端）；注解管格式、业务代码管不变式；防线要能自我守卫。
- **金句**：「没有任何一道防线需要『完美』，因为它们是一层压一层。」

## 详细笔记

对应 [[Superpowers]] 18 技能 debugging 类第 4 个（v5.0.7 14 技能口径新增之一，[[Superpowers 安装与技能全景]]）——**至此 debugging 类四技能（14/15/16/17）全部收录**。16 篇老王之问（「AI 绕过第一层防线，第二层能兜住吗？」）由本篇回答；结尾老王新问（「要是你自己忘了这些规矩，怎么办？」）为 18→20 篇（requesting-code-review → writing-skills）埋线——「不靠记性，靠流程，最好变成 AI 也认得的规矩」。

代码时间线注记：本篇 `MoneyRules.check`（返回 `Optional<String>`）即 19 篇审查意见的产物（代码注释自标「017 篇按审查意见新增」），15 篇原版为 `assertValid` 直接抛异常——系列代码前后自洽（见 [[Superpowers receiving-code-review 接受审查]]）。

## 与已有内容的关联

- **前篇**：[[Superpowers verification-before-completion 验证再收工]]（老王「防线层层」之问的直接回答；防回归测试是两篇共同的收尾装置）。
- **MoneyRules 谱系**：首建于 15 篇（[[Superpowers root-cause-tracing 根因追踪]]，BigDecimal 不变式）→ 本篇升格为纵深防御第 3 层 → 19 篇补 `check` 版本接口来历。
- **测试类呼应**：第 4 层「防回归测试钉死防线」与 [[Superpowers testing-anti-patterns 测试反模式]]、05 篇哨兵测试（[[Superpowers TDD 红绿循环]]）同一装置谱系。
- **跨源同构**：「层层设防 + 单点失守不致命」与 [[TAB 大仓 Harness 工程化实战]] 的 Harness 多道锁、[[去哪儿网 AI Coding 研发平台实践]] 的 Harness 四把锁互为印证（企业级把 defense-in-depth 做成平台机制）。

## ⚠️ 矛盾 / 待澄清

- 与 wiki 既有记载无冲突；该技能为此前「待收录」项，本篇收录后 18 技能中 debugging 类已收齐。
- 教学边界注记：本篇防线止于「应用层四道锁」，未涉及数据库层约束（如 CHECK/NOT NULL）与权限层——「纵深」的教学演示口径，非完备安全清单（推论）。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers verification-before-completion 验证再收工]] · [[Superpowers root-cause-tracing 根因追踪]] · [[Superpowers receiving-code-review 接受审查]] · [[Superpowers testing-anti-patterns 测试反模式]] · [[Superpowers TDD 红绿循环]] · [[去哪儿网 AI Coding 研发平台实践]] · [[TAB 大仓 Harness 工程化实战]]
