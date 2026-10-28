---
type: source
title: "Superpowers 组合实战1 从一句话到上线"
domain: [AI编程, 软件工程]
tags: [Superpowers, 老谭superpowers系列, 组合实战, 全栈, 流水线, 零花钱页]
sources: []
source_path: "raw/articles/21｜superpowers 组合应用1：从一句话到一期功能上线.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/A-EYgIlEcXvrXieUZjvqpg"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-20"
created: 2026-10-22
updated: 2026-10-22
status: active
aliases: [组合实战1, 从一句话到上线, 组合应用1]
---

# Superpowers 组合实战1 从一句话到上线

> 一句话要点：组合实战不是新技能，是**旧技能串起来**——乐乐零花钱需求从「艳姐一句话」到全栈上线，走完 `brainstorm → writing-plans → executing → TDD → API → 前端 → verification → 上线` 一条能反复用的流水线。

## 关键要点

- **需求**：乐乐「下月开始记账，每月 200 零花钱」+ 艳姐「做个零花钱页」——又一句模糊需求。brainstorming 钉死边界：只算乐乐（ownerId=2）与家庭账隔离 / 每月 200 额度 / 当月支出 > 200 算超支 / 页面显示「本月支出·额度·超没超」。
- **writing-plans 拆任务**（每步带验收=执行与审查的尺子）：阶段 A 后端（A1 `AllowanceService.check` 覆盖超支/够花；A2 `GET /api/reports/allowance` curl 返回 `{spending, limit, message}`）+ 阶段 B 前端（B1 零花钱页渲染三件套）。
- **TDD 先红后绿**：`AllowanceServiceTest` 三个用例（超支返回提醒 / 没超返回 empty / **没设额度不提醒**）→ 红了才写实现，比较 `compareTo`、减法全程 `BigDecimal`——**SKILL.md 里的红线，AI 照着守住了**（20 篇的技能第一次实战生效证据）。
- **REST + 前端**：复用 08/09 篇基建（`X-User-Id` 切账本、请求封装）；curl 验证契约（乐乐 8 月花 57，`message: null`）；零花钱页超支红色警告/未超绿色提示。
- **verification 收工**：测试绿 → 浏览器真走一遍（给乐乐记 150 支出刷新看红色超支警告出现）→ 验收对照（乐乐自己能看花了多少超没超）→ 上线。
- **注意事项**：组合实战不是新技能是旧技能串起来，**任何一步偷懒流水线就断**；前后端契约先行（先定死字段）；**账本隔离别漏**（忘了过滤 ownerId，乐乐就看到全家的钱）；**上线 ≠ 部署**——本地 H2 文件库适合教学，真给一家子长期用要换 MySQL、备份、监控，「那个是另一套工程，不在这 24 篇范围里」，但「从一句话到功能可用」的流水线放哪都一样。

## 详细笔记

系列组合实战第一篇（正向开发流全贯通），对应 [[Superpowers]] 7 步流程的端到端教学演示。彩蛋级细节：TDD 环节「SKILL.md 红线 AI 守住了」正是 20 篇第四步「验证技能真的会被用上」的实战兑现——20→21 篇叙事闭环。「上线≠部署」是老谭的诚实边界声明（教学项目 vs 生产工程）。结尾老王「万一哪天线上账又对不上」为 22 篇（逆向排查流）埋线——两篇组合实战恰好对应「把功能做出来」与「把问题查出来」两条流。

## 与已有内容的关联

- **流水线各环对应篇目**：[[Superpowers brainstorming 需求访谈]] · [[Superpowers writing-plans 施工图]] · [[Superpowers executing-plans 一步一确认]] · [[Superpowers TDD 红绿循环]] · [[幸福账本 REST API 基建]] · [[幸福账本 Vue 前端起步]] · [[Superpowers verification-before-completion 验证再收工]]。
- **技能生效证据**：[[Superpowers writing-skills 规矩固化成技能]] 第四步（让 AI 照规范写代码）的实战兑现。
- **后篇**：[[Superpowers 组合实战2 线上大排查]]（逆向排查流）。

## ⚠️ 矛盾 / 待澄清

- 与 wiki 既有记载无冲突。源文「这 24 篇」指系列正文总数（00–23），与本 wiki 收录口径一致。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers writing-skills 规矩固化成技能]] · [[Superpowers 组合实战2 线上大排查]] · [[Superpowers brainstorming 需求访谈]] · [[Superpowers writing-plans 施工图]] · [[Superpowers TDD 红绿循环]] · [[幸福账本 REST API 基建]] · [[幸福账本 Vue 前端起步]] · [[Superpowers verification-before-completion 验证再收工]]
