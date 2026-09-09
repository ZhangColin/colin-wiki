---
title: "mattpocock/skills AI 装不下的大工程？把迷雾画成地图，一次探一步"
source: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484086&idx=1&sn=df782c8e1d1c6780a9b19a8dd5950c32&chksm=ffe5d29bc8925b8d2eb0eb40a82486381e4dc750fe282c0da30f0a69faaa316d39bff5572b19&cur_album_id=4599400782759952387&scene=189#wechat_redirect"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-08
description: "05 课一句话带过的 wayfinder，这次展开讲透：想法大到一次会话装不下时，把迷雾画成地图，一次解决一个决策工单。四件套隐喻、四类工单、两步工作流，外加 EShop 订单重构的 gh 命令实战与 Java 代码链，全程可执行。"
tags:
---
## wayfinder 专题 | mattpocock/skills AI 装不下的大工程？把迷雾画成地图，一次探一步

超级奶爸老谭！ 共渡时艰 *2026年8月12日 08:05*

#mattpocock #wayfinder #skills

## 前言

你接了个大活。

大到什么程度呢，一句话装不下，一次 Agent 会话装不下。你盯着需求文档看了半天，脑子里一团浆糊，不知道从哪下脚。

这个场景我太熟了。玩过 RTS 游戏的人都知道战争迷雾，地图一开始是黑的，你派单位探过去，探过的区域才亮起来。没探过的地方，你只知道那里大概有东西，但不知道是资源点还是敌人家门口。

大工程就是这样。你只知道目的地大概在那边，中间全是黑的。

`/wayfinder` 干的事，就是把这团黑的，画成一张地图。

它不是替你写代码的。它把整团模糊的工作，画成一张挂在 issue tracker 上的共享地图，再用「决策工单」，一次只解决一个未知问题，直到通往目的地的路线完全清晰。

我打游戏有个毛病，非要把地图全探完才踏实，结果经常支线跑了一堆，主线还没动。。。wayfinder 这套东西，某种程度上治好了我这个毛病，因为它逼你一次只探一步，探完就记下来，绝不贪多。

顺嘴提一句，wayfinder 的前身叫 decision-mapping。Matt 自己觉得这名字「既术语化又不准确」，因为四类工单里只有一类是真的「决策」。改名之后，整个隐喻统一了，在迷雾中探路。

## 什么时候该用 Wayfinder

不是所有任务都要用它。Matt 自己的路由是这么分的。

小修改、小 bug，直接改，或走 `/diagnosing-bugs` 。

中型功能， `/grill-with-docs` → `/to-spec` → `/implement` 。

大型功能， `/grill-with-docs` → `/to-spec` → `/to-tickets` → `/implement` 。

超大、全新、多模块，才轮到 `/wayfinder` → `/to-spec` → `/to-tickets` → `/implement` 。

什么情况下你会想起它？两种典型场景。

全新项目（greenfield），连技术栈、模块边界都没定。

巨型功能构建，大到一次会话的上下文根本装不下。

Matt 自己就用它规划过一门完整课程，连着四天几乎每分钟都在用，攒下接近 100 个独立的 grilling / prototyping / research session，全汇总到同一张中央地图里。节奏很特别，对问题了解越多，地图越长，找到的答案越多，地图越缩。

## 核心隐喻，四件套

wayfinder 借用了游戏里的「战争迷雾」概念，整套东西就四样。

Map（地图），整个工作的唯一正本。一条带 `wayfinder:map` 标签的 issue，挂在 issue tracker 上。

Fog of War（战争迷雾），还没搞清楚、但已经在范围内的未知区域。

Frontier（前沿），迷雾边界上那些「阻塞条件已满足、可以被认领」的工单。tracker 的 UI 会直观地把它渲染出来，一眼就能看到现在能领哪张。

Destination（目的地），这次探路要抵达的终点，可以是一份 spec、一个待锁定的决策、或者一次就地改造，比如数据结构迁移。

这块需要注意一下，wayfinder 默认只做规划，不做执行。每张工单的产物是一个决策，不是可交付的代码。当你忍不住想「直接开干」的时候，往往就是你抵达了地图边缘的信号，该把活交给 `/implement` 了。

## 地图的结构

地图是索引，不是仓库。里面只有结论的摘要和指向工单的链接，细节一概不放。细节永远只待在工单里，免得同一份决策被复述好几遍，最后各说各话。

每次会话加载一次低分辨率全貌。

```
## Destination
<!-- 抵达终点时的样子：spec、决策、或改造。一两句话，每个会话在选择工单前都要先对齐它 -->

## Notes
<!-- 领域；每次会话应参考的 skill；本次工作的常驻偏好 -->

## Decisions so far
<!-- 索引：每个已关闭工单一行，足够判断相关性，细节点链接进去 -->
- [<已关闭工单标题>](link)：<一行结论摘要>

## Not yet specified
<!-- 见「战争迷雾」：范围内但还无法工单化的未知，随前沿推进而毕业 -->

## Out of scope
<!-- 见「超出范围」：被判定在目的地之外的活儿，关闭，永不毕业 -->
```

五块，各有各的活。Destination 定终点，Notes 写常驻偏好，Decisions so far 记走过的路，Not yet specified 装说不清的未知，Out of scope 扔明确不要的活。

这里我想起一件事。中世纪的海图，没探明的海域，画师会在上面画海怪，配一句 Here be dragons，此处有龙。Not yet specified 就是现代版的此处有龙，那些你隐约知道存在、但还说不清形状的未知。区别是，古人画了海怪就真的绕道走，我们把它写下来，是为了以后回头把它变成工单。

关键规则，按名称引用。人读到的所有地方，叙述、Decisions so far，都用工单标题指代，绝不用 `#42` 、 `#43` 这种裸 ID。一堵 `#42, #43, #44` 没人读得懂，名字一眼可知。ID 和 URL 不是消失了，而是裹在名称里面，绝不替代名称。

## 四种工单

每张工单都是地图的子 issue，体量控制在「一次 100K token 的 Agent 会话能装下」，并打上对应的 `wayfinder:<type>` 标签。四种。

**research** （调研），读文档、翻第三方 API、查本地资料，把某个决策等着用的事实捞出来。由 `/research` 子代理解析，无人值守（AFK）。

**prototype** （原型），先做个便宜的粗糙东西，大纲、粗略想法、stub、UI 或逻辑代码都行，让讨论有个实物可对着说。走 `/prototype` 技能，人在回路（HITL）。

**grilling** （盘问），用 `/grilling` 和 `/domain-modeling` ，一次一个问题地聊，把决策聊死。人在回路（HITL），默认就它。

**task** （任务），做决策之前必须先干完的杂活，注册个服务好评判它的 API、开通权限、把数据搬出来看看形状。它不产生决策，而是给决策开路。Agent 自己干得了就自己干（AFK），干不了就交给真人一份精确清单（HITL）。

> HITL 是 human in the loop，人在回路，这类工单必须和真人实时对话解决，Agent 绝不能替真人回答。AFK 是 away from keyboard，无人值守，Agent 独立搞定。四类工单正好按这个维度分成两半，research、task 偏 AFK，prototype、grilling 偏 HITL。

工单体量正好符合一次会话的大小，能增量推进、能跨会话交接。wayfinder 敢接「超大项目」，底气就在这里。

## 两步工作流

### 第一步，Charting，绘制地图，一次性

1. 命名 Destination，先跑 `/grilling`
	和 `/domain-modeling` ，把这张地图要通向哪里钉死。目的地定了，范围跟着就定了。
2. 广度优先探雾，再盘问一轮，这次在整个空间上扇开，而不是深钻某一条线，浮出所有待定决策和现在就能走的第一步。
3. 创建地图，带 `wayfinder:map`
	标签，填好 Destination 和 Notes，把雾草草描进 `Not yet specified` 。
4. 创建第一批工单，把眼下说得清的问题做成地图的子 issue。
5. 第二遍接线，用 tracker 原生的依赖关系把工单连起来（blocking edges），frontier 才能在 UI 上直接看出来。接线必须放第二遍，因为 issue 得先有 id 才能互相引用。
6. 并行触发 research 子代理，为刚创建的每张 research 工单派一个 `/research`
	子代理并行解析，结果落在一次性分支上。
7. 停，到此为止。绘制是一次会话的活，它不亲手解决任何工单。

### 第二步，Working Through，循环推进，反复做

每个会话都得守规矩，尤其一个会话只解析一张工单（research 工单除外）。

1. 认领，把工单 assign 给当前驱动地图的人，这一步先于任何工作，并发会话才会自动跳过它。没指派、没关闭的工单，就是没人认领的。
2. 调用 Notes 里指定的 skill，拿不准就用 `/grilling`
	或 `/domain-modeling` ，需要时抓一下相关或已关闭工单的完整正文。
3. 记录决议，把答案作为 resolution comment 发布，关闭 issue，在地图的 `Decisions so far`
	追加一行上下文指针（gist + 链接）。
4. 毕业新工单，这次答案让哪些「迷雾」变得能工单化了？创建它们、接好依赖，同时从 `Not yet specified`
	清掉。
5. 清理，如果哪张工单其实跑到了 Destination 外面，判成 out of scope 而不是硬解决；如果这次决策推翻了地图其他部分，把那些工单更新或删掉。

没被阻塞的工单，用户可以并行跑，所以随时可能有别的会话在改 tracker，心里有数就行。

## 与其他技能的配合

wayfinder 不是孤岛，它是整条协作链里的「探路者」，动手写代码之前，把该搞清楚的问题都搞清楚。

```
/wayfinder → 建立地图，逐个消除迷雾
     ↓ 地图完成，路线清晰
/grill-me   → 挑战决策，填平逻辑漏洞
     ↓ 想法被充分拷问
/to-spec    → 固化为结构化 Spec
     ↓ Spec 就是施工图纸
/to-tickets → 拆分为依赖排序的 Ticket
     ↓ 每个 Ticket 是一个追踪子弹
/implement → 逐个实现
     ↓
/code-review → 双轴审查
```

## 手把手实战，把 EShop 的订单折扣重构成策略链

纸上谈兵没意思，来真的。

假设 EShop 的订单金额计算已经长成一坨 if-else，满减、优惠券、会员价全堆在 `OrderService` 里，每加一种优惠都得动核心方法。你想把它重构成可扩展的折扣策略链，让以后新增优惠类型不用碰核心代码。

改动横跨金额计算、库存扣减、对账好几个模块，一次会话装不下，wayfinder 就是为这种场面准备的。

### 建图

先命名 Destination，创建地图。在仓库里执行（把 `<owner>/<repo>` 换成你自己的仓库）。

```
gh issue create --title "EShop 订单折扣策略链重构" --label "wayfinder:map" --body "## Destination
输出一份折扣策略链重构 spec：策略接口、叠加顺序、基线测试，足够 to-tickets 切出第一批实施工单。
## Notes
领域：Spring Boot 电商订单域。参考 skill：grilling、domain-modeling、prototype。金额一律 BigDecimal，叠加顺序先满减后优惠券。"
```

### 建第一批工单

眼下说得清的未知，做成子 issue。标题就是工单名，正文顶部标一行 `Part of #<map编号>` 。

```
# 调研：现有金额计算的耦合点到底有哪些
gh issue create --title "research: 盘点 OrderService 现有的优惠耦合点" --label "wayfinder:research" --body "## Question
OrderService 里满减、优惠券、会员价各自耦合在哪里？哪些规则被多个方法共享？输出带引用的梳理结果。"
```

另外三张工单照葫芦画瓢（假设 `gh` 依次分配编号，research 46，task 47，prototype 48，grilling 49）。

prototype，策略链在 Spring 里的装配方式（prototype，HITL，编号 48）。

grilling，叠加顺序与上限的边界规则（grilling，HITL，编号 49）。

task，重构前补齐基线测试（task，AFK，编号 47）。

### 第二遍接线

`research: 盘点 OrderService 现有的优惠耦合点` 和 `task: 重构前补齐基线测试` 不干完， `prototype: 策略链在 Spring 里的装配方式` 就跑不起来。用 GitHub 原生依赖连线，阻塞者要用 **database id** （不是 issue 编号）。

```
# 取阻塞者的 database id
BLOCKER_DB_ID=$(gh api repos/<owner>/<repo>/issues/46 --jq .id)

# 给 prototype 工单 48 添加「被 46 阻塞」
gh api --method POST repos/<owner>/<repo>/issues/48/dependencies/blocked_by -F issue_id=$BLOCKER_DB_ID
```

`task: 重构前补齐基线测试` （编号 47）同样堵着 48，把阻塞者换成 47 的 database id 再跑一遍就行。

### 认领并解析

`task: 重构前补齐基线测试` 没人堵，是第一个前沿。先认领，认领先于一切。

```
gh issue edit 47 --add-assignee @me
```

给它补上基线测试，把现状行为锁死。

```
class OrderServiceTest {

    @Test
    void calculateTotalWithCoupons() {
        Order order = order(1200);
        Coupon coupon = fullReductionCoupon(100);
        BigDecimal total = orderService.calculateTotal(order, coupon);
        assertEquals(BigDecimal.valueOf(1050), total);
    }
}
```

现状的 if-else 长这样。

```
@Service
public class OrderService {

    public BigDecimal calculateTotal(Order order, Coupon coupon) {
        BigDecimal total = order.getTotalPrice();
        // 满减：满 1000 减 50
        if (total.compareTo(BigDecimal.valueOf(1000)) >= 0) {
            total = total.subtract(BigDecimal.valueOf(50));
        }
        // 优惠券：满减券抵扣，最多减到 0
        if (coupon != null && coupon.getType() == CouponType.FULL_REDUCTION) {
            total = total.subtract(coupon.getAmount()).max(BigDecimal.ZERO);
        }
        return total;
    }
}
```

测试全绿，记下决议，关掉工单。

```
gh issue comment 47 --body "决议：基线测试已补齐，锁定满减 50、满减券 100、上限 0 三条行为。重构后必须保持全绿。"
gh issue close 47
```

### 毕业新工单

这个决议让「策略接口长什么样」变得能开工单了，于是毕业一张新工单，同时从 `Not yet specified` 清掉。

```
gh issue create --title "grilling: DiscountPolicy 接口的语义与职责边界" --label "wayfinder:grilling" --body "## Question
supports / apply 两个方法是否足够？策略要不要感知 Coupon 类型？"
```

### 继续推进

`grilling: 叠加顺序与上限的边界规则` 解完，接口设计定稿。

```
public interface DiscountPolicy {
    boolean supports(Order order, Coupon coupon);
    BigDecimal apply(BigDecimal price, Order order, Coupon coupon);
}
```

满减策略实现。

```
@Component
public class FullReductionPolicy implements DiscountPolicy {

    @Override
    public boolean supports(Order order, Coupon coupon) {
        return order.getTotalPrice().compareTo(BigDecimal.valueOf(1000)) >= 0;
    }

    @Override
    public BigDecimal apply(BigDecimal price, Order order, Coupon coupon) {
        return price.subtract(BigDecimal.valueOf(50));
    }
}
```

`OrderService` 缩成策略链的驱动者，以后新增优惠类型再也不用碰它。

```
@Service
public class OrderService {

    private final List<DiscountPolicy> policies;

    public OrderService(List<DiscountPolicy> policies) {
        this.policies = policies;
    }

    public BigDecimal calculateTotal(Order order, Coupon coupon) {
        BigDecimal total = order.getTotalPrice();
        for (DiscountPolicy policy : policies) {
            if (policy.supports(order, coupon)) {
                total = policy.apply(total, order, coupon);
            }
        }
        return total.max(BigDecimal.ZERO);
    }
}
```

跑一遍测试，基线测试依然全绿。答案越攒越多， `Not yet specified` 里的模糊区一块块变成新的、可执行的工单，未知被消灭一块，地图就「缩」回去一点。

等 `Decisions so far` 把影响 Destination 的决策都覆盖完，地图就画完了。这时候路已经完全可见，没有一丝迷雾，直接交给 `/to-spec` 进入施工。

## 常见误区

写到这里，把常见的坑一次说清。

把 wayfinder 当代码生成器用。它默认只产决策，不产代码。忍不住想直接写代码，就是抵达地图边缘的信号，该移交给 `/implement` 了。

一张工单体量过大。工单得能被一次 100K token 会话消化。一张工单塞太多未知，等于又回到「单次会话装不下」的老问题。

用 ID 而非名称引用工单。 `#42` 对人眼是噪声。用标题，「RBAC vs ABAC 选型」一眼可读。

先干活再认领。不先 assign 就开工，并发会话会撞车。认领是物理动作，必须排在干活前面。

把细节写进地图本体。地图是索引，不是仓库。细节留在工单里，地图只放一行 gist + 链接。同一份决策被复述好几遍，早晚漂移。

小型需求也上 wayfinder。改个按钮文字、修个小 bug，直接改或走 `/diagnosing-bugs` 就行。wayfinder 是给「装不下单次会话」的庞然大物准备的。

HITL 工单让 Agent 自问自答。grilling 和 prototype 必须有真人。盘问 Agent 自己回答自己的问题，这套机制就废了。

## 为什么它值得学

wayfinder 真正颠覆的不是技术，是工作哲学。它让 Agent 从「执行指令的工具」变成「理解上下文后帮你做判断的伙伴」。

核心纪律只有一条，别让 AI 同时负责「想清楚做什么」和「怎么写代码」。想清楚的部分，交给 wayfinder 慢慢探路；写代码的部分，等路线清晰了再交给 `/implement` 。两者一解耦，大项目才不会做到一半，发现「方案是在迷雾里画的」而散架。

记住路由口诀，不知道路线？ `/wayfinder` 。等地图画完，自然流入 `/to-spec` → `/to-tickets` → `/implement` 。

四步串起来，就是 Matt Pocock 面对「超大项目」时的完整武器链。

地图画完了，迷雾散了。

我打游戏探完整张地图的时候，心里是踏实的，因为每一寸都亮着。大工程做到这个份上，也是这种感觉，路完全可见，没有一丝未知，剩下的就是走。

磨平一点信息差，哪怕只是你自己和昨天的自己之间，那一点。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/d9Wx6XKcQmTjeuUOXom3iaSPY7iageGagiamyC6DCOJ3Q1piaewhUicy2FKGbwD6RxIN7x4OiaC9dyHNJudFKa4HjtsthOBgd82aic6DsdQ6nGQiaOs/640?wx_fmt=jpeg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

## 快速回顾

- 定位，一个会话装不下、路径不明的超大工作，画成决策工单地图，一次解决一个未知
- 四件套，Map（唯一正本）、Fog of War（说不清的未知）、Frontier（可认领的边界）、Destination（终点）
- 四类工单，research（AFK）/ prototype（HITL）/ grilling（HITL）/ task（AFK 或 HITL）
- 两步流程，Charting 一次性绘制（命名目的地 → 探雾 → 建图 → 建工单 → 接线 → 并行 research → 停）；Working Through 循环推进（认领 → 解析 → 记录决议 → 毕业新工单 → 清理）
- 按名称引用，叙述和索引里用工单标题，不用裸 ID
- 雾或工单，能精确陈述问题就开工单，不能就留在 `Not yet specified`
- 核心纪律，想清楚和写代码解耦，路线清晰后再交给 `/to-spec`
	→ `/to-tickets` → `/implement`

✨ 人工智能 · 目录