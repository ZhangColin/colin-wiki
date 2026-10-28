---
type: entity
title: Jev
domain: [AI模型]
tags: [判断模型, system one, 结构化输出, TypeSafe, Noul, choice, score]
sources:
  - "[[Jev 判断模型入门]]"
created: 2026-10-28
updated: 2026-10-28
status: active
aliases: [Jev 判断模型, Jevons, 聪明的 if 语句]
---

# Jev

> 一句话定义：TypeSafe 公司的 system one **判断模型**——不聊天、只做判断，输入 state + 问题组、返回结构化概率数据；「聪明的 if 语句」，名出 Jevons 悖论（判断便宜到可忽略 → 塞进以前算不过账的场景）。

## 关键属性 / 事实

- **发行方**：TypeSafe（本 wiki 首现的 AI 公司；与 [[Matt Pocock]] 的 Total TypeScript 无关，勿混淆）。发布时 Early Access（等待名单 / Vercel AI Gateway 限时免费）；演示版本 1.13.0。
- **性能/定价**：端到端 70–500ms（演示 114ms vs GPT-5.6 Terra 8.6s，约 1/75 时延）；输入 $42/十亿 token、**输出免费**（约同任务成本的 1/170）；快与便宜的根源：无需逐 token 生成，概率并行计算。
- **三类问题**：Noul（是非概率 0–1）/ choice（必选其一 + 全选项概率，选项由调用方定义、「其他」不能省）/ score（各级含义刻度 + 概率加权分，可落两级之间）；多问题共用 state 单次调用并行评估（问题间独立）；choice/score 带 confidence（按概率分布算，「置信度不是准确率的保证」）。
- **SDK**：`@typesafe-ai/sdk`（TypeScript），`client.system1(state, questions)`。
- **适用边界**：判断类（路由/分类/支撑结论/模型选型/即时决策）；不适用生成类；代码能精确算的用代码。

## 观察点

- 作为 [[Agent 测评 Eval]] 中 LLM-as-Judge 评分器的低成本基础设施的落地案例（需校准，confidence≠准确率）。
- 「判断模型」概念第二源触发时建概念页（见 [[INDEX]] Pending）；TypeSafe 实体页待第二源。

## 相关页面

- [[Jev 判断模型入门]] · [[老丁888]] · [[Agent 测评 Eval]]
