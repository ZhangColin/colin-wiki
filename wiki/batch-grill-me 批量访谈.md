---
type: source
title: "batch-grill-me 批量访谈"
domain: [AI编程, AI工具]
tags: [mattpocock-skills, batch-grill-me, grilling, 设计树, frontier, round, in-progress]
sources: []
source_path: "raw/articles/mattpocockskills grill-me 别挤牙膏：能问的问题一轮问完，答完自动解锁下一批.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484104&idx=1&sn=e400e02e708e16b260d201fe8309172a"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-14"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases: [batch-grill-me 专题, 设计树与前沿]
---

# batch-grill-me 批量访谈

> 一句话要点：把 grilling 的「一次一问」升级为「一轮问完整个前沿」——设计树 + 前沿 + 轮次三机制保证批量不乱问，每题带推荐答案，答完自动解锁下一批。

## 关键要点

- **状态声明**：batch-grill-me 是 **in-progress 草稿技能**（`skills/in-progress/batch-grill-me`，需源码自装）；但「规则已相当完整，值得现在就学」。grill 家族谱更新为**第五位**：grilling（内核，一次一问）/ grill-me / grill-with-docs / loop-me / batch-grill-me。
- **三核心机制**：**设计树**（每个决策是节点，下挂依赖它的子决策，父未定子没法答）；**前沿 frontier** = 前置已定、现在能问的问题集合（「树的边界每前进一步，前沿就往外推一步」）；**轮次 round** = 一轮问完当前整个前沿。
- **轮格式固定**：每题**编号**（对号入座防漏）+ 每题**推荐答案**（访谈不是考试是过方案；二十题每题带建议二十分钟过完，否则两小时）；关键规则：**依赖本轮未答问题的，留到更晚轮次，绝不猜**。
- **与 grilling「一次多问会把人问懵」不矛盾的辩护**：区别在怎么问——grilling 反对无序轰炸，batch 是结构化批量（按依赖排好+带推荐答案），用户做的是确认不是思考，认知负担完全不同。
- **事实归 agent、决策归用户**（继承并加强 grill 老纪律）：前沿问题需要环境事实（代码库结构、表字段、工具能力）时派子代理去查、**不阻塞**——只有依赖该事实的下游问题等着，其余照问。
- **完成标准**：**前沿为空**（每个分支都走过、没有静默假设），且用户确认共享理解前不动手——「访谈归访谈，实现归实现，中间隔着你的一句话」。
- **EShop 会员等级完整演示**：设计树图（根：按什么累计 → 档位/生效时机/历史订单/折扣率 + 事实分支：订单表能否支撑），三轮 3+3+3 问完；中途用户改答案（即时生效→次日生效）树照常重塑、解锁新问题；子代理报告剪掉「缺字段怎么办」分支。
- **选型标尺**：决策少需深思用 grill-me、决策多只想快速过用 batch-grill-me；直觉判断「**你答到第几个问题会开始烦**——5 个以内 grill-me，超过 10 个 batch，中间随意」。
- **边界**：batch ≠ 甩手（省思考成本不省拍板责任）；别用来问需深思的决策（定价/技术选型会淹没在琐碎里）；是访谈工具不是实现工具；**依赖关系是命根子**——发现 AI 问依赖未答的问题 = 违规，提醒重算前沿。

## 详细笔记

主系列外第 3 篇专题，主题 = 需求访谈提速（跟「访谈太慢→不想访谈→需求不清→AI 瞎猜」的敌人作战），是 grill 家族演进线（grill-me 立内核 → grill-with-docs 加沉淀 → loop-me 加定向 → batch-grill-me 加批量）的收尾一环；顺带输出通用思维「任何要梳理的事都可画成树、按依赖分层推进」。

## 与已有内容的关联

- 主题与 [[Matt Skills v1.2 grilling 重构]]、[[grill-me 实战指南]]、[[还在用 grill-with-docs]] 直接重叠（同领域三入口分工）。

## ⚠️ 矛盾 / 待澄清

- **与 v1.2 grilling 重构疑似同物（本批最重要的交叉标注）**：wiki 既有页 [[Matt Skills v1.2 grilling 重构]] 明确记载：**round-based 曾短暂作为独立技能 batch-grill-me 发布，随后并入 grilling 本体（v1.2.0，PR #593）**——v1.2 起 grilling 默认即「一轮问完 frontier、每题带编号+推荐答案」。而本文（08-14 发，晚于 v1.2 一周）把 grilling 仍描述为「一次一个问题」、把 batch-grill-me 当作未毕业独立草稿——与 wiki 的 v1.2 口径冲突。关系判断：本文大概率是 batch-grill-me 被并入 v1.2 之前的草稿快照（或作者未同步 v1.2）；引用时须注明「该机制现已并入 grilling 本体」。
- 老谭的「5 个内 grill-me / 超 10 个 batch」取舍建议恰与 v1.2 的「需要一次一问答一行配置 opt-out」互补但作者未提及。
- 把 grill-me 列为现行 coding 入口，与「grill-with-docs 取代」口径不符（老谭系列通病）。

## 相关页面

- [[Matt Skills v1.2 grilling 重构]] · [[grill-me 实战指南]] · [[mattpocock skills]] · [[超级奶爸老谭]] · [[还在用 grill-with-docs]] · [[loop-me 循环委托自动化]]
