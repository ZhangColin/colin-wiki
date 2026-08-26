---
type: source
title: "Cole Medin 黑灯工厂实验"
domain: [AI编程, 软件工程]
tags: [黑灯工厂, Dark Factory, Archon, Cole Medin, holdout, builder-validator, 蓝绿部署, 治理文件, 自主度分级]
sources: []
source_path: "raw/articles/从 Skills 到黑灯工厂，一次把开发交给 AI 的实验.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/pn__FFm0WCKxaTJWflrccg"
author: "[[老丁888]]"
date_published: "2026-08-19"
created: 2026-08-25
updated: 2026-08-25
status: active
aliases: [AI Dark Factory, dark-factory-experiment, 黑灯工厂]
---

# Cole Medin 黑灯工厂实验

> 一句话要点：[[Cole Medin]]（30+ 开源 skills / Archon 作者）的开源实验 **AI Dark Factory**——"规格说明（spec）进去，可上线代码出来"的自主仓库：issue 进 → 自动分诊/实现/盲测验证/合并/蓝绿部署，人类只留"两盏灯"（提 issue、发版本）。核心论点：**这不是 vibe coding——可靠性靠提前搭 harness，不靠模型那一刻的运气**。

## 关键要点

### 1. 定义与出处

- "黑灯工厂"概念出自 **Dan Shapiro**（Glowforge 创始人），灵感 FANUC 1980s 机器人车间（无人类工人，灯都不用开）；搬到软件 = "spec 进去，软件出来"。
- 成品：RAG 聊天应用（chat.dynamist.ai，检索创作者视频转录并带引用回答），Cole 自述构建全程"**连它产出的一行代码都没看过**"。
- 为什么现在才可行：模型变强 + 编码 agent 变强 + **围绕 agent 的 harness 变强**，三者同时发生。
- 成本坦白：数百万 token、好几个月、踩遍所有坑；**不承诺能为任何代码库产出生产级代码**。只处理**能被机器验证对错**的代码；手感/审美/可读性与认证/支付类高危功能明确排除。

### 2. 五级自主，停在第四级 ⭐

Dan Shapiro《The Five Levels: from Spicy Autocomplete to the Dark Factory》（2026-01）五档：L0 手动挡（spicy autocomplete）→ L1/2 结对程序员 → L3 人当经理 → L4/5 连方向盘都没有（只剩控制台给 PRD）。→ 与 [[去哪儿网 AI Coding 研发平台实践]] 的 L0–L5 构成同构双源，已独立建页 [[AI Coding 自主度分级]]。

- Cole 在 README 白纸黑字：工厂跑在**第四级不是第五级**——第五级要工厂连 issue 都自己写，而他的工厂不写自己的任务单。
- **"两盏灯"**：人类保留两个人环节点——入口（提交 issue）与出口（发布新版本）。
- 克制建议："**第三级才是多数人该待的地方**"。

### 3. 三层架构：零件与管道分开

| 层 | 实现 | 角色 |
| --- | --- | --- |
| harness | **Archon**（Cole 开源工作流引擎） | 确定性管道：跑脚本、调 gh、解析输出、按结果分叉；`.archon/workflows/` 目录下的工作流文件 |
| 编码 agent | Claude Code（可换：Codex/Cline/Pi） | 每个节点拉起一个 agent 执行 |
| 模型 | Sonnet（推理节点）+ Haiku（廉价抽取节点） | 逐节点配置 |

- **锐利判断：模型路由是全系统成本最低的杠杆**——agent 和模型是可替换零件，外面的管道不动；实验中换过 MiniMax M2.7、Kimi K2.6（经 Pi），其他设计一点不动。
- "没有 Archon 这种东西，人要么一步一提示地戳 agent，要么写一个又大又脆的脚本包起来"——Archon 把"AI 有时能做到"变成"工厂每隔几小时自己就能做到"。

### 4. 刻意做笨的调度器

- cron 每 30 分钟触发，约 100 行 bash，**无状态、无 LLM 决策**——教训：曾让模型决定派发任务，模型"幻听"出不存在的任务去执行，遂改纯 bash。
- **GitHub 标签即状态机**：issue 线 `triaging→accepted→in-progress→(PR | rejected 附理由)`；PR 线 `implementing→needs-review→approved 自动合并 | needs-fix（最多修 2 次）→needs-human`。分诊打优先级（critical/high/medium/low）。
- 并行度旋钮：最多 4 个并行（撞速率限制的现实权衡）。调度脚本未开源，README 只给行为规格。

### 5. 三份治理文件（宪法）⭐

| 文件 | 作用 |
| --- | --- |
| CLAUDE.md | global rules：任何时候都适用的全局约束 |
| FACTORY_RULES.md | factory rules：自主运行时更严的边界（如"一次只处理一个 bite-size 任务"——人不在环时任务越大可靠性越差） |
| MISSION.md | 源自 PRD：应用目标 + 明确 out of scope |

- **铁律：工厂永远不能改它自己的宪法**。
- **agent 有权拒绝**：spec 不是不容置疑的真理——agent 可回"这超出应用范围 / 太复杂得先拆"，等于给自主系统装刹车。
- **禁区**：认证、流量控制、部署配置、治理文件——任何 PR 一碰，在评估其他任何东西之前先自动打回。"**一个自主系统有多可信，取决于它对自己有哪些事永远改不了**。"

### 6. 盲测场景（holdout scenarios）⭐ 最硬核

- 两个 agent：**builder（构建者）与 validator（验证者）彻底分离——builder 不知道测试，validator 不知道计划**。编码 agent 会对自己刚写的东西心软（偏见），validator 不知道实现也不知道计划，只拿挑剔眼光看产物。
- holdout：开工前从高层问"成功长什么样"，写成一组测试场景，**builder 永远看不到**——看不到就无法为通过场景专门设计应用，无法"挟持验收标准"。
- 谱系：点名归功 StrongDM 的 holdout 做法；另提 Spotify Honk、Steve Yegge 的 Gas Town——"规则从每一个失败过的黑灯工厂尝试里研究出来"。
- Cole："没有这套盲测，连把黑灯工厂当实验来跑都不敢。"

### 7. 上线与自愈

- **蓝绿部署**：两份应用并存（在线 + 待命），合并后自动部署预览环境，人定期把预览升正式——"PR 合并后不自动上线，那搭的只是个 PR 生成器，不是黑灯工厂"。
- **每周自愈巡检**：每周启动应用，agent-browser 跑 4 个端到端浏览器场景，合成报告并把发现的问题**自动提回 GitHub issue**——工厂自己找自己的 bug，排进自己的队列。

### 8. 复现路径（4 条命令）

1. `/plugin marketplace add coleam00/skills` + `/plugin install skills@cole-medin`（32 个 skill 只读托管插件）
2. 装 Archon（可选）：`npx skills add coleam00/Archon` 或 `curl -fsSL https://archon.diy/install | bash`
3. `git clone https://github.com/coleam00/dark-factory-experiment.git`（工作流在 `.archon/workflows/`）
4. `/build-dark-factory product.prd.md`——skill 先**采访**（自主度/工作流偏好/验证策略）再建工厂，拒绝通用模板
- 实用起步建议：别急着搭整套，先搬一个零件——盲测场景（开工前写下"成功长什么样"且 builder 永远看不到），是整条视频里性价比最高的一课。
- 为什么用游戏测试：需要一个**能不断加难度的载体**压极限（聊天应用会"功能臃肿"）——这本身是关于"如何验证系统"的方法论。

## 与已有内容的关联

- [[AI Coding 自主度分级]]：本源（Dan Shapiro 五级）与 [[去哪儿网 AI Coding 研发平台实践]]（L0–L5）双源触发建页；两框架的"L3 分水岭"共识（多数人该待的地方 / 去哪儿 2026 目标）。
- [[Harness Engineering]]：第 5 个独立来源——三层架构 = Agent + Model + Harness 的具象化；"宪法不可自改"与禁区清单是约束支柱的极端形式；个人/小团队路线与大厂平台路线（腾讯/去哪儿）互补。
- [[Agent 测评 Eval]]：holdout 是防自欺的第三种机制——与腾讯基线（人确认过的快照）互补：基线防漂移，holdout 防挟持验收标准。
- [[子代理驱动开发]]：builder/validator 分离把"作者与审查者不同上下文"推到极致（互相都不知对方的信息），与 Superpowers v6.0 "Do Not Trust the Report" 只读怀疑论者 reviewer 同构。
- [[HITL 与 AFK]]：黑灯工厂 = AFK 极端形态 + 最小 HITL（两盏灯）；调度器"无 LLM 决策"是"机械执行才归自动化"判据的教科书案例。
- [[Superpowers]]：Cole skills 的 PIV 循环（Plan/Implement/Validate）与 7 步流程同族（brainstorm→plan→implement→verify→ship）。

## ⚠️ 矛盾 / 待澄清

- 文章为 [[老丁888]] 对 Cole Medin 视频及 README 的二手改写，非一手源；数据（数百万 token、耗时数月）为作者自述，无复核。
- 调度器脚本本身未开源（只给行为规格），完整复现依赖 build-dark-factory skill 引导——实验的"完全开源"程度需打折扣看待。
- "一行产出代码都没看过"是自述且限于其 demo 应用（可机器验证的类型），不可推广为普适结论。

## 相关页面

- [[Cole Medin]] · [[老丁888]] · [[AI Coding 自主度分级]] · [[Harness Engineering]] · [[Agent 测评 Eval]] · [[子代理驱动开发]] · [[HITL 与 AFK]] · [[Superpowers]] · [[去哪儿网 AI Coding 研发平台实践]]
