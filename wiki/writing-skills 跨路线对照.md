---
type: synthesis
title: writing-skills 跨路线对照
domain: [AI编程, AI工具]
tags: [writing-skills, SKILL.md, description触发, no-op, Gotchas, 跨路线对照, mattpocock skills, Superpowers]
sources:
  - "[[Matt Pocock writing-great-skills 八词诊断]]"
  - "[[Superpowers writing-skills 规矩固化成技能]]"
  - "[[Skills 设计方法论]]"
  - "[[Matt Pocock retro 拆解]]"
created: 2026-10-22
updated: 2026-10-28
status: active
confidence: medium
aliases: [写 skill 的跨路线共识, writing-great-skills vs writing-skills]
---

# writing-skills 跨路线对照

> 一句话结论：两条「路线之争」的 skills 体系，在**怎么写一个 skill** 上给出高度一致的答案——description 是触发器、指令要可执行（废话即 no-op）、坑位是资产、写完要验证；分歧只在姿态：mattpocock 路线是**做减法的诊断手术**，Superpowers 路线是**做加法的规矩固化**。

## 问题

[[Superpowers]] 与 [[mattpocock skills]] 是本 vault 记录的两条 Claude Code skills 主路线（见 [[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]]）。两者各有一个「教你怎么写 skill」的元技能：mattpocock 的 `writing-great-skills` 与 Superpowers 的 `writing-skills`。它们是各说各话，还是有跨路线共识？共识到了什么程度？

## 对比 / 分析

| 维度 | mattpocock writing-great-skills | Superpowers writing-skills（老谭教学口径） | 共识判定 |
| --- | --- | --- | --- |
| 定位 | 去除手术清单：诊断**已有** skill 里的病灶 | 固化流程：把项目规矩写成**新的** SKILL.md | **互补**：一减一加（见下） |
| description | 至少 2 个 trigger 短语；model-invoked vs user-invoked 决定 context 付费 | description 是触发条件，决定 AI 什么时候「想起来读它」；写不清根本不会被想起 | **完全一致**：description = 路由触发器，非功能文档（[[Skills 设计方法论]] 第 6 柱） |
| 指令质量 | no-op 判定：「删掉它，模型行为变没变」；「most prose that fails should go」 | 「正确的废话」vs 可执行指令：「注意精度」是废话，「用 `compareTo`」是指令；AI 不需要提醒，需要指令 | **同一测试的两种表述**；Perplexity 版本：「没有它 agent 会做错吗？不会就不能留」 |
| 坑位/教训 | sediment 清理 + gotcha 治理 | 「已踩过的坑（不要再踩）」区块固化进技能 | 同构于 **Gotchas 飞轮**（高 ROI 长期资产） |
| 验证 | （不显式；靠 evals 体系外挂） | 第四步：让 AI 照规范写代码逐条对照，漏了回炉 | 老谭版是**端到端任务 Eval 的雏形**（四层 Eval 第 3 层） |
| 加长指令 | 「concision skills fail by growing」，失败散文应删不应改写 | 规矩一二三条、具体到照着做 | 均反对膨胀；与「不应靠加长指令解决问题」同源 |

**分歧点（真分歧而非矛盾）**：
1. **姿态**：mattpocock 面向「已有 skill 的体检与瘦身」（八词是手术刀），Superpowers 面向「把人的工程纪律固化为 skill」（SKILL.md 是容器）——前者默认 skill 已存在且容易烂，后者默认规矩在人的脑子里而文件是归宿。
2. **适用域**：mattpocock 路线的 skill 面向跨项目复用与发布（工程化标准），老谭演示的是项目级规范技能（绑定幸福账本单一项目）。
3. **谁执笔**：两者都隐含「人写、AI 照做」——与 Perplexity「LLM 自己写的 Skills 平均无收益」一致；Superpowers 更进一步主张连「人的记性」也不可信（「记性靠不住，文件靠得住」）。

## 结论

跨路线共识四条可直接当 SKILL.md 写作检查单用：①description 写成「何时触发」而非「这是什么」；②每条规矩过 no-op 测试（删掉行为变没变 / 没有它 AI 会做错吗）；③把踩过的坑写成区块；④写完让 AI 实际用一次验证。分歧是适用姿态：改存量用 mattpocock 的手术清单，固增量用 Superpowers 的固化流程——**对 colin-wiki 自身的启示**：CLAUDE.md/AGENTS.md 这类「规矩文件」同样适用四条检查单（如「全部用中文输出」过 no-op 测试，「改一处必同步另一处」是 gotcha 式条款）。

**v1.3 新证据（2026-10 补）**：mattpocock `retro` 把分水岭再推一格——**机械违规默认造检查（linter/CI），判断题才配写进规范文件**（「检查会失败，一句话挡不住」），且编码规范应由评审 agent（上下文干净）而非实现 agent（上下文拥堵）负责、新规则不进 AGENTS.md。这与 Superpowers writing-skills「把规矩写进 SKILL.md」形成正面张力，但会合点仍是 no-op 测试：**能用会失败的机制挡住的，不写成一句 prose**。见 [[Matt Pocock retro 拆解]]。

## 引用

- [[Matt Pocock writing-great-skills 八词诊断]] — 八词/no-op 判定/Information Hierarchy/最小模板
- [[Superpowers writing-skills 规矩固化成技能]] — description 触发/可执行指令/踩坑区块/技能生效验证
- [[Skills 设计方法论]] — description 路由触发器/Gotchas 飞轮/四层 Eval/「不应靠加长指令」

## 相关页面

- [[Superpowers]] · [[mattpocock skills]] · [[Skills 设计方法论]] · [[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]] · [[Matt Pocock writing-great-skills 八词诊断]] · [[Superpowers writing-skills 规矩固化成技能]] · [[超级奶爸老谭]] · [[LLM Wiki]]
