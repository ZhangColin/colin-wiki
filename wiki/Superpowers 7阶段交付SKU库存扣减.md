---
type: source
title: "Superpowers 7阶段交付SKU库存扣减"
domain: [AI编程]
tags: [Superpowers, Claude Code, 电商, 库存扣减, FastAPI, 悲观锁, TDD, 子代理开发]
sources: []
source_path: "raw/articles/Superpowers 最佳实战：7 阶段工作流交付电商系统 SKU 商品库存扣减逻辑.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247490419&idx=1&sn=2c553ab75ccbcbd72fc22aefa670c4e6"
author: "[[运维有术]]"
date_published: "2026-04-14"
created: 2026-08-10
updated: 2026-08-10
status: active
aliases: [Superpowers SKU库存扣减实战, Superpowers 电商实战]
---

# Superpowers 7阶段交付SKU库存扣减

> 一句话要点：用 Python（FastAPI + SQLAlchemy）电商"带 SKU 商品库存扣减"场景，逐阶段演示 [[Superpowers]] 7 阶段工作流的命令、代码与 AI 交互全过程。

## 关键要点

- 核心理念 Process over Prompt：与其调教提示词，不如让 AI 走固定的工程流程。
- 三难点：库存校验（扣减前检查充足）、原子性扣减（高并发不超卖，需行级锁/乐观锁）、边界条件（库存为零/SKU 不存在/重复扣减）。
- brainstorming 苏格拉底式一次一问，实战中依次澄清：是否并发（推荐悲观锁 SELECT...FOR UPDATE）、失败返回信息粒度、是否部分扣减（全部失败）。
- [[Jesse Vincent]] 原则：设计文档要详细到"一个刚入职、经验不多、不喜欢写测试的初级工程师"都能照做。
- writing-plans 任务粒度示例：Task1 SKU 模型定义拆成"写测试→运行确认失败→实现→运行确认通过→提交"五步，**写测试与确认失败是分离的两步**。
- subagent-driven-development 两轮审查职责分明：第一轮规格符合性（查"缺失"与"多余"），第二轮代码质量；必须按序。实战中规格审发现"多余：InsufficientStockError 类，规格只要求 ValueError"→子代理改为 ValueError 子类。
- TDD 铁律："没有看到测试失败，就不能写生产代码"。GREEN 阶段只写让测试通过的最小代码。
- 全局代码审查产出分级（优点/Important/Minor），Important 如"commit 失败未处理→加 try/except+rollback"、"批量扣减锁等待→按 sku_id 排序避免死锁"。
- 三种技能组合模式：Bug 修复流（systematic-debugging→TDD→verification）；快速实现流（brainstorming→writing-plans→executing-plans）；大型功能流（完整 7 阶段）。
- 渐进式上手 5 周路线图：第1周 brainstorming、第2周 +writing-plans、第3周 +TDD、第4周 +subagent、第5周+ 完整 7 阶段。

## 详细笔记

实战主线覆盖库存校验/原子性扣减/边界条件三难点。

## 与已有内容的关联

- 相关概念：[[Superpowers]]、[[子代理驱动开发]]、[[mattpocock skills]]
- 相关实体：[[运维有术]]、[[Jesse Vincent]]
- 相关源：[[Superpowers 5万 Star 工程纪律框架]]、[[Superpowers 实战指南 7步流程14技能]]、[[Superpowers 7步闭环工作流深度指南]]、[[mattpocock skills 秒杀系统实战]]（同为电商高并发扣减场景，可对照）

## ⚠️ 矛盾 / 待澄清

- 本文与 [[mattpocock skills 秒杀系统实战]] 是同作者、同"电商高并发扣减"场景但用不同 skills 框架的实战——可做有价值的对照综合（推论：值得新建 synthesis 页对比两套 skills 在库存/秒杀场景的流程差异）。
- 悲观锁 vs 乐观锁选择：本文 brainstorming 选悲观锁，而 [[Superpowers 7步闭环工作流深度指南]] 的优惠券核销选乐观锁——不同场景选择不同，无矛盾，但可综合。

## 相关页面

- [[Superpowers]] · [[子代理驱动开发]] · [[Superpowers 5万 Star 工程纪律框架]] · [[Superpowers 实战指南 7步流程14技能]] · [[Superpowers 7步闭环工作流深度指南]] · [[Superpowers 6.0 reviewer 只读重写]] · [[mattpocock skills 秒杀系统实战]] · [[运维有术]] · [[Jesse Vincent]] · [[mattpocock skills]]
