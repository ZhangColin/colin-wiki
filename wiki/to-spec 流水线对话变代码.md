---
type: source
title: "to-spec 流水线对话变代码"
domain: [AI编程, AI工具]
tags: [mattpocock-skills, to-spec, to-tickets, implement, wayfinder, issue-tracker, 跨会话记忆]
sources: []
source_path: "raw/articles/05 - mattpocockskills 聊完需求 AI 就失忆？一条流水线把对话变成代码.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484082&idx=1&sn=ff27dcddc731d3d510190041af035335"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-07"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases: [05 - 聊完需求就失忆, 对话是沙文档是石]
---

# to-spec 流水线对话变代码

> 一句话要点：访谈结论只活在聊天记录里、换个会话就丢；解法是 `grill → to-spec → to-tickets → implement` 流水线——对话是沙，文档是石，且解释它为什么不是瀑布式、何时别走全套。

## 关键要点

- **问题**：访谈四问的结论「躺在聊天记录里」，新会话 AI 全忘，等于重新访谈半小时。聊天记录是最不靠谱的存档（无结构、没法评审）。
- **流水线全图**：grill 访谈结论 → `to-spec`（对话→规格文档）→ `to-tickets`（规格→工单）→ `implement`（工单→代码）→ 可运行功能。前一环节输出 = 后一环节输入。
- **to-spec**：把当前对话整理成规格**发布到 issue tracker**（不是本地备忘录——「issue tracker 是 AI 和人都能读的公共场地」）。只做沉淀、不重新访谈。**关键澄清：规格不是瀑布式蓝图**——记录的是「已访谈确认过的决策」不是「预测未来需求」；当预测用是滥用。1880 年代电力普及类比：电动机替代蒸汽机但工厂布局不变 = 新工具走老流程。
- **to-tickets**：把规格拆成带「阻塞」关系的工单（依赖图保执行顺序）；每张工单是「探路」的最小单元——写完一个验证一个，防 AI 改到第五个文件忘了第一个文件。
- **implement 三条纪律**：① 每个工单边界处驱动 tdd；② 提交前 code-review 双轴审查（编码标准 + 忠实实现规格）；③ **严守工单边界**——只做工单写的事，不顺手重构不扩范围（越界 = 责任无法归属）。
- **wayfinder（超大工作先探路）**：`to-tickets` 管「一个功能」规模；「重建整个订单体系」这种项目级工作先 wayfinder 画调查工单地图、一张张解决，路清晰后再回 to-tickets。普通开发者 90% 场景用不到。作者注：与 Strangler Fig 同源（大改造靠一块块替换，不靠大爆炸）。
- **为什么拆（三可）**：每步可审查、每步可复用（AI 掉线换人结论还在）、每步可验证（跑偏在第一个工单发现而非全做完）。**流程 vs 仪式的判据 = 每一步能不能验证**。
- **反仪式警示**：若变成「每次改动都必须走全套」= 「敏捷产业复合体（agile industrial complex）的 AI 版」，流程服务于项目而非项目服务于流程。
- **何时不要用**：改动极小；探索性工作（验证「满减放数据库还是写死」，代码用完就扔）；团队只想快速试 AI。
- 金句收尾：对话是沙，文档是石——磨平自己和昨天的自己之间的信息差。

## 详细笔记

系列第 5 篇承接 04 篇访谈课，解决「访谈完了怎么把结论留下来」的跨会话问题，是 v1.1 命名（to-spec/to-tickets/implement）下的流水线教学课。作者现身说法：曾跳过 to-spec 两次、踩坑两次，「多花十分钟写规格，省下第二天重新访谈半小时」。

## 与已有内容的关联

- 与 [[mattpocock skills 标准工作流]] 主流程同构：该页 v1.0 记 `grill-with-docs → to-prd → to-issues → tdd/implement`；本文用 v1.1+ 命名（wiki 已注明改名），并补充 implement 三纪律、工单阻塞依赖图等执行细节——细化补充，无冲突。
- wayfinder 一节与 [[Matt Pocock wayfinder handoff 接力协议]] 重叠：「map 清空后必须回 main flow」一致；本文未触及 decision ticket 等 v1.2 深水内容。
- 「发布到 issue tracker」呼应 setup 三问（[[Matt Pocock setup-matt-pocock-skills 配置落地]]）。

## ⚠️ 矛盾 / 待澄清

- **入口技能未点名**：流水线首环写作「grill 访谈」（泛指）；本文示例语境默认 04 篇的 grill-me 访谈路径，读者易得出「grill-me → to-spec」链条——与官方推荐链路（grill-with-docs → to-spec → to-tickets → tdd，见 [[grill-me 实战指南]]）有出入。按老谭系列 v1.1 快照口径理解，已在本页标注。
- **版本信息缺失**：本文 08-07 发布（v1.2.0 已于 08-05 发布），但全文无 v1.2 内容，也未说明依据版本——按「v1.1 时代行为口径」标注。

## 相关页面

- [[mattpocock skills]] · [[超级奶爸老谭]] · [[mattpocock skills 标准工作流]] · [[Matt Pocock wayfinder handoff 接力协议]] · [[grill-me 实战指南]] · [[Matt Pocock setup-matt-pocock-skills 配置落地]]
