---
title: "17｜superpowers/defense-in-depth 给 API 多上几道保障"
source: "https://mp.weixin.qq.com/s/b22LES2veuZApZqgIKKcgg"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-15
description: "一道防线会被绕过，那就上几道。前端校验、Bean Validation、业务校验、防回归测试，层层兜底，一处失守仍有后手。"
tags:
---
超级奶爸老谭！ 共渡时艰 *2026年9月15日 08:05*

## 前文回顾

上一篇 [16｜superpowers/verification-before-completion 验证完成再收工](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484339&idx=1&sn=9245ee65c3edb757e5e4cae7aae2a80b&scene=21#wechat_redirect) 把"完成"重新定义为"验证过"：测试绿 + 真实走一遍 + 验收对照。

老王盯着我那套防线，问了句扎心的： **“你这些防线，要是一层被 AI 写代码绕过了呢？”**

## 场景：单一防线，一破全破

很多人防御的思路是"只做一道锁"：前端表单校验了，后端就不管了；后端校验了，数据库就不用管了。

这种"一道锁"的防御有个致命弱点： **锁一旦被绕过，整个系统就裸奔。**

比如我们早期版本，只在 **前端** 限制了"金额必须大于 0"：

```
if (form.amount <= 0) {
  ElMessage.warning('金额必须大于 0')
  return
}
```

看着挺对。可有人绕过前端，直接 `curl` 打后端接口，传一个 `amount: -5` ——后端照单全收，一条负数账进了库。 **前端那道锁，对 curl 形同虚设。**

`defense-in-depth` （纵深防御）解决的就是这个： **不指望任何一道防线万无一失，而是把防线一层一层叠起来——一处失守，下一层还在。**

## 技能是什么：防御要"纵深"，不要"单点"

`defense-in-depth` 的核心思想来自安全领域： **系统的每一层都有独立防御，攻击者要层层突破，而不是一层就到底。**

放在"记账 API"上，钱从浏览器到数据库，每一层都该有一道独立的校验：

```
第 1 层  前端表单校验    用户输入时就拦（体验好）
第 2 层  后端参数校验    @Valid 标注的 Bean Validation（接口层）
第 3 层  后端业务校验    MoneyRules（金额不变式）
第 4 层  防回归测试      哨兵测试兜住"改回 double"这类回归
```

每一层 **独立工作、不依赖上一层"已经拦过"** 。这就是纵深： **单点防御赌"拦得住"，纵深防御赌"就算漏了，下面还有"。**

## 怎么用：给"记一笔"上四道锁

拿"记一笔账"这个接口，看看四道锁怎么叠。

### 第 1 层：前端表单校验——用户输入时就拦

前端 `LedgerEntry.vue` 提交前先检查：

```
async function submit() {
  if (!form.categoryId || !form.amount) {
    ElMessage.warning('请选分类并填金额')
    return
  }
  // ...
}
```

这层的价值是 **体验** ：用户还没点提交，就告诉他漏了啥。但它只是第一层—— **它挡不住绕过它的请求** 。

### 第 2 层：后端参数校验——@Valid 兜底

后端接口层，用 Bean Validation 在入参 `record` 上标注约束：

```
package com.laotan.ledger;

import java.math.BigDecimal;

import jakarta.validation.constraints.NotNull;
import jakarta.validation.constraints.Positive;

/** 记账入参：第一道锁，入口就拦。 */
public record EntryRequest(
        @NotNull Long categoryId,
        @NotNull EntryType type,
        @Positive(message = "金额必须大于 0") BigDecimal amount,
        String note
) {
}
```

Controller 里 `@Valid @RequestBody` 触发校验：

```
@PostMapping
public Entry create(@Valid @RequestBody EntryRequest request, ...) {
    return appService.addEntry(ownerId, request);
}
```

前端绕过？直接 `curl` 传 `amount: -5` ，后端这层直接 400：

```
{"code":"validation-error","message":"amount 金额必须大于 0"}
```

### 第 3 层：后端业务校验——MoneyRules 守住金额不变式

`@Positive` 管"大于 0"，可"金额最多两位小数"这种业务规则，注解表达不了。交给业务层 `MoneyRules` ：

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

`addEntry` 里落库前先过它：

```
public Entry addEntry(Long ownerId, EntryRequest request) {
    Optional<String> violation = MoneyRules.check(request.amount());
    if (violation.isPresent()) {
        throw new IllegalArgumentException(violation.get());
    }
    // ... 落库
}
```

试一下 `amount: 88.505` （三位小数）：

```
{"code":"bad-request","message":"金额最多两位小数，收到 88.505"}
```

**这层管的是注解管不了的业务不变式** ——就算有人绕过 `@Positive` ，也过不了这层。

### 第 4 层：防回归测试——把防线钉死

最后，把"金额非法必须被拦"写成测试，防止哪天有人把这层防线拆了：

```
/** 防回归：金额三位小数必须被 MoneyRules 拦下。 */
@Test
void amountWithTooManyDecimalPlacesRejected() {
    Optional<String> violation = MoneyRules.check(new BigDecimal("88.505"));
    assertThat(violation).isPresent();
    assertThat(violation.get()).contains("两位小数");
}
```

四道锁齐了。现在就算前端被绕过、注解被绕过，还有 `MoneyRules` ；就算 `MoneyRules` 被人改坏，防回归测试会立刻红给你看。 **没有任何一道防线需要"完美"，因为它们是一层压一层。**

## 注意事项

1. 每层防御要独立。 别写"因为前端已经校验了，后端就不管"——每层都要假设上一层失效了。
2. 前端校验管体验，后端校验管安全。 前端校验给用户即时反馈；真正的安全边界在后端，因为后端才挡得住绕过前端的请求。
3. 注解管格式，业务代码管不变式。 `@Positive`
	管"大于 0"，"两位小数"这种业务规则用 `MoneyRules` 这类代码表达，两者互补。
4. 防线要能自我守卫。 防回归测试是"防线的防线"——它保证防线本身不被拆掉。

## 本文小结

`defense-in-depth` 把"一道锁"换成"纵深防线"：前端表单、后端 `@Valid` 、业务 `MoneyRules` 、防回归测试，一层叠一层。 **不赌哪层拦得住，赌的是即使漏了一层，下面还有后手。**

老王把这四层画成一张纵向的图，看了半天，忽然问：「你这些防线啊测试啊，都是给自己定的规矩。 **要是哪天你自己忘了这些规矩，怎么办？** 」

我指了指代码里那些中文注释：「所以得把它们固化下来——不靠记性，靠流程。最好，能变成 AI 也认得的规矩。」

下一站： `requesting-code-review` ——提交前，先让 AI 和你自己都审一遍。

18｜requesting-code-review，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #调试 #防御

共渡时艰

邀请你前往腾讯公益一起捐

陪乡村孩子阅读世界

✨ 人工智能 · 目录