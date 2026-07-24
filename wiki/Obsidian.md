---
type: entity
title: "Obsidian"
domain: [知识管理, AI工具]
tags: [Obsidian, 知识库, markdown, 双链, LLM Wiki]
sources: ["[[LLM Wiki 方法论 gist]]", "[[Karpathy 的 LLM Wiki 搭建实战]]"]
created: 2026-07-24
updated: 2026-07-24
status: active
aliases: ["Obsidian.md"]
---

# Obsidian

> 一句话定义：基于本地 markdown 文件的知识管理软件，在 [[LLM Wiki]] 模式里扮演"IDE"——LLM 在一侧编辑文件，Obsidian 实时刷新页面与图谱。

## 概述

Obsidian 是 [[LLM Wiki]] 模式的落地载体。Karpathy 的概括是「Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase」。vault 就是一个文件夹，wiki 就是其中的 markdown 文件——天然是 git 仓库。免费下载（官网 obsidian.md）。

## 关键属性 / 事实

- **本地优先**：vault = 本地文件夹 + `.obsidian/` 配置；markdown + YAML frontmatter + 双向链接 `[[]]`。
- **LLM 协作画面**：LLM agent 开一边改文件，Obsidian 开另一边秒级刷新，点链接、看图谱、读更新。
- **核心被用到的能力**：
  - **Web Clipper**（官方浏览器扩展）：网页文章一键转 markdown 存进 `raw/`，快速喂数料。
  - **图片本地化**：Settings → Files and links 把 Attachment folder path 设成固定目录（如 `raw/assets/`）；Hotkeys 搜 "Download"，绑「Download attachments for current file」快捷键，剪藏后一键下载所有图片。价值：让 LLM 看本地图片而非会失效的 URL。
  - **Graph view**：看 wiki 形态最直观的方式——什么连什么、哪些是 hub、哪些是孤儿。
  - **插件生态**：[[Dataview]]（frontmatter 查询→动态表格）、Marp（md→slide deck）。
- **colin-wiki 实例化**：附件目录指向 `raw/assets`；用 `.base` 文件（Obsidian 原生 Bases）做聚合。

## 演变 / 时间线

- 在 [[LLM Wiki 方法论 gist]] 中被定位为协作画面的"IDE"侧。
- 2026-07-03：[[Karpathy 的 LLM Wiki 搭建实战]] 给出 Obsidian 具体配置（Attachment folder、快捷键、插件安装步骤）。

## 相关页面

- [[LLM Wiki 方法论 gist]] · [[Karpathy 的 LLM Wiki 搭建实战]] · [[LLM Wiki]] · [[Dataview]]
