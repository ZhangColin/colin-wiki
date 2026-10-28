---
title: 19｜superpowers/receiving-code-review 接受代码审查
source: https://mp.weixin.qq.com/s/Xxgxauyi8un94UKfftc57Q?poc_token=HA5dq2qjy2gHeONnoZJ-aEgKak8Y_1HlJIDEz2ig
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-17
description: 审查意见是协作不是批评。接住意见四步：别急着辩护、先理解、逐条回应、改完验证。用 MoneyRules 的一次审查，演示怎么接住意见。
tags:
  - superpowers
---
超级奶爸老谭！ 共渡时艰 *2026年9月17日 08:05*

## 前文回顾

上一篇 [18｜superpowers/requesting-code-review 提交前主动审查](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484345&idx=1&sn=912f2eead8c0f2b1dd7b475c51ebd14c&scene=21#wechat_redirect) 我们把请求审查做成了一套准备流程：自看 diff、自跑测试、自查清单、自写说明。老王审出了两处问题。

**「那你可别上头。」** 他最后那句话，就是今天的开场。

## 场景：审查意见第一秒，真想顶回去

老王审完我的变更，提了两条意见。第一条就让我心口一堵：

「老谭，你那个 `MoneyRules.assertValid` ，直接抛异常。调它的人想知道具体哪儿错了，只能 catch 完再猜。 **能不能改成返回 `Optional` ，让上层决定怎么处理？** 」

第一反应是什么？是辩解。我心里瞬间冒出三句：

- “抛异常不是挺标准的吗？”
- “别人都这么写。”
- “改了又是额外的工作。”

这就是 `receiving-code-review` （接受审查）要驯服的本能： **听到批评，第一反应是防御，而不是理解。** 防御会毁掉审查的价值——你请人来挑毛病，人家挑了，你却把毛病顶回去。

## 技能是什么：审查是协作，不是打分

`receiving-code-review` 的第一性原理： **审查意见不是在否定你这个人，是在帮你把代码变得更好。** 它把"接住审查"拆成四步：

1. 别急着辩护——先让情绪过去，别在第一秒反驳；
2. 先理解，再回应——没听懂就问，别假装懂了就敷衍；
3. 逐条回应——每条意见都给出结论：改 / 讨论 / 不改（并说明理由）；
4. 改完要验证——回应"改好了"之前，先把验证做了。

核心心态就一句： **代码是你们的共同作品，不是你的私人领地。** 意见砸在代码上，不是砸在你脸上。

## 怎么用：把老王那条意见接住

### 第一步：先别辩护，把意见听完

老王说 `assertValid` 抛异常不好，我的三句辩解在脑子里过了三秒。然后我想起 `receiving-code-review` 的提醒：

> 先理解，再回应。别急着维护上一版。

我压住辩解，先问清楚：「你担心的具体是什么？」

老王：「调用的地方，想知道『为什么被拒』——是空？是负数？还是小数位超了？抛异常它只能拿一句 message。改成返回 `Optional<String>` ，原因就在返回值里，调用方能自己决定怎么响应。」

**问完我就懂了** ：他提的不是"风格偏好"，是"接口设计"。这确实更好。

### 第二步：想清楚要不要改

意见理解了，不代表照单全收。逐条判断：

- 这条对么？ 对。 `Optional<String>`
	让调用方（比如 API 层）能精确地把"金额最多两位小数"这种原因，原样变成给用户的提示，而不是一句笼统的"参数错误"。
- 值得改么？ 值得。调用方就一个，改动可控。

决定： **改。**

### 第三步：改，并且保留旧接口

改法上， `receiving-code-review` 还有个好习惯： **别让意见打架** 。我保留 `assertValid` （有的地方确实要"不合法就抛"），新增返回 `Optional` 的版本，让上层按需选择：

```
package com.laotan.ledger;

import java.math.BigDecimal;
import java.util.Optional;

/** 金额不变式：金额必须是两位小数的正数，全程 BigDecimal。 */
public final class MoneyRules {

    private MoneyRules() {
    }

    /** 015 篇版本：不合法直接抛异常，调用方想省事时用。 */
    public static void assertValid(BigDecimal amount) {
        if (amount == null) {
            throw new IllegalArgumentException("金额不能为空");
        }
        if (amount.compareTo(BigDecimal.ZERO) <= 0) {
            throw new IllegalArgumentException("金额必须大于 0");
        }
        if (amount.scale() > 2) {
            throw new IllegalArgumentException("金额最多两位小数，收到 " + amount);
        }
    }

    /** 017 篇按审查意见新增：不抛异常，返回失败原因，由上层决定怎么响应。 */
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

`addEntry` 换成用 `check` ，把原因直接抛给 `GlobalExceptionHandler` ，用户就能看到"金额最多两位小数，收到 88.505"这种具体提示。

### 第四步：回应 + 验证

改完，不是回一句"改好了"就完事。 **回应要有证据：**

「老王，按你意见改了：新增 `MoneyRules.check` 返回 `Optional<String>` ， `addEntry` 用它。测试补了三个用例（空/非正/三位小数）。 `mvn test` 全绿。你再看看？」

测试补上：

```
/** 金额三位小数必须被 check 拦下，并返回具体原因。 */
@Test
void checkReportsDecimalPlaceReason() {
    Optional<String> violation = MoneyRules.check(new BigDecimal("88.505"));
    assertThat(violation).contains("金额最多两位小数，收到 88.505");
}
```

**“改好了 + 有验证 + 有测试”** ，这才是对一条审查意见的完整回应。

## 意见不合怎么办？

不是每条意见都得照改。 `receiving-code-review` 允许你 **有理由地不改** ，但要回应清楚：

- 我有不同考虑： **讲出你的理由** （性能、兼容、成本）；
- 我暂时不改： **说清为什么现在不改** ，记下来，将来再说；
- 我听懂了但不同意： **明确说明** ，别含糊答应又偷偷不改。

**关键是不沉默、不敷衍。** 审查意见最怕的不是"被拒绝"，是"被假装接受"——你说好改，结果没改，下次审查者对你就只剩怀疑。

## 注意事项

1. 第一秒别辩解。 情绪上头时的反驳，九成是错的。先让三秒过去，先听懂再开口。
2. 没听懂就问。 "你担心的是哪类问题？"这一句，能把"意见"从模糊变清楚。
3. 逐条回应，改完给证据。 每条意见都有结论；"改好了"要有测试和验证撑腰。
4. 可以有理由地不改，但不能沉默。 意见被好好拒绝，好过被假装接受。

## 本文小结

`receiving-code-review` 把"被挑毛病"变成"接住意见"：别急着辩护、先理解、逐条回应、改完验证。 **代码是共同作品，审查意见是协作的信号，不是攻击。**

那条意见改完之后， `MoneyRules` 反而更清爽了——一个抛异常版本、一个返回 `Optional` 版本，各司其职。老王再审，只回了一句：「这回没得挑了。」

「那要不要把这套规矩……」我话没说完，老王接上：「固化下来，让 AI 也照着来？」

「对，」我说，「我们定了这么多规矩——金额走 BigDecimal、验证再收工、纵深防御……靠记性迟早漏。该把它们写成 AI 也认得的文件了。」

下一站： `writing-skills` ——把规矩固化成技能。

20｜writing-skills，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #代码审查 #协作

共渡时艰

邀请你前往腾讯公益一起捐

广州公交AED项目

✨ 人工智能 · 目录