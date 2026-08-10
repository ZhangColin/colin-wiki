---
type: source
title: "Matt Pocock 的 Git 护栏"
domain: [AI编程]
tags: [mattpocock-skills, git-guardrails, pre-commit, merge-conflicts, git, Agent护栏]
sources: []
source_path: "raw/articles/Matt Pocock 的 3 道 Git 护栏：命令前、提交前、冲突时，让 Agent 改代码不弄坏仓库.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491588&idx=1&sn=4e48c79f48c8d019cfed92eadfaaacb7"
author: "[[运维有术]]"
date_published: "2026-08-03"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [Git 护栏, git-guardrails-claude-code, setup-pre-commit, resolving-merge-conflicts, 三道护栏]
---

# Matt Pocock 的 Git 护栏

> 一句话要点：Agent 在真实仓库里失控有三类 Git 事故——force push 覆盖远端、commit 没跑过 typecheck 的脏代码、rebase 冲突挑错一边；它们发生在三个不同时间点（命令前/提交前/冲突时），分别由 `git-guardrails-claude-code`、`setup-pre-commit`、`resolving-merge-conflicts` 三个 skill 各守一道。

## 关键要点

- **三时间点 × 三 skill × 三事故**：命令执行前（`git-guardrails-claude-code` 拦 force push/reset --hard/clean -f）× commit 前（`setup-pre-commit` 跑 lint-staged+typecheck+test）× 冲突出现时（`resolving-merge-conflicts` 按意图而非默认解决）。
- **预防层核心机制 = Claude Code PreToolUse hook + exit 2**：脚本从 stdin 读 JSON，`jq` 取 command，正则匹配危险模式，命中向 stderr 打 BLOCKED 并 `exit 2`。**只有 exit 2 才是真正的阻止信号**——exit 0 不代表批准，exit 1 是非阻塞错误照常执行。用 exit 1 拦截的网上示例基本形同虚设。
- **反例一：guardrails 装在 CI 而非 agent hook**。CI 在 push 之后才触发，等 CI 跑完事故已完成。PreToolUse 是唯一能在"命令执行前"说话的机制。
- **提交层 = Husky pre-commit 固定编排**：`npx lint-staged` → `npm run typecheck` → `npm run test`。顺序理由：lint-staged 只处理暂存文件（毫秒级），先收拾干净再跑全量 typecheck/test。
- **提交层编排坑**：①typecheck 不适合进 lint-staged 逐文件跑（`tsc --noEmit` 是项目级语义检查）②lint-staged v10+ 自动重新 stage 修复后文件，别再手写 `git add` ③仓库无 typecheck/test 脚本时跳过对应行而非补空脚本。
- **提交层是纪律工具不是安全边界**：`git commit --no-verify` 能绕过，CI 才是最终关卡。
- **冲撞层 = `resolving-merge-conflicts` 14 行 5 步**：①看当前状态 ②找 primary source（读 commit message/PR/原始 issue）③逐 hunk 解决（尽量保留双方意图）④跑项目自动化检查 ⑤完成 merge/rebase。
- **冲撞层关键约束**：①rebase 中 ours/theirs 方向与 merge 相反（搞反选错边）②`--continue` 之后不能 abort ③`git checkout --ours|--theirs` 会整体覆盖整个文件丢掉另一侧非冲突修改（故要求逐 hunk，用 `git merge-file`）④"永远不要 --abort" 是 Matt 个人哲学非社区共识。
- **作者分类透露的优先级**：`resolving-merge-conflicts` 在 `skills/engineering` 主列表；`git-guardrails-claude-code` 与 `setup-pre-commit` 在 `skills/misc`（作者自标 "rarely use, not promoted"）——只代表使用频率，不代表价值。

## 详细笔记

冒烟测试方法：guardrails 用 `echo '{"tool_input":{"command":"git push origin main"}}' | script.sh`，正确结果应 exit 2 并向 stderr 打 BLOCKED。作者坦承三层都不是铜墙铁壁：hook 是字符串正则、`--no-verify` 可绕 pre-commit、按意图解冲突不能排除逻辑错误。价值是把事故从"悄无声息发生"变成"每个时间点被迫停一次"。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[diagnosing-bugs 与 tdd]]、[[mattpocock skills 推荐工作流速查]]

## ⚠️ 矛盾 / 待澄清

- **Matt 个人哲学 vs 社区共识**："永远不要 --abort"是 Matt 个人立场，GitHub/GitLab 官方及社区主流把 `--abort` 当合法逃生通道。引用时须标注。
- **作者使用频率信号 ≠ 价值排序**：guardrails/pre-commit 被归入 `skills/misc`，但不代表没用，不应因分类而低估。
- 轻度缺口：作者自述"脚本配置尚未在生产环境逐项验证"——具体正则模式引用时建议回仓库源码核对。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock]] · [[运维有术]] · [[diagnosing-bugs 与 tdd]] · [[mattpocock skills 推荐工作流速查]]
