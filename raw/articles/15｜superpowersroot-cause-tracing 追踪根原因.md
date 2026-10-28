---
title: "15｜superpowers/root-cause-tracing 追踪根原因"
source: "https://mp.weixin.qq.com/s/0yweHvFrNrImJPpnJhlt8A"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-11
description: "月底对账差几分钱，表象在账目，根因却藏在两行 double。顺着症状一路往上追，用证据锁定根子，再用防回归测试钉死。"
tags:
---
超级奶爸老谭！ 共渡时艰 *2026年9月11日 08:05*

## 前文回顾

上一篇 [14｜superpowers/systematic-debugging 系统化调试](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484333&idx=1&sn=f9aebd906a3c2067ccd4fc35d029aa84&scene=21#wechat_redirect) 用四阶段探案，查清了报表混入子账本账目的 bug。系统化排查教的是"流程"，老王却问了句更深的： **“你最后怎么从一堆可能性里，精准锁定根因的？”**

## 场景：月底对账，又差了 0.01

故事回到一个月底。

艳姐在报表页对账，忽然喊我：「老谭，这个月支出是 5793.4，可我卡里流水加出来是 5793.41。 **差一分钱。** 」

一分钱。看着微不足道，但记账系统差一分钱，跟差一万块是同一个 bug—— **账不平，就是系统在说谎。**

`systematic-debugging` 告诉我先复现、再假设。可这次的问题不是"哪个功能坏了"，而是"数字为什么差一点"。这时候需要的不是四阶段排查，而是 `root-cause-tracing` （根因追踪）： **从表象出发，顺着代码的因果链一路往上追，直到追到那个真正的根子。**

## 技能是什么：根因追踪是"顺着症状往上走"

`root-cause-tracing` 和 `systematic-debugging` 的分工：

- systematic-debugging： 给排查搭 **流程** （复现 → 假设 → 验证 → 根因）；
- root-cause-tracing： 教你怎么 **从症状出发，沿因果链定位根因** ——不是"猜哪可能错"，而是"顺着这一条链，追到源头"。

它最反直觉的一点是： **表象往往离根因很远。** 表面上是"某笔账金额不对"，实际上可能是一个几层之外的求和用了 `double` 。症状只是根因在水面的影子，你要潜下去找本体。

## 怎么用：追一分钱的账

### 第一步：从症状出发，先问"差在哪一笔"

一分钱的差，先定位到"哪笔账开始对不上"。艳姐把 8 月的账逐笔核，发现 8 月 7 日的"周末下馆子"88.50，跟小票上的 88.50 一样——但 **报表里这个分类的月度合计，跟逐笔相加差了 0.01** 。

症状锁定： **"吃饭"分类 8 月合计，与逐笔相加不符，差 0.01。**

### 第二步：沿因果链往上追

"吃饭分类的合计"是谁算的？顺着调用往上走：

```
报表页显示 → 前端调 GET /api/reports/monthly
           → 后端 LedgerAppService.listMonthlyTotals 聚合
           → 逐笔 expense 累加（BigDecimal.add）
```

后端聚合用 `BigDecimal.add` ，这条路看着没毛病。再往上追一层—— **累加的数据从哪来？** 追到 `EntryRepository.expenseAmounts` ：

```
/** 反模式现场：为了"省事"，累加前把 BigDecimal 转成了 double。 */
public double sumExpensesAsDouble(List<Entry> entries) {
    double sum = 0.0;
    for (Entry entry : entries) {
        sum += entry.amount().doubleValue();   // ← 根因就在这一行
    }
    return sum;
}
```

找到了。 **根因： `doubleValue()` 把 `BigDecimal` 转成了 `double` 再累加。**

`double` 用二进制表示小数，像 `0.1` 这种在二进制里是无限循环小数，存下来就是近似值。几十笔近似值累加，误差攒到第 N 位，最后一位就对不上了——差的那 0.01，就是这么"攒"出来的。

### 第三步：修根子，不修表象

修"表象"是去把 88.50 改个值——那是在用谎言盖谎言。修 **根子** 是把求和从 `double` 换回 `BigDecimal` ：

```
/** 修复：全程 BigDecimal，不留 double 接缝。 */
public BigDecimal sumExpenses(List<BigDecimal> amounts) {
    BigDecimal sum = BigDecimal.ZERO;
    for (BigDecimal amount : amounts) {
        sum = sum.add(amount);
    }
    return sum;
}
```

同时，把"钱不许经过 double"这条规矩，固化成代码。 `MoneyRules` 负责守住金额不变式：

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

### 第四步：防回归——让"double 就是不对"成为红线

修完根子，还得防它再回来。 `root-cause-tracing` 的收尾要求： **把根因写成测试，让 bug 想回来都回不来。**

写一个"哨兵测试"——它不测功能，专测"double 算钱必然错"这条铁律：

```
/** 哨兵测试：double 累加必然产生误差，防止有人把金额改回 double。 */
@Test
void doubleSummationHasError() {
    double sum = 0.0;
    for (int i = 0; i < 10; i++) {
        sum += 0.1;
    }
    assertThat(sum).isNotEqualByComparingTo(1.0); // 0.1+0.1+... 十个 0.1 ≠ 1.0
}
```

这个测试 **永远绿** ——因为 `0.1` 累加 10 次在 `double` 里真的不等于 `1.0` 。它的价值是红线：将来谁手一抖把金额改回 `double` ，这个测试会立刻提醒你"你正走进那个坑"。

再配一个正面的防回归——BigDecimal 累加是精确的：

```
/** 正例：BigDecimal 累加结果精确。 */
@Test
void bigDecimalSummationIsExact() {
    BigDecimal sum = BigDecimal.ZERO;
    for (int i = 0; i < 10; i++) {
        sum = sum.add(new BigDecimal("0.1"));
    }
    assertThat(sum).isEqualByComparingTo("1.00");
}
```

一正一反两个测试，把"钱必须走 BigDecimal"钉死成代码层的红线。

## 为什么是"根因"而不是"又一个 bug"

追完你会看到：差的那 0.01，表象在"某分类合计不对"，根因在"两行 `doubleValue()` "。 **如果你只修表象** ——把这笔账改个值、或者把 88.50 改成 88.49——账暂时平了，但下一笔、下个月，误差还会在别处冒出来。 **修根因，是一次修好一整类问题。**

`root-cause-tracing` 判断你是不是追到根因，有个标准： **这个根因被修掉后，能不能解释"之前所有相关症状"？** 能，才是根子；只能解释一个症状，那只是又修了一个表象。

## 注意事项

1. 症状往上追，每一层都要有证据。 “哪笔账不对 → 谁算的 → 数据哪来的 → 哪里转成 double”，每跳一层都靠证据支撑，不靠猜。
2. 修根因，不修表象。 改个值把账平了，是拆东墙补西墙。根因是一次修好一类问题的钥匙。
3. 用"能否解释所有症状"检验根因。 只能解释一个症状的，不是根因。
4. 根因要固化成防线。 哨兵测试 + 金额不变式，让 bug 想回来都回不来。不留防线的修复，等于没修。

## 本文小结

`root-cause-tracing` 教我们顺着因果链从表象追到根子：一分钱的账，追到底是一行 `doubleValue()` 。修根子、固防线，一次修好一类问题。

那晚我把哨兵测试写完，艳姐凑过来看：「这测试名字写的什么？‘double 累加必然产生误差’？」

「意思是，」我说，「咱家的账，以后不许再走 double 那条歪路了。」

「好，」她难得地点点头，「钱的事，就该一是一二是二。」

老王在旁边记了半页笔记，忽然抬头：「你前面说了这么多’防线’——一道校验、一道测试……要是哪天 AI 写代码，绕过了你的第一道防线呢？」

「那就得有第二道、第三道。」我说，「今天这个，值得单独开一篇讲。」

下一站： `verification-before-completion` ——修好了，验证过，才算完。

16｜verification-before-completion，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #调试 #根因

共渡时艰

邀请你前往腾讯公益一起捐

陪乡村孩子阅读世界

✨ 人工智能 · 目录