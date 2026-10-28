---
type: source
title: "幸福账本 REST API 基建"
domain: [AI编程, 软件工程, Java]
tags: [Superpowers, 老谭superpowers系列, 基建篇, REST, SpringBoot, JPA, 分层架构]
sources: []
source_path: "raw/articles/08｜基建篇 - REST API：把账本逻辑变成接口.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484285&idx=1&sn=86aac4dd4fd823e86be007e88aa1000e"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-02"
created: 2026-09-10
updated: 2026-09-10
status: active
aliases: [幸福账本 REST API, 基建篇 REST]
---

# 幸福账本 REST API 基建

> 一句话要点：基建篇第一弹（非技能篇）——持久化层 + REST 接口把账本从"Java 代码库"变成"能被浏览器访问的 API"：领域 record 与 JPA 实体分层、`X-User-Id` 头切账本、统一错误 JSON、seed 开箱数据。

## 关键要点

- **定位**：不是 superpowers 技能篇，是项目基建——"浏览器不认识 Java 对象，它只认 HTTP 接口"。这步做完前端（09 篇）才有得调。
- **持久化两件套**：H2 文件库落盘（`jdbc:h2:file:./data/happy-ledger`，`ddl-auto=update` 自动建表）；**领域 record 与 JPA 实体分层**——实体（带 getter/setter）只存在于 `persistence` 包，Controller 只跟不可变 record 打交道，"混了，不可变的好习惯就守不住"。
- **应用门面**：`LedgerAppService` 是 Controller 与仓储之间的唯一入口，负责实体 ↔ 领域 record 互转与聚合；`addEntry` 里金额先过 `MoneyRules.check` ——"入口就拦，这是后面 defense-in-depth 的伏笔"。
- **`X-User-Id` 请求头 = 账本切换唯一通道**：不传默认家庭账 1，传 2 即乐乐零花钱，免登录；后端别在 body 里再传 ownerId，"避免两处来源打架"。
- **统一错误 JSON**：`@RestControllerAdvice` 把参数校验（@Valid）/ 404 / 兜底异常全转成 `{code, message}`——"返回裸堆栈，前端没法处理"。
- **工程细节**：`SeedDataRunner`（`CommandLineRunner`）空库自动播种分类/预算/账目；`./data/` 是运行时产物要 gitignore；curl 四条验收命令（分类/列表/记账/报表）。

## 详细笔记

与技能篇的区别：基建篇教的是"完整项目"的地基（00 篇四规矩之二），没有 skill 触发机制内容。金额非法的响应示例 `{"code":"validation-error","message":"amount 金额必须大于 0"}` 展示了 MoneyRules → 统一错误的贯通。

## 与已有内容的关联

- `MoneyRules` 入口拦截被预告为 defense-in-depth（纵深防御）第一层——该技能篇属系列待收录篇目（[[Superpowers 安装与技能全景]]）。
- Spring Boot REST 上手教学与 [[AgentScope Java 2.0 上手 Web 接口]] 同类（HarnessAgent 接 Spring Boot 最小闭环），但本篇是纯工程教学、无 agent 框架内容。
- "X-User-Id 设计"与 14 篇 bug 直接相关：报表接口漏按 ownerId 过滤导致子账本混入（[[Superpowers systematic-debugging 四阶段调试]]）——基建设计面即 bug 漏洞面。

## ⚠️ 矛盾 / 待澄清

- 无矛盾。免登录 `X-User-Id` 切账本是教学取舍（家庭内网场景），生产环境不可照搬（推论，源内未讨论安全性）。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[幸福账本 Vue 前端起步]] · [[Superpowers systematic-debugging 四阶段调试]] · [[AgentScope Java 2.0 上手 Web 接口]]
