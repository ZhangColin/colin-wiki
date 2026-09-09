---
type: entity
title: DeepSeek Harness
domain:
  - AI编程
  - 软件工程
tags:
  - Harness
  - Agent运行时
  - DeepSeek
  - 开源框架
  - 插件化
sources:
  - "[[DeepSeek Harness 源码拆解]]"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases:
  - DSH
  - DeepSeek-Harness
---

# DeepSeek Harness

> 一句话定义：DeepSeek 开源的 Agent 运行时底座（MIT，约 80k stars / Developer Preview，2026-08），以 [[Cordis]] 插件运行时为核心——「Everything is a plugin」，把模型适配、工具、会话、Loop、沙箱、子 Agent 全部插件化；定位是「面向开发者的 Agent 底座」，默认产品形态（Web/headless）本身也是插件配置拼出来的。

## 概述

[[DeepSeek Harness 源码拆解]]（[[翟星人的实验室]]）给出公式定位：`Agent = Model + Harness`——DSH 是 Harness 层的开源实现，负责 Prompt 组装、工具执行、状态保存、权限控制、失败恢复。与 [[AgentScope Java]]（轻量 Java 运行时）形成「Java 轻量 vs DSH 插件化重量」对照；与 LangChain 的范式对比是「LangChain 向上封装（降开发门槛）vs DSH 向下拆开（框架层透明）」。

## 关键属性 / 事实

- **底层**：[[Cordis]] 插件运行时，四概念：Service（能力提供，依赖接口非具体类）/ Event（Waterfall、单调 Guard）/ Effect（卸载清理）/ Scope（agent→preset→global 逐层解析）。**组合隔离 ≠ 安全隔离**。
- **四层装配**：Profile（启动装配方案）→ Bundle（可复用配置包）→ Patch（覆盖层）→ Preset（决定 Agent 工具+Prompt）；Preset 出生前挂好、运行中锁死，切换写进日志。MCP Client / Skill Provider / Hook Bridge 全是 Cordis 插件。
- **运行时内核**：Agent 与 Agent Loop 两个包（ReactLoopAgent 只是默认实现）；Turn/Step 两级循环；`followup()`/`steer()`/`inject()` 三种输入路由；AbortSignal 停止 + dispose 等 quiescence。
- **Session Event Log**：事实流 + 多投影（Web Transcript/恢复/Fork/遥测同一投影），「模型可见的东西必须能被日志重建」；JSONL/SQLite 双 Provider、flush 耐久屏障。详见 [[Session Event Log]]。
- **工具长链**：pre-execute → Approval → 单调 Guard → execute Waterfall → post-execute → 规范化 → result；Definition/Provider/Consumer 三角色；presentCall/presentResult 执行与展示解耦。
- **子 Agent**：独立 Session/Inbox；Activation/Spawn/Fork/composeFrom()（绑定父 Preset 版本）；Settlement 结算进父 Inbox；不能自行提权。已知缺口：耐久邮箱/任务租约缺失。
- **自修改**：`cordis_define → cordis_run → cordis_stop → cordis_undefine` 动态 Package 生命周期，只在进程内存、重启全丢。
- **已知缺口（作者横评结论）**：产品解释力、安全边界（文件沙箱非统一策略）、耐久状态（跨进程租约）、Benchmark（无公开结果/轨迹/权限/Token 预算口径）、默认产品碎活。

## 演变 / 时间线

- 2026-08-14：[[翟星人的实验室]] 发布源码拆解文（文中口径：MIT、约 80k stars、Developer Preview、官方明示兼容性破坏）。
- 缓存优势归因：一半靠模型、一半靠 Harness 前缀稳定复用 KV Cache（作者纠正「全归功于 DSH」的网上口径）。

## 相关页面

- [[DeepSeek Harness 源码拆解]] · [[Cordis]] · [[Harness Engineering]] · [[Session Event Log]] · [[AgentScope Java]] · [[HITL 与 AFK]] · [[从 Chat UI 到 Agent UI]]
