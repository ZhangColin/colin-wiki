---
title: "03｜superpowers/writing-plans 把想法拆成步骤"
source: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484240&idx=1&sn=cc5212d2a38e84ab9dfa6ed9e1478024&chksm=ffe5d37dc8925a6bdc03f0d660eac9069a0087e69b47c4f89e5e9cbf36122f97ab6482a190c1&cur_album_id=4599400782759952387&scene=189#wechat_redirect"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-09
description: "规格聊清楚了，但「聊天」和「能照着做」之间隔着一张施工图。用 writing-plans 把规格拆成前后端任务清单，每步带验收标准。"
tags:
  - "superpowers"
---
超级奶爸老谭！ 共渡时艰 *2026年8月26日 08:05*

## 前文回顾

上一篇 [02｜superpowers/brainstorming 先把需求聊清楚](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484237&idx=1&sn=d8dbdd22f64b4b0c13a7d8d4898b1c65&scene=21#wechat_redirect) 我们用一问一答，把艳姐那句“做个家庭记账”聊成了一份规格：五件事（记账/预算/对账/报表/零花钱子账本）、领域模型（ `Budget` / `BudgetCheckResult` ）、页面草图（五屏）、三条验收标准。

老王看完，把手机往兜里一揣，问题又来了： **“聊清楚了，然后呢？总不能一口气全写完吧？”**

## 场景：规格是张地图，还不是施工图

晚上我把那份规格翻出来给艳姐看。她扫了两眼，说了一句特别在行的话：

「你这上面写的是 **要啥** ，没写 **先干啥后干啥** 。」

我当时一愣。她说得对。

![03-1](https://mmbiz.qpic.cn/mmbiz_jpg/d9Wx6XKcQmRz70UoLhgiaiaH3Zw3DgTdn0icXVHFvQ9JIL4PibVZmhFpv1hVMog3Bm0ibiaDwIq586usjNfuIia5hQMs0jZwj2qe2HJVmOMxb01wP0/640?from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

一份规格，是一张“终点在哪”的地图；但施工要的是“第一步迈哪只脚、第二步迈哪只脚”的 **施工图** 。中间差的那层东西，就是 superpowers 协作技能里排第二的： `writing-plans` 。

`writing-plans` 干的事一句话： **把设计规格，翻译成一份能照着执行的实施计划。**

它跟 brainstorm 的分工很清楚：

- brainstorming： 把模糊想法问成 **规格** （What）
- writing-plans： 把规格拆成 **计划** （How，一步步）
- executing-plans： 照着计划 **执行** （Do，人机确认）

老王点点头：「所以是 问清楚 → 写计划 → 干。三张入场券齐了。」

「对，就差今天的第二张。」

## 技能是什么：writing-plans 是“把目标翻译成任务树”

`writing-plans` 的核心是 **粒度控制** 。一份好的实施计划，不是“实现预算功能”这种废话，而是拆到 **每一个可独立验证的步骤** ：

- 每一步有明确的 **任务内容** （做什么）
- 每一步有 **验收标准** （做完怎么算成）
- 步骤之间有 **依赖顺序** （哪些必须在前）
- 每一步小到 **AI 能一次性做对** ，大到 **你还能看出它在干嘛**

在 Claude Code 里直接敲：

```
/write-plan
```

它会读你 `brainstorm` 出的规格，然后生成一份计划文档。

## 怎么用：把幸福账本拆成第一批任务

我以「幸福账本」第一批任务为例，带你看看一份计划长什么样。注意它怎么把前后端两条线都安排进去的。

![03-2](https://mmbiz.qpic.cn/mmbiz_jpg/d9Wx6XKcQmTeYWI50qRo3f1icRb9G7ibhHV2Vhfbch6VTvMiazq9ZjuLHRVRr68ElooSJaVsAHWDRF0ZtaZHXAfOux2IYYoazFGfG8sccuORqI/640?from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

### 计划的骨架：分阶段，每阶段几步

```
幸福账本 · 实施计划 v1（第一批）

阶段 A：后端领域与测试（可独立验证）
  A1 领域对象：Entry / Category / Budget / BudgetCheckResult
      验收：mvn test 全绿，EntryTest 过
  A2 预算服务：BudgetService.check(spent, limit)
      验收：BudgetServiceTest 覆盖超支/不超支/空限额
  A3 金额不变式：MoneyRules 校验两位小数
      验收：MoneyRulesTest 覆盖非法金额

阶段 B：把领域变成接口（下一批，见 008 篇）
阶段 C：前端页面（再下一批，见 009 篇）
```

看到没有， **计划不是一次列完所有事** ，而是把当下能做的拆出来，每步带验收。写完后我自己都忍不住点头——这才叫能照着干。

### 每一步的粒度：小到 AI 一次能做完

拆完阶段， `writing-plans` 还会把每一步再往下压一层。拿 A2 举例：

```
A2 预算服务
  内容：写 BudgetService，方法 check(BigDecimal spent, BigDecimal limit)
       返回 BudgetCheckResult(exceeded, spent, limit)
  验收：assertThat(result.exceeded()).isTrue() 当 spent > limit
       金额比较必须用 compareTo，禁止 equals
  依赖：A1 完成（BudgetCheckResult 已定义）
```

这一步小到什么程度？ **小到 AI 一看就懂、一次能写对** ，大到 **你还看得出来它在干嘛、验收能不能过** 。这个粒度，是 `writing-plans` 的灵魂。

### 计划的验收要可测试

`writing-plans` 还有个要求： **每步验收必须是能跑的命令或断言，不是“大概好了”。** 计划里写的验收，都是后面 `executing-plans` 和 `verification-before-completion` 要拿去当尺子的。

你看 A 阶段三步，验收全是“哪个测试绿”。没有一句“功能完成度 80%”这种没法验证的话。

```
package com.laotan.ledger;

import java.math.BigDecimal;
import java.util.Optional;

/** 金额不变式：金额必须是两位小数的正数，全程 BigDecimal。 */
public final class MoneyRules {

    private MoneyRules() {
    }

    /** 不合法返回失败原因，由上层决定怎么响应。 */
    public static Optional<String> check(BigDecimal amount) {
        if (amount == null) {
            return Optional.of("金额不能为空");
        }
        if (amount.compareTo(BigDecimal.ZERO) <= 0) {
            return Optional.of("金额必须大于 0");
        }
        if (amount.scale() > 2) {
            return Optional.of("金额最多两位小数，收到 " + amount);
        }
        return Optional.empty();
    }
}
```

这段代码会出现在计划里的 A3 步——但计划本身 **不写代码** ，计划只写“这一步要达成什么、怎么验证”。代码是执行阶段（ `executing-plans` ）的事。

## 注意事项

1. 计划 ≠ 需求文档。 需求文档回答“要什么”，计划回答“先干什么、干到什么算完”。别把计划写成第二份需求文档。
2. 粒度是双向的。 太粗（“实现记账”）AI 会自由发挥，太细（“把第 3 行改成 4”）你会累死。标准：AI 一次能做对，你一眼能看出对不对。
3. 每步都要有验收标准。 没有验收的步骤，等于没有终点的跑步——执行时你不知道该停在哪。
4. 计划要进版本库。 计划文档跟代码一起提交，后面审查、复盘都有据可查。

## 本文小结

`writing-plans` 把规格翻译成了 **带粒度、带验收、带依赖的施工图** 。第一批任务就位：领域对象 → 预算服务 → 金额不变式，每步都钉死了验收。

老王盯着计划看了半天，冒出一句：「写是写清楚了，可真让它干的时候，它会不会又放飞？」

「这就是第三张入场券要解决的。」我打开终端，「明天我让你看看什么叫 **照着计划干** 。」

我们下一节，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #sdd #计划

✨ 人工智能 · 目录