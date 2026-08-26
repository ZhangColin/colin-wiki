---
type: source
title: "去哪儿网 AI Coding 研发平台实践"
domain: [AI编程, 软件工程, 研发管理]
tags: [AI Coding, Harness, 出码率, 自动化分级, L0-L5, Skills 治理, 研发平台, 双估时, 度量, 大厂实践]
sources: []
source_path: "raw/articles/精华：去哪儿网AI Coding研发平台实践，值得读三遍的样本.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/Ug_fMuGkQmM4tECUbpfXOg"
author: "[[爱海贼的无处不在]]"
date_published: "2026-06-24"
created: 2026-08-25
updated: 2026-08-25
status: active
aliases: [去哪儿 AI Coding, Qunar AI Coding, QunarDevCenter, 天弦, QDO, Qsuperpowers, 去哪儿李佳奇分享]
---

# 去哪儿网 AI Coding 研发平台实践

> 一句话要点：去哪儿旅行基础架构负责人/技术总监**李佳奇**的技术大会分享（[[爱海贼的无处不在]] 深度解读）——几千人研发组织 100% 使用 AI Coding 之后，如何**度量（Volume×Maturity）、控制（Harness 四把锁）、编排（天弦 QDO）、定制（Qsuperpowers）、治理（Skills Gateway）**，把 AI Coding 从"个人玩具"做成"组织能力"。核心论点：**AI Coding 的上限，取决于研发系统是否为 AI 重新设计。**

## 关键要点

### 1. 度量先行：AI R&D Metrics = Volume × Maturity

- 最"反共识"的一页：出码率/出码量/团队覆盖率/需求覆盖率全是**过程指标**（"越多就越好吗？"）——"出码率 90%"背后的质量、安全、可维护性谁来负责？
- **量**（Quantity）：出码率、出码量、团队覆盖率、需求覆盖率；**质**（Quality）：Coding 自动化水平（L1-L3）+ Harness 等级（Refined）。
- **T/M/C 三指标**（均可观测、越小越好）：**T**=会话绝对时长跨度（推进效率）、**M**=用户消息数（人工介入频次）、**C**=用户输入字符数（需求表达精炼度）。T/M/C 同降说明 Skills 沉淀与约束在起作用，反之 AI 还在"乱撞"——把"AI 写代码"从模型能力问题变成工程问题。
- **出码率计算**：两次 tag 间 diff 为基线 + **Git Blame 区分"人写的 vs AI 写的"**（避免把用户微调算成 AI 产出）；必须可下钻到部门/项目/人/session/文件，否则是"自欺欺人的数字"。
- 数据采集底座：2025 H1 先做研发全流程数字化埋点，2026 才在其上建 AI Coding 数据体系——**先有数据底座，才有 AI 研发管理**。

### 2. AI Coding 自动驾驶分级 L0–L5

| 等级 | 定义 | 类比 |
| --- | --- | --- |
| L0 | 全手动，不用 AI | 人工驾驶 |
| L1 | 补全/辅助，人主导 | 辅助驾驶 |
| L2 | 按注释/文档生成模块，人监控组装 | 部分自动驾驶 |
| L3 | 人给需求+规范+示例，AI 生成可运行代码并自测，阻塞时求助 | 有条件自动驾驶 |
| L4 | AI 承担编码/测试/集成/交付，人管目标/架构/审查 | 高度自动驾驶 |
| L5 | 需求→上线全 AI，人仅异常介入 | 无人驾驶 |

- 价值：① 统一语言（"我们用 AI 写代码"人人理解不同，分级后可对齐）② 可治理（不同等级配不同人工审查强度）③ 可衡量（每升一级，个人效率→组织能力的跃迁非线性）。
- 目标：2026 H1 **L3 任务占比 30%+**；实际 1-2pd 规模需求 L3 占比已达 **80%+**。

### 3. Harness：决定上限的不是模型 ⭐

- **Harness = AI 研发过程控制能力**——衡量 AI 在流程中是否被**稳定触发、被约束、被隔离、被审查**（不是只看 AI 是否参与，更看参与方式是否可控、可复用、可审计、可规模化）。
- 把核心研发流程 **12 个环节**逐一拆出 AI 参与控制点：需求文档编写 / 需求评审 / 方案设计 / 代码编写 / 代码测试 / Checklist 编写 / 开发自测 / 代码 Review / QA 测试 / 自动化回归 / 灰度验证 / 生产发布——每环节都是"AI 动作 + 约束 + 人工节点"三段式（如代码编写 = Skills 触发→编码规范→隔离环境）。
- **四把锁**：① AI 触发机制（关键环节引入 Skills/Workflow/Agent）② 约束与门禁（输入模板/编码规范/质量标准/准入条件/失败拦截）③ 安全隔离环境（沙箱/beta，AI 不直接进生产链路）④ 人工审查节点（需求/设计/合入/灰度/发布保留人工确认权）。
- 目标：**安全地提升自动化**。"没有 Harness 的 AI Coding 系统，就像没有方向盘的车，模型再强也只会撞墙。"
- → [[Harness Engineering]] 概念的第 4 个独立来源（OpenAI 提出、腾讯落地、AgentScope 框架化、去哪儿平台化）。

### 4. 三大平台工程实现

**QunarDevCenter（数据采集 + CLI 代理）**：
- 多端 session 采集：Claude Code 扫 `~/.claude/projects/**/*.jsonl`、Codex 扫 `~/.codex/sessions/**`、OpenCode 读 SQLite 拼等价 jsonl；统一 Provider 抽象屏蔽差异。
- 本地过滤上报：仅上传 git remote url 命中公司 GitLab 白名单的会话（安全合规红线）；sha1 去重、mtime+size 快速路径、限速、4xx 不计失败/5xx 累计 3 次跳过。
- 三张核心表：`session_upload_objects`（原始 artifact+元数据）、`session_meta_info`（会话级，含 token 数）、`ai_gen_code_change`（AI 代码变更，add/delete 行数+分支+时间）——为出码率与 T/M/C 分析量身定做。

**天弦 QDO（研发自动化编排平台）**：
- 多 Agent（Claude Code/Codex 统一调度）+ 多 Skills + 全链路编排：任务输入→编排→Agent 执行→基础 Skills→验证闭环→代码交付。
- **核心能力是自修复闭环**：编译失败/部署失败/测试失败 → AI 查日志 → 分析原因 → 修复重试。
- 传统 11 步人工流程（数小时~数天）→ AI 11 步（分钟~小时级），保留人工关键 Review。
- 场景：一句话需求 / 异常修复 / workflow 需求 / 线上诊断 / **JDK 自动升级**（OpenRewrite 规则 + Agent 补长尾 + 流水线闭环：累计升级 211 个应用、编译通过率 93%）。

**Qsuperpowers（基于社区版 [[Superpowers]] 的企业定制）** ⭐：
- 动机原话："通用 superpowers 提供流程标准化能力，但**不包含企业个性化需求、内部系统集成、工程规范落地**，所以我们需要 Qsuperpowers。"
- 定位从"助手"升级为"工程**搭档**"：减少偏差、瞎猜、无验证、非随意发挥。
- 四阶段闭环直接复用 Superpowers skill 名：ai-task-generation（brainstorming + writing-plans）→ ai-coding（**subagent-driven-development** + executing-plans）→ ai-deployment（Noah CI/CD）→ ai-testing（**test-driven-development** + requesting-code-review + finishing-a-development-branch）。
- 四大定制集成：飞书文档（降信息损耗）/ AST（提理解准确率）/ CI-CD（提交付效率）/ 灰霸测试（保障可靠性）。
- 案例 FD-396503（脱敏）：3 系统协作、8 任务跨项目复杂需求，人工估时 3pd → AI 实际 1pd，**提效 66%，无 bug 上线**。

### 5. Skills 沉淀与治理：从"个人经验"到"组织资产"

- 规模：**50 个 Skills、4 个 Domain**（通用研发能力 / 业务域开发 / 企业工具链接入 / Agent 角色与治理）。
- 流转闭环：团队**私有仓验证** → PR 提交**标准仓** → 基础研发团队审核（边界/安全/通用性/依赖）→ 合并统一分发——"创新在线，标准化在平台"。
- **Skills Gateway**：统一收口下发/执行/观测/分析——记录谁在什么场景以什么效果使用，沉淀 usage、成功率、复用率、失效点。
- **种子上下文**：技能执行前注入任务背景/目标/历史进展/当前阶段/上下游依赖，减少 skill"只看当前输入"的盲区。
- Skill 市场：可搜索、带下载量/版本/共享方元信息——"像 App Store 之于移动应用"。
- 编排型 skill 代表 `cm/auto_dev`：证据驱动的端到端编排——不按固定流程硬走，根据最新证据决定下一步（读代码/读飞书/部署/查日志/改配置/跑 E2E）。

### 6. 成果与"双估时"

- 成果（2026-06 口径，自述）：100% 研发人员覆盖（FE/BE/QA/部分 PM）、全司出码率 **75%+**、L3 占比 30%+、天弦任务 **3000+**、测试通过率 90%+、**150 人 1 天 3 个真实复杂需求黑客松，完赛出码率 90%+**（头部队伍达 L3）。
- **双估时机制**（价值衡量的"终极答案"）：保留**原始估时**（不用 AI 的 pd，价值衡量基线）+ 新增 **AI 结合估时**（实际排期用）→ AI 估时压缩率 =（原始−AI结合）/原始；AI 估时兑现率 =（原始−实际）/（原始−AI结合）。核心原则：**原始估时衡量价值，AI 结合估时指导排期——AI 的价值要进入计划阶段，不是只做事后复盘**。
- 业务价值三类：风险规避 / 方案优化 / 收益加速——让 AI Coding 与业务结果挂钩，从"研发部门的事"变"全公司的事"。
- 下一阶段：深化 L3 场景、沉淀 Skills 资产、强化质量门禁、从效率到 AI Native 能力升级。

## 与已有内容的关联

- [[Harness Engineering]]：第 4 个独立来源，且给出最操作化的定义（四把锁 + 12 环节控制点）。侧重差异：OpenAI 视角是"给模型装手脚的运行环境"，去哪儿视角是"过程控制能力"——同词互补。
- [[Superpowers]]：Qsuperpowers 是社区版 Superpowers 的**企业定制实证**——7 步流程 skill 名被原样复用，同时印证其定位边界（通用流程标准化，企业需自建集成层）。
- [[Skills 设计方法论]]：去哪儿 Skills Gateway 治理（私有仓→PR→审核→统一观测 + 种子上下文）是 Anthropic/Perplexity 方法论的企业级组织化落地——种子上下文 ≈ 上下文工程；失效点分析 ≈ Gotchas 飞轮。
- [[子代理驱动开发]]：Qsuperpowers ai-coding 阶段直接复用 subagent-driven-development，大厂生产环境再印证。
- [[Harness Engineering 团队落地规范]]（腾讯）：同为"Agent = Model + Harness"的大厂落地，腾讯走"规范+审计"路线，去哪儿走"平台+数据"路线，可对照。
- 度量概念（L0-L5 分级、双估时、T/M/C、出码率 Git Blame 法）目前为单源，已列入 INDEX pending 观察区。

## ⚠️ 矛盾 / 待澄清

- 所有成果数字（75%+ 出码率、3000+ 任务、66% 提效）均为**去哪儿自述**，无第三方复核；且为大会分享口径，非审计数据。
- 文章是 [[爱海贼的无处不在]] 对分享 PPT 的二手解读（作者自注"解读过程中可能存在主观判断"），非李佳奇原文；表结构/截图细节为作者转述。
- 出码率 75%+ 的口径（是否含 AI 生成后被人重写的部分、Git Blame 归属规则）未公开细节，与业界其他公司出码率数字不可直接横比。

## 相关页面

- [[去哪儿网]] · [[爱海贼的无处不在]]
- [[Harness Engineering]] · [[Superpowers]] · [[子代理驱动开发]] · [[Skills 设计方法论]] · [[Harness Engineering 团队落地规范]]
