---
type: source
title: "幸福账本 Vue 前端起步"
domain: [AI编程, 软件工程, 前端]
tags: [Superpowers, 老谭superpowers系列, 基建篇, Vue, Vite, ElementPlus, 组合式函数]
sources: []
source_path: "raw/articles/09｜基建篇 - Vue 前端起步：把账本跑进浏览器.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484288&idx=1&sn=b5e880394f3619d533e35910373f1479"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-03"
created: 2026-09-10
updated: 2026-09-10
status: active
aliases: [幸福账本 Vue 前端, 基建篇 Vue]
---

# 幸福账本 Vue 前端起步

> 一句话要点：基建篇第二弹（非技能篇）——Vue 3 + Element Plus 把 `/api` 接口变成能点的页面：请求封装自动带账本头、`useLedger` 单例状态联动全站、`watch` 驱动刷新，完成前后端第一次握手。

## 关键要点

- **前端骨架三件套**：
  1. `api/request.js` fetch 封装——统一 baseURL、**ownerId 自动转 `X-User-Id` 头**、非 2xx 直接抛后端返回的 `message`（页面 catch 到就提示用户）；URL 统一管理，"别在组件里写魔法 URL"。
  2. `useLedger` 组合式函数（composable）做**单例账本状态**——模块级 `ref`，切换后所有页面共享。
  3. `App.vue` 侧栏五页菜单 + 顶栏账本切换下拉——"免登录切换账本的真身"。
- **第一屏账目列表**：选月份 → 拉数据 → el-table 渲染；灵魂是 `watch([month, ownerId], load)` ——"账本一换、月份一改，列表自动跟着变"；忘了它 = "前后端最尴尬的假握手"（切账本后页面停留旧数据）。
- **第二屏记账表单**：类型/分类/金额/备注 → POST；后端"金额必须大于 0"等错误经 `ElMessage.error(error.message)` 原样弹给用户——"这就是上一篇统一错误 JSON 的回报"。
- **握手验收**：浏览器 5173 → Vite `/api` 代理 → 后端 8080 → H2 落库 → 回显，一条链路走通；seed 的家庭账可见、记账即回显、切乐乐账本只剩她的两笔。
- 错误处理纪律："吞掉异常 = 艳姐点了没反应。"

## 详细笔记

基建篇定位同 08 篇：为"前后端完整项目"补前端一环，无 skill 机制内容。技术栈回到 00 篇约定：Vue 3 + Vite + Element Plus + vue-router，测试 Vitest（05 篇已演示 `useLedger` 的 TDD）。

## 与已有内容的关联

- `useLedger` 即 05 篇 TDD 前端实录的同一模块（[[Superpowers TDD 红绿循环]]）——教学系列的代码前后自洽。
- 前端轮询"对账结果"接口已在 06 篇出现（[[Superpowers condition-based-waiting 条件等待]]）。
- 艳姐"我要的是点开就能用的页面"是 [[从 Chat UI 到 Agent UI]] 之外另一方向的"给人看的界面"——本篇为纯前端工程教学，与 Agent UI 无直接关联。

## ⚠️ 矛盾 / 待澄清

- 无矛盾。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[幸福账本 REST API 基建]] · [[Superpowers TDD 红绿循环]] · [[Superpowers using-git-worktrees 工作树隔离]]
