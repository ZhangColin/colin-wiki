---
type: entity
title: Cordis
domain:
  - AI编程
  - 软件工程
tags:
  - 插件运行时
  - 开源框架
  - DeepSeek-Harness
  - 依赖注入
sources:
  - "[[DeepSeek Harness 源码拆解]]"
created: 2026-09-09
updated: 2026-09-09
status: active
aliases:
  - Cordis 插件运行时
---

# Cordis

> 一句话定义：独立开源的插件运行时（framework），[[DeepSeek Harness]] 基于它搭建——用 Service / Event / Effect / Scope 四个概念支撑「Everything is a plugin」的架构，是 DSH 一切插件化能力的底座。

## 概述

Cordis 使 DSH 能把模型适配、工具、会话、Loop、沙箱、子 Agent、MCP Client、Skill Provider、Hook Bridge 全部写成插件，共享一套生命周期。[[翟星人的实验室]] 预告下篇专题拆解 Cordis 本体（本 wiki 目前仅有经 DSH 视角的记录）。

## 关键属性 / 事实

- **Service**：能力提供者；消费者依赖接口（如 `ctx.shell`）而非具体类，Provider 可替换。
- **Event**：带顺序语义，三类——普通通知 / Waterfall（需调 `next()` 才放行）/ 单调 Guard（一旦拒绝不可翻案）。
- **Effect**：卸载时收尾清理，防重复工具与幽灵监听器。
- **Scope**：可见性按 `agent → preset → global` 逐层解析。
- **安全边界**：组合隔离 ≠ 安全隔离；动态 Package 的 Host Half 跑在 `node:vm`，官方明确非恶意代码安全边界（「当 Bash 权限授予」）。
- **自修改**：Agent 可经 `cordis_define/cordis_run/cordis_stop/cordis_undefine` 定义动态 Package——只在进程内存、重启全丢、不写 cordis.yml。

## 演变 / 时间线

- 2026-08：经 [[DeepSeek Harness 源码拆解]] 进入本 wiki 视野（DSH 的底座，独立开源项目）。

## 相关页面

- [[DeepSeek Harness]] · [[DeepSeek Harness 源码拆解]] · [[翟星人的实验室]] · [[Harness Engineering]]
