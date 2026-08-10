---
type: source
title: "Matt Pocock writing-great-skills 八词诊断"
domain: [AI编程]
tags: [Agent Skills, writing-great-skills, leading word, no-op, skill 拆分诊断]
sources: []
source_path: "raw/articles/从一句\"认真 X\"到可执行 Agent Skill：Matt Pocock 的 8 个词帮你砍掉 prompt 里的 no-op.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491506&idx=1&sn=cbfadaa25b67ebc2d27638e6939a17da"
author: "[[运维有术]]"
date_published: "2026-07-24"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [writing-great-skills 八词诊断, 认真X 改造成 skill, no-op 砍除, tight-review 示例]
---

# Matt Pocock writing-great-skills 八词诊断

> 一句话要点：把 writing-great-skills 当作"去除手术清单"而非方法论手册，用 8 个词诊断自己的 prompt，并以"认真 review"改造成可执行 `tight-review` skill 为完整示例。

## 关键要点

- 核心论点："认真 X"类口号在 skill 里是 no-op（SKILL.md:82）——模型读了点头然后按默认行为跑；判定标准="删掉它，模型行为变没变"。
- writing-great-skills 把症状归四轴：Invocation（怎么触发）、Information Hierarchy（怎么编排）、Steering（怎么塑形）、Pruning（怎么精简），每症状配 cure。
- **8 个诊断词**：①Leading word（pretrained 词锚定行为区，如 `tight`=`fast,deterministic,low-overhead`、`red`=`a loop you believe in`）；②Completion criterion（done 条件）；③No-op（模型默认就做、写了白写）；④Duplication（同义多处）；⑤Sediment（失效内容堆积）；⑥Sprawl（每行都活但太长）；⑦Premature completion（step 没完就滑下一步）；⑧Negation（"不要 X"反激活 X，cure=写正面目标）。
- Information Hierarchy 三档：in-skill step（有序动作带 completion criterion）/ in-skill reference（定义规则可有可无）/ external reference（推外部文件）；判断标准"push too little→顶层臃肿；push too much→隐藏 agent 实际需要的信息"。
- Invocation 决策：model-invoked（agent 自动+人工，description 保留带 trigger，永远占 context）vs user-invoked（仅人工，零 context load，frontmatter `disable-model-invocation: true`）。
- **tight-review 完整改造示例**（作者构造，非官方范例）：3 phase——Phase1 取 diff、Phase2 per-file 循环查三件事（match spec/保 public interface/无 TODO 无 issue）每件出 verdict、Phase3 Completion（每文件三检查全有 verdict 才 done）。
- skill 最小模板：description 至少 2 个 trigger 短语；至少一 phase 带 per- 循环；completion criterion 用可数名词禁用"大致/尽量"；失败处理用祈使句。
- 六个真实 skill 结构示范：grilling（12 行，leading word=`relentlessly`）、tdd（36 行，leading word=`red`）、prototype（26 行，leading word=`throwaway`）、handoff（16 行，禁止 duplicate 已有 artifact）、implement（15 行 orchestrator）、grill-me（7 行纯路由）。
- 边界提醒：leading word 效果是机制描述非量化承诺；`disable-model-invocation` 是 Claude Code 实现，Codex/Cursor/Continue 各有不完全相同字段，非全平台通用。

## 详细笔记

作者严格区分三类内容来源：【源码】（GLOSSARY.md/SKILL.md 原文）、【分析】（基于源码推理）、【迁移】（个人建议）。诊断清单六步：挑一条 prompt rule→跑 no-op test→若有用则展开→8 词诊断→看 Invocation→看 Information Hierarchy。最直接判断标准："most prose that fails should go, not be rewritten"。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[mattpocock skills 推荐工作流速查]]、[[Skills 设计方法论]]
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[Matt Pocock 6 个 skill 工程师本能]]、[[Matt Pocock 三个 skill 结构词汇]]、[[MattPocock Skills 21 个 skill 工程纪律体系]]

## ⚠️ 矛盾 / 待澄清

- 本文"6 个真实 skill 示范"中 tdd 描述含"red→green→refactor（refactor 不在 loop 里）"；而 [[MattPocock Skills 21 个 skill 工程纪律体系]] 称"v1.1.0 tdd 移除 refactor 阶段、refactor 划给 code-review"。两者一致（refactor 不在 tdd loop 内），但本文措辞易误读——引用时采用总览文"移除、划给 code-review"表述更准。

## 相关页面

- [[mattpocock skills]]
- [[运维有术]]
- [[Matt Pocock 6 个 skill 工程师本能]]
- [[Skills 设计方法论]]
