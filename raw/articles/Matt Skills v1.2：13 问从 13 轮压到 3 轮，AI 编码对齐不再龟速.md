---
title: "Matt Skills v1.2：13 问从 13 轮压到 3 轮，AI 编码对齐不再龟速"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491659&idx=1&sn=d0909b183f526cc897f59f86907e54c9&chksm=cf40551df837dc0bf157b9035c0e63b8621bdebb68a1d7b5086e76fe39e3c29e58d9815f49fc&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-12
description:
tags:
---
运维有术 术哥无界 *2026年8月11日 08:20*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *192* 篇，AI 编程最佳实战「2026」系列第 *69* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

现在爆火的 Grill-Me，核心就一步： **先对齐需求，再写代码** 。

v1.1 靠着一次一问 + 确认门控 + 事实决策分离这三板斧，把 AI 编码的对齐体验从群魔乱舞拉回正轨，成了无数开发者的标配入口。

但新问题很快浮出水面： **一次一问，太慢了** 。

- 13 个问题就要 13 轮，一轮一轮等，急性子直接崩溃
- 简单需求也被拆成连环追问，问得人想摔键盘
- 想跳过几个问题，AI 还不让，必须答完才继续

社区吐槽的声音越来越大： **对齐质量上来了，对齐效率下去了** 。

终于，GitHub 212.2K Star 的 Matt Skills 在 2026 年 8 月 5 日发布 **v1.2 史诗级更新** 。

这次更新把底层 /grilling 面试原语彻底重构：从一次一问升级为 **round-by-round frontier 推进** ，13 个问题约 3 轮问完，效率大幅提升！

今天深度拆解这次重构的底层逻辑、争议边界、全家桶更新与落地配置，看完直接淘汰旧工作流。

## 1\. v1.1 的功与过：救回对齐，却慢到让人想放弃

先说清楚，v1.1 不是失败版本，它打了一场漂亮的翻身仗：

- **一次一问** ：单问单答，杜绝批量轰炸
- **确认门控** ：不确认 shared understanding 绝不开工
- **Facts vs Decisions 分离** ：AI 只查事实，决策全交用户

这三个修复把 AI 编码对齐从混乱拉回正轨，社区口碑直接爆了。

但问题也随之而来： **一次一问的正确性，是用效率换来的** 。

翻了一圈官方文档和社区反馈，v1.1 时代的新槽点出奇一致：

- 13 个问题 13 轮，一轮一轮地答，急性子直接崩溃
- 简单需求也被拆成连环追问，问得人想摔键盘
- 对齐一次要来回磨很久，比写代码还累

简单说：v1.1 解决了 AI 乱来，但代价是人太累。正确性和效率，成了新的矛盾。

## 2\. v1.2 核心重构：从一次一问到 round-by-round frontier

这次 v1.2 的核心变化，是把 /grilling 从一次一问重构为 **round-by-round frontier 推进** （PR #593），逻辑就三个词： **设计树、frontier、轮次** 。

### 设计树：把需求画成一棵决策树

grilling 会把待对齐的事映射成一棵 **设计树（design tree）** ，每个决策分支，下面挂着依赖它的子决策。

树的形状决定了提问顺序：父决策没定，子决策就没法问。

### frontier：当前能诚实问的问题集合

**frontier（前沿）** ，是所有前置条件已满足的决策集合。翻译成人话： **当前这一刻，能问出口的问题** 。

一轮只问 frontier 里的问题，用户答完，答案重塑决策树，frontier 外扩，再进下一轮。

### round：一轮问完整个 frontier

每个问题带上编号， **附上 AI 的推荐答案** ：

> ❓ **Q1** - : \<question body, might be multiple paragraphs, including multiple choices>
> 
> ➡️ ：\<your recommended answer>

用户按编号回答，答得上的直接答，答不上的看推荐答案做选择，一轮下来效率极高。

官方文档给出明确数据： **13 个问题约 3 轮问完，而不是 13 轮** 。

### 一次一问 vs 一轮 frontier

| 维度 | v1.1 一次一问 | v1.2 一轮 frontier |
| --- | --- | --- |
| 提问方式 | 答完一个再问下一个 | 一轮问完整个 frontier |
| 问题数量 | 13 个问题 13 轮 | 13 个问题约 3 轮 |
| 推荐答案 | 无 | 每题附 AI 推荐答案 |
| 推进方式 | 线性推进 | 树状外扩 |
| 用户负担 | 高（全程等待） | 低（批量作答） |

有意思的是，round-based 曾短暂作为独立技能 batch-grill-me 发布，随后并入了 grilling 本体，所有依赖它的技能一次获得这个能力。

![[Image 108.webp|round-by-round frontier 机制图解：设计树与轮次外扩]]

round-by-round frontier 机制图解：设计树与轮次外扩

## 3\. Facts vs Decisions：权责分离在轮次里怎么运作

v1.1 引入的 Facts vs Decisions 分离，v1.2 不仅保留，还强化了。

**Facts（事实）** ：代码规范、现有实现、仓库配置等客观信息，AI 直接派 sub-agent 去查， **不阻塞当前轮** ：只有依赖它的后续问题才需要等待。

**Decisions（决策）** ：架构选型、功能范围、交互逻辑等主观选择， **必须逐一提给用户并等待回答** 。

| 类型 | 定义 | 处理方式 |
| --- | --- | --- |
| Facts（事实） | 代码规范、现有实现、仓库配置等客观信息 | AI 派 sub-agent 自主查询，不阻塞轮次 |
| Decisions（决策） | 架构选型、功能范围、交互逻辑等主观选择 | 必须提问用户，由用户最终拍板 |

这套分离在 frontier 的运作逻辑里尤其关键： **AI 查事实的同时，用户已经在回答决策了** ，两边并行，谁都不等谁。

不过边界也有已知 bug 形态：当另一个技能在 resolve-ticket 框架内运行 grilling 时，周边任务容易被误读为可以自主回答决策。官方文档明确这是已知问题，使用时要注意。

![[Image 109.webp|一次一问 vs 一轮 frontier 五维对比图]]

一次一问 vs 一轮 frontier 五维对比图

## 4\. 确认门控与 opt-out：争议设计的边界在哪

v1.2 保留了确认门控： **frontier 为空 ≠ 结束** ，必须用户确认 shared understanding 后才可行动。

官方原话是： **Do not act on it until the user confirms you have reached a shared understanding** 。

换句话说，就算问题全问完了，AI 也不能擅自开工，这道闸门永远在用户手里。

### opt-out：一行配置回到一次一问

round-based 是官方明确标注的 **争议设计（contested design）** ，不是所有人都适合。

读得慢的人、二语使用者、需要顺序脚手架的用户，可以在全局 CLAUDE.md 加一行配置回到一次一问：

`When grilling, ask one question at a time.`

官方文档特意强调，这是 **supported rather than tolerated** （受支持而非被容忍），团队认真对待了这个争议，而不是敷衍。

### 两个已知边界

- **弱模型仍可能失效** ：低 effort 模型会把 interview until shared understanding 压缩成几个问题加一个大纲，可靠修复是在 AGENTS.md/CLAUDE.md 加一句 not to implement without permission
- **frontier 是判断不是计算图** ：一轮内两个问题可能实际有依赖，只能靠用户指出后在下一轮重开分支

说实话，这两个边界比功能本身更值得关注： **工具再强，护栏也得自己上** 。

## 5\. v1.2 全家桶：不止 grilling 一场重构

除了 grilling 重构，v1.2 还带来了一批值得关注的变化：

- **Claude Code 官方插件** ：一条命令 `claude plugins install mattpocock-skills` 安装，进入官方 marketplace，只读 bundle 自动更新
- **新技能三连** ：/wait-wait（单字纠正啰嗦）、/to-questionnaire（问卷榨取他人知识）、/wizard（交互式 bash 向导）
- **/prototype 重构** ：逻辑分支产出单一可分享 HTML 文件，无需构建和服务器
- **writing-great-skills → writing-for-agents 改名** ：覆盖所有 agent 消费的文档
- **双 harness Codex 支持** ：每个技能加 agents/openai.yaml，AGENTS.md 是 CLAUDE.md 的符号链接
- **减法更新** ：移除 6 个技能，to-prd → to-spec 改名彻底完成，spec 成为统一术语

这一波更新说明一件事： **Matt Skills 不再只是 Claude Code 专属，而是朝着通用 agent 技能标准走** 。

## 6\. 入口怎么选：三大 Grilling 入口 + 7 行薄封装

v1.2 里，grill-me 变成了 **7 行薄封装** ，正文只有一句：Run a /grilling session。

grill-with-docs 同样 7 行：Run a /grilling session, using the /domain-modeling skill。

核心逻辑全部下沉到 /grilling 原语，三大入口按场景选：

### 1\. /grill-with-docs（日常推荐）

**适用：已有代码库、迭代开发、存量项目改造**

面试同时搭建领域模型，自动更新 CONTEXT.md 和 ADR，对齐精度高，是 Matt 官方主推方案。

### 2\. /grill-me（轻量推荐）

**适用：新项目早期、无代码库、纯产品需求讨论、小型功能验证**

纯对话轻量化追问，无代码依赖，快速厘清需求边界。

### 3\. /wayfinder（大型复杂项目推荐）

**适用：超大模糊需求、跨会话迭代的大型项目**

v1.2 引入 **decision ticket（决策票）** 概念：单元改称决策票，research 票由 subagent 并行烧掉，HITL（grilling/prototype）和 AFK（research/task）分类管理。

一个提醒： **grill-me 依赖 grilling** ，只装 grill-me 不装 grilling 会 nothing happens，官方文档明确写了这个坑，别踩。

## 7\. 社区口碑：212K Star 背后的真实评价

先看数据（截至 2026-08-10）：

- **GitHub 212.2K stars** ，全站第 19 名
- **skills.sh 累计 13.5M 下载**
- 增长曲线：5 月 77K → 7 月 170K → 8 月 212K，三个月涨了近 3 倍

第三方评测怎么看？

AI Coding Daily 专门实测了 v1.2 的 round-based grilling，称 grill-me **一直是 planning 领域的标准** 。

ryanuo.cc 的深度对比给了一个很精准的定位： **grill-me is the pressure-test primitive; Superpowers is the default engineering OS** ：grill-me 是压测原语，Superpowers 是默认工程操作系统。

用户反馈更有说服力。GitHub Discussions #214 里有人写道：过去 AI 经常跑偏要逐步微调，试了一次 /grill-with-docs 后， **现在大部分时候只需要说 yes** 。

社区共识是： **可组合而非互斥** ，先用 grill-me 对齐，再用 Superpowers 的 plan/TDD/worktree 跑全流程，各取所长。

![[Image 110.webp|Matt Skills 社区口碑与增长数据图]]

Matt Skills 社区口碑与增长数据图

## 8\. 2026 极简配置（直接抄作业）

### 安装

两种方式任选：

- Claude Code 插件： `claude plugins install mattpocock-skills`
- skills.sh 可编辑文件： `npx skills@latest add mattpocock/skills` ，务必选 setup-matt-pocock-skills

每个仓库跑一次 /setup-matt-pocock-skills：选 issue tracker、triage labels、文档保存位置。

### 五步闭环工作流（官方主链路）

**Grilling 需求对齐 → Spec 规格固化 → Tickets 任务拆解 → Implement 落地编码 → Code-Review 自动审校**

1. **Grilling** ：grill-with-docs 或 grill-me，扫清需求模糊点
2. **Spec** ：对话决策一键生成标准化规格文档
3. **Tickets** ：按依赖拆分可执行子任务（tracer-bullet 票，声明 blocking edges）
4. **Implement** ：逐任务自动编码、自测、修复
5. **Code-Review** ：双轴审查（Standards + Spec），并行 subagent

### 通用极简配置（90% 开发者适用）

核心闭环： **grill-with-docs → to-spec → to-tickets → implement → code-review**

### 进阶增强配置

在通用流程上补两个关键技能：

- **/improve-codebase-architecture** ：v1.2 加了 YAGNI 过滤，Explore 只聚焦活跃开发路径（读最近约 20 条 commit 消息），更精准
- **/wait-what** ：模型啰嗦时单字纠正，配合 /caveman 把输出压到最简
![[Image 111.webp|五步闭环工作流：从需求对齐到自动审校]]

五步闭环工作流：从需求对齐到自动审校

## 九、总结：2026 AI 编码对齐的最终答案

Matt Skills v1.2 的更新，本质是 **把对齐从正确但慢推向又快又准** 。

它没有堆砌新概念，而是做了一件反直觉的事：

- 管住 AI 的嘴：从一次一问升级为一轮 frontier，问得更快
- 管住 AI 的手：确认门控保留，frontier 为空也不许开工
- 理顺权责：facts 并行查、decisions 必须问，边界比以前更清晰

如果说 Superpowers 是全流程 OS，那 **grill-me 就是那个把入口对齐做到位的 scalpel** ：轻、快、准，还给你留了 opt-out 的后门。

round-based 这个设计能不能成为标准，半年后看有多少人切回一次一问就知道了。

> **说明** ：本文内容基于 Matt Pocock 的 mattpocock/skills 仓库源码（v1.2.3）和 v1.2.0 官方发布说明分析整理而成，源码分析基于笔者本地仓库版本，尚未在生产环境中完成全场景验证。 **文中的配置模板和参数建议仅供参考，实际效果请以你的业务数据和环境测试结果为准。** 如果有实际使用经验，欢迎在评论区分享交流。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录