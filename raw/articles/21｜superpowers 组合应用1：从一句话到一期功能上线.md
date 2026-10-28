---
title: "21｜superpowers 组合应用1：从一句话到一期功能上线"
source: "https://mp.weixin.qq.com/s/A-EYgIlEcXvrXieUZjvqpg"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-20
description: "把前面 20 篇的技能串起来真刀真枪干一票：乐乐零花钱，从艳姐一句话到全栈上线。brainstorm 到验证，一条流水线走完。"
tags:
---
超级奶爸老谭！ 共渡时艰 *2026年9月20日 08:05*

## 前文回顾

上一篇 [20｜superpowers/writing-skills 把自己的规矩固化成技能](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484351&idx=1&sn=bae699aa63606bdb974dec1eb1edac08&scene=21#wechat_redirect) 把 20 篇攒的规矩固化成了 SKILL.md。老王提议： **“拿这套技能，真刀真枪做一票大的。”**

正赶上一个真需求——乐乐放学回来，举着零花钱罐子，理直气壮：

「爸爸，我下个月要开始记账了！妈妈说我每个月有 200 块零花钱，花超了就不给！」

艳姐在厨房补了一句：「她说的对。你给她做个『零花钱页』，让她自己看着花。」

一句话需求，又来了。

## 场景：一句话需求，全栈落地

这次跟前二十篇不一样——前二十篇我们一个个技能拆开练，今天要 **把技能串起来** ，把一个需求从"艳姐一句话"干到"乐乐真能用"，走完一条完整流水线。

目标拆开就两块：

- 后端： 算乐乐本月花了多少、超没超 200 额度，给一个接口；
- 前端： 零花钱页，显示支出、额度、超支提醒，乐乐一开就知道。

听起来简单，但"从一句话到上线"的每一步，都要靠前面学过的技能撑住。今天的流水线长这样：

```
brainstorm → writing-plans → executing-plans
→ TDD → REST API → 前端 → verification → 上线
```

## 第一步：brainstorm——先把需求问清楚

乐乐那句"记零花钱账"，跟当年艳姐那句"做个家庭记账"一样模糊。按 `brainstorming` 的老规矩，跟 AI 一问一答，把边界钉死：

- 范围： 只算乐乐（ownerId=2）的账，跟家庭账隔离；
- 额度： 每月 200 元，超支要提醒；
- 怎么算超支： 当月支出 > 200 就算超；
- 页面： 零花钱页，显示"本月支出 / 额度 / 超没超"。

产出：一句话需求 → 一份可执行的规格。

## 第二步：writing-plans——拆成能照做的活

按 `writing-plans` ，把规格拆成带验收的任务：

```
阶段 A：后端（可独立验证）
  A1 AllowanceService.check(spending, limit) → 超支返回提醒
      验收：AllowanceServiceTest 覆盖超支/够花
  A2 接口 GET /api/reports/allowance?month=
      验收：curl 返回 {spending, limit, message}
阶段 B：前端（可独立验证）
  B1 零花钱页渲染支出/额度/超支提醒
      验收：npm run dev 页面可见，切到乐乐账本生效
```

每步带验收——这是执行和审查的尺子。

## 第三步：executing-plans + TDD——后端先做对

照计划执行 A1。按 `test-driven-development` ， **先写失败的测试** ：

```
package com.laotan.ledger;

import static org.assertj.core.api.Assertions.assertThat;

import java.math.BigDecimal;
import java.util.Optional;

import org.junit.jupiter.api.Test;

class AllowanceServiceTest {

    private final AllowanceService service = new AllowanceService();

    /** 月支出超过零花钱额度，返回超支提醒。 */
    @Test
    void checkOverSpentReturnsWarning() {
        Optional<String> warning = service.check(
                spent("210.00"), limit("200.00"));

        assertThat(warning).contains("乐乐的零花钱本月已超支 10 元");
    }

    /** 月支出没超额度，不返回提醒（Optional.empty）。 */
    @Test
    void checkWithinLimitReturnsEmpty() {
        Optional<String> warning = service.check(
                spent("150.00"), limit("200.00"));

        assertThat(warning).isEmpty();
    }

    /** 没有设置额度，不提醒。 */
    @Test
    void checkWithoutLimitReturnsEmpty() {
        assertThat(service.check(spent("210.00"), null)).isEmpty();
    }

    /** 字符串转金额：测试数据用。 */
    private static BigDecimal spent(String s) { return new BigDecimal(s); }

    /** 字符串转额度：测试数据用。 */
    private static BigDecimal limit(String s) { return new BigDecimal(s); }
}
```

红了（类还不存在）→ 写最少实现让它绿：

```
package com.laotan.ledger;

import java.math.BigDecimal;
import java.util.Optional;

/** 零花钱预算检查：超支就提醒，够花就不吭声。 */
public class AllowanceService {

    /** 检查月支出是否超过零花钱预算，超支返回提醒。 */
    public Optional<String> check(BigDecimal monthlySpending, BigDecimal allowanceLimit) {
        if (allowanceLimit == null) {
            return Optional.empty();
        }
        if (monthlySpending.compareTo(allowanceLimit) > 0) {
            BigDecimal over = monthlySpending.subtract(allowanceLimit);
            return Optional.of("乐乐的零花钱本月已超支 " + over + " 元");
        }
        return Optional.empty();
    }
}
```

`mvn test` 全绿。注意比较用 `compareTo` 、减法全程 `BigDecimal` ——SKILL.md 里的红线，AI 照着守住了。

## 第四步：REST API——把零花钱暴露成接口

按 008 篇的基建套路，在 `ReportController` 加一个接口，复用 `LedgerAppService` （它负责聚合本月支出 + 调 `AllowanceService` 出提醒）：

```
/** 乐乐零花钱报表（默认看子账本 ownerId=2）。 */
@GetMapping("/allowance")
public AllowanceReport allowance(@RequestParam(required = false) String month,
                                 @RequestHeader(value = "X-User-Id", defaultValue = "2") Long ownerId) {
    return appService.allowanceReport(ownerId, parseMonth(month));
}
```

响应是一个小 record：

```
package com.laotan.ledger;

import java.math.BigDecimal;

/** 零花钱报表：某账本某月支出、额度与超支提醒。 */
public record AllowanceReport(BigDecimal spending, BigDecimal limit, String message) {
}
```

curl 一打，接口通了：

```
curl -H "X-User-Id: 2" "http://localhost:8080/api/reports/allowance?month=2026-08"
# {"spending":57.00,"limit":200.00,"message":null}
```

seed 数据里乐乐 8 月花了 57 块，没超 200， `message` 是 null——没超支，不提醒。

## 第五步：前端——零花钱页

按 009 篇的套路，写零花钱页。它要显示"本月支出 / 额度 / 超没超"，切到乐乐账本时生效：

```
<template>
  <div>
    <el-date-picker v-model="month" type="month" :clearable="false" />
    <el-tag>{{ ledger.label }}</el-tag>

    <el-alert v-if="report?.message" :title="report.message" type="warning" show-icon :closable="false" />
    <el-alert v-else title="零花钱没超支，乖～" type="success" show-icon :closable="false" />

    <el-descriptions :column="2" border style="margin-top: 16px">
      <el-descriptions-item label="本月支出">¥ {{ report?.spending }}</el-descriptions-item>
      <el-descriptions-item label="每月额度">¥ {{ report?.limit }}</el-descriptions-item>
    </el-descriptions>
  </div>
</template>
```

顶栏切到"乐乐零花钱"，页面就是乐乐的账；超支了，红色警告弹出来。

## 第六步：verification + 上线

按 `verification-before-completion` 收工：

1. 测试绿： `mvn test` / `npm test` 全绿；
2. 浏览器真走一遍：起后端起前端，切到乐乐账本，看到 57 块、200 额度、绿色"没超支"提示；把预算思路换一下——给乐乐记一笔 150 块的支出，刷新，红色"超支"警告出现；
3. 验收对照：乐乐开页面能自己看花了多少、超没超——达成。

然后"上线"：本地起后端、起前端，乐乐在浏览器里点开零花钱页，就能看自己的账了。

> 说句实在话：现在这个"上线"，数据落在本地 **H2 文件库** ——适合教学演示，重启数据还在。真要给一家子长期用，得换 MySQL 这类生产数据库、做备份、上部署。那个是另一套工程，不在这 24 篇范围里。 **但"从一句话到功能可用"的这条流水线，放哪都一样。**

## 注意事项

1. 组合实战不是"新技能"，是"旧技能串起来"。 每一步都靠前面的技能撑：不问清楚就写（brainstorming）、不带验收就拆（writing-plans）、不验证就收工（verification）——任何一步偷懒，流水线就断。
2. 前后端契约先行。 零花钱接口返回什么字段，先定死，前端才好写。改契约要两边同步。
3. 账本隔离别漏。 零花钱只看 ownerId=2，家庭账是 1。忘了过滤，乐乐就看到全家的钱了。
4. 上线≠部署。 本地跑起来能自己用，是"功能可用"；给很多人用，还要考虑数据库、备份、监控。分清楚。

## 本文小结

组合实战第一篇收官：乐乐零花钱，从一句话到全栈上线——brainstorm 问清楚 → writing-plans 拆任务 → TDD 做对 → API 暴露 → 前端可点 → 验证收工。 **这套技能不是一个个孤立的技巧，是一条能反复用的流水线。**

乐乐抱着手机蹲在沙发上，对着零花钱页念叨："57 块……还有 143 块能花。"她满意地点了点头。

老王看着这画面，忽然压低声音：「老谭，你乐呵啥。功能是上线了，可你想想—— **万一哪天线上账又对不上了呢？** 」

他这么一说，我脸上的笑僵住了。因为我知道，他说的是真事。

下一站：组合实战第二篇——线上「账对不上」的大排查。

22｜组合2：线上大排查，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #组合实战 #全栈

共渡时艰

邀请你前往腾讯公益一起捐

微心愿善暖万家

✨ 人工智能 · 目录