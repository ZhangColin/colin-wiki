---
type: source
title: "diagnosing-bugs 与 tdd"
domain: [AI编程]
tags: [mattpocock-skills, tdd, diagnosing-bugs, 调试, red-capable-command, 反馈信号]
sources: []
source_path: "raw/articles/用户报 Bug，Agent 立刻猜根因？Matt Pocock 这 2 个 skill 把它拉回正轨.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491490&idx=1&sn=f8d12d71a20ff139603599bb92e1f2e1"
author: "[[运维有术]]"
date_published: "2026-07-22"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [diagnosing-bugs, /diagnosing-bugs, tdd, /tdd, red-capable command, tracer bullet]
---

# diagnosing-bugs 与 tdd

> 一句话要点：[[mattpocock skills]] 把 Agent 失败模式 #3 "The Code Doesn't Work" 拆给两个 skill——`/tdd` 管"我知道要什么行为、增量实现"，`/diagnosing-bugs` 管"我知道它坏了、不知为什么"；两者共同纪律是**先建立可靠的反馈信号（red-capable command）**，再谈假设与修复。

## 关键要点

- **对应失败模式**：仓库 README 把 Agent 失败模式归四类，`/tdd` + `/diagnosing-bugs` 对应 #3 "The Code Doesn't Work"。仓库引《The Pragmatic Programmer》："The rate of feedback is your speed limit."
- **`/diagnosing-bugs` 强制 6 阶段线性**（跳阶段须显式说明）：① Build a feedback loop（产出 red-capable command）② Reproduce + minimise ③ Hypothesise（3–5 个 ranked、falsifiable 假设）④ Instrument ⑤ Fix + regression test ⑥ Cleanup + post-mortem。
- **核心硬约束（Phase 1 入场券）——red-capable command 4 判据**：① Red-capable（断言用户确切症状）② Deterministic ③ Fast（秒级）④ Agent-runnable。仓库原话："No red-capable command, no Phase 2."
- **Phase 3 假设必须 falsifiable**：必须能陈述"如果 X 是原因，那么改 Y 会让 bug 消失"；Phase 5 找不到 correct seam 写回归测试本身就是发现——说明代码测试边界划错，须重构。
- **`/tdd` 不是为写更多测试**：核心是 red→green loop。关键概念：**Public interface / Seam**（测试观察行为的边界）、**Pre-agreed seam**（先和用户约定再写测试）、**Tracer bullet**（一个 vertical slice：1 测试 + 1 最小实现 + 干净 red→green）。
- **vertical vs horizontal slicing**：仓库强调 "AI loves coding horizontally instead of focusing on the vertical slices." agent 默认把一类事全做完再切换，是它的惯性；反例是先写完 10 个 login 测试再写实现，集成时全红。
- **Mocking 原则**：只在系统边界 mock（外部 API、数据库、时间、随机性、文件系统），不要 mock 自己的类/模块/内部协作者。
- **两 skill 服务场景不同，不可互套**：`/tdd` = "我知道要什么行为、增量实现"；`/diagnosing-bugs` = "我知道它坏了、不知为什么"。硬套会卡死。
- **与 superpowers `systematic-debugging` 对照**：[[Superpowers]] 的 `systematic-debugging` 用 4 阶段覆盖同一领域；社区有人认为已覆盖，有人认为没显式产出 red-capable command 这么硬。作者判断：两者不能无条件互换。

## 详细笔记

登录白屏案例演示错误开场：Agent 读 `LoginForm.tsx` 后直接猜"useEffect 依赖漏了 password"。正确开场是先建 red-capable command——用 Playwright 跑断言"提交后 body 非空且非纯白背景"的测试，稳定红后才有资格进 Phase 2。反模式：没真正跑过的测试、Tautological test（恒真）、测实现细节、一次性写完整套测试、只提一个假设、改完不写回归测试。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[子代理驱动开发]]
- 相关实体：[[Matt Pocock]]、[[运维有术]]、[[Superpowers]]
- 相关源：[[grill-me 实战指南]]、[[还在用 grill-with-docs]]、[[mattpocock skills 推荐工作流速查]]

## ⚠️ 矛盾 / 待澄清

- **时效性（重要）**：截至 2026-07-21 仓库处于 v1.0.x，是 breaking change——旧 skill 名 `diagnose` 已重命名为 `diagnosing-bugs`，`write-a-skill`→`writing-great-skills`、`ubiquitous-language`→`domain-modeling`。引用老博客/老 issue 须注意。
- **与 Superpowers 的覆盖争议**：本 vault 自用的 [[Superpowers]] `systematic-debugging`（4 阶段）与本源 `/diagnosing-bugs`（6 阶段 + red-capable command 硬约束）覆盖同一领域但实现不同，"不能无条件互换"。
- **数据缺口**：6 阶段流程的实测 ROI 无公开数据；"Build the right feedback loop, and the bug is 90% fixed" 是作者经验性比喻非对照数据。
- **非官方**：mattpocock/skills 是事实标准但非 Anthropic 官方背书。

## 相关页面

- [[mattpocock skills]] · [[Matt Pocock]] · [[运维有术]] · [[Superpowers]] · [[mattpocock skills 推荐工作流速查]]
