---
title: "07｜superpowers/testing-anti-patterns 测试反模式-这些坑我都踩过"
source: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484282&idx=1&sn=b3449c30a475f6c8a05939c19d572198&chksm=ffe5d357c8925a41e2ffdfcdadefcd913b1c028fd9900ba9b6575241d5ae35fc5dea4a375f65&cur_album_id=4599400782759952387&scene=189#wechat_redirect"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-09
description: "测试也不是越多越好——测实现细节、依赖时机、复制粘贴、只测快乐路径，都是反模式。用幸福账本的例子，把坑一个个指出来。"
tags:
  - "superpowers"
---
超级奶爸老谭！ 共渡时艰 *2026年9月1日 08:05*

## 前文回顾

上一篇 [06｜superpowers/condition-based-waiting 条件等待-别让测试睡大觉](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484279&idx=1&sn=979c264b40fd7566a6b7d4a79c71ea81&scene=21#wechat_redirect) 我们把异步对账的测试做对了：不睡大觉，等条件。Awaitility + 前端轮询，同一个思路。

老王翻着我们的测试目录，感慨：「测试是越写越多了……但有没有那种看着就难受的写法？我听说测试也有反面教材。」

「有，」我笑，「而且我每一条都踩过。」

## 场景：测试的敌人，是"看着像测试"

很多人以为测试越多越好。但有一种测试，多了反而有害——它 **看着像测试，其实在帮倒忙** ：测的是实现细节、依赖的是运行时机、改个小重构就碎一地。

`testing-anti-patterns` （测试反模式）就是 superpowers 测试类里专门"避坑"的一篇：把那些常见的坏测试写法列出来，告诉你为什么坏、怎么改。

今天我不讲理论，直接拿「幸福账本」里真实踩过的四个坑给你看。

## 坑一：测实现细节，不测行为

这是最经典的反模式。 **测试应该锁定"行为"（输入输出），而不是"内部实现"（调了什么方法、用了什么数据结构）。**

假设我们给预算服务写测试，有个人这么写：

```
/** 反例：断言调用了多少次 check——锁定实现细节，重构必碎。 */
@Test
void badTestAssertsImplementationDetail() {
    BudgetService service = spy(new BudgetService());
    service.check(spent("3100.00"), limit("3000.00"));

    verify(service, times(1)).check(any(), any()); // 这是测实现，不是测行为
}
```

这个测试在测" `check` 被调用了一次"——可这跟"超支判断对不对"有什么关系？哪天真把 `check` 拆成两个方法，这个测试就碎了，而预算功能其实没坏。

正确写法是 **只断言行为** ：

```
/** 正例：只断言行为——超支标记为 true。 */
@Test
void goodTestAssertsBehavior() {
    BudgetCheckResult result =
            new BudgetService().check(spent("3100.00"), limit("3000.00"));

    assertThat(result.exceeded()).isTrue();
}
```

规则： **测试要能"重构自由，行为不变"** 。锁实现细节的测试，是把重构的手脚捆住。

## 坑二：依赖"时机"，测试又脆又慢

第二个坑，跟上一篇讲的条件等待正好相反—— **用固定时间猜异步** 。

```
/** 反例：固定 sleep 猜异步，慢且脆。 */
@Test
void badTestSleepsForAsync() throws Exception {
    Reconciliation recon = new Reconciliation();
    new AsyncReconciler(recon).submit();
    Thread.sleep(1000); // 猜 1 秒够不够？猜错了就红
    assertThat(recon.summary()).isNotNull();
}
```

- 任务变慢 → 1 秒不够，红（其实只是慢）
- 任务变快 → 白等 999 毫秒，慢

正解就是上一篇的 `await().atMost(...).untilAsserted(...)` ——等条件，不猜时间。 **凡是测试里出现 `sleep` ，先问自己：我要等的到底是个"时间"还是个"结果"？**

## 坑三：复制粘贴，测试变"花架子"

第三个坑是我们团队真干过的事：功能 A 的测试写好了，功能 B 跟它有点像，于是 Ctrl+C、Ctrl+V，把断言里的数字改一改就交差。

```
/** 反例：复制粘贴的测试，名字都忘了改，断言没跟上业务。 */
@Test
void copyPastedBudgetTestActuallyChecksNothingNew() {
    BudgetCheckResult result =
            new BudgetService().check(spent("3100.00"), limit("3000.00"));
    // 断言的是和上面那个用例一模一样的东西
    assertThat(result.exceeded()).isTrue();
    assertThat(result.spent()).isEqualByComparingTo("3100.00");
}
```

它跟已有用例重复，什么都没多验证——唯一的贡献是让测试数量好看一点。 **测试的数量不是 KPI，覆盖的行为才是。**

复制粘贴测试还有更隐蔽的版本： **断言写错还不自知** 。比如该断言 `limit` 却断言了 `spent` ，两个都是数字，红不了，也测不到点子上。

规则： **每个测试用例必须覆盖一条"别人没测过的行为"** 。复制前先问：这个用例想守护什么新的东西？

## 坑四：只测快乐路径，不测边界

第四个坑最阴—— **只测"一切正常"的路径** ，把边界条件全漏了。

我们那个 `sumExpenses` ，最需要测的恰恰是空列表（返回 0 而不是 null）。要是只测"三笔加起来 302.50"这一个快乐路径，哪天有人把实现改成"空列表返回 null"，测试照样绿，但功能已经坏了。

```
/** 正例：边界条件——空列表返回 ZERO，而不是 null。 */
@Test
void sumExpensesOfEmptyListIsZero() {
    assertThat(new MonthlyReportService().sumExpenses(List.of()))
            .isEqualByComparingTo(BigDecimal.ZERO);
}
```

规则： **每个方法先想三件事——空输入、最大边界、非法输入** 。快乐路径让测试好看，边界路径让测试值钱。

## 前端也一样：一个反模式 + 一个正解

前端测试照样有反模式。最常见的，是测"组件里写了什么"而不是"用户做了什么"。

**反例** ——断言组件内部的响应式状态（实现细节，重构就碎）：

```
it('反例：断言组件内部数据，而不是用户可见行为', () => {
  const wrapper = mount(LedgerList)
  expect(wrapper.vm.list.length).toBeGreaterThan(0) // 这是实现细节
})
```

**正解** ——断言用户看到的东西（渲染出的表格行数）：

```
it('正解：断言渲染结果——账目表格有行', async () => {
  const wrapper = mount(LedgerList)
  await flushPromises()
  expect(wrapper.findAll('.el-table__row').length).toBeGreaterThan(0)
})
```

同一个原则： **测行为，不测实现。**

## 注意事项

1. 测试是给"行为"上保险，不是给"行数"凑数。 新增用例前，问一句"它守护了什么新行为"。
2. 测试要能容忍重构。 改了内部实现测试还绿，是好测试；动一动就红，是反模式。
3. 别把时间猜进测试。 出现 `sleep`
	、固定等待，换成条件等待。
4. 反模式不是"没用"，是"有害"。 坏测试会让你误以为有保障，其实它什么都没守住——比没有测试更危险。

## 本文小结

`testing-anti-patterns` 教我们识别测试里四个最常见的坑：测实现细节、依赖时机、复制粘贴、只测快乐路径。前端后端一个道理： **测行为，不测实现；测边界，不测花架子。**

测试网补全了。可艳姐那边有了新动静——她打开浏览器，想看账本，却发现……页面还是一片空白。后端一堆测试绿有什么用？ **她点的页面，我们还没做。**

好在，地基和测试网都就位了，接下来就动真格的：把藏在 Java 代码里的领域，变成艳姐网页能调用的接口。

下一站： `REST API` ——把账本从"代码库"变成"能访问的接口"。

08｜REST API，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #测试 #反模式

✨ 人工智能 · 目录