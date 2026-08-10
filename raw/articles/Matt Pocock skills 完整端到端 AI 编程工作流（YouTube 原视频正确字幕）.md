---
title: "Matt Pocock skills：完整端到端 AI 编程工作流（YouTube 原视频正确字幕）"
source: "https://www.youtube.com/watch?v=M6mYodf0dJM"
author:
  - "[[Matt Pocock]]"
published: 2026-07-16
created: 2026-07-26
description: "Matt Pocock 本人讲解 mattpocock/skills 主流程的官方教程视频（17:17，161K 查看，录制时仓库 162K★/750 万下载）。本文为 web reader 重抓的正确字幕版，替代同目录下 transcript 串台的脏数据原文。"
tags: [mattpocock-skills, AI编程工作流, Claude Code]
---

> [!warning] 数据说明（必读）
> 本文件是 **正确字幕重抓版**，用于替代同目录下 `双语字幕 mattpocockskills 完整端到端 AI 编程工作流 A complete AI Coding workflow, end-to-end.md`。
> 那篇原文的 frontmatter/description 正确，但 **transcript 正文因抓取工具字幕匹配错误，内容为韩综《GOING SEVENTEEN》造船一期**，与 Matt Pocock 视频无关。
> 2026-07-26 用 web reader 重新抓取 YouTube 官方 description + chapters + transcript，整理如下。原脏数据文件保留不动（`raw/` 只读）。

## 视频信息

- **频道**：Matt Pocock
- **发布**：2026-07-16
- **时长**：17:17
- **查看**：161,408 / **点赞**：5,712
- **录制时仓库数据**：162,000 stars、7.5 million downloads
- **演示用仓库**：AI Hero CLI（Matt 的工作 repo，brownfield；流程对 greenfield 同样适用）

## 章节

- 0:00 Introduction and overview
- 0:54 Installing the skills repo
- 4:34 Running setup and configuration
- 6:52 Getting started with Ask Matt
- 8:23 Main workflow explained
- 8:31 Grill with docs interview session
- 10:08 Creating specs and tickets
- 13:51 Implementation and code review
- 16:22 Workflow recap and newsletter

---

## Transcript（整理稿）

### 0:00 · 概述

Matt 表示此前从未为 skills repo 做过一份正经的 tutorial。录制时仓库已达 162,000 stars、7.5 million downloads。常被问的问题：这些 skills 应该按什么顺序用、怎么装、怎么配。本视频只讲 **main flow**（不涉及 advanced/new 内容），用他的 work repo「AI Hero CLI」演示——流程对 brownfield 和 greenfield 仓库都一样。

### 0:54 · 安装 skills repo

`npx skills@latest add mattpocock/skills`（需要 Node.js；这调的是 Vercel 的 skills.sh 命令行安装器）。

- 找到 **38 个 skills，分两组**：`mattpocockskills`（作者认证的 official/blessed）与 `other`（实验性，未来可能删）。推荐全选 official 组。
- 支持多种 agent：Claude Code、Cursor、Codex 等（任何用 Claude skills 的都支持）。
- 安装 scope：**project**（团队协作推荐，统一 skill 集、可共同贡献）或 **global**（solo 开发者）。推荐 **symlink** 方式安装。

**关键设计点**：Matt 的 skills 多为 **user-invoked**，descriptions 短而精——全部 skills 只占 **660 tokens**。对比其他 skills repo 会把大量描述「leech」进 context，他这套刻意保持轻量。

### 4:34 · 运行 setup 与配置

`/setup-mattpocock-skills` 会做几件事：

1. **Issue tracker 选择**：GitHub / 本地 markdown / 任意。强调：Jira、Linear、Beads 等都「已经支持」——只需运行 setup 时告诉 agent「设成 Jira」即可，skills 读本地配置。
2. **Triage labels**：skills 依赖一组标签来传达 ticket 状态信息。默认即可（详见 triage skill 文档）。
3. **Domain documentation**：写入 `context.md` + ADR。选 **single context**（99% 场景）或 **multi context**（大 monorepo、多个 bounded context）。
4. 把 issue tracker docs、triage labels、domain docs 的链接写进 `CLAUDE.md`。

### 6:52 · 用 Ask Matt 起步

`/ask-matt`：作者「把自己做成 skill」——它知道整个 repo 该怎么用、该先做什么。问它「how do I get started」，它会推荐 main flow。

强调两个原则：
- 在**一个不间断的 context window** 里走完 main flow。
- **对 token 预算的自觉是用好 AI 的关键**——ask-matt 会明确建议「从 grill-with-docs 开始」。

### 8:23 · Main workflow + 8:31 · Grill with docs

默认主流程从 `/grill-with-docs` 起步：基于一个 idea（可以非常模糊）盘问，同时探索代码，把学到的东西记录进 `context.md` 与 ADRs。目的是把「我想改 X」变成一份 **crisp, defensible plan**。

- 一次 grilling 通常 ~20 个问题（演示那次只 6 个），直到双方达成 shared understanding。
- 可以在 Claude Code 的 **auto mode** 下跑，无需 plan mode。

演示案例：要把 AI Hero CLI「移除大部分内部工具、只保留 public-facing」→ grill 后得到「删 10 个命令文件、删 3 个测试、rewire shared modules」的计划。

> **smart zone 概念（重要）**：Matt 把 ~**140k tokens** 以内视为 LLM 的「聪明区」。超过这条线会出现 attention degradation、幻觉、怪行为。判断工作量是否需要拆分，就看会不会撑过这个 smart zone。

### 10:08 · 创建 specs 与 tickets

grill 完成分岔点：
- 工作量 **≤ 单 session smart zone** → 直接 `/implement`。
- 工作量 **> 单 session** → `/to-spec` → `/to-tickets`。

**`/to-spec`**：把整段讨论（演示中 46.1k tokens）压缩成一份 spec 文档——即「目的地/终态描述」。内容含：problem statement、solution、user stories、implementation decisions、testing decisions。发布到 issue tracker。这份 spec 后续用来**对照验收**实现是否达标。

**`/to-tickets`**：把 spec 拆成 implementation plan，**每个 ticket = 一个 context window / smart zone 的工作量**（垂直切片）。真实例子：一份 spec 下挂 **11 个 sub-issues/tickets**，每个 ticket 内容很短（大部分验收标准已在 spec 里），只说明「这个 session 做什么」。

### 13:51 · 实现与 code review

`/implement`：清掉 context，逐个 ticket 实现（「at tickets / implement this」）。每个 ticket 之间清 context。全部实现完，跑 code review 做最终对照。

**`/code-review`（implement 内置自动触发）——双轴审查**：
1. **Spec 轴**：把已完成工作对照原 spec，防止 ticket 遗漏或未specified（大块工作后 agent 可能忘事）。
2. **Standards 轴**：对照仓库自己的编码标准；若仓库没有，则回落到 **Martin Fowler 的经典 code smells** 体系。

> **为什么必须用 sub-agents 做 review（重要）**：如果在 main agent 里审，它会护短自己刚写的代码（「我写的，挺好的」）。拆给 sub-agents，它们有清晰的 context window，审查更客观、更狠。implement 还会跑 typecheck、build、verification，最后 commit 到当前分支。

### 16:22 · 回顾

完整 main flow：**先对齐（grill）→ 用 spec/tickets 保证可跨 session → implement（内置 code review）**。Matt 说他所有工作都跑这个 loop；loop 外的是实验性内容，持续迭代让它更快更好。日常更新见他的 newsletter。

---

## 来源链接

- YouTube：https://www.youtube.com/watch?v=M6mYodf0dJM
- B 站（小匠Skills 搬运，字幕串台的那个）：https://www.bilibili.com/video/BV1f1KH6CES3/
- 仓库：https://github.com/mattpocock/skills
- Skills newsletter：https://aihero.dev/s/4arzG4
