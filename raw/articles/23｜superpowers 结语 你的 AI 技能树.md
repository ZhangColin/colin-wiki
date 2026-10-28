---
title: "23｜superpowers 结语 你的 AI 技能树"
source: "https://mp.weixin.qq.com/s/0SExza7mNm80wyIHSDlJOQ"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-22
description: "二十几篇走完，幸福账本立住了，你的 AI 技能树也立住了。回望这一路：从一句抱怨到一套能用的系统，从\x26#39;会写代码\x26#39;到\x26#39;会干活\x26#39;。"
tags:
  - "superpowers"
---
超级奶爸老谭！ 共渡时艰 *2026年9月22日 08:04*

## 前文回顾

上一篇 [22｜superpowers 组合应用2：线上「账对不上」的大排查](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484365&idx=1&sn=eca69da11eda585047e2f68c7a119c2f&scene=21#wechat_redirect) 线上"账对不上"的大排查，三个调试技能协同收尾。账本清爽了，艳姐气消了，老王也长舒一口气。

今天没有新技能。今天，我们站在 23 篇的终点，回头看看这一路。

## 场景：这一路，我们到底干了什么

回到八月那个傍晚——艳姐把手机往桌上一放：“咱家账又对不上了。”

就是从这句话开始，我们用同一个项目「幸福账本」，走了二十多篇：

- 装好 superpowers，把 AI 从"会写代码"变成"会干活"；
- 用 brainstorming 把模糊需求问成规格；
- 用 writing-plans / executing-plans 把规格拆成能照做的活，一步一步干完；
- 用 TDD 把"写对"变成"可证明"；
- 用 worktree 让前后端并行不打架；
- 用系统化调试，把 57 块、一分钱的账，追到根子；
- 用 defense-in-depth，给钱多上了几道锁；
- 用 code review 两篇，让提交和接审都变成协作；
- 用 writing-skills，把规矩固化下来；
- 最后，两篇组合实战，把技能串成流水线，从一句话干到上线。

而现在， **幸福账本真的能用了** ：艳姐在网页上记账、看报表、管预算，乐乐有自己的零花钱页。这不是教学 Demo——它是一个真的在跑的前后端完整项目。

## 这一路，三个最重要的心得

站在终点回望，有三句话我想留给你。

### 第一句：技能的价值，是把"运气"变成"流程"

一个人写代码，写对 80% 靠能力，写对 100% 靠流程。

没有流程的时候，AI 写代码是"开盲盒"——有时惊艳，有时惊吓。有了 brainstorming → writing-plans → executing-plans → TDD → verification 这套流程，写对不是撞运气，是 **每一步都有证据地推着走** 。

流程把"我觉得应该没问题"，换成了"测试绿了、页面走了、验收过了"。

### 第二句：完整的项目，是最好的老师

这套教程最不一样的地方，是它从头到尾 **只有一个项目** ——而且是个前后端完整的项目。

为什么？因为零散的知识点记不住，串成一条线才记得住。你在哪一篇学的 API，在前面哪一篇埋的伏笔，后面哪一篇用的它—— **知识有了上下文，才真正长在你身上。**

艳姐每次"验收"、乐乐每次"超支"，都是这条线的节点。追剧式学技术，学的不是剧情，是 **因果** 。

### 第三句：规矩靠固化，不靠记性

二十多篇攒下的规矩——金额走 BigDecimal、验证再收工、纵深防御、账本隔离……单靠记性，迟早漏。

所以最后我们做了 `writing-skills` ：把规矩写进 SKILL.md，让 AI 一开工就先读。 **把经验固化成可执行的东西，才是经验真正的归宿。**

## 你的技能树，已经立住了

现在，把这二十多篇摊开，你会看到一棵完整的技能树：

```
先聊清楚     brainstorming
写下来       writing-plans → executing-plans
写对代码     TDD → condition-based-waiting → testing-anti-patterns
并行不打架   git-worktrees → finishing-branch → parallel-agents → subagent-driven
出 bug 不慌  systematic-debugging → root-cause-tracing
           → verification-before-completion → defense-in-depth
审好代码     requesting-review → receiving-review
沉淀自己     writing-skills
```

这棵树不是我的，是 **你的** 。技能是工具，工具是拿来用的——从明天起，你在真实项目里每用一次，它就长高一点。

## 本文小结

23 篇，一个项目，一棵技能树。

从"艳姐说账对不上"，到"艳姐在网页上平了账"——这中间差的，不是更聪明的 AI，是 **一套跟 AI 配合的规矩** 。

下篇是总结篇——不写新东西，把全系列掰开揉碎，做成一份你能随时翻的 **速查手册** 。技能总表、常用命令、代码约定、踩过的坑，一张表全收好。

24｜总结篇：全系列回顾与速查合集，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #结语 #教程

✨ 人工智能 · 目录