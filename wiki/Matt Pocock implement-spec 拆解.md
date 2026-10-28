---
type: source
title: "Matt Pocock implement-spec 拆解"
domain: [AI编程, 软件工程]
tags: [mattpocock skills, implement-spec, v1.3, 任务图, frontier, 集成分支, 编排会话, 并行子代理, worktree]
sources: []
source_path: "raw/articles/mattpocockskills implement-spec 拆解，一次跑完整份 spec.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/rrRRTgkarRoZL-ddSIBefg"
author: "[[超级奶爸老谭]]"
date_published: "2026-10-15"
created: 2026-10-28
updated: 2026-10-28
status: active
aliases: [implement-spec 拆解, 一次跑完整份 spec]
---

# Matt Pocock implement-spec 拆解

> 一句话要点：`implement-spec` 把「整份 spec 一次跑完」——编排会话按**任务图**派实现子代理（各 worktree）、按**前沿**维持最大并发、成果快进并进**集成分支**、终点跑一次 code-review 关票。九个步骤没有一步是写代码，它只决定谁在什么时候写什么。**「它交付的是草稿，评审才完成它。」**

## 关键要点

- **版本口径**：2026-08-21 以 in-progress 进仓库，**2026-10-04 随 v1.3.0 毕业**进 Engineering 桶（与 retro、pr 同批，PR #1120；v1.3.1 为当前最新）。手动触发（`disable-model-invocation: true` + openai.yaml 双保险）——「编排权在你，不在模型」。
- **工作方式**：编排 agent 把每张票交给一个 implementer subagent（各自 git worktree），完成的分支由 merger subagent 并进一条集成分支；全部落地后在集成分支跑一次 code-review，按 issue tracker 方式逐张关票。SKILL.md 全文 40 行、九步骤。
- **三核心概念 + 上下文指针**：
  - **任务图**：票不是步骤清单，是带阻塞边的图——「定节奏的是图的形状，不是票在 tracker 上的排列顺序」；
  - **前沿 (frontier)**：阻塞项全部落地的票的集合，前沿上每张同时跑，要最大并发；
  - **集成分支**：成果唯一落点；实现子代理报告完成前先把集成分支 tip 合进自己分支 → 落地即快进，冲突不堆积；
  - **上下文指针**：与子代理通信保持稀疏，spec/票/探索笔记/commit 用指针不复制——子代理 prompt 小，编排窗口留给任务图。
- **选型**：spec 已拆带阻塞边的票、想一次落地 → `/implement-spec`；一次一票、小活、想逐票读代码 → `/implement`。三前置：有 issue tracker / 票带阻塞边（没边图是平的，所有票第一秒同时开跑）/ harness 支持后台子代理+独立 worktree（串行 harness 上它只是更慢的 implement）。澄清：不依赖 GitHub，本地 markdown tracker 可全程离线。
- **与 /implement 七维差异**：调度者（你逐票 vs 编排会话一次）/ 并发（同一 checkout 并行会互踩——官方收录过 amend 落到别人 commit、stash 消失的报告）/ 分支 / 评审时机（每票 vs 终点一次）/ 收尾（到 commit 为止 vs 按 tracker 关票）/ seam 约定（现场约定 vs 必须写进 spec 或票）。代价明码标价：**你不再逐票读代码，审查的是集成分支，在终点**。
- **FAQ 六坑（全部来自真实报告）**：① code-review 只能终点跑一次（中途跑会把未建的票读成失败，曾有 4 小时评审-修复循环）；② 并行冲突只是推迟到合并时刻（web/mobile 票把同一字符串各写成 blockedSince/blockedOn——共享文件的票加阻塞边串行，或在共享笔记钉死名字）；③ GitHub blocked-by 计数只在阻塞票**关闭**时下降，而票在 PR 合并才关——「tracker 是起点的正确图源，却是中途的过期图源」→ 编排者自记已合并票算前沿；④ worktree 只装 git 跟踪内容，读 gitignored 夹具的测试**静默跳过并报绿**——依赖未跟踪材料的票回主 checkout 跑；⑤ tdd 现在每个实现子代理都驱动（早期版本红绿循环会停），但无交互式 seam 约定——想钉死写进 spec/票；⑥ 不替代 Sandcastle/AFK 脚本——真正无人值守用确定性循环（shell/CI）更快更便宜更可靠，implement-spec 的价值是 agent 在编排位、你能看能纠偏。

## 详细笔记

[[超级奶爸老谭]]「共渡时艰 · 拆解」栏目（源码级拆解，风格区别于两大教学系列）：基于本地克隆 main 分支 commit 49dd158（2026-10-09）的 SKILL.md + 官方文档 + CHANGELOG 交叉核实；星标 290612（GitHub API 2026-10-14 查证）。**本篇与 [[Matt Pocock retro 拆解]] 是 wiki 捕捉 mattpocock/skills v1.3.0 演进的依据**（此前口径停在 v1.2/24 promoted skills，见 [[mattpocock skills]]）。

## 与已有内容的关联

- **跨路线黄金对照**：与 [[子代理驱动开发]]（Superpowers SDD）同为「编排者 + 实现子代理 + 独立审查 + worktree」——分歧在调度依据与审查时机：SDD 按计划逐 task、每 task 审查（v6.0 单 reviewer）；implement-spec 按任务图前沿最大并发、集成分支终点一次审查。「任务图定节奏，前沿定人数，集成分支定终点，指针代替寒暄」与 SDD「fresh subagent per task」是同一问题的两种解。
- **frontier 一词双义辨析**：本篇的 frontier = 可并行的任务集（调度概念）；[[Matt Skills v1.2 grilling 重构]] 的 round-by-round frontier = 待问问题的边界（访谈概念）。同词不同义，引用时须带语境。
- **与并行派发对照**：[[Superpowers dispatching-parallel-agents 并行派发]]（人拆好人肉派发、契约先钉死）vs 本技能（编排会话按图自动派发）——自动化程度光谱的两端。
- **AFK 边界**：「不替代任何 AFK 脚本」与 [[Matt Pocock 后台 research 与主线程并行]] 的 handoff≠AFK 判据同族：有人可看可纠偏的并行 ≠ 无人值守。
- 同批毕业：[[Matt Pocock retro 拆解]]。

## ⚠️ 矛盾 / 待澄清

- 与 wiki 既有记载无冲突；v1.3 为既有 v1.2 口径的**增量演进**（已更新 [[mattpocock skills]] 版本时间线）。
- 注记：六坑均为官方 FAQ 收录的用户报告（节译），非老谭自创；星标 290612 与 8 月 213.4K 记录为正常增长曲线。

## 相关页面

- [[mattpocock skills]] · [[超级奶爸老谭]] · [[Matt Pocock retro 拆解]] · [[子代理驱动开发]] · [[Superpowers dispatching-parallel-agents 并行派发]] · [[Matt Skills v1.2 grilling 重构]] · [[Matt Pocock 后台 research 与主线程并行]] · [[Superpowers using-git-worktrees 工作树隔离]]
