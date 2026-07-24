---
type: entity
title: "Dataview"
domain: [知识管理, AI工具]
tags: [Obsidian, 插件, frontmatter, 动态聚合]
sources: ["[[LLM Wiki 方法论 gist]]", "[[Karpathy 的 LLM Wiki 搭建实战]]"]
created: 2026-07-24
updated: 2026-07-24
status: active
aliases: ["Dataview 插件", "Obsidian Dataview"]
---

# Dataview

> 一句话定义：Obsidian 社区插件，基于页面 YAML frontmatter 跑查询，自动生成动态表格与列表——让 `index.md` 的聚合"跟着实际文件走"，不会和手写表格对不上。

## 概述

在 [[LLM Wiki]] 模式里，Dataview 负责"展示层"：LLM 给每个 wiki 页面加 YAML frontmatter（`type`/`tags`/`created` 等），Dataview 基于这些字段生成动态表格。这样 `index.md` 永远跟着实际文件走，避免手写表格与文件不同步。在中等规模（~100 源、几百页）下，index + Dataview 足以替代向量 RAG 基础设施。

## 关键属性 / 事实

- **输入**：wiki 页面的 frontmatter 字段（`type`、`tags`、`created`、`sources` 等）。
- **输出**：基于字段过滤、分组、排序的动态表格与列表。
- **安装**：在 Obsidian 设置里关闭安全模式后从社区插件市场装。
- **分工**：LLM 维护 frontmatter，Dataview 负责展示——两者配合实现"自动汇总"。

## 与 colin-wiki 的关系（本地化取舍）

- 文章推荐用 Dataview 插件做聚合。
- **colin-wiki 实际用的是 Obsidian 原生 Bases**（`bases/*.base` 文件，见 [[bases/全部页面.base]]、[[bases/待办与缺口.base]]），功能与 Dataview 等价但为 Obsidian 1.9+ 原生内置，无需装社区插件。
- → 这是一个"方法论落地时用更新原生能力替代所推荐插件"的实例。

## 相关页面

- [[LLM Wiki 方法论 gist]] · [[Karpathy 的 LLM Wiki 搭建实战]] · [[LLM Wiki]] · [[Obsidian]]
