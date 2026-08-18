---
type: source
title: "Matt Pocock Skills v1.2 更新全景"
domain: [AI编程]
tags: [mattpocock-skills, v1.2, wizard, to-questionnaire, wait-what, prototype, 双平台]
sources: []
source_path: "raw/articles/Matt Pocock Skills v1.2：3 天 3 版，3 个新技能 + 1 个重塑，我该更新工作流里的什么.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491686&idx=1&sn=69445fc6cb6e6050ce5703a19910522a"
author: "[[运维有术]]"
date_published: "2026-08-14"
created: 2026-08-17
updated: 2026-08-17
status: active
aliases: [v1.2 全家桶, v1.2 边界位移, 3 天 3 版]
---

# Matt Pocock Skills v1.2 更新全景

> 一句话要点：[[运维有术]] 拆开 [[mattpocock skills]] v1.2.0-1.2.3 的 CHANGELOG 与源码，指出真正值得跟进的是 **4 处人机分工边界的位移**——wizard 接走人墙步骤、to-questionnaire 挖别人脑子、wait-what 七词纠偏、prototype 从删除变留档。

## 关键要点

- v1.2.0 于 2026-08-05 发布，同日 1.2.2、次日 1.2.3；仓库 **213.4k star**（写作时）。
- **wizard**：从 in-progress 毕业进 Engineering，**转 model-invoked**——agent 在对话中撞到"只有人能做的步骤"（配密钥/开 dashboard/一次性迁移）时自动伸手；原话 "Work an agent can do, an agent should do"。详见 [[Matt Pocock wizard 人墙自动化]]。
- **to-questionnaire**：毕业进 Productivity；核心 move 是 **grill the send, not the subject**——你无法回答的问题不要 grill 文档，只 interview 发送对象；产物 `to-questionnaire-<slug>.md` 固定结构（Purpose/收件人用途/按重要度排序的问题区/Anything else）。与 `/grill-me` 互为镜像：grill-me 挖你自己脑子，to-questionnaire 挖别人脑子。第三方定位：**"a patch for the fact that agents are still hard to collaborate around"**。
- **wait-what**：新增 7 行 user-invoked，全文一行正文——让模型重新讲一遍（ASD-STE100 简化技术英语 + CONTEXT.md ubiquitous language），不是压缩。**命名的是听者的状态（没听懂），不是输出形态**；"concision skills fail by growing" 保持极短。边界：修复一条消息不预防下一条，预防靠 `/grill-with-docs` 建共享语言。shadcn/ui 作者发推玩梗。
- **prototype 重塑**：throwaway 不再等于删除——**原型是 primary source**；logic 分支产物从 TUI 变**单个可分享 HTML 文件**（无 build/无 server，带标签 state panel + free-play buttons + tabbed guided walkthroughs，面向非开发者）；验证完的问题归档到 `prototype/<name>` 分支 + issue 上留 context pointer；UI 分支 `?variant=` 上限 5 个变体，胜出 fold 进真实代码，输家和 switcher 上 throwaway 分支。金句：**"throwaway 定义的是命题，不是代码本体"**；归档 ≠ 生产化。
- **writing-great-skills → writing-for-agents** 改名，转 model-invoked（覆盖所有 agent 消费的文档）。
- **双平台**：Claude plugin（`claude plugins install mattpocock-skills`，2026-08-05 起被官方 marketplace 收录，订阅制自动更新，24 个 promoted skill）vs skills.sh（`npx skills@latest add`，fork 制手动更新）——两条路线互斥，装两份每个 skill 出现两次。**Codex native plugin 未上线**（ADR 0002：plugin.json 只接受单路径字符串）；Codex 走元数据（每 SKILL.md 旁 `agents/openai.yaml` + AGENTS.md 为 CLAUDE.md symlink）。invocation 语义双平台对齐：`disable-model-invocation: true` ⇔ `policy.allow_implicit_invocation: false`。
- v1.2.0 顺手删掉 6 个旧 skill；wayfinder 引入 **decision ticket** 概念，research 票由 subagent 并行烧掉（`research/<name>` 分支 + context pointer）——与 prototype 留档同一套思路。
- patch 信号：v1.2 主战场是**让同一套技能在不同 harness 上行为一致**（#766 writing-for-agents 在 Codex 恢复 model-invokable；#779 diagnosing-bugs 加 Redact 章节；#781 去 Claude 专用工具名；#783 wizard 删时间估算）。
- 提醒：**不要指望更新到 v1.2 就自动提速**——价值在边界不在速度；社区已有新手反馈 "I couldn't build anything"（全家桶编排成本真实痛点）。

## 详细笔记

按身份跟进表：Claude Code 用户→plugin+wizard+prototype；Codex 用户→skills.sh+wait-what+openai.yaml；想学人墙自动化→抄 wizard template.sh；想建 domain vocabulary→wait-what+CONTEXT.md。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[HITL 与 AFK]]、[[Skills 设计方法论]]、[[Harness Engineering]]（harness-neutral）
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[Matt Skills v1.2 grilling 重构]]（同日背景）、[[Matt Pocock wizard 人墙自动化]]（wizard 深拆）、[[Matt Pocock Prototype 保真度方法论]]（v1.1 玩法，本文为其 v1.2 变更源）、[[MattPocock Skills 21 个 skill 工程纪律体系]]、[[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]]

## ⚠️ 矛盾 / 待澄清

- **版本演进**：本文是 v1.1→v1.2 的权威中文解读；[[Matt Pocock Prototype 保真度方法论]] 记录的"TUI shell + 用完删除"在 v1.2 已变（单文件 HTML + 留档）——旧页已加 v1.2 注记。
- **skill 数口径再变**：v1.2 plugin 显式列出 **24 个 promoted skill**（此前 v1.1 源码核对为 18）——口径随版本演进，引用须带版本号。
- star 数：213.4k（本文 08-14 写作）vs [[Matt Skills v1.2 grilling 重构]] 212.2K（08-10 检索）——时间点正常增长，非矛盾；增长曲线 5 月 77K → 7 月 170K → 8 月 212K。
- 原文一处笔误："新技能三连：/wait-wait"应为 **wait-what**。

## 相关页面

- [[mattpocock skills]] · [[Matt Skills v1.2 grilling 重构]] · [[Matt Pocock wizard 人墙自动化]] · [[Matt Pocock Prototype 保真度方法论]] · [[HITL 与 AFK]] · [[Matt Pocock]] · [[运维有术]] · [[Skills 设计方法论]]
