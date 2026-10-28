---
title: "14｜superpowers/systematic-debugging 系统化调试"
source: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484333&idx=1&sn=f9aebd906a3c2067ccd4fc35d029aa84&chksm=ffe5d380c8925a96b311dce83669ba74ccfa4a6fd349b87fe6330eb685da1c85516a192f2633&cur_album_id=4599400782759952387&scene=189#wechat_redirect"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-10
description: "报表数字对不上，别急着改代码。四阶段排查：复现 → 假设 → 验证 → 根因。用家庭报表混入子账本账目的 bug，走一遍跨前后端排查。"
tags:
---
超级奶爸老谭！ 共渡时艰 *2026年9月10日 08:05*

## 前文回顾

上一篇 [13｜superpowers/subagent-driven-development 子代理开发 - 请个 AI 当项目经理](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484330&idx=1&sn=5a62f33abf0ad9efd1c07ef280fabec8&scene=21#wechat_redirect) 让 AI 项目经理把"超支提醒"全栈跑通。流水线转得顺，老王却问了个让我心里一紧的问题： **“万一转着转着，账对不上了怎么办？”**

他这一问，让我想起前两天的深夜——报表的数字，真对不上过一次。

## 场景：报表支出，比账目列表多了 57 块

那是"超支提醒"上线第二天。晚上十点，艳姐把手机怼到我面前：

「老谭，你自己看看。报表页写的这个月家庭支出是 3791 块，可账目列表我逐笔加，明明只有 3734 块。 **差了 57，你账怎么平的？** 」

我一看，还真是。可后端测试全绿，账目列表也对……到底哪一环出了问题？

我第一反应是扑上去改代码。手都摸到键盘了，忽然想起 `systematic-debugging` 技能的第一条铁律：

> **先别急着改。先搞清楚"到底哪里不对、为什么不对"。**

## 技能是什么：调试的四个阶段

`systematic-debugging` （系统化调试）把排查拆成四个阶段，像探案一样按顺序走：

1. 复现（Reproduce）： 稳定地让 bug 出现，拿到确凿证据；
2. 假设（Hypothesize）： 列出所有可能的病因，不急着下结论；
3. 验证（Verify）： 一个一个假设去验证，排除掉错的，留下真的；
4. 根因（Root Cause）： 确认根因，再动手修——修完还要验证修对了。

口诀： **复现 → 假设 → 验证 → 根因** 。中间任何一步没做完，都不许碰代码。

## 怎么用：57 块的深夜排查

### 第一阶段：复现——把 bug 钉死在证据上

我没急着改，先"复现"。问题不是一个模糊的"数字不对"，我要把它变成可重复的现象。

后端是接口，先分开验证前后端。用 curl 直接查后端，绕开前端：

```
# 家庭账本 2026-08 的报表
curl -H "X-User-Id: 1" "http://localhost:8080/api/reports/monthly?month=2026-08"
# 家庭账本 2026-08 的账目列表
curl -H "X-User-Id: 1" "http://localhost:8080/api/entries?month=2026-08"
```

把报表里所有"支出"加起来，再把账目列表里所有支出加起来——复现了：

- 报表支出合计： **3791.00**
- 账目列表逐笔相加： **3734.00**
- 差： **57.00**

数字差 57，可复现，稳定。 **第一阶段的产出是一句话：后端报表接口的支出合计，比账目列表多 57.00 元。**

### 第二阶段：假设——列全可能的病因

现在列假设。前后端之间，"数字对不上"可能出在哪？我列了三条：

- 假设 A：前端报表页算错了——页面在渲染前对接口数据做了处理，加坏了；
- 假设 B：后端报表接口算错了——聚合逻辑有 bug，多加了钱；
- 假设 C：后端报表接口漏过滤账本——把乐乐（ownerId=2）的账也算进家庭账了。

三条假设，谁真谁假，用证据说话。

### 第三阶段：验证——用证据逐个排除

**验证假设 A** ：前端我不管，直接看后端接口原样返回的数。上面 curl 的结果说明—— **后端接口自己就多 57** 。前端还没参与，A 排除。（前端再花，也改不了后端返回的数。）

**验证假设 C** ：乐乐 2026-08 的两笔账是多少？curl 看一眼：

```
curl -H "X-User-Id: 2" "http://localhost:8080/api/entries?month=2026-08"
```

乐乐两笔：文具 45.00 + 零食 12.00 = **57.00** 。正好等于差的数！

**证据链闭合了** ：多出的 57.00，正是乐乐两笔账的合计。假设 C 成立—— **报表接口把子账本的钱算进了家庭账** 。

### 第四阶段：根因 + 修复 + 验证

打开 `LedgerAppService.listMonthlyTotals` ，根因一眼可见——聚合循环里， **没按 ownerId 过滤** ：

```
/** 有 bug 的版本（示意）：没按账本过滤，把全家账都聚合进来了。 */
public List<MonthlyTotal> listMonthlyTotals(Long ownerId, YearMonth month) {
    Map<Long, BigDecimal> expenseByCategory = new LinkedHashMap<>();
    for (EntryEntity entity : entryRepository
            .findByOwnerIdAndOccurredAtBetweenOrderByOccurredAtDesc(
                    ownerId, monthStart(month), monthEnd(month))) {
        // 嗯？明明按 ownerId 查了仓库……
        // 等等——这里 onwerId 参数根本没被用到！
    }
    // 实际根因：聚合的是"全部账目"，不是"当前账本账目"
}
```

修法：让聚合基于 **当前账本的账目** 。最干净的办法是把"按账本查询"这个意图写清楚：

```
/** 修复后：聚合当前账本（ownerId）某月的账目。 */
public List<MonthlyTotal> listMonthlyTotals(Long ownerId, YearMonth month) {
    Map<Long, BigDecimal> expenseByCategory = new LinkedHashMap<>();
    for (EntryEntity entity : entryRepository
            .findByOwnerIdAndOccurredAtBetweenOrderByOccurredAtDesc(
                    ownerId, monthStart(month), monthEnd(month))) {
        if (entity.getType() == EntryType.EXPENSE) {
            expenseByCategory.merge(entity.getCategoryId(), entity.getAmount(), BigDecimal::add);
        }
    }
    return categoryRepository.findAll().stream()
            .map(category -> new MonthlyTotal(month, toCategory(category),
                    BigDecimal.ZERO,
                    expenseByCategory.getOrDefault(category.getId(), BigDecimal.ZERO)))
            .toList();
}
```

修完， **验证修对了** ——回到第一阶段那句"可复现"的话，看它是否还成立：

```
# 修复后：报表支出合计
curl -H "X-User-Id: 1" "http://localhost:8080/api/reports/monthly?month=2026-08"
# 家庭账目逐笔相加 = 3734.00，报表也应该是 3734.00
```

再补一个 **防回归测试** ——“家庭报表不含子账本账目”：

```
/** 防回归：家庭报表只能统计 ownerId=1 的账目。 */
@Test
void familyReportExcludesKidLedger() {
    // 造数据：家庭 1 笔 + 乐乐 1 笔
    // 断言报表合计只含家庭那笔
}
```

这下，深夜 57 块的案子，从"改两行代码碰运气"变成"四阶段探案 + 防回归测试"，彻底结案。

## 注意事项

1. 没复现就改代码，等于蒙。 先拿到可复现的证据，再谈修。复现不了，你改完也不知道改没改对。
2. 假设要列全，验证要逐个。 只列一条假设然后蒙头验证，容易一条道走到黑。列全 → 逐个排除，才不冤枉好代码。
3. 前后端分开验证。 数字对不上，先 curl 后端，把前端摘出去。一锅粥地看，找不到根因。
4. 修完要验证，还要防回归。 修完跑一遍"当初复现那句话"，再加测试钉死，别让 bug 卷土重来。

## 本文小结

`systematic-debugging` 用"复现 → 假设 → 验证 → 根因"四阶段，把"数字对不上"这种焦虑的 bug，变成一步步可验证的探案。 **先搞清楚为什么，再动手改** ——改代码前的那几分钟冷静，能省一整夜。

57 块的案子结了，账平了。老王听完整个过程，若有所思：「你刚才是按步骤走的……可你最后是怎么从一堆可能性里，精准锁定是『子账本混入』的？」

「因为顺着症状往上追，每一步都有证据。」我说，「那个追的过程，叫根因追踪——明天讲。」

下一站： `root-cause-tracing` ——从表象挖到根子。

15｜root-cause-tracing，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #调试 #排障

共渡时艰

邀请你前往腾讯公益一起捐

陪乡村孩子阅读世界

✨ 人工智能 · 目录