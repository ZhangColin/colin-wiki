---
type: source
title: "DeepSeek Harness 源码拆解"
domain: [AI编程, 软件工程]
tags: [Harness, Agent运行时, DeepSeek, Cordis, 插件化架构, Session-Event-Log, 子Agent, 权限沙箱]
sources: []
source_path: "raw/articles/DeepSeek Harness 源码拆解：看看DeepSeek怎么理解Agent，怎么设计Harness.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/UmwwoCpQS5gLnORreMVnzQ"
author: "[[翟星人的实验室]]"
date_published: "2026-08-14"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases: [DSH 源码拆解, DeepSeek Harness 拆解]
---

# DeepSeek Harness 源码拆解

> 一句话要点：从源码拆解 DeepSeek Harness（DSH）：一个 MIT 开源的开发者级 Agent 运行时底座，把模型适配、工具、会话、Loop、沙箱、子 Agent 全部插件化（"Everything is a plugin"），由 [[Cordis]] 插件运行时装配——它的定位是"面向开发者的 Agent 底座"，不是 2C 产品。

## 关键要点

- **定位与公式**：`Agent = Model + Harness`；DSH 定位为运行时框架，Harness 负责 Prompt 组装、工具执行、状态保存、权限控制、失败恢复。发布即 MIT 协议、约 80k stars（时点数据）、Developer Preview，官方明示会有兼容性破坏。作者判断：用产品体验评价它是"用成熟产品标准衡量裸奔框架"。
- **插件系统深度（底座是 Cordis）**：不是简单 `registerTool()`，而是一套完整插件系统。四个核心概念：**Service**（能力提供，消费者依赖 `ctx.shell` 而非具体类，Provider 可换）、**Event**（带顺序语义，分普通通知 / Waterfall 需调 `next()` 才放行 / 单调 Guard 一旦拒绝不可翻案）、**Effect**（卸载时收尾清理，防重复工具与幽灵监听器）、**Scope**（可见性，按 `agent → preset → global` 逐层解析）。强调：**组合隔离 ≠ 安全隔离**。
- **四层装配**：Profile（一次启动的总装配方案）+ Bundle（可复用插件配置包）+ Patch（覆盖层）+ Preset（决定某 Agent 带什么工具和 Prompt）。Web 四种模式 standard/minimal/code/cordis 不是四个二进制，只是四份 `agent.cordis.yml`。**默认 Coding Agent 本身就是一行行插件配置拼出来的，无"内部特供版"**。MCP Client、Skill Provider、Hook Bridge 在 DSH 里全部是 Cordis 插件（Hook Bridge 把 Claude Code/Codex 的 `hooks.json` 翻译成 Cordis 拦截点）——三套机制一套生命周期。Preset 在 Agent 出生前挂好、运行中锁死，切换写进 Session Log。
- **运行时内核**：Agent 与 Agent Loop 是两个包（ReactLoopAgent 只是默认实现，可换）；创建走 `ctx.agents.create()`——先建局部 Scope 安装全部配置，**成功才进 Registry，失败整体回滚**。Turn = 一次用户任务；Step = 一次"组装上下文+请求模型+执行工具"。三种输入路由：`followup()` 进 next-turn（唤醒，追加完整任务）、`steer()` 进 next-step（唤醒，任务不停换方向）、`inject()` 进 next-step（不唤醒，后台补上下文）。每 Step 取走所有等待的 Step 消息、至多一条 Turn 消息。停止用 AbortSignal 沿模型流/工具/子进程下传，`dispose()` 要等 **quiescence**（活动真正静止）。
- **上下文与缓存**：System Prompt 是插件向 Registry 注册的带名带序 Section（非写死长文本），工具 Schema 与 Prompt Section 同一次 Assembly 收集（"告诉模型能做什么"与"真给了什么"必须对齐）。**前缀稳定以复用 KV Cache**：运行时事实（Sandbox/Approval/工作区规则）以 user-role snapshot 追加进历史而非塞回 System Prompt——作者指出这就是网上说的"缓存高"的工程点。AGENTS.md/CLAUDE.md 规则有字节预算：先丢宽泛文件、最后才截断最具体的，省略会告知模型。Skill"目录常驻、正文按需"。
- **Session Event Log（反对把 messages 数组当数据库）**：先保存事实流（turn/start、assistant/chunk、tool/call 等），`deriveMessages()` 及 Web Transcript、恢复、Fork、遥测都从同一流投影。**铁律：模型可见的东西必须能被日志重建**。工具调用先记 `tool/call` 再执行——进程崩在中间，恢复为"这件事开始过、结果未知、需检查现场"。Compaction 是 Surface Replacement（旧表面被摘要替换但原始事实仍在日志）。持久化用 JSONL/SQLite Provider 共用写入协调器，事件进批次、`flush` 是耐久屏障。日志中间损坏直接拒绝，只可丢未写成的尾部碎片。
- **工具执行长链**：tool/call 落日志 → `tools/pre-execute`（Hooks/权限/Sandbox）→ Approval → 单调 Guard → `tools/execute` Waterfall（超时/重试/指标）→ post-execute（可阻断/替换）→ 规范化 + `finalizeContent` → `tools/result`。并行工具调用按模型给出 ordinal 固定结果顺序；取消等整批收口，已发生的照样记账。`presentCall`/`presentResult` 把"执行了什么"与"用户怎样看见"解耦。能力分 **Definition/Provider/Consumer** 三角色，Filesystem 与 Subprocess Provider 真正定义执行世界，Shell/PTY/LSP 是其上层消费者。SandboxMode 词表仅 read-only/workspace-write/danger-full-access，**只约束文件副作用，网络与进程策略不在内**（"不是宇宙级玻璃罩"）。权限分软（System Prompt）硬（Guard/文件策略/进程隔离）两层。PTC Code Mode 让模型写 TypeScript 风格程序经 `run_code` 组合调用，子调用仍过权限与日志。
- **子 Agent 模型**：不共享父消息数组，各有独立 Session/Inbox/状态；对象化概念：**Activation（运行实例）、Spawn（无历史）/Fork（复制某时间点历史）、composeFrom() 绑定父 Preset 版本**（防父工作途中配置文件变化导致父子工具集不一致）、one-shot/continuable、Settlement 结算通知进父 Inbox（父忙则排队、空闲可唤醒）。Jobs（后台作业）/Workflow（多步编排）/Subagent 三者分离，所有权不同。子 Agent 不能自行提权，只能上报。**已知缺口**：Activation 与所有权在进程内，缺耐久邮箱与任务租约，消息已收未落日志时崩溃仍可能丢，远程 ACP 子 Agent 偏一次性。
- **自修改（生态亮点）**：Agent 能检查运行中的 Cordis 并定义动态 Package——四步生命周期 `cordis_define`（只语法检查登记，不执行）→ `cordis_run`（挂载 Host Half，有 Browser Half 需页面明确允许）→ `cordis_stop` → `cordis_undefine`。动态定义**只在进程内存、重启全丢、不写 cordis.yml**。Host Half 跑在 `node:vm`，官方明确非恶意代码安全边界，"当 Bash 权限授予"；无页面连接时运行请求会等到 Turn 取消、无超时。
- **横评与缺口**：Codex = 产品内核集中（`codex-core` 集中拥有业务逻辑/会话/Sandbox Policy）；Claude Code = 成熟产品加扩展面（核心是 query 的 while 循环，Session/Subagent/Hooks/Skills/MCP 齐全）；Pi = 轻量直接好嵌入（运行时是从提示词编排出来的流程，无完整 Harness，Token 成本低；**作者自述是 Pi 深度玩家，其多个智能体产品以 Pi SDK 为核心运行近一年**）；DSH = 默认产品也走插件系统。范式对比：**LangChain 向上封装（降开发门槛）vs DSH 向下拆开（降体验与成本优化复杂度、框架层透明）**。五个缺口：产品解释力、安全边界（文件沙箱非统一策略）、耐久状态（跨进程租约）、**Benchmark（BENCHMARK.md 只讲怎么跑，无公开结果/轨迹/权限/Token 预算/失败口径）**、默认产品碎活。

## 与已有内容的关联

- 核心关联 [[Harness Engineering]]（"Agent = Model + Harness"公式直接呼应）及 [[Harness Engineering 三次演进与头部实践]]、[[企业 Agent 落地十二大工程框架]]。
- Benchmark 缺口与 [[腾讯 Agent 与 Skill 测评方案]] / [[Agent 测评 Eval]] 的"基线反作弊"思路互证（要固定模型版本/Preset/工具/Sandbox/轨迹）。
- 权限硬软两层与 [[TAB 大仓 Harness 工程化实战]]、[[去哪儿网 AI Coding 研发平台实践]] 可互证；与 [[AgentScope Java]] 形成"Java 轻量 vs DSH 插件化重量"对照。
- 与 [[HITL 与 AFK]] 相关（Approval、单调 Guard、子 Agent 不能自行提权）。
- Session Event Log 与 [[从 Chat UI 到 Agent UI]] 的"Snapshot+Event Log+Live Stream"同题异构，可合成 [[Session Event Log]]。

## ⚠️ 矛盾 / 待澄清

- 文中"梁圣的风格……亮大招，蚕食，碾压"含强烈个人褒贬色彩（作者明确说了"个人观点，仅供参考"）——wiki 记录剥离情绪、保留事实；"梁圣"指 DeepSeek 创始人，是否等于梁文锋属推断，不写实。
- 星数"80k"为文章发布时点数据，快速变动。
- 作者称缓存优势"一半靠模型、一半靠 Harness 前缀稳定性"，与网上"全归功于 DSH 快"的口径互斥——作者明确批评后者只看到一半。
- 文中 PTC Code Mode 描述与产品侧菜单（Web 界面截图显示"PTC 模式"）命名口径略有出入：正文称 code 模式，截图称 PTC 模式——原文自身未统一（标准/极简/代码/创造 vs 标准/PTC/极简/创造）。

## 相关页面

- [[Harness Engineering]] · [[DeepSeek Harness]] · [[Cordis]] · [[翟星人的实验室]] · [[Session Event Log]] · [[AgentScope Java]] · [[从 Chat UI 到 Agent UI]] · [[HITL 与 AFK]] · [[Agent 测评 Eval]]
