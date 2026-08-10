---
type: source
title: "Matt Pocock skills Handoff 官方文档"
domain:
  - AI编程
  - AI工具
  - 软件工程
tags:
  - mattpocock-skills
  - handoff
  - AI编程工作流
sources: []
source_path: "raw/articles/Matt Pocock skills Handoff 官方文档（aihero.dev）.md"
source_type: article
source_url: "https://www.aihero.dev/skills-handoff"
author: "Matt Pocock"
date_published: "早于 v1.1（v1.0 已有）"
created: 2026-07-28
updated: 2026-07-28
status: active
aliases:
  - Handoff 官方文档
  - handoff skill
  - /handoff
---

# Matt Pocock skills Handoff 官方文档

> 一句话要点：[[Matt Pocock]] 官方 `/handoff` 文档页一手原文。handoff 把当前对话**压缩（compaction）**成一份 fresh agent 可读的交接文档，用于跨 session/agent 续接——只载"活的线索"，不重述已写别处的内容。补 [[mattpocock skills]] concept 页"辅助与交接"段的一句话概括。

## 关键要点

### 做什么

把当前对话压缩成一份 **handoff document**——fresh agent 读了就能从你停下的地方接手。

- **不重述已写别处的**：已被 spec / plan / ADR / issue / commit / diff 捕获的内容，只用 path 或 URL 引用，**绝不复制**。
- 只承载"**活的线索**"——你在做什么、为什么、下一步。
- 默认存 **OS 临时目录**（不进 workspace，所以不会变成另一个要维护的 artifact）；实战中有人改存项目内（不 commit）。

### 什么时候用（核心）

当对话长到 **context 有风险**时：

- 接近 **context limit**（实战案例：Codex 超 80% 时用）
- **收工**（wrapping for the day），隔天再接
- 刻意**交给另一个 agent** 接手

核心思想是 **compaction（压缩）**：想保留这条线索，但不想拖着整个 transcript 一起搬。

- **user-invoked**：手动 `/handoff` 触发，agent 不会自动调。
- 可附一句 **note** 说明下个 session 目的，文档据此定制。
- 足够简单，**也可直接当提示词用**（不调 skill，手写一份 handoff 文档）。

### 文档承载四样

1. **live thread** —— 在飞的事 + 为什么，用对话自己的话。
2. **suggested skills** —— 下一个 agent 该用的 skill 指针。
3. **references, not copies** —— spec / plan / ADR / issue / diff 的路径与链接（settled detail 的去处）。
4. **redacted secrets** —— API key / 密码 / PII 写入前自动剔除。

> 要抱住的想法：handoff = 对话被挤到只剩"**可接手的核心**"，让 fresh agent **继承势头（momentum），不继承噪音（noise）**。

### 如何在新会话从 handoff 继续

官方说它让 fresh agent "pick up the work where you left off"；GitHub Issue #202 把它叫作 **"a tight bootstrap prompt the user pastes into the new thread"**。续接流程：

1. 当前 session 跑 `/handoff`（可附 note，如"下个 session 继续调 X"）→ 生成 `handoff.md`（默认 OS tmp）。
2. **开新 session / 新 thread**（fresh agent）。
3. 把 `handoff.md` 贴进去 / 让新 agent 读它——它就是 **bootstrap prompt**。
4. handoff 告诉新 agent 三件事：**what to read**（按引用去读 spec / ADR / issue 等 settled detail）、**what to skip**（噪音）、**what NOT to touch**（secret 已 redact）。
5. 新 agent 继承势头，从 suggested next steps 接着干。

### 真实 handoff.md 的结构（参考实战样例）

来自 [aicodingdaily 的实战](https://aicodingdaily.com/article/skill-handoff-by-matt-pocock-for-another-agent)（作者在 Codex 超 80% context 时生成）：

```
# Handoff: <主题>
## Context              — 仓库、背景
## Current Goal         — 当前目标
## Important Current State — 环境/工具版本（如 .venv、pytest/ruff/mypy 版本）
## <主体当前形态>        — 文件结构、关键代码位置
## Current Tests        — 当前测试覆盖
## Recent Fixes and Decisions — 最近的修复与决策（编号）
## Verification Commands Already Run — 已跑过的验证命令
## Latest Signal        — 最新评估信号/状态
## Scripts Changed      — 本 session 改过的文件
## Things to Watch      — 坑/注意事项
## Suggested Next Steps — 建议的下一步（编号）
```

## 详细笔记

- **安装**：`npx skills add mattpocock/skills --skill=handoff`（可单项装）。
- **位置**：standalone，坐在**两个 session 的接缝**，不在构建链里——随时可用、独立。
- **与 to-spec 配对**：完成的 spec 正是 handoff 引用而非重复的那种 settled detail。
- 不确定用哪个 skill 时，`/ask-matt` 路由。

## 与已有内容的关联

- **核心概念**：[[mattpocock skills]]（本页补其"辅助与交接"段的 handoff 细节）。
- **同体系对照**：[[Matt Pocock skills Wayfinder 官方文档]] —— 同为跨 session 机制，但 wayfinder 是**结构化多 session 规划**（map/ticket 存 issue tracker，大活、团队协作），handoff 是**临时压缩交棒**（不依赖 tracker，轻、即兴，单对话快满了就用）。
- **速查**：[[mattpocock skills 推荐工作流速查]]（命令表含 handoff 行）。
- **相关实体**：[[Matt Pocock]]。

## ⚠️ 矛盾 / 待澄清

- 暂无。handoff 非 v1.1 新增（v1.0 已有，[[Matt Pocock 完整工作流视频]] 时代就在），与 v1.1 的 wayfinder 并行存在、用途不同。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock skills Wayfinder 官方文档]] · [[mattpocock skills 推荐工作流速查]] · [[Matt Pocock]]
