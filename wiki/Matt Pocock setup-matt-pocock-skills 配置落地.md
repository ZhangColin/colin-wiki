---
type: source
title: "Matt Pocock setup-matt-pocock-skills 配置落地"
domain: [AI编程]
tags: [Agent Skills, setup, issue tracker, triage labels, CONTEXT.md, AGENTS.md]
sources: []
source_path: "raw/articles/setup-matt-pocock-skills 不是脚本，是一次对话：把工单、标签、域文档问清楚再落地.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491618&idx=1&sn=c51628d09468e0432c0e4b264efd2fb2"
author: "[[运维有术]]"
date_published: "2026-08-06"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [setup-matt-pocock-skills 配置, setup 不是脚本是对话, 工单标签域文档落地]
---

# Matt Pocock setup-matt-pocock-skills 配置落地

> 一句话要点：setup-matt-pocock-skills 是所有 engineering skill 的 precondition，但它不是脚手架脚本而是 prompt-driven 对话——先 explore 仓库真实状态再逐个 section 确认，把"工单在哪/标签叫什么/文档放哪"三个事实一次写对。

## 关键要点

- 官方定位："Run once per repo before using the other engineering skills"（每仓库跑一次）；设计原则"It writes config, it does not hard-code behaviour"。
- 与脚手架的区别：脚手架是确定性（输参数吐模板），setup 是 prompt-driven（先 explore `git remote -v`/`.git/config`/`AGENTS.md`/`CLAUDE.md`/`CONTEXT.md`/`.scratch/`/monorepo 信号，把发现摆给你看，每问带推荐答案，确认才写入）。
- 只能 user-invoked 触发，不会偷偷自动执行——是"user-invoked 而非 model-invoked"哲学的一部分。
- **三个 section**：A. Issue tracker（默认 GitHub `gh` CLI；分支 GitLab `glab`/本地 markdown `.scratch/<feature>/`/Jira/Linear）→产 `docs/agents/issue-tracker.md`；B. Triage labels（**仅 triage skill 已装才问**，默认 5 标签：`needs-triage`/`needs-info`/`ready-for-agent`/`ready-for-human`/`wontfix`）→产 `docs/agents/triage-labels.md`；C. Domain docs（默认 single-context 根 `CONTEXT.md`+`docs/adr/`，**仅 monorepo 信号才 offer multi-context**）→产 `docs/agents/domain.md`。
- Agent 约定文件选择规则：`CLAUDE.md` 存在则编辑它，否则用 `AGENTS.md`，两者都没有就问用户，**绝不同时新建两个**。
- 30 秒验收四步：①三件 docs 都在；②`CLAUDE.md`或`AGENTS.md` 有 `## Agent skills` 块指向三文件；③标签语义一致；④域文档布局确定。
- 四个反例：①不 explore 直接套 GitHub 模板；②triage 没装硬写 labels；③单仓库硬上 multi-context；④`AGENTS.md` 已存在又新建 `CLAUDE.md`。
- setup 跑一次就够，重跑仅两种场景：换 issue tracker；配置乱到推倒重来。日常微调直接编辑 `docs/agents/*.md`。

## 详细笔记

`docs/agents/domain.md` 种子模板含"If any of these files don't exist, proceed silently... The `/domain-modeling` skill creates them lazily."——即 setup 不预创 CONTEXT.md/ADR，留给 domain-modeling 懒创建。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[mattpocock skills 推荐工作流速查]]
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[MattPocock Skills 21 个 skill 工程纪律体系]]、[[Matt Pocock main flow 五环节]]

## ⚠️ 矛盾 / 待澄清

- 无明显矛盾。本仓库自身 `CLAUDE.md` 采用 `AGENTS.md` 镜像约定，与 setup 的"两者择一、不并存"规则相符（本仓库是用户手动维护的镜像，非 setup 产物，规则适用对象不同，非矛盾）。
- 社区批评（知乎）：无全局继承机制，多仓库都得重跑一遍 setup，多仓库场景重复劳动。

## 相关页面

- [[mattpocock skills]]
- [[运维有术]]
- [[Matt Pocock main flow 五环节]]
