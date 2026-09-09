---
title: "04｜superpowers/executing-plans 让 AI 按计划执行"
source: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484243&idx=1&sn=6567895526e2d02cd0fce73a8b478cca&chksm=ffe5d37ec8925a68cacabf911e24446b24bbae8ed2f6c5cca725e1fc947c84ecdfb94af82459&cur_album_id=4599400782759952387&scene=189#wechat_redirect"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-09
description: "计划写得再漂亮，执行时 AI 也会跑偏。用 executing-plans 照计划分批执行、每步人机确认，写错就回滚重来。"
tags:
  - "superpowers"
---
超级奶爸老谭！ 共渡时艰 *2026年8月27日 08:05*

## 前文回顾

上一篇 [03｜superpowers/writing-plans 把想法拆成步骤](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484240&idx=1&sn=cc5212d2a38e84ab9dfa6ed9e1478024&scene=21#wechat_redirect) 我们把「幸福账本」的规格拆成了一份能照着干的计划：阶段 A（领域对象、预算服务、金额不变式）带验收标准，阶段 B/C 留到后面。

老王的问题来了： **“计划是写清楚了，可真让它干的时候，它会不会又放飞？”**

## 场景：计划很丰满，AI 很骨感

第一天早上，我把计划文档摆到 Claude Code 面前，跟它说：「按这个计划，开始执行 A 阶段。」

结果你猜怎么着？它上来就自己加戏。

「我先帮你把项目结构重新组织一下，加一个 `core` 包……」它在计划文档外面自由发挥。

我当场叫停：「打住。计划里 A1 是领域对象，没有 ‘重构项目结构’ 这一步。按计划来。」

![04-1](https://mmbiz.qpic.cn/mmbiz_jpg/d9Wx6XKcQmQzOtcjOmQVtEWibMsuSdej6aO6icibepImibHM0cIAdV7YjDAmppLGAb7P4n52FuEWP9F6tERFB9N219dpvgBGY2nJIV0iaP3XXmr0/640?from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

这就是没有 `executing-plans` 时的日常： **AI 一拿到大任务，就容易“规划之外的发挥”** 。它不是你坏，是它缺一个“照着计划走、走完一步跟你确认”的约束。

`executing-plans` 就是干这个的。它是三张入场券里的最后一张：

- brainstorming： 问清楚（What）
- writing-plans： 写计划（How）
- executing-plans： **照着计划走，一步一确认（Do）**

## 技能是什么：executing-plans 是“计划执行器”

`executing-plans` 的核心是 **分批 + 确认** ：

1. 一次只执行计划里的一个步骤，不一口气吞掉整个计划；
2. 每步做完，停下来给你看结果，等你确认；
3. 确认才进下一步；有偏差就修正或回滚，绝不带着错往下走；
4. 它是 **有状态** 的——它知道现在走到计划第几步、下一步是什么。

用大白话说： **它把 AI 从一个“一上来就狂写”的选手，变成了一个“走一步、报一步、听指挥”的选手。**

在 Claude Code 里敲：

```
/execute-plan
```

它会读计划文档，然后从第一步开始执行。

## 怎么用：执行 A2 预算服务这一小步

我以计划里的 A2「预算服务」为例，带你看看“一步一确认”到底怎么走。

![04-2](https://mmbiz.qpic.cn/mmbiz_jpg/d9Wx6XKcQmRoialmeGsmmvGibmR2ibew1FibWL5POB77OzB5EwOgl8icia4icicUGBW2up18lGInHV4fgrEjicbVAKCIX6GMfBLLzu7VTDlvp6z4ov70/640?from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

### 第一步：AI 照计划写出代码

计划说得很清楚：写 `BudgetService.check(spent, limit)` ，返回 `BudgetCheckResult` 。AI 交出来的实现是这样的：

```
package com.laotan.ledger;

import java.math.BigDecimal;

/** 预算检查：某分类累计支出是否超过预算。 */
public class BudgetService {

    /** 检查某分类累计支出是否超过预算上限。 */
    public BudgetCheckResult check(BigDecimal spent, BigDecimal limit) {
        if (limit == null) {
            return new BudgetCheckResult(false, nullToZero(spent), null);
        }
        boolean exceeded = spent != null && spent.compareTo(limit) > 0;
        return new BudgetCheckResult(exceeded, nullToZero(spent), limit);
    }

    /** null 金额按 0 处理。 */
    private static BigDecimal nullToZero(BigDecimal v) {
        return v == null ? BigDecimal.ZERO : v;
    }
}
```

我盯着看了一遍，注意到两个细节：金额比较用的是 `compareTo` （不是 `equals` ）， `limit == null` 时按“不超支”处理——这两条都跟计划里的约定对上了。

### 第二步：AI 跑验收测试，给你看证据

计划里 A2 的验收标准是“BudgetServiceTest 覆盖超支/不超支/空限额”。AI 没嘴上说“写好了”，而是真的把测试写出来、跑给你看：

```
package com.laotan.ledger;

import static org.assertj.core.api.Assertions.assertThat;

import java.math.BigDecimal;

import org.junit.jupiter.api.Test;

class BudgetServiceTest {

    private final BudgetService service = new BudgetService();

    /** 支出超过预算上限，应标记为超支，且支出金额原样返回。 */
    @Test
    void checkSpentExceedsLimitMarksExceeded() {
        BudgetCheckResult result =
                service.check(spent("3100.00"), limit("3000.00"));

        assertThat(result.exceeded()).isTrue();
        assertThat(result.spent()).isEqualByComparingTo("3100.00");
    }

    /** 支出刚好等于预算上限，不应视为超支。 */
    @Test
    void checkSpentEqualsLimitIsNotExceeded() {
        BudgetCheckResult result =
                service.check(spent("3000.00"), limit("3000.00"));

        assertThat(result.exceeded()).isFalse();
    }

    /** 没有预算记录（limit 为 null），不视为超支。 */
    @Test
    void checkWithoutLimitIsNotExceeded() {
        BudgetCheckResult result = service.check(spent("3100.00"), null);

        assertThat(result.exceeded()).isFalse();
    }

    /** 字符串转金额：测试数据用。 */
    private static BigDecimal spent(String s) { return new BigDecimal(s); }

    /** 字符串转预算上限：测试数据用。 */
    private static BigDecimal limit(String s) { return new BigDecimal(s); }
}
```

跑一下 `mvn test` ：

```
[INFO] Tests run: 3, Failures: 0, Errors: 0, Skipped: 0
[INFO] BUILD SUCCESS
```

绿灯。注意那个 `isEqualByComparingTo` ——金额断言必须用它，不能 `isEqualTo` ，这是全系列的规矩。

### 第三步：停一停，等你确认

A2 做完，AI 停下来，给你一个明确的“下一步确认”：

> A2 预算服务已完成，测试全绿。是否继续 A3 金额不变式？回复确认继续，或指出需要修改的地方。

这就是 `executing-plans` 和普通“直接写”的最大区别： **它不擅自往前冲** 。每步都像一个检查点，你点头它才走。

### 第四步：写错了怎么办？回滚，重来

有一次 AI 在 A3 阶段自作主张，把 `MoneyRules` 的 `check` 从"返回 `Optional` “改成了”抛异常“——它以为那样更”规范"。这违背了计划（计划里写的是 `Optional<String>` ）。

我直接说：「A3 改错了， `check` 必须返回 `Optional<String>` ，不能抛异常。回退这个改动。」

AI 立刻回滚，重做，再把测试跑绿给你看。 **这就是回滚点存在的意义** ——一步的偏差，只影响一步，不会滚雪球。

## 注意事项

1. 确认是双向的。 你确认它才前进；你发现问题，它可以回滚重来。别让“确认”变成“走过场”。
2. 别让它跳步。 计划里 A1 没完，不许它先干 A3。依赖顺序就是铁律。
3. 验收要看证据，不是听汇报。 它说“测过了”，不如让它把 `mvn test`
	的输出贴出来。看不到绿的确认，等于没确认。
4. 一步的偏差就地解决。 发现偏了，回滚这一步重做，不要带着偏差往下走——带着错往下走，是 bug 雪球的开端。

## 本文小结

`executing-plans` 用“分批 + 确认”把 AI 的执行框进了计划里：一次一步、步停确认、错了回滚。三张入场券到此集齐——问清楚、写计划、照计划干。

阶段 A 的三步全绿，领域的地基打好了。可老王又盯着屏幕问：「地基是有了，可艳姐要的是网页啊？后端的东西她看得见吗？」

对，账本还藏在 Java 代码里，没变成艳姐能点的页面。不过在那之前，我们要先把“怎么保证代码写对”这件事做扎实——因为后面每一层，都站在今天的测试地基上。

下一站，是 superpowers 测试类技能里最有名的一张： **先写测试，再写代码** 。

下一课，我们不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #sdd #vibecoding

✨ 人工智能 · 目录