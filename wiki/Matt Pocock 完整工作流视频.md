---
type: source
title: "Matt Pocock skills：完整端到端 AI 编程工作流（视频）"
domain:
  - AI编程
  - AI工具
  - 软件工程
tags:
  - mattpocock-skills
  - 视频
  - 一手源
  - smart-zone
  - AI编程工作流
sources: []
source_path: raw/articles/Matt Pocock skills 完整端到端 AI 编程工作流（YouTube 原视频正确字幕）.md
source_type: article
source_url: https://www.youtube.com/watch?v=M6mYodf0dJM
author: Matt Pocock
date_published: 2026-07-16
created: 2026-07-26
updated: 2026-07-26
status: active
aliases:
  - Matt Pocock 完整工作流视频
  - A complete AI Coding workflow end-to-end
  - mattpocock skills 教程视频
---

# Matt Pocock skills：完整端到端 AI 编程工作流（视频）

> [!warning] ⚠️ 数据质量（必读）
> 本 source 对应的 raw 原文有**两个文件**：
> - **正确字幕版（本摘要的内容依据）**：`raw/articles/Matt Pocock skills 完整端到端 AI 编程工作流（YouTube 原视频正确字幕）.md`——2026-07-26 用 web reader 重抓 YouTube 官方 transcript 整理。
> - **脏数据原文（内容不可信，保留不动）**：`raw/articles/双语字幕 mattpocockskills 完整端到端 AI 编程工作流 A complete AI Coding workflow, end-to-end.md`——其 transcript 因抓取工具字幕匹配错误，内容为韩综《GOING SEVENTEEN》造船一期，与视频无关（frontmatter/description 正确，仅 transcript 串台）。
>
> 按 `raw/` 只读红线，脏数据文件不删不改；正确内容以独立新文件补入。

> 一句话要点：[[Matt Pocock]] 本人首份完整 tutorial（YouTube，17:17，录制时 162K★/750 万下载），一手演示 [[mattpocock skills]] main flow——安装、setup、grill、spec、tickets、implement + code review 全流程。

## 视频信息

- **频道**：Matt Pocock · **发布**：2026-07-16 · **时长**：17:17 · **查看**：161,408 / **点赞**：5,712
- **录制时仓库数据**：162,000 stars、7.5 million downloads
- **YouTube**：https://www.youtube.com/watch?v=M6mYodf0dJM
- **B 站搬运**（[[小匠Skills]]，字幕串台的那个）：https://www.bilibili.com/video/BV1f1KH6CES3/
- **演示仓库**：AI Hero CLI（Matt 的工作 repo，brownfield；流程对 greenfield 同样适用）

## 关键要点

- **安装**：`npx skills@latest add mattpocock/skills`（Vercel skills.sh CLI，需 Node.js）。找到 38 skills，分两组：`mattpocockskills`（official blessed）+ `other`（实验性，可能删）。支持 Claude Code/Cursor/Codex 等。scope：project（团队，统一 skill 集）或 global（solo），推荐 symlink 安装。
- **轻量设计（关键）**：skills 多为 user-invoked，descriptions 短而精——**全套只占 660 tokens**。对比其他 skills repo 会把大量描述「leech」进 context，他这套刻意保持轻量。
- **/setup-mattpocock-skills 配置**：① **issue tracker**（GitHub / 本地 markdown / 任意——Jira、Linear、Beads 都「已经支持」，告诉 agent 设成什么即可，skills 读本地配置）；② **triage labels**（默认即可）；③ **domain docs**（`context.md` + ADR，single 99% / multi 大 monorepo 多 bounded context）；④ 写入 `CLAUDE.md` 若干链接（issue tracker docs、triage labels、domain docs）。
- **/ask-matt**：作者「把自己做成 skill」——知道整个 repo 怎么用、推荐 main flow。强调"在一个**不间断的 context window** 里走完 main flow"、"对 token 预算的自觉是用好 AI 的关键"。
- **main flow**：`/grill-with-docs` 起步（基于模糊 idea 盘问，~20 问，探索代码，写入 context.md/ADR，把"我想改 X"变成 crisp defensible plan；可在 auto mode 跑，无需 plan mode）→ **分岔**：工作量 ≤ 单 session → `/implement`；> 单 session → `/to-spec` → `/to-tickets` → `/implement`。
- **smart zone（重要，中文圈未充分传达）**：~**140k token** 内是 LLM 聪明区，超过会 attention degradation、幻觉、怪行为。判断工作要否拆分就看这条线。
- **/to-spec**：把整段讨论（演示中 46.1k tokens）压缩成 spec（目的地/终态描述：problem statement、solution、user stories、implementation/testing decisions），发布到 issue tracker，用于最终对照验收。
- **/to-tickets**：把 spec 拆成 implementation plan，每个 ticket = 一个 smart zone 的**垂直切片**工作量。真实例子：一份 spec 下挂 **11 个 sub-issues/tickets**，每个很短（验收标准已在 spec 里）。
- **/implement**：清 context，逐个 ticket 实现（"at tickets / implement this"），每个 ticket 之间清 context；全部实现完跑 code review 做最终对照。内置 typecheck、build、verification，最后 commit 到当前分支。
- **/code-review（双轴，重要）**：① **Spec 轴**——对照原 spec，防大块工作后 agent 忘事或 ticket 未specified；② **Standards 轴**——对照仓库编码标准，无则回落 **Martin Fowler 经典 code smells**。
- **sub-agent 做 review 的理由（重要）**：main agent 写完会护短（"我写的，挺好的"）；拆给 sub-agents，它们有清晰 context window，审查更客观更狠。

## 详细笔记

Matt 强调的完整 loop：**先对齐（grill）→ 用 spec/tickets 保证可跨 session → implement（内置 code review）**。他说所有工作都跑这个 loop；loop 外的是实验性内容，持续迭代让它更快更好。日常更新见他的 newsletter。演示案例：要"移除 AI Hero CLI 大部分内部工具、只保留 public-facing"→ grill 后得到"删 10 个命令文件、删 3 个测试、rewire shared modules"的计划。

## 与已有内容的关联

- **核心概念**：[[mattpocock skills]]（本视频是其 main flow 的一手权威演示）。
- **相关实体**：[[Matt Pocock]]（作者/讲解者）、[[小匠Skills]]（B 站搬运者）。
- **互补**：[[mattpocock skills 标准工作流]]（v1.0 中文总览）、[[MattPocock Skills v1.1 重磅更新]]（v1.1 新内容补齐）。

## ⚠️ 矛盾 / 待澄清

- 录制时仓库 162K★，YouTube 描述写 170K（描述可能后续更新），[[MattPocock Skills v1.1 重磅更新]] 记 160K——时间点差异，非矛盾。
- 本视频只讲 v1.0 主流程（main flow），**未涉及 v1.1 的 Wayfinder 等新内容**（由 [[MattPocock Skills v1.1 重磅更新]] 补齐）。
- **脏数据说明**：B 站搬运版的 transcript 串台为韩综内容（见页首警示），正确字幕以重抓版 raw 文件为准。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock]] · [[小匠Skills]] · [[mattpocock skills 标准工作流]] · [[MattPocock Skills v1.1 重磅更新]]
