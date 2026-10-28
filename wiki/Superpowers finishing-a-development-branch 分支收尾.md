---
type: source
title: "Superpowers finishing-a-development-branch 分支收尾"
domain: [AI编程, 软件工程, Git]
tags: [Superpowers, 老谭superpowers系列, finishing-a-development-branch, 分支合并, 合修弃, 质量门禁]
sources: []
source_path: "raw/articles/11｜superpowersfinishing-a-development-branch 安全合回主线.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484294&idx=1&sn=711559e3e40b826edb17c5604ecc429a"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-07"
created: 2026-09-10
updated: 2026-09-10
status: active
aliases: [finishing-a-development-branch, 分支收尾]
---

# Superpowers finishing-a-development-branch 分支收尾

> 一句话要点：`finishing-a-development-branch` 把"合分支"从拍脑袋变成四步流程——自检 → 判断（合/修/弃）→ 安全合并 → 清理；**测试绿、审查过、验收对，才有资格进主线**。

## 关键要点

- **三种结局**：合（merge，活干完测试绿验收过）/ 修（fix，方向对有瑕疵，修好再合）/ 弃（discard，方向错直接删）。判断依据三样：代码审查、测试结果、与验收的对齐。"**弃分支不丢脸，死扛才是浪费**"（预算自动调额分支写到一半发现与艳姐验收对不上，果断弃掉重开）。
- **四步流程**：
  1. **自检**：分支上 `mvn test` 全绿（"测试不过的分支，没有资格进主线"）+ `git log main..branch` 与 `git diff main...branch` 看全量差异——"把'我好像改了啥'变成'我清清楚楚知道改了啥'"。
  2. **判断**：对着差异问三问（代码能讲明白吗 / 测试绿吗新增有覆盖吗 / 做的是计划要的事吗，有没有夹带私货）。
  3. **合并**：主工作区 `git pull` 先拉最新 + `git merge --no-ff`（保留明确合并提交，历史可追溯）→ **合完立刻在主干再跑一遍测试**——"分支上绿 ≠ 合完绿，合并冲突可能把好好的代码弄坏"。
  4. **清理**：`branch -d`（小写，未合并时拒绝删除是保护，"别用 -D 强删"）+ `worktree remove`。
- **checklist 表格化**：审查/测试/验收/主线最新/主干再测五项，每项"过/不过怎么办"。
- 主线永远要绿是底线："合完主干再测一遍"是最容易被跳过、也最重要的一步。

## 详细笔记

对应 [[Superpowers]] 7 步流程末步。与前序技能的关系：worktrees（10 篇）负责"并行不打架"，本技能负责"合回得干净"——一开一合构成并行开发的完整生命周期。

## 与已有内容的关联

- "主干再测一遍"与 12/13 篇收尾同款纪律（[[Superpowers dispatching-parallel-agents 并行派发]]、[[Superpowers subagent-driven-development AI 项目经理]]）——三条路径（手动并行/AI 并行/AI 项目经理）共用同一门禁。
- 与 [[TAB 大仓 Harness 工程化实战]] 的 7 道门禁同构：都是"进主线前的机械化检查点"，本技能是个人级最小版。
- 弃分支的果断与 [[Matt Pocock 的 Git 护栏]] 的护栏思路互补（护栏防误操作，收尾流程防半成品入库）。

## ⚠️ 矛盾 / 待澄清

- 无矛盾。`--no-ff` 是老谭的推荐写法（历史可追溯），非技能强制要求——squash 等团队惯例可另行约定（推论）。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers using-git-worktrees 工作树隔离]] · [[Superpowers dispatching-parallel-agents 并行派发]] · [[Matt Pocock 的 Git 护栏]] · [[TAB 大仓 Harness 工程化实战]]
