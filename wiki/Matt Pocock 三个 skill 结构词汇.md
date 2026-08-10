---
type: source
title: "Matt Pocock 三个 skill 结构词汇"
domain: [AI编程]
tags: [Agent Skills, codebase-design, prototype, improve-codebase-architecture, deletion test]
sources: []
source_path: "raw/articles/Matt Pocock 三个 skill：4 个核心词 + 1 张决策表，让 Agent 先问对问题.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491498&idx=1&sn=6a25bfdfd85e0ceae41a45f21b81505c"
author: "[[运维有术]]"
date_published: "2026-07-23"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [codebase-design/prototype/improve 三 skill, 术哥结构词汇, deletion test 决策表]
---

# Matt Pocock 三个 skill 结构词汇

> 一句话要点：通读 codebase-design / prototype / improve-codebase-architecture 三个 SKILL.md，提炼 4 个结构核心词（deep module/seam/locality/leverage）+ 1 张决策表，主张"词汇对了争论基线才能对齐"，并强调这些 skill 是约束而非银弹。

## 关键要点

- 三 skill 分工：codebase-design 提供共享词汇与判断原则（不直接给方案）；prototype 用 throwaway code 验证未成型问题（不写测试/不上线）；improve-codebase-architecture 列候选让你挑（不直接画最终接口）。
- codebase-design 硬规则："Use these terms exactly — don't substitute"，必须用 module/interface/seam/adapter，禁用 component/API/boundary/layer——目的是防 Agent 冒出"代码更干净"空话。
- 4 个核心词：**deep module**（Ousterhout《A Philosophy of Software Architecture》，interface 小而稳定、藏大量实现复杂度）；**seam**（Feathers《修改遗留代码的有效方法》，不改该处就能改行为的位置，"One adapter means a hypothetical seam. Two adapters means a real one"）；**locality**（改动落多少文件，跨文件越多越差）；**leverage**（被多少地方调用，高 leverage 值得重点评估）。
- 隐含 testability 三原则：依赖注入而非 `new`、返回结果而非副作用、小 surface area——是判断 deep module 是否真深的硬指标。
- prototype 定义锁死："A prototype is throwaway code that answers a question. The question decides the shape."；LOGIC.md 硬约束"Don't add tests... Don't wire it to the real database. Don't generalise."；只保留 TUI/UI shell 后的纯模块，shell 扔 throwaway branch 作 primary source。
- UI prototype 走 `?variant=` 在已有页面切 3 个 structurally different 变体（不是颜色不同是结构不同），底部居中浮动条左右切换，production build 必须隐藏。
- improve-codebase-architecture 第一阶段"Scope before you scan"：先看 `git log --oneline` 找 hot spots（被多 feature 反复触碰处），**不扫整个仓库**。
- **deletion test**（codebase-design 核心工具）："Imagine deleting the module. If complexity vanishes, it was a pass-through. If complexity reappears across N callers, it was earning its keep."
- HTML 候选报告每卡含 Files/Problem/Solution/Benefits（locality+leverage+tests）/Before-After/Recommendation strength（Strong/Worth exploring/Speculative 三档）；写到 OS temp dir 不进 repo。
- 反直觉设计："Do NOT propose interfaces yet"——Agent 先列候选问用户，因直接给接口会让用户评估"设计好不好"而非"问题值不值得修"。
- 加深后旧 unit test 必须删（DEEPENING.md）：浅模块加深后，旧实现细节测试成废。
- 社区 issue 反馈的真实问题：#449（codebase-design 被当流程而非词汇表，Agent 自行探索烧 105k token）；#384（禁止自动调用后 Agent 仍在文字里按名推荐 prototype）；#458（TS 大型代码库只给小抽象建议）；#640（skills 倾向切英文破坏中文会话连续性）。

## 详细笔记

Question/Run 模板（基于 LOGIC.md 整理）：`# Question` 一句话说要回答什么；`# Run` 一条命令跑起来；`# State surface` 每次 action 后状态怎么打印。deletion test 模板：`# Module`+`# Delete scenario`+`# Verdict`（pass-through→删/earning its keep→留或加深/unclear→先不决定）。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[mattpocock skills 推荐工作流速查]]
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[Matt Pocock 6 个 skill 工程师本能]]、[[Matt Pocock writing-great-skills 八词诊断]]、[[MattPocock Skills 21 个 skill 工程纪律体系]]、[[Matt Pocock Prototype 保真度方法论]]

## ⚠️ 矛盾 / 待澄清

- 本文与 [[Matt Pocock 6 个 skill 工程师本能]] 对 codebase-design/prototype/improve 的描述一致，deletion test、seam 判定（One vs Two adapter）措辞相同，无矛盾。两者均强调"Agent 自执行风险真实存在"（#449/#384），互相印证。

## 相关页面

- [[mattpocock skills]]
- [[运维有术]]
- [[Matt Pocock 6 个 skill 工程师本能]]
- [[Matt Pocock Prototype 保真度方法论]]
