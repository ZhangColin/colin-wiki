---
title: "08｜基建篇 - REST API：把账本逻辑变成接口"
source: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484285&idx=1&sn=86aac4dd4fd823e86be007e88aa1000e&chksm=ffe5d350c8925a46d07bfb18b18b48efe737cf3595eca97b67b01e728323824fc7353e454a77&cur_album_id=4599400782759952387&scene=189#wechat_redirect"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-09
description: "领域和测试都齐了，但艳姐的网页还看不到。基建篇第一弹：持久化层 + REST 接口，让账本从代码库变成能被浏览器访问的 API。"
tags:
  - "superpowers"
---
超级奶爸老谭！ 共渡时艰 *2026年9月2日 08:05*

## 前文回顾

上一篇 [07｜superpowers/testing-anti-patterns 测试反模式-这些坑我都踩过](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484282&idx=1&sn=b3449c30a475f6c8a05939c19d572198&scene=21#wechat_redirect) 把测试反模式讲透了：测行为不测实现、等条件不猜时间。领域服务带着测试网就位了。

艳姐那边催得紧：「老谭，你天天说账本账本，我打开浏览器一片白。 **我到底啥时候能点上两下？** 」

## 场景：代码库和"能用的网页"之间，隔着一层接口

账本的领域代码、测试，全齐了。可它们是 Java 库——艳姐的浏览器怎么调？

浏览器不认识 Java 对象，它只认 **HTTP 接口** ：给一个地址，回一段 JSON。

所以完整项目的中轴，就是把领域变成 REST API。这一步做完，前端（下一篇）才有得调。

这不是 superpowers 的技能篇，是 **基建篇** ——把「幸福账本」从"后端库"补齐成"完整项目"的关键一环。今天我们做两件事：

1. 持久化层： 账目能存进 H2 文件库（重启不丢），seed 一批开箱数据；
2. REST 层： 用 Controller 把领域暴露成 `/api/**` 接口，curl 一打就有。

## 怎么用（一）：先让数据能落盘

### H2 文件库：数据重启不丢

`application.properties` 里，数据库地址指向本地文件（不再是内存，重启数据还在）：

```
spring.application.name=happy-ledger
server.port=8080

spring.datasource.url=jdbc:h2:file:./data/happy-ledger
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

spring.jpa.hibernate.ddl-auto=update
spring.jpa.open-in-view=false
```

`ddl-auto=update` ：表结构自动跟着实体建好。第一次启动，`./data/` 下会多出个库文件。

### 领域 record 与持久化实体，分开两层

我们的领域对象是 **不可变 `record`** （ `Entry` / `Category` / `Budget` ）。但 JPA 实体需要无参构造 + 可变字段，两者打架。

解决方式： **实体只存在于 `persistence` 包，跟领域 record 分离** 。领域 record 管业务，实体只管存库，Controller 只跟 record 打交道。

```
package com.laotan.ledger.persistence;

import java.math.BigDecimal;
import java.time.LocalDateTime;

import com.laotan.ledger.EntryType;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.Table;

/** 账目持久化实体：与领域 record Entry 对应，只存在于持久化层。 */
@Entity
@Table(name = "entries")
public class EntryEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    /** 账本归属：1 = 家庭总账，2 = 乐乐零花钱。 */
    private Long ownerId;

    @Enumerated(EnumType.STRING)
    private EntryType type;

    private Long categoryId;

    @Column(precision = 19, scale = 2)
    private BigDecimal amount;

    private LocalDateTime occurredAt;

    private String note;

    // getter / setter 略，按《阿里巴巴 Java 开发手册》逐字段生成
}
```

Spring Data 仓储——一个接口，增删改查基本不用写 SQL：

```
package com.laotan.ledger.persistence;

import java.time.LocalDateTime;
import java.util.List;

import org.springframework.data.jpa.repository.JpaRepository;

/** 账目仓储：持久化 + 按月/账本查询。 */
public interface EntryJpaRepository extends JpaRepository<EntryEntity, Long> {

    /** 某账本在 [start, end) 时间段内的账目，按时间倒序。 */
    List<EntryEntity> findByOwnerIdAndOccurredAtBetweenOrderByOccurredAtDesc(
            Long ownerId, LocalDateTime start, LocalDateTime end);
}
```

### 应用门面：实体 ↔ record 的翻译官

Controller 不该直接摸实体。中间加一个 `LedgerAppService` ，负责实体和领域 `record` 互转、做聚合。这是 REST 层唯一依赖的业务门面：

```
package com.laotan.ledger.persistence;

import java.time.LocalDateTime;
import java.time.YearMonth;
import java.util.List;
import java.util.Map;
import java.util.Optional;

import com.laotan.ledger.Category;
import com.laotan.ledger.Entry;
import com.laotan.ledger.EntryNotFoundException;
import com.laotan.ledger.EntryRequest;
import com.laotan.ledger.MoneyRules;
import org.springframework.stereotype.Service;

/** 应用门面：Controller 与仓储之间的唯一入口，负责实体↔领域 record 映射与聚合。 */
@Service
public class LedgerAppService {

    private final EntryJpaRepository entryRepository;
    private final CategoryJpaRepository categoryRepository;

    public LedgerAppService(EntryJpaRepository entryRepository,
                            CategoryJpaRepository categoryRepository) {
        this.entryRepository = entryRepository;
        this.categoryRepository = categoryRepository;
    }

    /** 某账本某月的账目列表（按时间倒序）。 */
    public List<Entry> listEntries(Long ownerId, YearMonth month) {
        Map<Long, Category> categories = categoryMap();
        return entryRepository
                .findByOwnerIdAndOccurredAtBetweenOrderByOccurredAtDesc(ownerId,
                        monthStart(month), monthEnd(month)).stream()
                .map(entity -> toEntry(entity, categories))
                .toList();
    }

    /** 记一笔账：金额先过 MoneyRules，再落库。 */
    public Entry addEntry(Long ownerId, EntryRequest request) {
        Optional<String> violation = MoneyRules.check(request.amount());
        if (violation.isPresent()) {
            throw new IllegalArgumentException(violation.get());
        }
        EntryEntity entity = new EntryEntity();
        entity.setOwnerId(ownerId);
        entity.setType(request.type());
        entity.setCategoryId(request.categoryId());
        entity.setAmount(request.amount());
        entity.setOccurredAt(LocalDateTime.now());
        entity.setNote(request.note());
        return toEntry(entryRepository.save(entity), categoryMap());
    }

    // categoryMap / toEntry / monthStart / monthEnd 等私有方法略
}
```

注意 `addEntry` 里金额先过 `MoneyRules.check` —— **入口就拦，这是后面 defense-in-depth 的伏笔** 。

## 怎么用（二）：把领域暴露成接口

### Controller：薄薄一层，只做"收参 → 调门面 → 返回"

```
package com.laotan.ledger.web;

import java.time.YearMonth;
import java.util.List;

import com.laotan.ledger.Entry;
import com.laotan.ledger.EntryRequest;
import com.laotan.ledger.persistence.LedgerAppService;
import jakarta.validation.Valid;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.*;

/** 账目接口：列表 / 记账 / 删账。 */
@RestController
@RequestMapping("/api/entries")
public class EntryController {

    /** 账本切换请求头：不传默认家庭总账。 */
    private static final String USER_HEADER = "X-User-Id";

    private final LedgerAppService appService;

    public EntryController(LedgerAppService appService) {
        this.appService = appService;
    }

    /** 某账本某月的账目列表（按时间倒序）。 */
    @GetMapping
    public List<Entry> list(@RequestParam(required = false) String month,
                            @RequestHeader(value = USER_HEADER, defaultValue = "1") Long ownerId) {
        return appService.listEntries(ownerId, parseMonth(month));
    }

    /** 记一笔账。 */
    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public Entry create(@Valid @RequestBody EntryRequest request,
                        @RequestHeader(value = USER_HEADER, defaultValue = "1") Long ownerId) {
        return appService.addEntry(ownerId, request);
    }

    /** 月份参数缺省取当前月。 */
    private static YearMonth parseMonth(String month) {
        return (month == null || month.isBlank()) ? YearMonth.now() : YearMonth.parse(month);
    }
}
```

几个关键点：

- @RequestHeader(value = "X-User-Id", defaultValue = "1")—— **账本切换就靠这个头**
	：不传默认家庭账，传 `2` 就是乐乐零花钱。免登录，前端顶栏切一下就行。
- @Valid @RequestBody EntryRequest——入参校验交给 Bean Validation， `EntryRequest`
	上已经标了 `@NotNull` / `@Positive` 。
- 月份 `2026-08` 直接 `YearMonth.parse` ，格式非法交给统一异常处理。

### 统一错误：所有异常都返回 JSON

后端挂了、参数错了，浏览器拿到的应该是一段能看的 JSON，而不是一坨堆栈。加一个 `@RestControllerAdvice` ：

```
package com.laotan.ledger.web;

import com.laotan.ledger.EntryNotFoundException;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.*;

/** 全局异常 → 统一 JSON 错误体。 */
@RestControllerAdvice
public class GlobalExceptionHandler {

    /** 参数校验失败（@Valid 触发的 @NotNull/@Positive 等）。 */
    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public ApiError handleValidation(MethodArgumentNotValidException ex) {
        String message = ex.getBindingResult().getFieldErrors().stream()
                .map(error -> error.getField() + " " + error.getDefaultMessage())
                .findFirst()
                .orElse("参数不合法");
        return new ApiError("validation-error", message);
    }

    /** 账目不存在。 */
    @ExceptionHandler(EntryNotFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public ApiError handleNotFound(EntryNotFoundException ex) {
        return new ApiError("entry-not-found", ex.getMessage());
    }

    /** 兜底。 */
    @ExceptionHandler(Exception.class)
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    public ApiError handleOther(Exception ex) {
        return new ApiError("internal-error", "服务器开小差了");
    }
}
```

`ApiError` 就是两个字段的小 record： `(String code, String message)` 。

## 怎么用（三）：seed 数据 + curl 验收

接口有了，可库是空的，艳姐打开什么也看不见。加一个启动即执行的 seed：库空时自动写入分类、预算和艳姐家 2026-08 的账。

`SeedDataRunner` 实现 `CommandLineRunner` ，在 `categoryRepository.count() == 0` 时播种（分类 + 预算 + 9 笔账，家庭 7 笔 + 乐乐 2 笔）。

启动后端：

```
cd happy-ledger
mvn spring-boot:run
```

然后 `curl` 一打，接口就活了：

```
# 分类列表
curl http://localhost:8080/api/categories
# 家庭账本 2026-08 的账目（X-User-Id 缺省为 1）
curl -H "X-User-Id: 1" "http://localhost:8080/api/entries?month=2026-08"
# 记一笔（金额非法会被拦）
curl -X POST -H "Content-Type: application/json" \
  -d '{"categoryId":1,"type":"EXPENSE","amount":20.00,"note":"奶茶"}' \
  http://localhost:8080/api/entries
# 月度报表
curl -H "X-User-Id: 1" "http://localhost:8080/api/reports/monthly?month=2026-08"
```

响应长这样——账目带着嵌套的分类对象，金额是 `BigDecimal` 的精确小数：

```
[
  {
    "id": 7,
    "type": "EXPENSE",
    "category": { "id": 5, "name": "娃", "type": "EXPENSE" },
    "amount": 199.00,
    "occurredAt": "2026-08-16T11:00:00",
    "note": "乐乐玩具",
    "ownerId": 1
  }
]
```

金额非法呢？看统一错误怎么兜住：

```
curl -X POST -H "Content-Type: application/json" \
  -d '{"categoryId":1,"type":"EXPENSE","amount":-5}' \
  http://localhost:8080/api/entries
# {"code":"validation-error","message":"amount 金额必须大于 0"}
```

## 注意事项

1. 领域 record 和 JPA 实体分层，别混。 实体带着 getter/setter 只在 `persistence`
	包活动；Controller 只碰不可变 `record` 。混了，不可变的好习惯就守不住。
2. `X-User-Id` 头是账本切换的唯一通道。 前端顶栏切账本，本质就是改这个头。后端别在 body 里再传 ownerId，避免两处来源打架。
3. H2 文件库别入库。`./data/`
	是运行时产物， `gitignore` 掉；别人克隆后首次启动会自动 seed。
4. 错误响应统一 JSON。 前端拿到 `{code, message}`
	才好提示用户。返回裸堆栈，前端没法处理。

## 本文小结

基建第一步完成：账本从"Java 代码库"变成了 **能访问的 REST API** 。数据落盘 H2、账本用 `X-User-Id` 切换、错误统一 JSON、开箱有 seed。 `curl` 一打，接口全通。

老王在旁边看得直咂嘴：「接口是有了，可艳姐还是开不了网页吧？」

「对，」我把命令行关掉，「下一步，就是把这堆 `/api` 变成她浏览器里能点的页面。」

下一站：Vue 前端起步——让账本跑进浏览器。

09｜Vue 前端起步，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #REST #SpringBoot

✨ 人工智能 · 目录