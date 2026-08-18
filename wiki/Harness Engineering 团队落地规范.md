---
type: source
title: "Harness Engineering 团队落地规范"
domain: [AI编程, 软件工程]
tags: [Harness Engineering, 团队规范, CodeBuddy, Rules, MCP, Skills, SDD, harness-audit]
sources: []
source_path: "raw/articles/驾驭AI Coding：一份面向团队的Harness Engineering落地规范.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/g4nTfxm7ebzRwkAVIGdIbg"
author: "[[腾讯程序员]]"
date_published: "2026-07-17"
created: 2026-08-17
updated: 2026-08-17
status: active
aliases: [腾讯 Harness 规范, 驾驭AI Coding, harness-audit]
---

# Harness Engineering 团队落地规范

> 一句话要点：[[腾讯程序员]]（atreusliu）把 OpenAI 的 Harness Engineering 理念落成团队规范——**"把'好代码'的标准写进系统里，让 AI 在约束下自己干活"**：6 大支柱 + 3 阶段路线图 + SOP/红线/反模式 + harness-audit 自动化审计。

## 关键要点

- **出处**：OpenAI 2026-02 文章《Harness Engineering: Leveraging Codex in an Agent-First World》——3 人（后 7 人）团队**完全禁止手写代码**，5 个月 100 万+ 行代码、1500 个 PR、效率约 10 倍。公式：**Agent = Model + Harness**（Harness 本意"马具"）。
- **Vibe Coding 三致命问题**：架构混乱（无分层，换底层全改）/ 上下文雪崩（超 50 文件后 Agent 忘事，user_id 变 uid）/ 可维护性丧失（黑盒开发，人接手不如重写）。
- **6 大支柱及工具映射**（详见 [[Harness Engineering]] 概念页）：上下文管理（渐进式披露：AGENTS.md ~100 行目录索引——OpenAI 自己踩过"巨型 AGENTS.md"失败的坑）、工具系统（MCP 是开门的钥匙 / Skills 是开门后做的事 / 知识库是进门前读的说明书）、执行编排（"3+1 Phase"：计划→编码→交付→沉淀；Planner/Generator/Evaluator/Archiver 四角色；SDD 工作流）、状态与记忆（短期会话/中期 Memories/长期 Git Spec/变更 Spec Deltas）、评估与观测（L1 语法→L2 逻辑→L3 规范→L4 架构四层）、约束与恢复（Rules 硬性红线/Skills 软性约束/Safety 兜底三级 + Git 回滚）。
- **3 阶段实施路线图**：基础建设（1-2 周：CodeBuddy 安装 + team-harness 仓库 + 基础 Rules + 知识库）→ 工具接入（2-4 周：MCP + Skills 沉淀 + Plan 模式 SDD）→ 持续优化（度量看板 + 规范迭代 + 知识飞轮）。
- **team-harness 仓库**：团队规范唯一真实来源（rules/ skills/ templates/ docs/），sync 脚本 + CI 自动同步到业务项目；Rules 三级（User 个人 / Team 平台下发 / Project `.codebuddy/rules/`，type: always 或 manual）。
- **模型选择安全策略**：复杂编码用外部模型（Claude/GPT，会外传代码上下文）；敏感业务用内部部署模型（DeepSeek/GLM/HY，代码不出域）；不确定用 Auto。
- **MCP 三协议**：stdio（本地）/ streamable-http（推荐远程）/ sse（逐步废弃）；"何时不该用 MCP"：简单脚本直调 API、纯逻辑推理、复杂度大于解决的问题。
- **子智能体（SubAgent）**：给智能体加准确职责描述（描述决定匹配）+ 勾选子智能体，默认 Agent 对话时动态调用；推荐配置需求分析/架构/数据库/CR/运维排障五专家。
- **Plan 模式 4 Stage**：Plan 模式生成 requirements.md → 人工逐项审核 → Agent 模式生成 task.md 并逐步执行（每任务自动编译验证）→ 人工审查 + 归档到 archive/。
- **SOP 三套**：新需求（半天以上必须走 Plan；简单需求快捷流程但仍守 Rules）/ Bug 修复（一个 PR 一个 Bug、必须写复现测试、commit 格式 `fix: [模块] (#issue)`）/ AI 辅助 CR（提交前自查 + Review 他人 PR，检查清单覆盖架构/质量/安全/性能）。
- **团队红线 5 条**：先 Spec 后 Code；Rules 必须进 Git 不许本地私有；通用逻辑必须沉淀 Skill；关键元数据 MCP 实时同步不维护副本；变更必须可追溯。
- **8 反模式**：巨型 Prompt / 跳过审核直接编码 / Rules 写了不维护 / MCP 过度接入（Token 暴增）/ Skill 不原子化 / 盲信 AI 输出 / Chat 历史当文档 / 一个 PR 改所有。
- **harness-audit Skill（亮点）**：把整套规范固化成可执行合规审计——7 维度打分（AGENTS.md 15% / Rules 20% / Skills 15% / MCP 10% / Plan 模式 15% / 工程规范 15% / Commit 10%），S/A/B/C/D 五级，输出报告到 `.codebuddy/reports/`。**"Skill 就是规范的可执行版本"**；示例项目 75 分 A 级（MCP 0 分是 P0 短板）。使用节奏：初次接入/季度复盘/新项目立项/CR 前按需/月度跨项目对比——**"审计报告是体检结果，不是 KPI"**。
- 金句：Django 创始人"交付代码的成本已经接近免费，但交付好代码的成本依然很高"；"流水的工具，铁打的规范"；"让各类工具适配规范，而不是靠个人去适配各类工具"。

## 与已有内容的关联

- 相关概念：[[Harness Engineering]]（本文是其团队落地样本）、[[Skills 设计方法论]]（支柱一/二的理论化）、[[mattpocock skills]]、[[Superpowers]]（都是 harness 实例）
- 相关实体：[[腾讯程序员]]（作者）、[[Anthropic]]（Agent Skills 开放标准生态同向）
- 相关源：[[Anthropic 与 Perplexity 的 Skills 设计方法论]]（渐进式披露同源）、[[AgentScope Java 2.0 正式版]]（Harness 框架化的另一实现路线）

## ⚠️ 矛盾 / 待澄清

- "100 万行/10 倍效率"出自 OpenAI 文章自述，无第三方复核。
- 工具链（CodeBuddy/Knot/工蜂/TAPD/iWiki）为腾讯内网生态，外部可借鉴的是方法论骨架（6 支柱/SOP/审计思路），配置不可照抄。
- 与 [[mattpocock skills]] 无冲突，同一理念的两条落地路线：Matt 用 SKILL.md + 触发机制做个人级 harness，本文用 Rules/MCP/Skills + 平台做团队级 harness（推论）。

## 相关页面

- [[Harness Engineering]] · [[腾讯程序员]] · [[Skills 设计方法论]] · [[mattpocock skills]] · [[Superpowers]] · [[AgentScope Java]] · [[Anthropic 与 Perplexity 的 Skills 设计方法论]]
