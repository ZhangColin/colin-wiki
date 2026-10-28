---
title: "20｜superpowers/writing-skills 把自己的规矩固化成技能"
source: "https://mp.weixin.qq.com/s/btihnCdaj9yQRQLk44waRw"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-18
description: "记性靠不住，规矩要写成 AI 也能读的 SKILL.md。用「幸福账本代码规范」这个真实技能，讲怎么写、写在哪、怎么让 AI 照着做。"
tags:
  - "superpowers"
---
超级奶爸老谭！ 共渡时艰 *2026年9月18日 08:05*

## 前文回顾

上一篇 [19｜superpowers/receiving-code-review 接受代码审查](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484348&idx=1&sn=3e495c480d7aae28b543407e345eaaa8&scene=21#wechat_redirect) 我们接住了审查意见， `MoneyRules` 多了一个 `Optional` 版本，更清爽了。老王提议： **“把这套规矩固化下来，让 AI 也照着来。”**

## 场景：规矩写在记性里，迟早漏

二十篇下来，我们攒了一堆规矩：

- 金额必须 `BigDecimal` ，比较用 `compareTo` ；
- 断言用 `isEqualByComparingTo` ；
- 验证再收工，没证据不算完成；
- 纵深防御，别信单点；
- 方法名纯英文驼峰，每个方法配中文注释……

这些规矩，散落在每一篇文章里，也烙在我脑子里。但脑子这东西——老王有次问我："上周那条规矩是啥来着？"我愣了三秒。

**记性靠不住。** 尤其当 AI 要照着这些规矩写代码时，你得让它 **能读到** 规矩，而不是靠它"猜"。

`writing-skills` （写自己的技能）就是这个元技能： **把一套做法，写成一个 AI 也能读、能照着执行的 SKILL.md。**

## 技能是什么：SKILL.md 是"写给 AI 看的规矩文件"

superpowers 里的"技能（skill）"，本质是一个带 frontmatter 的 Markdown 文档，放在项目的 `skills/` 目录下。AI 在合适场景会自动读到它、照着做。

一份 SKILL.md 长这样：

```
---
name: happy-ledger-coding-standards
description: 幸福账本全栈代码规范。写 Java / Vue 代码前先读它，避免金额、命名、测试层面的低级错误。
---

# 幸福账本代码规范

## 金额（红线）
- 一律 BigDecimal，禁止 double 参与累加/比较
- 比较用 compareTo，断言用 isEqualByComparingTo
- 两位小数，超出用 MoneyRules.check 拦

## 命名
- 方法名纯英文驼峰，禁止下划线、禁止中文
- 每个方法配中文注释（Javadoc / 行注释）

## 验证再收工
- 提交前 mvn test / npm test 必须全绿
- 前端改动要在浏览器真实走一遍
- 对照验收标准逐条核对，才宣布完成

## 纵深防御
- 前端校验 + 后端 @Valid + 业务 MoneyRules 层层叠
- 每层独立，不赌上一层拦得住
```

三个部分各有讲究：

- frontmatter 的 `name` 和 `description` ： `description` 决定 AI 什么时候"想起来"读它——写清楚"什么场景用"；
- 正文： 把规矩写成一二三条，具体到能直接照着做（“用 `compareTo` "而不是"注意精度”）；
- 目录位置： 放进项目 `skills/` ，AI 在项目里干活时能读到。

## 怎么用：把幸福账本的规矩写成技能

### 第一步：先想清楚，这个技能"什么时候该被读"

`description` 是触发条件。我写的是：

> 写 Java / Vue 代码前先读它，避免金额、命名、测试层面的低级错误。

这样 AI 一要在项目里写代码，就会先去读这份规范—— **规矩从"散落各篇"变成"开工即读"。**

### 第二步：规矩要具体到"照着做"

规矩最怕"正确的废话"。对比两种写法：

- 废话版：“注意金额精度问题”
- 可执行版：“金额一律 `BigDecimal` ；比较用 `compareTo` ；断言用 `isEqualByComparingTo` ；超过两位小数用 `MoneyRules.check` 拦下”

AI 不需要"提醒"，需要"指令"。 **能直接变成代码的规矩，才算写到位。**

### 第三步：把"反模式"也写进去

光写"该怎么做"，AI 还是会踩坑。把踩过的坑也固化进去：

```
## 已踩过的坑（不要再踩）
- 求和时把 BigDecimal 转 double → 月底差一分钱
  （防回归：doubleSummationHasError 哨兵测试）
- 报表聚合漏按 ownerId 过滤 → 家庭账混入乐乐
  （防回归：familyReportExcludesKidLedger）
```

**把教训写进技能，AI 就不会重蹈覆辙。** 这比让它反复犯同样的错、再反复修，高效太多。

### 第四步：验证"技能真的会被用上"

写完别急着收工——按 `verification-before-completion` 那套，验证一下技能管不管用。最直接的验证： **让 AI 照着这份规范写一个方法，看它守不守规矩。**

比如让它写 `sumExpenses` ，看它：金额用 `BigDecimal` 吗？断言用 `isEqualByComparingTo` 吗？方法名驼峰吗？配中文注释吗？全中，技能生效；漏了，说明规范还没写清楚，回去补。

## 注意事项

1. `description` 决定 AI 什么时候读它。 触发条件写不清楚，AI 根本不会想起这份规矩。这是整个技能的门面。
2. 规矩要可执行，不要"正确的废话"。 "注意精度"是废话，"用 `compareTo`
	"是指令。AI 需要指令。
3. 把踩过的坑写进去。 教训固化进技能，是让 AI 不重蹈覆辙的最省力方式。
4. 写完要验证 AI 真会用。 让它按规范写一段代码，对照规范逐条检查。不管用的技能，等于没写。

## 本文小结

`writing-skills` 把散落的规矩固化成 AI 能读的 SKILL.md： `description` 管触发、正文管可执行、反模式管避坑、验证管生效。 **记性靠不住，文件靠得住。**

幸福账本的代码规范，从此不再是"我记得有这么条规矩"，而是"AI 一开工就先读它"。

老王看着那份 SKILL.md，忽然说了句：「你看，咱们二十篇攒的东西，现在都变成 AI 能照着走的规矩了。 **要不……拿这套技能，真刀真枪做一票大的？** 」

我眼睛一亮。下一站，就是这套技能的第一次实战检验——而且这一次，是 **全栈** 的。

下一站：组合实战第一篇——从一句话到上线。

21｜组合1：从一句话到上线，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #元技能 #skill

共渡时艰

邀请你前往腾讯公益一起捐

陪乡村孩子阅读世界

✨ 人工智能 · 目录