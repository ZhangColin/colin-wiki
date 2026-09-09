---
title: "00｜superpowers 开篇：艳姐说，我们家钱算不清了"
source: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484223&idx=1&sn=da68f8d58934055a5ffac2166ba99884&chksm=ffe5d312c8925a04d353ce7a86fc8aebf96e194cef31a2d07eb01027527b1c97712ff41e40b1&cur_album_id=4599400782759952387&scene=189#wechat_redirect"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-09
description: "艳姐要家庭记账，老谭决定用 AI 写一套全家能用的网页记账系统。开篇讲清 superpowers 是什么、系列怎么读、技术栈与项目场景的约定。"
tags:
  - "superpowers"
---
超级奶爸老谭！ 共渡时艰 *2026年8月21日 11:56*

> 老谭，这个月工资到账我没看错的话，咱家又对不上账了。你是不是又偷偷买了啥？
> 
> —— 艳姐，站在账本前，双手叉腰

![00-1](https://mmbiz.qpic.cn/mmbiz_jpg/d9Wx6XKcQmTtrQJ3DqYpicerBNTBjPta3icviavbvouXnRM3n6vemKsZxJdlSicuibZdf19qp03VYNQXTDk3icyAyiaQf8BClFBwJsurm4ZCU5iciakM/640?from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

## 一顿晚饭

那是八月里普通的一顿晚饭。

排骨刚上桌，乐乐扒着饭哼歌，艳姐却把手机往桌面上一放。咚的一声，筷子都跟着跳了跳。

「你听我给你念啊。」她点开这个月的支出流水，「买奶茶 15，买菜 88.5，乐乐的玩具 199……加起来跟卡里对不上，差了十几块。」

她一条一条往下翻，越翻眉头越紧：「这月到底花了多少，你心里有数吗？」

我心里没数。

我们家的账，一直活在微信账单、支付宝账单和一句「大概吧」里。我摸出手机，把银行 App 划了半天，也没划出个所以然。

「你倒是说话呀。」艳姐敲了敲屏幕。

「艳姐，咱家以前也记过账。」我试探着说。

「记了三天就断了，App 太难用。」她理直气壮，「这不是我的问题。」

我深吸一口气，把筷子放下：「我给你写个『幸福账本』——一个网页，你上班摸鱼的时候点开就能记，月底自动对账，超支自动提醒。」

艳姐斜了我一眼：「你写？」

「我用 AI 写。」

「那下个月发工资前，能好？」

「能。」

其实我不是拍脑袋答应的。

上个月在小区门口碰见老王，他神神秘秘地举着手机跟我说：「老谭，我现在写代码，都是让 AI 按规矩来。你再也不是一个人在战斗了。」

他说的那套东西，叫 **obra/superpowers** 。今天这篇，就是这个系列的缘起。

那天晚上，乐乐抱着她的新玩具钻进我书房：「爸爸，我的玩具也要记账吗？」

「记，往后你的玩具都算在『娃』那一类里。」

「那我要是有自己的零花钱账本呢？」

「……行，给你单独开一个。」我随口应下，转身对着电脑屏幕想：这账本，到底该怎么写？

## superpowers 是什么

先交代背景。 **obra/superpowers** 是 Jesse Vincent（网名 obra，大家叫他“老欧”）做的开源项目。他是谁不重要，重要的是他干了一件事：

> **把“怎么用 AI 把软件做靠谱”这件事，拆成了一整套可以照着做的技能。**

以前我们让 AI 写代码，是什么画风？我去年就干过一回。

「给我写个记账系统。」我甩下这句话，十分钟后 AI 交了我三千行代码。看着挺像回事，我花了三个晚上才看懂它想干嘛，最后发现连启动都启动不了。

需求没聊清楚、代码没测试、出 bug 直接改到怀疑人生。

这不怪 AI，怪我没给它规矩。

superpowers 想解决的就是这个。它不是让你背更多 API，而是给 AI 一套 **干活的规矩** ：

- 需求含糊？有 **brainstorming** ，让 AI 用苏格拉底式提问，一个问题一个问题地帮你把想法问清楚；
- 想法清楚了？有 **writing-plans** ，把需求拆成一张张能照做的任务清单；
- 开始写了？有 **test-driven-development** ，先写测试再写代码，红绿循环；
- 出 bug 了？有 **systematic-debugging** ，先查根因再动手，而不是乱改一通；
- 写完了？有 **requesting-code-review** ，提交前先让 AI 和人都审一遍。

一句话总结： **superpowers 让 AI 从“会写代码”，变成“会跟人一起把事干成”。**

![00-2](https://mmbiz.qpic.cn/mmbiz_jpg/d9Wx6XKcQmQWCOKRxMde6qylhTthbI3YicjicaOV3LTrVI7Ly8WcGogg1tp4vZHrKFwAfFtIFGJAwIuGKysbYbv2CEfPXviaxs6rjylt4MmEp0/640?from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

我把这套规矩讲给艳姐听。她听完，若有所思：「这不就是先想清楚再动手吗？」

「对。」

「你们程序员，现在才学会这个？」

「……对。」

它怎么运作的？Claude Code 里装了它之后，会注入一个入口技能 `using-superpowers` ，然后按场景自动激活对应的技能。你甚至可以直接敲 `/brainstorm` 、 `/write-plan` 、 `/execute-plan` 来主动调它。

## 这个系列打算怎么写

我是 Java 出身的老程序员，也是公众号读者们的老朋友。我给自己和这个系列定了四条规矩。

**第一，示例代码尽量用 Java，而且必须能跑。** 老欧的仓库里多是 TypeScript 的例子。国内读公众号的朋友，很多是 Java 技术栈。我会把每个技能的思路，翻译成 Java 生态的写法。

**代码要正确、要可运行** ，这是我下的死命令。

**第二，同一个完整项目，从第一篇看到最后一篇。** 主角还是「幸福账本」，但这次它不只是一个后端库，而是一套 **前后端完整的项目** ：Java 后端提供接口，Vue 前端跑在浏览器里，艳姐和乐乐真的能用它记账、看报表、管预算。我们从艳姐的一句抱怨开始，一路经历头脑风暴、写计划、TDD、疑难 bug、线上排查……读到最后一篇，你会看到这套系统是怎么从零长成一套能用的网页应用的。

像追剧一样学技术，才记得住。

**第三，一篇讲一个技能。** 每个技能都按「使用场景 → 技能是什么 → 怎么用 → 注意事项」来写。讲完所有技能，再写两篇组合实战，让你看到真实工作里它们怎么协同。

**第四，用 Obsidian 管理这套笔记。** 每篇都有属性块、标签和双链，方便你检索、串联。固定标签是 `superpowers` 。

技术栈约定如下，整个系列不会变：

- 后端：Java 21 + Spring Boot 3 + Maven + **H2 文件库** （数据落盘，重启不丢）
- 前端：Vue 3 + Vite + **Element Plus** （浏览器访问， `/api` 代理到后端）
- 测试：后端 JUnit 5 + Mockito + AssertJ（异步等待用 Awaitility）；前端 Vitest
- 金额一律 `BigDecimal` ，禁止 `double` 算钱（后面有专文讲为什么）

## 这一路我们会遇见哪些技能

按老欧仓库的分类，我把系列路线图画给你。这张图不是画着玩的，是接下来二十多篇的路书，每条都是我们正在做的事：

- 先聊清楚
- brainstorming（头脑风暴）
- 写下来
- writing-plans
	- executing-plans
- 写对代码
- test-driven-development
	- condition-based-waiting
	- testing-anti-patterns
- 并行不打架
- using-git-worktrees
	- finishing-a-development-branch
	- dispatching-parallel-agents
	- subagent-driven-development
- 出 bug 不慌
- systematic-debugging
	- root-cause-tracing
	- verification-before-completion
	- defense-in-depth
- 审好代码
- requesting-code-review
	- receiving-code-review
- 沉淀自己
- writing-skills

中间还会穿插两篇 **基建篇** ：一篇把领域变成 REST API，一篇让账本跑进浏览器。最后两篇是组合实战：一篇从需求到上线，一篇线上大排查。

老王听完我的路线图，问了一句：「这么多技能，你一个人啃得动吗？」

「我啃不动，还有 AI 帮我。」我指了指屏幕，「而且，艳姐的验收标准就一条——」

「每月工资到账那天，账能自己对平，我在网页上点几下就能看到花了多少、超没超预算，就算这系统写成了。」

## 本文小结

跟 AI 协作写代码，缺的从来不是 AI，而是 **一套跟 AI 配合的规矩** 。这套规矩，就是 superpowers 教你的「技能」。

艳姐的账本，从今天开始。这不止是一套记账系统的开发记录，更是一趟「把 AI 用成老伙计」的旅程。

下一篇我们先把插件装起来——五分钟，让 superpowers 出现在你的 Claude Code 里，顺手把前后端双工程的骨架搭起来，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #Java #Vue #教程

✨ 人工智能 · 目录