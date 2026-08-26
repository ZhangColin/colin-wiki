---
type: source
title: "Harness Engineering 三次演进与头部实践"
domain: [AI编程, 软件工程]
tags: [Harness Engineering, Prompt Engineering, Context Engineering, Anthropic, OpenAI, LangChain, 渐进式披露]
sources: []
source_path: "raw/articles/一文带你弄懂 AI 圈爆火的新概念：Harness Engineering.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/gs5ndvlMqM-Y4jg1_D2aFw"
author: "[[ConardLi]]"
date_published: "2026-04-03"
created: 2026-08-26
updated: 2026-08-26
status: active
aliases: [Harness 三次演进, 一文弄懂 Harness Engineering]
---

# Harness Engineering 三次演进与头部实践

> 一句话要点：[[ConardLi]]（code秘密花园）的 Harness Engineering 科普/理论文——AI 工程重心三次迁移（**Prompt 解决表达问题 → Context 解决信息问题 → Harness 解决过程问题**，层层递进、后者包含前者）+ Harness 六层拆解 + OpenAI/Anthropic/LangChain 头部实践。本文正是 [[Harness Engineering 团队落地规范]] 文中引用的「对 Harness 做了详细讲解」的那篇（2026-04-03 发布，早于腾讯落地文）。

## 关键要点

- **作者实证开场**：帮朋友团队调 Agent，不动模型、不改提示词，只重设计任务拆解/状态管理/校验机制/失败恢复，成功率 70% → 95%+——「我改的那些东西，就是 Harness」。
- **三次演进**（层层递进，后者包含前者）：
  - Prompt Engineering：解决「表达问题」（怎么说）——模型不是不会，是没讲清楚；本质是「塑造局部概率空间」，天花板在于不能替代事实本身。
  - Context Engineering：解决「信息问题」（给什么）——「你有没有把对的信息，以对的形式，在对的时刻，交给模型」；RAG 是起点，Agent Skills 的渐进式披露（元数据 ~50 Token / 指令 ~500 Token / 资源按需三层）是又一典型实践；局限性：只作用在输入侧。
  - Harness Engineering：解决「过程问题」——模型从「回答问题」走向「执行任务」，系统必须**驾驭过程**：持续监督、约束、纠偏、收敛。
- **Harness 六层拆解**（作者个人拆解，与腾讯 6 支柱高度同构）：上下文管理（角色目标定义/信息选择裁剪/结构化组织）、工具系统（给什么工具/何时调用/结果如何喂回）、执行编排（步骤划分/决策节点/中间产物/终止条件/异常处理）、状态与记忆（临时状态/会话记忆/长期偏好三分）、评估与观测（输出验收/环境验证/自动测试/过程观测/质量归因）、约束校验与失败恢复（约束/校验/恢复 = 鲁棒性设计）。
- **定义**（LangChain 工程师）：Agent = Model + Harness；Harness = Agent - Model。金句：「模型的能力上限由 OpenAI、Anthropic 决定，Harness 由工程师决定」。
- **Anthropic 实践**（《Harness design for long-running application development》）：两个失败模式——①「上下文焦虑」（接近窗口极限时模型想赶紧收尾）；②「自评失真」（自己评自己偏乐观）。解法：① **Context Reset** 而非 Compaction——压缩不能解决上下文焦虑，换干净上下文的新 Agent + 交班交接清楚才有效（像进程重启 vs 清理缓存）；② **generator + evaluator 分离**（后扩展为 planner + generator + evaluator）——「生产与验收必须分离」，Evaluator 不是读代码打分而是**实际操作页面、跑交互**（带环境的验证）。
- **OpenAI 实践**（100% 由 Agent 编写、耗时仅为人工 1/10）：铁律「人类不写代码，只设计环境」，工程师三件事——拆解意图/补全能力（Agent 失败时问「环境里缺了什么让它失败」）/建立反馈回路。金句：「**修复方案几乎从来不是'更努力'，而是'缺了什么结构性的能力'**」。渐进式披露：巨型 AGENTS.md 失败 → ~100 行目录页 + **CI 自动校验文档新鲜度与交叉引用** + 「文档园丁」Agent 定期扫描过时文档提 PR。让 Agent「看见」应用：单次 Codex 运行连续工作 6 小时+、自己跑应用验 bug 提 PR。架构约束写进系统：固定分层（Types/Config/Repo/Service/Runtime/UI）+ 检查结果自带修复提示 + 后台 Agent 持续扫描打质量分、趁问题还小就修掉。
- **LangChain 数据**：底层模型不变、仅改造 Harness，自家代码智能体在 Terminal Bench 2.0 从 52.8 → 66.5（Top 30 开外 → Top 5）——Harness 质变的最硬证据。
- **三家本质同构**：都在回答六个问题——模型看到什么/能做什么/下一步做什么/如何连续工作/怎么知道对不对/出错怎么拉回来。
- 金句：「真正决定 AI 产品上限的，也许是模型；但真正决定 AI 产品能否落地、能否稳定交付的，往往是 Harness」；「AI 落地的核心挑战，正从'让模型显得聪明'转向'让模型在真实世界里稳定工作'」。

## 与已有内容的关联

- 本文是 [[Harness Engineering 团队落地规范]]（腾讯）中引用的 Harness 详解文，两者构成「理论 + 落地」配对：腾讯文 6 支柱与本文六层拆解高度同构。
- Anthropic 的「生产与验收必须分离」与 [[子代理驱动开发]]、[[Agent 测评 Eval]]（自评失真）直接呼应；Context Reset 与 [[Matt Pocock 会话边界五问决策树]]/[[smart zone]] 的会话边界管理同题异构。
- 渐进式披露三层（~50/~500/按需 Token）与 [[Skills 设计方法论]]（Perplexity 三层成本）表述一致，互相印证。

## ⚠️ 矛盾 / 待澄清

- 六层拆解为作者「个人拆解」自述，与腾讯 6 支柱的命名略有差异（本文「约束、校验与失败恢复」合一 vs 腾讯「约束与恢复」；腾讯另有「执行编排与多 Agent 协作」更强调多 Agent），同构非冲突。
- OpenAI「100 万行/1/10 耗时」为 OpenAI 自述口径（与腾讯文一致），无第三方复核。
- 文章发布于 2026-04-03，早于腾讯落地文（07-17）与部分头部文章，属概念早期科普，部分细节（如 LangChain 榜单分数）为当时数据。

## 相关页面

- [[Harness Engineering]] · [[ConardLi]] · [[Harness Engineering 团队落地规范]] · [[Skills 设计方法论]] · [[Agent 测评 Eval]] · [[子代理驱动开发]] · [[smart zone]]
