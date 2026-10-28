---
type: source
title: "Superpowers 组合实战2 线上大排查"
domain: [AI编程, 软件工程, 调试]
tags: [Superpowers, 老谭superpowers系列, 组合实战, 调试, 前后端联查, watch依赖, 防回归]
sources: []
source_path: "raw/articles/22｜superpowers 组合应用2：线上「账对不上」的大排查.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/6xmF28D-etTQpuZSLNjP5g"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-21"
created: 2026-10-22
updated: 2026-10-22
status: active
aliases: [组合实战2, 线上大排查, 组合应用2]
---

# Superpowers 组合实战2 线上大排查

> 一句话要点：三个调试技能协同——systematic-debugging 搭流程、root-cause-tracing 追因果、verification-before-completion 收尾——把「乐乐账本冒出全家工资」钉死在「**前端 `watch` 漏了 `ownerId` 依赖**」：后端无罪，页面根本没重新请求，「数据不对」其实是「没刷新」。

## 关键要点

- **现象**：切到乐乐零花钱账本看报表，显示家庭工资收入 12000——「这不就是账本串了吗？」但这次没急着改代码，调试三技能全部摆上桌。
- **复现的第一步是分清前后端**：先 curl 单独验后端（乐乐账本只有文具 45 + 零食 12，**根本没有工资**——后端是对的）→ 问题在前端。**一锅粥地看只会越看越乱**。
- **三假设逐一排除**：A 前端切账本后没重新拉数据（旧页面）/ B 请求没带 `X-User-Id`（DevTools 查：带对了）/ C 后端缓存（无缓存层 + curl 已证对）→ **A 存活**：`watch([month], load)` 只监听月份，**账本变了根本没触发重拉**。
- **根因与修法**：根因 = 报表页 watch 漏 `ownerId` 依赖；修法一行（`watch([month, ownerId], load)`）→ **回到复现那一步验证**（切账本报表立即重拉、工资消失）→ 防回归测试钉死「账本切换后数据要刷新」。
- **三技能分工表**：systematic-debugging 搭流程（复现→假设→验证→根因，步步有证据）；root-cause-tracing 顺因果链（切账本→页面没刷新→watch 漏依赖）；verification-before-completion 收尾（回复现验证+防回归才宣布完成）。**单靠哪个都不够——流程让你不走弯路，追踪让你挖到根子，验证让你不再犯第二次**。
- **注意事项**：「数据不对」不一定是「数据错了」，可能是「没刷新」（先查请求发没发出去，再看返回对不对）；**`watch` 漏依赖是前端串数据的头号嫌疑**（页面依赖账本/筛选条件，依赖要全写进 watch，「写一半，就是这次这个 bug」）。

## 详细笔记

系列组合实战第二篇（逆向排查流），与 21 篇构成「做出来 / 查出来」双流。作者诚实注记：文中防回归测试代码是「示意骨架」（自身断言未真正覆盖「账本变化触发拉取」），真实工程应写成组件挂载测试断言 `reports.monthly` 被重新调用——**教学示例与真验证的边界，作者主动声明**（与 16 篇「证据不能是示意」精神互文）。bug 模式（旧数据残留 vs 数据错误）是前端排查通用启发。结尾老王「技能树是真立住了」转入 23 篇结语。

## 与已有内容的关联

- **三技能各自 source 页**：[[Superpowers systematic-debugging 四阶段调试]] · [[Superpowers root-cause-tracing 根因追踪]] · [[Superpowers verification-before-completion 验证再收工]]——本篇是三页的协同实战总演习。
- **前篇**：[[Superpowers 组合实战1 从一句话到上线]]（上线第三天即出 bug，双流呼应）。
- **基建复用**：`X-User-Id` 账本切换出自 [[幸福账本 Vue 前端起步]] / [[幸福账本 REST API 基建]]。
- **后篇**：[[Superpowers 结语 AI 技能树]]。

## ⚠️ 矛盾 / 待澄清

- 与 wiki 既有记载无冲突。「示意测试骨架」声明为作者原文注记，非本 wiki 推论——引用该测试代码时须带此限定。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers systematic-debugging 四阶段调试]] · [[Superpowers root-cause-tracing 根因追踪]] · [[Superpowers verification-before-completion 验证再收工]] · [[Superpowers 组合实战1 从一句话到上线]] · [[幸福账本 Vue 前端起步]] · [[Superpowers 结语 AI 技能树]]
