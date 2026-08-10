---
type: source
title: "Matt Pocock 6 个 skill 工程师本能"
domain: [AI编程]
tags: [Agent Skills, leading word, TDD, diagnosing-bugs, codebase-design, prototype]
sources: []
source_path: "raw/articles/AI 代码总要我返工：Matt Pocock 的 6 个 skill 让它学会工程师的本能.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491433&idx=1&sn=10ee5393ccab9ea5a4105082823027db"
author: "[[运维有术]]"
date_published: "2026-07-17"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [6 个 skill 让 Agent 学会工程师本能, 术哥 6 skill 纪律样张]
---

# Matt Pocock 6 个 skill 工程师本能

> 一句话要点：术哥挑出 6 个 promoted skill 作为"工程纪律样张"，核心论点是这些 skill 用"可检查的 completion criterion + 共享词汇 + 写进流程的失败模式"把工程纪律变成可审阅的 workflow artifact。

## 关键要点

- 仓库定位原文："My agent skills that I use every day to do real engineering - not vibe coding."；真正要说的是"Software engineering fundamentals matter more than ever"。
- **writing-great-skills**（productivity，元规范）：定义"skill 的职责是从随机系统里挤出确定性"；核心词 leading word（pretrained 紧凑概念，如把"fast, deterministic, low-overhead"折叠成 `tight`）；点名失败模式：duplication、sediment、sprawl、negation、premature completion、no-op。
- **codebase-design**：围绕 deep module（Ousterhout，小 interface 大行为）和 seam（Feathers，无需直接编辑就能改行为的位置）；判定"**One adapter means a hypothetical seam. Two adapters means a real one**"；可抄工具=deletion test（删掉模块，复杂度消失=pass-through，散回 N 个调用者=earning its keep）。
- **tdd**：收窄到只剩 red→green loop，要求测 public interface 行为不测实现；核心词 pre-agreed seam + tracer bullet；三反模式：Implementation-coupled、Tautological、Horizontal slicing。
- **diagnosing-bugs**：Phase 1 是核心（"This is the skill"），必须产出"已运行至少一次的 red-capable command"并满足 red-capable/deterministic/fast/agent-runnable；防止的"exact failure"=jumping straight to a hypothesis；要求 3-5 个排序可证伪假设对抗 single-hypothesis anchoring。
- **improve-codebase-architecture**：复用 codebase-design 词汇，目标是把 shallow module 变 deep module；报告阶段"Do NOT propose interfaces yet"，只列候选让用户选；每条收益必须回到 locality/leverage。
- **prototype**：定义极窄"throwaway code that answers a question"，问题决定形状；分支选错浪费整个原型；可抄=Question/Run 两行模板。
- 四种共同模式：①用 completion criterion 卡门而非用形容词劝告；②用共享词汇压缩决策空间；③把失败模式直接写进流程；④人的控制权不被吞掉。
- 三件下班前能做的小事：bug issue 模板加 `One command:`；对反复改动模块跑 deletion test；PR 模板加 `Question:`/`Run:`（可再加 `Throwaway? Y/N`）。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[mattpocock skills 推荐工作流速查]]
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[Matt Pocock writing-great-skills 八词诊断]]、[[Matt Pocock 三个 skill 结构词汇]]、[[MattPocock Skills 21 个 skill 工程纪律体系]]

## ⚠️ 矛盾 / 待澄清

- 本文引 diagnosing-bugs 概括句时区分两个版本：AI Hero 站点页面用"The tight loop is the skill"，SKILL.md 第 12 行用更朴素的"This is the skill"——同义不同措辞，非实质矛盾，记录供引用时注意出处选择。

## 相关页面

- [[mattpocock skills]]
- [[运维有术]]
- [[Matt Pocock writing-great-skills 八词诊断]]
- [[Matt Pocock 三个 skill 结构词汇]]
