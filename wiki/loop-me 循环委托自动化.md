---
type: source
title: "loop-me 循环委托自动化"
domain: [AI编程, AI工具]
tags: [mattpocock-skills, loop-me, 委托自动化, workflow-spec, Push-right, in-progress]
sources: []
source_path: "raw/articles/mattpocockskills 你把每天重复的事情写成说明书交给 AI，一次说清，永久代劳.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484088&idx=1&sn=51f6756c1484072b385d7123a882010e"
author: "[[超级奶爸老谭]]"
date_published: "2026-08-13"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases: [loop-me 专题, 循环委托]
---

# loop-me 循环委托自动化

> 一句话要点：让 AI 替你干那些从没想过能交给它的活——把生活循环用 `/loop-me` 访谈成「工作流说明书」（workflow spec），可预测才值得委托。

## 关键要点

- **状态声明（重要）**：loop-me 是 **in-progress 草稿技能**（官方仓库 `skills/in-progress/loop-me`），不在插件包、不在官方 README 列表，需自己从源码装；「草稿恰恰说明是新方向，Matt 自己还在打磨」。
- **定位**：grill 家族第四位，唯一把「访谈」和「委托」直接连起来的成员；产出**唯一且专门** = workflow spec。家族谱：grilling（内核循环，模型自动调用）/ grill-me（轻量壳，问完就走）/ grill-with-docs（重量壳，问完写 CONTEXT.md+ADR）/ loop-me。
- **loop 视角**：大循环套小循环；「可预测的事才值得委托，而大多数你以为临时处理的事，其实是可预测的」（通知类型有限、动作有限 = 一个可预测的循环）；loop-me 还会**主动提议你没注意到的 loop**。
- **四个描述词（是语言不是 checklist，不强制结构）**：**Trigger**（事件 vs 计划触发；事件触发通常更高效，不空转）、**Checkpoint**（人回环的位置，可无）、**Push right**（把检查点推到最晚，人类只被问一次、打断次数压最低、每次打断价值提最高）、**Brief**（给人类看紧凑可决策摘要+链接，不是原始输出；「审阅越快，你越愿意把更多循环交给 AI」）。
- **完成标准（狠）**：一份 spec 算完成，当**实现它的 agent 能不问一个问题就把它建出来**——任何可能让执行者卡住的细节都必须在访谈里问出、写进。
- **工作区有状态**：`workflows/*.md` 每个 workflow 一个文件 = 事实源；`NOTES.md` 记「你的世界」（工具/渠道/称呼），访谈中的模糊说法收敛成规范术语（「清一下收件箱」→ Gmail 主收件箱 = 归档+回复紧急），跨会话记忆，越用越懂你。
- **模拟六问会话**（非真实录音，规则真实：一次一问、每问附推荐答案）：事件触发 / 完成度=产 brief 不直接改代码（回复/关 issue 是决策留给人类）/ 检查点放最后 / 呈现 brief / 补「完成动作」（人审后：确认跟进的转 issue 并标记，其余归档标记已读）——spec 若缺完成动作就不算完成。
- **边界**：频率低内容各异的循环不值得委托（写 spec 成本 > 自己做）；一个 workflow 一个循环（职责单一）；spec 写完 ≠ 事情做完（实现/部署/持续运行另接）；与 wayfinder 分工：一个管「一次会话装不下的大」（产决策），一个管「反复发生的小」（产说明书）；loop 里要写代码照走 tdd/code-review。

## 详细笔记

主系列外第 2 篇专题，主题 = 把「让 AI 干得靠谱」反转成「让 AI 干你没想过能交给它的活」，补上委托视角。wiki 首个 loop-me 深度源。

## 与已有内容的关联

- 与 [[grill-me 实战指南]]（grill 家族谱 v1.1 口径）及 [[还在用 grill-with-docs]]（grill-with-docs = grilling+domain-modeling 的划分）一脉相承，家族谱一致。
- 「有状态工作区」思路与 [[mattpocock skills]] 概念页、[[Matt Pocock wizard 人墙自动化]]（把固定步骤交给 agent）可关联。

## ⚠️ 矛盾 / 待澄清

- 把 grill-me 列作现行三兄弟之一（轻量壳），未提「grill-with-docs 取代 grill-me」——与 wiki 取代口径不一致（老谭系列通病）。
- 文中说 loop-me「不在官方 README 技能列表」——若日后毕业（类似 batch 并入 grilling），该状态声明会过期，需按时间戳引用。
- 文中自称「主系列 8 课」——与实际发布口径（主系列 01-07）不符，应为笔误或未兑现的计划，按 7 课计。

## 相关页面

- [[mattpocock skills]] · [[超级奶爸老谭]] · [[grill-me 实战指南]] · [[还在用 grill-with-docs]] · [[Matt Pocock wizard 人墙自动化]] · [[wayfinder 迷雾地图实战]]
