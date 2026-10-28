---
type: source
title: "Matt Pocock retro 拆解"
domain: [AI编程, 软件工程]
tags: [mattpocock skills, retro, v1.3, 环境改进, CODING_STANDARDS, 机械违规, 造检查, writing-for-agents, AGENTS.md]
sources: []
source_path: "raw/articles/mattpocockskills retro 技能深度拆解.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484378&idx=1&sn=a234a51b93253f9486b5839875e58d1a"
author: "[[超级奶爸老谭]]"
date_published: "2026-10-14"
created: 2026-10-28
updated: 2026-10-28
status: active
aliases: [retro 拆解, retro 技能深度拆解]
---

# Matt Pocock retro 拆解

> 一句话要点：`retro` 是 45 行的手动复盘技能——**它不改代码，改下次运行时 AI 所处的环境**。「AI 犯的错，大部分不是能力问题，是环境问题。你在 CLAUDE.md 里堆规则，是在给堵车的路口加红绿灯。」

## 关键要点

- **定位与触发**：跑砸的会话后手动 `/retro`（默认复盘当前会话，可指向日志中任意历史会话）。手动是写死的硬约束（`disable-model-invocation: true` + openai.yaml）——「跑得顺的会话没什么可学的，产出大多来自那些难熬的会话」，复盘需要人判断，自动执行只会攒一堆没人需要的规则。
- **七检查类别，各带 Use when 触发条件**：导航（信息难找）/ 自动化检查（工具本可拦下的错）/ 编码规范（reviewer 漏错）/ 全局 AGENTS.md（引导指令该搬家）/ 工具经济性（烧 token 的笨办法）/ 无效指令（不改变行为的句子）/ 信息获取（关键信息进不来）。只提议不动手；按严重程度排序呈现。
- **最锋利的一刀（结构性论证）**：实现 agent 承受最大上下文压力（探索+编码+调试塞满窗口），评审 agent 上下文最干净（只看 diff）→ **编码规范应该由评审 agent 负责，而不是实现 agent**。推论：新规则不进 AGENTS.md（每会话必推、用不上也推）。文件分工：AGENTS.md/CLAUDE.md 只放导航指针（极省着用）/ CODING_STANDARDS.md 评审阶段读（>1000 行往文档目录拆）/ 文档是被指路才读的参考 / 技能留给自动触发内容。
- **机械违规 vs 判断题（分水岭）**：固定句法/禁用 API/import 形态/文件位置 → **默认造检查**（linter / pre-commit / CI，选最省事的）——「检查会失败，规范文件里的一句话不会。会失败的东西能挡住错误，一句话挡不住」；跨文件一致性、贴合周边风格这类**判断题**才配 CODING_STANDARDS.md 一行字。配套推论：先读仓库已有的检查命令——已存在但没接上/悄悄失效的检查，发现应是「接上」而不是重造；**没 lint 的仓库本身就是一个发现项**（「不是一个中性默认状态，而是一个长期错过的机会」）。
- **官方三条自我批评**：① 最大风险是为填满类别而**编通用建议**（任务一结束 AI 倾向忘掉会话中段吃过的苦）——硬约束：每条候选必须来自会话自己的记录，追溯不到具体时刻的直接丢；② **规则只增不减**——它不审计历史规则（哪条 lint 现在触发太频繁、背后的 bug 已不存在），「修剪检查这件事还得你自己来」；③ 长会话滑出智能区就清空重开、指向日志复盘。CODING_STANDARDS.md 由 retro 首次提议创建，之后 code-review 去读（已有 CONTRIBUTING.md 同样管用）。
- **链上位置**：主链终点 `grill-with-docs → to-spec → to-tickets → implement → code-review → retro`；与 improve-codebase-architecture 的区别在输入（会话历史 vs 代码）；第一步先调 writing-for-agents 拿写作风格再读记录——「先学会怎么写，再去写」。不做记忆系统：**「它不记录发生过什么，它改变环境，让错误没法再发生一次。」**

## 详细笔记

[[超级奶爸老谭]]「共渡时艰 · 拆解」栏目（源码级，本地克隆 commit 49dd158 / 2026-10-09，星标 289024 / 10-13 API 查证）。retro 2026-10-04 随 **v1.3.0** 毕业（PR #1120，与 implement-spec、pr 同批）——本篇与 [[Matt Pocock implement-spec 拆解]] 同为 v1.3 口径依据。文末对话式收束点出方向：「从人去找错误，变成环境不让错误发生」。

## 与已有内容的关联

- **对 colin-wiki 直接自反**：本 wiki 的 CLAUDE.md/AGENTS.md schema 正是 retro 批判的对象形态——「机械违规默认造检查」提示：能写成可失败检查的规矩（如「改一处必同步另一处」）优于一句 prose（推论，可作下次 lint 议题）。
- **与 writing-great-skills 同族**：七类中的「无效指令」= no-op 判定、「AGENTS.md 该搬家」= Information Hierarchy、「规则只增不减」= sediment（[[Matt Pocock writing-great-skills 八词诊断]]）——retro 可视为八词诊断的**会话后自动化体检**形态。
- **与 Superpowers writing-skills 的张力（synthesis 素材）**：[[Superpowers writing-skills 规矩固化成技能]] 主张把规矩写进 SKILL.md（写规则）；retro 主张能造成检查的绝不写规则（造检查）——两者在「判断题才配写文件」处会合，已注记于 [[writing-skills 跨路线对照]]。
- **与 Skills 设计方法论**：「AGENTS.md 每会话必推」即三层成本模型的 Index 层税；「导航指针极省着用」即 progressive disclosure（[[Skills 设计方法论]]）。
- 同批毕业：[[Matt Pocock implement-spec 拆解]]。

## ⚠️ 矛盾 / 待澄清

- 与 wiki 既有记载无冲突。
- 注记：三条自我批评为官方文档自述（罕见地把批评写进正文），非老谭评价；「星标 289024（10-13）→290612（10-14）」日增千余为该仓库正常增速。

## 相关页面

- [[mattpocock skills]] · [[超级奶爸老谭]] · [[Matt Pocock implement-spec 拆解]] · [[Matt Pocock writing-great-skills 八词诊断]] · [[writing-skills 跨路线对照]] · [[Skills 设计方法论]] · [[improve-codebase-architecture 代码库体检]] · [[LLM Wiki]]
