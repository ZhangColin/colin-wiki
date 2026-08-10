---
type: entity
title: Anthropic
domain: [AI编程, AI工具]
tags: [Anthropic, Claude, Claude Code, Agent Skills, 开放标准]
sources:
  - "[[Anthropic 与 Perplexity 的 Skills 设计方法论]]"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases:
  - Anthropic
---

# Anthropic

> 一句话定义：Claude 与 Claude Code 的开发公司；Agent Skills 开放标准的发起方，2025-12-18 将标准捐给 Agentic AI Foundation（AAIF）。

## 概述

在本 wiki 中，Anthropic 主要作为 [[Skills 设计方法论]] 的两方权威来源之一出现（另一为 [[Perplexity]]）。其 Claude Code 团队工程师 Thariq Shihipar 在 2026-03 官方博客中提出 Skills 的 9 类分类体系与 8 条最佳实践，纠正"Skills 不过是个 markdown 文件"的误解——它是含脚本、资源、数据的文件夹。本 wiki 自身使用的 Claude Code 即 Anthropic 产品。

## 关键属性 / 事实

- **产品**：Claude（模型）、Claude Code（CLI agent，本 vault 实际使用的 agent）。
- **Agent Skills 9 类分类**（Thariq Shihipar）：库与 API 参考、产品验证、数据获取与分析、业务流程与团队自动化、代码脚手架与模板、代码质量与审查、CI/CD 与部署、运维手册、基础设施运维。
- **亮点 skill 案例**：`babysit-pr`（监控 PR→重试 CI→解冲突→自动合并）、`/careful`（PreToolUse 钩子拦危险命令）、`adversarial-review`（全新子智能体挑刺避免自审）。
- **核心观点**："不要说显而易见的事"（与 [[Perplexity]] 的 Python 之禅失效论同义）。
- **生态**：内部活跃 Claude Code Skills 已几百个；管理分层（小团队直接提交 `./.claude/skills`，人多搭内部 Plugin Marketplace）。

## 演变 / 时间线

- 2025-10：Claude Skills 上线。
- 2025-12-18：将 Agent Skills 发布为开放标准并捐给 AAIF。
- 2026-03：Thariq Shihipar 发 Skills 设计深度博客（9 类分类 + 8 条最佳实践）。

## 相关页面

- [[Perplexity]] · [[Skills 设计方法论]] · [[Anthropic 与 Perplexity 的 Skills 设计方法论]] · [[Obsidian]] · [[LLM Wiki]]
