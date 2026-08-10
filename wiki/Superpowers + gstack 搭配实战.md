---
type: source
title: "Superpowers + gstack 搭配实战"
domain: [AI编程]
tags: [superpowers, gstack, Claude Code 插件, 插件搭配, 技能路由, 开发闭环]
sources: []
source_path: "raw/articles/Superpowers + gstack 搭配实战：2 个插件，37 个技能，5 个关键交接点，闭环标准开发流程.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247490401&idx=1&sn=f3648316799b8784771ffc0f6d17c11b"
author: "[[运维有术]]"
date_published: "2026-04-12"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [Superpowers + gstack 搭配, 37 个技能搭配, superpowers gstack 交接点]
---

# Superpowers + gstack 搭配实战

> 一句话要点：[[Superpowers]]（方法论纪律框架，自动触发）与 [[gstack]]（角色化虚拟团队，手动斜杠命令）能力边界几乎无重叠，二者在安装路径、命名空间、触发机制三层互不冲突，通过 5 个交接点串联成一条从想法到上线的闭环。

## 关键要点

- **核心论点**：插件多 ≠ 能力强，反而让行为不可预测。[[Superpowers]]（145K Star）专注"怎么写好代码"，[[gstack]]（69K Star）专注"做什么、做成什么样、怎么上线"，一个管思考一个管执行，从源码层面天然互补。
- **作者背景决定差异**：[[Jesse Vincent]]（开源老兵）关注工程规范和代码质量；[[Garry Tan]]（YC 总裁）关注产品决策和全生命周期交付。
- **源码级差异表**：Superpowers = 14 个 Skills，每技能一个纯 Markdown 的 `SKILL.md`，**自动触发**（"Mandatory workflows, not suggestions"），无哲学/架构独立文档；gstack = 23 个斜杠命令 + 8 个工具，TypeScript + Go Template 生成 `SKILL.md`，**手动触发**，有 `ETHOS.md` 和 `ARCHITECTURE.md`。
- **三层不冲突**：① 安装路径隔离 ② 命名空间隔离（可用 `./setup --prefix` 启用 `/gstack-qa` 前缀避免重叠）③ 触发机制互补（superpowers 处理编码过程决策点，gstack 处理产品流程决策点）。
- **Superpowers 的纪律本质**：源自一个 94% PR 拒绝率的开源项目。brainstorming 强制"先想后做"、TDD 强制红绿循环、systematic-debugging 强制"找不到根因就不能修"、subagent-driven-development 强制"实现者和审查者绝不在同一上下文里"。
- **5 个关键交接点**：① `brainstorming → /autoplan`；② `writing-plans → /plan-eng-review`；③ `test-driven-development → /qa`；④ `systematic-debugging → /investigate`；⑤ `finishing-a-development-branch → /ship`。
- **标准开发闭环**（13 步接力）：Superpowers 处理编码质量环节，gstack 处理外部世界环节，交替工作。
- **两种推荐方案**：方案 A（Superpowers 为主、gstack 为辅）适合追求工程规范的团队；方案 B（gstack 为主、Superpowers 为辅）适合个人开发者/快速原型。
- 需注意的冲突点：CLAUDE.md 配置分区管理、命令前缀、Token 消耗、上下文窗口（用 `/freeze` 限制编辑范围）。

## 详细笔记

文章给出一份可直接抄的 CLAUDE.md 配置模板，核心作用是把模糊指令映射到确定技能。安装两步：`/plugin install superpowers@claude-plugins-official` + `git clone ... ~/.claude/skills/gstack && ./setup`。

## 与已有内容的关联

- 相关概念：[[Superpowers]]、[[mattpocock skills]]（对照）、[[LLM Wiki]]
- 相关实体：[[运维有术]]、[[Jesse Vincent]]、[[Garry Tan]]、[[gstack]]
- 相关源：[[gstack 最佳实战]]、[[Claude Code 双插件搭配 superpowers 与 gstack]]

## ⚠️ 矛盾 / 待澄清

- **marketplace 名称不一致**：本文称用 `/plugin install superpowers@claude-plugins-official`；[[Claude Code 双插件搭配 superpowers 与 gstack]] 用 `superpowers@superpowers-marketplace`。待澄清哪个为正确/current。
- **技能数 14 vs 11**：本文称 Superpowers 14 个 Skills；[[Claude Code 双插件搭配 superpowers 与 gstack]] 列出约 11 个。差额待在 [[Superpowers]] 实体页统一口径。

## 相关页面

- [[Superpowers]] · [[gstack]] · [[Jesse Vincent]] · [[Garry Tan]] · [[运维有术]] · [[gstack 最佳实战]] · [[Claude Code 双插件搭配 superpowers 与 gstack]] · [[mattpocock skills]] · [[LLM Wiki]]
