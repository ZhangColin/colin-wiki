---
type: source
title: "Superpowers using-git-worktrees 工作树隔离"
domain: [AI编程, 软件工程, Git]
tags: [Superpowers, 老谭superpowers系列, using-git-worktrees, git worktree, 并行开发, 上下文隔离]
sources: []
source_path: "raw/articles/10｜superpowersusing-git-worktrees 前后端并行开发不打架.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484291&idx=1&sn=77c53d602d4fc095028a745be917f750"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-04"
created: 2026-09-10
updated: 2026-09-10
status: active
aliases: [using-git-worktrees, git worktree 技能, 工作树]
---

# Superpowers using-git-worktrees 工作树隔离

> 一句话要点：`using-git-worktrees` 用 git 工作树让"一个仓库多个活并行"互不打架——一个工作目录只能承载一个"正在改的上下文"；**隔离靠目录，不靠自觉**。

## 关键要点

- **事故场景**：同一目录开两个 Claude Code 会话（A 改前端报表页、B 加后端接口）→ 写入互相覆盖，"改好的代码说没就没"。结论："**一个工作目录只能承载一个正在改的上下文。**"
- **机制**：`git worktree`（git 自带命令）——一个 `.git` 挂多个工作目录，各 checkout 不同分支，各自改/存/跑测试互不干扰，提交与历史共享。比喻：给每个分支开"单独的套间"，代码还是同一栋楼。
- **实操流程**：`git branch report-frontend / report-backend` 建分支 → `git worktree add ../hl-fe-report report-frontend`（**工作区放仓库外面**）→ 两个会话各进各的目录干活 → 各自提交 → 合回主线（11 篇的事）→ `git worktree remove` + `branch -d` 清理（"干完即拆"）。
- **四条纪律**：① 一个工作区只服务一个正在进行的活（成本低，别省）；② 工作区目录放仓库外；③ 主工作区保持干净，只做合并/提交/发布；④ **别在同一工作区开两个会话——worktree 隔离的是目录不是会话，同目录两会话照样互相覆盖。**
- 一句话总结："并行开发不打架，靠的不是默契，是隔离。"

## 详细笔记

对应 [[Superpowers]] 7 步流程第 2 步（brainstorming → **using-git-worktrees** → writing-plans → …）。验证隔离的动作用两个工作区各自的 `git status` 只见自己的改动。

## 与已有内容的关联

- 12/13 篇的地基：dispatching-parallel-agents "可以配合 worktree"（[[Superpowers dispatching-parallel-agents 并行派发]]）、subagent-driven-development 子代理"各用各的 worktree"（[[Superpowers subagent-driven-development AI 项目经理]]）。
- "一个目录一个上下文"与 [[子代理驱动开发]] 的 fresh subagent per task（上下文隔离防污染）同一原则在**文件系统层**的落法；也呼应 [[Session Event Log]] 背后的会话状态隔离问题。
- 老谭在 mattpocock 系列未讲 worktree（mattpocock 无此技能）——这是 Superpowers 路线重流程的体现（[[Superpowers 与 mattpocock skills 路线对照]] 表"会话/上下文"行）。

## ⚠️ 矛盾 / 待澄清

- 无矛盾。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers finishing-a-development-branch 分支收尾]] · [[Superpowers dispatching-parallel-agents 并行派发]] · [[Superpowers subagent-driven-development AI 项目经理]] · [[子代理驱动开发]]
