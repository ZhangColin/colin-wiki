---
title: "02｜superpowers/brainstorming 先把需求聊清楚"
source: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484237&idx=1&sn=d8dbdd22f64b4b0c13a7d8d4898b1c65&chksm=ffe5d360c8925a76676aaf9444e6da03d038d0bfbffb8011e2b1367ba4c0a1997cf65f627386&cur_album_id=4599400782759952387&scene=189#wechat_redirect"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-09
description: "艳姐一句「做个家庭记账」，信息太少没法动手。用 superpowers 的 brainstorming 技能，一问一答把需求聊成领域模型和页面草图。"
tags:
  - "superpowers"
---
          超级奶爸老谭！ 共渡时艰 *2026年8月25日 08:05*

## 前文回顾

上一篇 [01｜安装：五分钟把 superpowers 装进 claude code](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484230&idx=1&sn=39f4ff9211d0a43fb8af2511da2c0464&scene=21#wechat_redirect) 我们五分钟装好了 superpowers，还搭起了「幸福账本」的前后端双工程骨架：Java 后端管数据，Vue 前端跑浏览器。技能树 18 个技能，先从哪张入场券开始？

老王已经把问题顶到门口了： **“我得先说清楚我到底要啥吧？”**

## 场景：一句话需求，信息量为零

艳姐坐在沙发上，把手机往茶几上一搁，吐出了一句：

「老谭，你给我做个家庭记账。要能看每月花多少钱、有没有超预算、账对不对得上。哦对了，乐乐还要单独一个账本记她的零花钱。」

……没了。

就这三句话。你要是直接甩给 AI「给我写个记账系统」，十分钟后它交你三千行代码，你花三个晚上看不懂它想干嘛——我去年就吃过这个亏。

![02-1](https://mmbiz.qpic.cn/sz_mmbiz_jpg/d9Wx6XKcQmRx7XZ6Rgg2icvJYxHhMN0bFicP77nZXDxqOXXjTIrI7zSJcSg9ibKfIgJdMXNlYNW6OW1SmCiaf3pfWC99Z7RuCK0p8WdmClx4FRk/640?from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

**需求没说清楚就动手，是对自己和 AI 的双重折磨。**

superpowers 解决这个的第一张入场券，就是 `brainstorming` ： **先用苏格拉底式提问，一个问题一个问题地，把脑子里那团模糊的想法，问成一份能写的规格。**

我跟老王解释：「它不写代码，它先当面试官。」

「谁面谁？」老王问。

「它面我。」我苦笑，「问得比艳姐还细。」

## 技能是什么：brainstorming 是“把想法问成规格”

`brainstorming` （头脑风暴）是 superpowers 协作类技能里排第一的那个。它的核心不是“想点子”，而是 **收敛** ——用提问把你的想法逼出来、钉死，直到能落成一份设计规格。

它跟“瞎聊”的区别在三点：

1. 一次只问一个问题，不轰炸你，让你每个回答都是认真想过的；
2. 问的是决策点： 目标、边界、取舍、验收标准，不是“你高兴吗”；
3. 产出是文档： 聊完给你一份设计规格，写进项目，成为后面的施工图。

在 Claude Code 里，装好 superpowers 后直接敲：

```
/brainstorm
```

它就开问了。

## 怎么用：和 AI 一问一答，把账本聊清楚

我跟 AI 的这场头脑风暴，从艳姐那三句话开始。我把它整理成你也能照着走的四轮。

![02-2](https://mmbiz.qpic.cn/mmbiz_jpg/d9Wx6XKcQmQFrR08G5K1GPWNJKLfAMzTPRePSgNMx8cBJ1csBfE6EiaK5P01pWRTWlfc6rTUwHT00ClKNA0dPo9RWcHqk2FRHVTDt831mIiao/640?from=appmsg&watermark=1#imgIndex=1)

### 第一轮：先把范围钉死

我敲了 `/brainstorm` ，把需求原文贴给它。AI 的第一个问题就问到了点子上：

> **AI** ：你这次想解决的核心问题是什么？“家庭记账”范围很大——是单纯记录流水，还是要管预算、做报表、对账？

这正是艳姐那三句话里没说清的东西。我的回答直接圈定：

- 记账：记收入/支出（金额、分类、时间、备注）
- 预算：每月每个分类设限额，超了要提醒
- 对账：月底汇总核对，账要平
- 报表：按分类看月度汇总
- 零花钱：乐乐单独子账本，跟家庭账隔离

一轮下来，范围从“家庭记账”收缩成五件事。 **范围钉死之前，不写一行代码。**

### 第二轮：把领域对象聊出来

AI 接着追问：

> **AI** ：记账的时候，“分类”是怎么来的？每笔账必须选分类吗？

我答：分类是预置好的（吃饭、穿衣、住房、出行、娃、医疗、人情往来……），每笔账必须选一个。这一问，把核心领域对象 **分类（Category）** 聊出来了。

AI 再问：

> **AI** ：预算按什么维度设？一个月一个分类一个额度？

对。这就是 **预算（Budget）** ，它的唯一性约束是“分类 + 月份”。

一轮对话结束，领域模型浮出水面。记账的核心对象在 Java 里长这样：

```
package com.laotan.ledger;

/** 某分类某月的预算上限 */
public record Budget(Long id, Long categoryId, java.time.YearMonth month,
                     java.math.BigDecimal limitAmount) {
}
```

预算检查结果——超没超、花了多少、上限多少，也要一个不可变对象装：

```
package com.laotan.ledger;

import java.math.BigDecimal;

/** 预算检查结果：是否超支、已花多少、限额多少。 */
public record BudgetCheckResult(boolean exceeded, BigDecimal spent, BigDecimal limit) {
}
```

注意两处：月份用 `YearMonth` 而不是 `String` ，金额用 `BigDecimal` 而不是 `double` 。 **这些都是 brainstorm 阶段就该定的约束** ，别等写代码了再回头改。

### 第三轮：把页面草图聊出来

我提醒 AI：这是网页应用，不是命令行。于是它换了个角度：

> **AI** ：艳姐打开浏览器，第一步先看到什么？她要完成的核心操作是什么？

我按艳姐的习惯画了张草图，AI 帮我校准成五屏：

- 账目页： 看某月流水，能删错账
- 记账页： 记一笔，选类型/分类/填金额/写备注
- 报表页： 月度分类汇总，一眼看清钱花哪了
- 预算页： 给分类设月度预算
- 零花钱页： 乐乐的子账本，看花了多少、超没超

这个“页面草图”最后落成一个表格，就是前端路由的雏形：

```
/          账目页    某月流水列表
/entry     记账页    记一笔账
/reports   报表页    月度分类汇总
/budgets   预算页    设月度预算
/allowance 零花钱页  乐乐子账本
```

**需求聊得越清楚，后面每一篇的示例代码就越有根。**

### 第四轮：把验收标准问出来

AI 的最后一问最扎心：

> **AI** ：怎么算“做成”？艳姐验收看什么？

我想起艳姐的原话，一字不落地写进去：

- 每月工资到账那天，账能自己对平
- 在网页上点几下，就能看到花了多少、超没超预算
- 乐乐的账跟家里的账，各是各的，混不了

这几条，后来成了全系列的 **验收标尺** 。

## 注意事项

1. 别跳过 brainstorm 直接写代码。 需求里藏着大量你没意识到的决策——分类从哪来、预算按什么维度、子账本怎么隔离。这些不聊清楚，写出来全是返工。
2. 一个问题一个问题答。 `brainstorming`
	一次只问一个，是故意的。你急着一次性交代完，反而容易漏决策点，让它问下去。
3. 产出要落文档。 聊完把规格写进项目（superpowers 会让你存成 `spec`
	文档）。口头聊完就动手，等于没聊。
4. 金额、时间的约束，在需求阶段就定。 `BigDecimal`
	\+ `YearMonth` 这种决定，现在定下来，后面二十篇都不走样。

## 本文小结

`brainstorming` 教的不是“怎么想点子”，而是 **怎么把模糊需求问成一份能施工的规格** 。这一场问下来，我们拿到了：范围（五件事）、领域模型（ `Budget` / `BudgetCheckResult` ）、页面草图（五屏）、验收标准（三条）。

规格有了，老王下一个问题又冒出来：「聊清楚了，然后呢？总不能一口气全写完吧？」

对。 **要把规格拆成能照着做的一步步的活** ——这就是下一篇 `writing-plans` 技能要干的事。

我们下节课，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #brainstorming #需求 #vibecoding

✨ 人工智能 · 目录