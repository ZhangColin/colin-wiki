---
type: source
title: "teach 跨会话教学"
domain: [AI编程, AI工具, 学习方法]
tags: [mattpocock-skills, teach, 跨会话学习, 学习科学, 合意困难, 最近发展区]
sources: []
source_path: "raw/articles/新知识学完就忘记了？mattpocockskills 来助力.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484137&idx=1&sn=14ded14c8df30ddff9e46b3a4eb553c7"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-17"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases: [teach 专题, 新知识学完就忘记]
---

# teach 跨会话教学

> 一句话要点：`/teach` 把「学习」从聊天变成工程——在当前目录建有状态教学工作区，进度落盘、跨会话延续，并用学习科学（流利 vs 存储、合意困难）解释「学完就忘」。

## 关键要点

- **状态声明（与 loop-me/batch 相反）**：teach 是 **正式技能**——productivity 桶少数正式成员，在官方 README 和 ask-matt 路由器里挂号、有独立文档页。
- **一句话定义**：跨会话教用户一个新技能或概念，把当前目录变成有状态教学工作区；与 loop-me 的「有状态工作区」同一思路（凡要跨会话持续的都靠目录文件撑状态）。
- **工作区七件套**：MISSION.md（为什么学，锚定一切教学）、RESOURCES.md（高质量资源清单）、NOTES.md（学习偏好）、lessons/（课程，官方设定自包含 HTML，一课一个小胜利）、reference/（压缩精华速查，**会被一直重访**，课程很少被重访）、learning-records/（编号 0001 递增，**类似 ADR**：记非显然教训+进度+「下一步学什么」，AI 据此算下一步教什么）、assets/（复用组件，共享样式表是每个工作区第一个挣到的）。
- **知识/技能/智慧三分**：Knowledge 靠高质量高信任资源；Skills 靠高度相关的交互式课程（带练习、即时反馈——光看不练不是技能）；Wisdom 来自真实互动，**AI 教不了**——涉及智慧的问题 AI 先答但最终把你引向社区。
- **流利 vs 存储强度**（「学完就忘」的机制解释）：流利 = 当下的提取能力（刚读完觉得全会是工作记忆还热乎的幻觉）；存储 = 长期保持，才是目标。应对 = **合意困难**：提取练习（先回忆再看）、间隔（分散练习）、交错（混练相关主题，仅技能）。细节：测验每个答案字数必须一致，防靠格式猜答案。
- **最近发展区**：每课挑战「刚好够」；没指定学什么时，teach 读记录+对使命自己判断下一步。
- **硬规矩**：**RESOURCES.md 填好之前，教学重点是找高质量资源，绝不信任 AI 自己的参数化知识**（「你以为 AI 在教你，其实它在背课文，背错了你也不知道」）。
- **其他纪律**：术语表一旦建立每课必须遵守；使命会变（更新 MISSION.md + 加学习记录 + **改前跟你确认**，不能偷偷改）；可改编——课程格式 HTML 可换 Markdown，Matt README 明言「hack around with them, make them your own」，工具不是信仰。
- **手把手流程**：/teach → 答「为什么学」（不问学什么细节，问 why）→ 填 RESOURCES → 第一课 10 分钟讲一件事（如 JUnit 5 断言）+ 推一手来源 → 学习记录 0001 → 下节课读记录续教。

## 详细笔记

主系列外第 4 篇专题，主题 = 把「学得会」变成「学得好」——补上系列里「学习/输入侧」的拼图，与 loop-me（委托侧）同为「把重复的事工程化」的延伸。wiki 首个 teach 深度源。

## 与已有内容的关联

- wiki 暂无 teach 专门页，本文是首个深度源。
- 「学习记录类似 ADR」与 [[还在用 grill-with-docs]] 的 ADR 三门槛同域概念；有状态工作区与 [[loop-me 循环委托自动化]] 同思路。
- 弱关联 [[LLM Wiki 方法论 gist]]（学习/知识管理主题）。

## ⚠️ 矛盾 / 待澄清

- 无重大矛盾。文中称 teach「在官方 README 和 ask-matt 路由器里挂号」——与 ask-matt 路由器仍存活的叙述一致（07 课也用 ask-matt），与 v1.2 全景不冲突（v1.2 三新技能是 wizard/to-questionnaire/wait-what，teach 属此前已存在的正式技能）。

## 相关页面

- [[mattpocock skills]] · [[超级奶爸老谭]] · [[loop-me 循环委托自动化]] · [[还在用 grill-with-docs]] · [[Matt Pocock]] · [[LLM Wiki 方法论 gist]]
