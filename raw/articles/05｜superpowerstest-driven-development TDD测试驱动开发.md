---
title: "05｜superpowers/test-driven-development TDD测试驱动开发"
source: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484246&idx=1&sn=96a5bb63e9c29b9d2451d66d9e3c6365&chksm=ffe5d37bc8925a6dee5b8adaf423f6a6e9d4964586e95f2423c9e8f5e5eae8cb178464107732&cur_album_id=4599400782759952387&scene=189#wechat_redirect"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-09
description: "写代码不保证写对，先写测试才保证写对。TDD 红绿循环，后端 JUnit 演示 + 前端 Vitest 演示，同一个技能两条战线。"
tags:
  - "superpowers"
---
超级奶爸老谭！ 共渡时艰 *2026年8月28日 08:05*

## 前文回顾

上一篇 [04｜superpowers/executing-plans 让 AI 按计划执行](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484243&idx=1&sn=6567895526e2d02cd0fce73a8b478cca&scene=21#wechat_redirect) 我们用“分批 + 确认”把计划 A 阶段执行完了，预算服务、金额不变式全绿。领域地基打好了。

老王看着测试输出，突然冒出一句：「等等，你们是先写代码，后补测试的对吧？」

我笑了：「那你就还停在 2023 年。」

## 场景：先写测试，还是先写代码？

艳姐听我跟老王聊测试，插了一嘴：「测试是干嘛的？跟对账一样吗？」

「对，就是给代码对账。」我说，「而且我们反过来—— **先写账，再花钱** 。」

她愣了一下：「啥意思？」

我打个比方：写一个“月度汇总”功能，把一个月三笔饭钱加起来。正常人的顺序是——先写代码，再写测试验证它。

![05-1](https://mmbiz.qpic.cn/sz_mmbiz_jpg/d9Wx6XKcQmQSSgHrkURwicgwAeJDNAFPgSqnBnf6lbn1IlSoaMibup3AGK7KvNu7kOibwmIBItG0eqmmz9V0wciaQl6LrMKS1eB2opZ5HnjyiaC4/640?from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

但这里有个陷阱： **代码是你写的，测试也是你写的，你很容易写出“自己哄自己”的测试** 。先写测试，等于先把“账”记下（要什么结果），再去写代码把账平上（让它真跑出这个结果）。账不对，代码就写不出来。

这就是 `test-driven-development` （TDD）的核心： **红 → 绿 → 重构** 。

## 技能是什么：TDD 是“红绿重构”三拍子

`test-driven-development` 的节奏只有三步，循环往复：

1. 红（Red）： 先写一个 **会失败** 的测试，描述你要的行为；
2. 绿（Green）： 写 **最少** 的代码，让测试通过；
3. 重构（Refactor）： 在测试保护下，把代码改干净。

要点在于“先红后绿”： **先看到它失败，再让它通过** 。这一步看着简单，却是“验证代码真的管用”和“你以为它管用”的分水岭。

在 Claude Code 里，这个技能通常在你说“开始实现功能”时自动激活。它不会一股脑写完整个功能，而是 **一个测试一个测试地逼你前进** 。

## 怎么用：后端先来一遍红绿

我们拿「幸福账本」的月度汇总功能练手。它要能把一个月里若干笔支出加总——就一个方法 `sumExpenses(List<BigDecimal>)` 。

### 第一步：红——先写一个失败的测试

我让 AI 先别写实现，先写测试。第一个用例： **空列表求和，应该是 0，不是 null** 。

```
package com.laotan.ledger;

import static org.assertj.core.api.Assertions.assertThat;

import java.math.BigDecimal;
import java.util.List;

import org.junit.jupiter.api.Test;

class MonthlyReportServiceTest {

    /** 空列表求和：结果应为 0（BigDecimal.ZERO），而不是 null。 */
    @Test
    void sumExpensesOfEmptyListIsZero() {
        assertThat(service.sumExpenses(List.of()))
                .isEqualByComparingTo(BigDecimal.ZERO);
    }
}
```

`MonthlyReportService` 还不存在， `service` 字段都编译不过。跑 `mvn test` ：

```
[ERROR] 找不到符号：类 MonthlyReportService
```

**红了。这个红，是有意义的红** ——它证明“测试确实在要求一个还不存在的东西”。

### 第二步：绿——写最少代码让它过

先建一个最朴素的 `MonthlyReportService` ：

```
package com.laotan.ledger;

import java.math.BigDecimal;
import java.util.List;

/** 月度汇总：金额从源头到终点全程 BigDecimal，不留 double 接缝。 */
public class MonthlyReportService {

    /** 将若干笔支出金额相加求和（全程 BigDecimal，不转 double）。 */
    public BigDecimal sumExpenses(List<BigDecimal> amounts) {
        BigDecimal sum = BigDecimal.ZERO;
        for (BigDecimal amount : amounts) {
            sum = sum.add(amount);
        }
        return sum;
    }
}
```

再跑 `mvn test` ：

```
[INFO] Tests run: 1, Failures: 0, Errors: 0
[INFO] BUILD SUCCESS
```

**绿了。** 注意我们只写了“让这一个测试过”的最少代码，没画蛇添足。

### 第三步：循环——再加一个用例

红绿循环的价值在于 **每多一个用例，就多钉死一条行为** 。第二拍：三笔支出 15.00 + 88.50 + 199.00 = 302.50：

```
/** 三笔支出求和：15.00 + 88.50 + 199.00 = 302.50。 */
@Test
void sumExpensesOfThreeIsCorrect() {
    List<BigDecimal> amounts = List.of(
            new BigDecimal("15.00"),   // 一杯奶茶
            new BigDecimal("88.50"),   // 一顿饭
            new BigDecimal("199.00")); // 乐乐的玩具

    assertThat(service.sumExpenses(amounts))
            .isEqualByComparingTo("302.50");
}
```

这个用例第一次跑是绿的（实现已经能加对了）。它看起来“白写”，但它的价值是 **守护** ：以后谁把实现改坏了，它立刻红给你看。

### 第四步：把 double 的坑，也变成红

再来一个更狠的。我们要证明" `double` 算钱是会错的“——这是后面”账对不上"bug 的根源。用测试把它钉死：

```
/** 哨兵测试：double 累加必然产生误差，防止有人把金额改回 double。 */
@Test
void doubleSummationHasError() {
    double sum = 0.0;
    for (int i = 0; i < 10; i++) {
        sum += 0.1;
    }
    assertThat(sum).isNotEqualByComparingTo(1.0);
}
```

这个测试 **永远红不了，因为它断言的就是“double 确实不对”** 。它是一道防线：谁要是把金额类型改成 `double` ，这个测试会立刻提醒你——你正在走进那个坑。

![05-2](https://mmbiz.qpic.cn/mmbiz_jpg/d9Wx6XKcQmSWVbyEoj2O0ZcQ9X6hKaCiaiahgWtW4UgHddgYKC8TDvZws0t9qQ7WYGia0XSn3oRtw8ORZ2hCrdwjCuWzFHslDibPDaTIypEjMN8/640?from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

## 怎么用：前端也一样，Vitest 来一遍

TDD 不是后端专利。咱们前端 `happy-ledger-web` 里那个“账本切换”（ `useLedger` ），也用同一个技能写。前端测试框架用 **Vitest** ，语法跟 JUnit 神似。

先写失败的测试——“切到乐乐后 ownerId 应该变成 2”：

```
import { beforeEach, describe, expect, it } from 'vitest'
import { LEDGER_FAMILY, LEDGER_KID, useLedger } from './useLedger'

describe('useLedger 账本切换', () => {
  beforeEach(() => {
    useLedger().switchTo(LEDGER_FAMILY)
  })

  it('切到乐乐零花钱后 ownerId = 2', () => {
    const { ownerId, switchTo } = useLedger()
    switchTo(LEDGER_KID)
    expect(ownerId.value).toBe(2)
  })
})
```

`useLedger` 还没写 `switchTo` ，跑 `npm test` 会红。然后写实现：

```
import { computed, ref } from 'vue'

export const LEDGER_FAMILY = { id: 1, label: '家庭账本' }
export const LEDGER_KID = { id: 2, label: '乐乐零花钱' }

const current = ref(LEDGER_FAMILY)

export function useLedger() {
  const ownerId = computed(() => current.value.id)

  function switchTo(ledger) {
    current.value = ledger
  }

  return { ownerId, switchTo }
}
```

再跑 `npm test` ，绿。 **同一个红绿循环，后端前端通吃** ——这就是技能的力量，它不绑定语言。

## 注意事项

1. “先红”必须有意义。 写个编译不过的测试，和写个“断言实现里写死值”的测试，红法不一样。红，要红在“行为还没实现”上。
2. 绿的判据是测试，不是“看起来对”。 实现写完，跑测试看输出。看不到绿，别宣布完成。
3. 金额断言必须 `isEqualByComparingTo` 。 `BigDecimal("88.50")`
	和 `"88.5"` scale 不同， `isEqualTo` 会误判。全系列统一。
4. TDD 的产出是一套测试网。 每多一个用例，就多一条防护。以后重构、改 bug，都靠这张网兜底。

## 本文小结

TDD 用“红绿重构”把“写对”变成“可证明”：先看它失败，再看它通过。后端 JUnit、前端 Vitest，同一个三拍子。

领域服务的测试网开始成型。但老王发现了一个新问题：「你们那个对账不是异步的嘛？测试的时候，它还没跑完怎么办？总不能 `Thread.sleep(1000)` 干等吧？」

问到点子上了。异步等待怎么测，是下一篇的事—— **别让测试睡大觉** 。

下一节我们不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #TDD #测试驱动开发

✨ 人工智能 · 目录