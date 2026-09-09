---
title: "06｜superpowers/condition-based-waiting 条件等待-别让测试睡大觉"
source: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484279&idx=1&sn=979c264b40fd7566a6b7d4a79c71ea81&chksm=ffe5d35ac8925a4c8525963d61389ba3d1231e0bd403e853e4f2faf56215d42a3f53e756eb53&cur_album_id=4599400782759952387&scene=189#wechat_redirect"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-09
description: "异步任务完成时间不定，固定 sleep 一是不稳二是浪费。用条件轮询等「条件成立」，后端 Awaitility、前端轮询接口，同一个思路。"
tags:
  - "superpowers"
---
超级奶爸老谭！ 共渡时艰 *2026年8月31日 08:05*

## 前文回顾

上一篇 [05｜superpowers/test-driven-development TDD测试驱动开发](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484246&idx=1&sn=96a5bb63e9c29b9d2451d66d9e3c6365&scene=21#wechat_redirect) 我们用红绿循环把月度汇总写对了，后端 JUnit、前端 Vitest 同一个三拍子。领域服务的测试网开始成型。

老王盯着"月度汇总"四个字，提出了新问题： **“你们那个对账不是异步的吗？测试的时候它还没跑完，你总不能 `Thread.sleep(1000)` 干等吧？”**

## 场景：异步任务，等还是不等？

「幸福账本」有个对账功能：艳姐月底点一下，系统在 **后台线程** 汇总当月账目，汇总完写结果。这个"后台跑一会儿"的异步动作，怎么测试？

新手最常见的写法是：睡一觉。

```
reconciler.submit();
Thread.sleep(1000);   // 等一秒再说
assertThat(recon.summary()).isNotNull();
```

这个写法有两个毛病：

1. 慢： 本来 200 毫秒能好，你硬等 1 秒；
2. 脆： 哪天任务变慢超过 1 秒，测试就红；任务变快，你又在白等。

问题本质是： **你在等"时间"，而不是等"结果"。** 我们想要的明明是"等 `summary` 从 null 变成有值"，你却拿"等 1 秒"去猜。

`condition-based-waiting` （条件等待）就是把"等时间"换成"等条件"： **轮询地检查目标条件，成立就停，超时才放弃。**

## 技能是什么：等条件，不猜时间

`condition-based-waiting` 的核心就一句话：

> **用"条件是否成立"判断异步任务完成，而不是用"过了多久"来猜。**

它通常配合一个 **轮询 + 超时** 的机制：

- 每隔一小会儿，检查一次目标条件；
- 条件成立，立刻继续（不浪费哪怕 1 毫秒）；
- 设一个超时上限，条件一直不成立就报错（不无限等）。

在 Java 里，superpowers 生态最常用的轮询库是 **Awaitility** ；前端浏览器里，就是 **轮询接口** 。同一个思路，两条战线。

## 怎么用：后端用 Awaitility 等"条件"

先看"对账"这个异步任务的三个类。对账对象 `Reconciliation` 装结果，初始是 null：

```
package com.laotan.ledger;

/** 对账对象：异步任务完成后写入汇总结果，summary() 由 null 变为非 null。 */
public class Reconciliation {

    private volatile Summary summary;

    /** 读取当前对账结果，任务未完成时为 null。 */
    public Summary summary() {
        return summary;
    }

    /** 写入对账完成结果。 */
    public void complete(Summary summary) {
        this.summary = summary;
    }
}
```

异步执行器 `AsyncReconciler` ，提交后在后台线程模拟汇总（这里用 200ms 模拟真实耗时）：

```
package com.laotan.ledger;

import java.math.BigDecimal;
import java.util.concurrent.CompletableFuture;

/** 异步对账：入队后在后台线程汇总今日账本。 */
public class AsyncReconciler {

    /** 模拟后台汇总耗时（毫秒）。 */
    private static final long SIMULATED_WORK_MILLIS = 200L;

    /** 模拟汇总出的今日支出合计。 */
    private static final BigDecimal SIMULATED_TOTAL = new BigDecimal("302.50");

    private final Reconciliation recon;

    public AsyncReconciler(Reconciliation recon) {
        this.recon = recon;
    }

    /** 提交异步对账任务：后台线程汇总，完成后写入 recon。 */
    public void submit() {
        CompletableFuture.runAsync(() -> {
            try {
                Thread.sleep(SIMULATED_WORK_MILLIS);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
            recon.complete(new Summary(SIMULATED_TOTAL));
        });
    }
}
```

测试怎么写？ **不睡 1 秒，用 Awaitility 等"summary 非 null"这个条件** ：

```
package com.laotan.ledger;

import static org.assertj.core.api.Assertions.assertThat;
import static org.awaitility.Awaitility.await;

import java.time.Duration;

import org.junit.jupiter.api.Test;

class ReconciliationAsyncTest {

    /** 异步任务完成时机不定，超时上限设为 5 秒。 */
    private static final Duration AWAIT_TIMEOUT = Duration.ofSeconds(5);

    /** 异步对账：submit 后不睡大觉，轮询「条件」（summary 非 null）等任务完成。 */
    @Test
    void asyncReconcilerSubmitEventuallySummarizes() {
        Reconciliation recon = new Reconciliation();
        AsyncReconciler reconciler = new AsyncReconciler(recon);

        reconciler.submit(); // 异步任务入队

        // 等"条件"成立，而不是睡大觉
        await().atMost(AWAIT_TIMEOUT)
                .untilAsserted(() -> assertThat(recon.summary()).isNotNull());

        assertThat(recon.summary().expenseTotal()).isEqualByComparingTo("302.50");
    }
}
```

`await().atMost(5 秒).untilAsserted(...)` ：每过一小会儿就断言一次"summary 非 null"，成了就过，5 秒还不行就报错。 **它等的是条件，不是时间。**

跑一下：

```
[INFO] Tests run: 1, Failures: 0, Errors: 0
```

顺便说， `pom.xml` 里要加这个依赖（这是 Awaitility 的唯一门槛）：

```
<dependency>
    <groupId>org.awaitility</groupId>
    <artifactId>awaitility</artifactId>
    <version>4.2.2</version>
    <scope>test</scope>
</dependency>
```

## 怎么用：前端轮询"对账结果"接口

同样的思路，在网页上就是 **轮询接口** 。我们的后端已经有一个对账接口： `POST /api/reconciliations` 触发对账， `GET /api/reconciliations/latest` 返回状态（ `RUNNING` 或 `DONE` ）。

前端点"对账"后，用定时器隔一会儿查一次结果，直到 `status` 变成 `DONE` ：

```
async function reconcile() {
  await reconciliations.trigger()          // 触发异步对账
  const timer = setInterval(async () => {
    const result = await reconciliations.latest()   // 轮询结果
    if (result.status === 'DONE') {
      clearInterval(timer)                 // 条件成立，停
      ElMessage.success('对账完成，支出合计 ' + result.expenseTotal)
    }
  }, 300)                                 // 每 300ms 查一次
}
```

对比一下"睡大觉版"的前端： `setTimeout(() => 拿结果, 3000)` ——你又在猜 3 秒够不够。轮询版等的是" `DONE` 这个条件"，快慢自适应，永远不多等、不少等。

**后端测试等条件、前端页面轮询接口，本质上都是 condition-based-waiting。**

## 注意事项

1. 超时上限必须有。 条件一直不成立，没有超时就是无限等。 `atMost(...)`
	是保命绳。
2. 别用固定 sleep 顶替条件。 时间猜不中，测试就会在"变慢"的那天红给你看。
3. 轮询间隔别太短。 前端 300ms 一次够了，别把后端打爆；Awaitility 默认间隔也够用。
4. 异步任务里被中断要恢复中断位。 你看 `AsyncReconciler`
	里 `catch (InterruptedException e) { Thread.currentThread().interrupt(); }` ——这不是废话，是线程协作的礼貌。

## 本文小结

`condition-based-waiting` 把"等多久"换成"等什么"：Awaitility 等条件、前端轮询接口，同一种不猜时间的哲学。

后端测试网已经能接住异步了。可老王翻着测试文件，叹了口气：「测试是越写越多了，但有没有那种……一看就反胃的写法？我听说测试也有反面教材？」

有，而且我踩过不少。下一篇，专门聊 **测试反模式** ——这些坑我全都替你踩过。

07｜testing-anti-patterns，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #测试 #异步

✨ 人工智能 · 目录