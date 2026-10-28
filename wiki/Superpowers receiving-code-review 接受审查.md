---
type: source
title: "Superpowers receiving-code-review 接受审查"
domain: [AI编程, 软件工程, 协作]
tags: [Superpowers, 老谭superpowers系列, receiving-code-review, 代码审查, 审查协作, MoneyRules]
sources: []
source_path: "raw/articles/19｜superpowersreceiving-code-review 接受代码审查.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/Xxgxauyi8un94UKfftc57Q?poc_token=HA5dq2qjy2gHeONnoZJ-aEgKak8Y_1HlJIDEz2ig"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-17"
created: 2026-10-22
updated: 2026-10-22
status: active
aliases: [receiving-code-review, 接受审查, 接受代码审查]
---

# Superpowers receiving-code-review 接受审查

> 一句话要点：`receiving-code-review` 驯服「听到批评第一反应是防御」的本能——**审查意见不是在否定你这个人，是在帮你把代码变得更好**：别急着辩护 → 先理解再回应 → 逐条回应 → 改完给证据。

## 关键要点

- **第一性原理**：听到批评，第一反应是辩解（「抛异常不是挺标准的吗」「别人都这么写」「改了又是额外工作」）——防御会毁掉审查的价值：你请人来挑毛病，人家挑了，你却把毛病顶回去。
- **接住四步**：
  1. **别急着辩护**：先让情绪过去，「情绪上头时的反驳，九成是错的」；
  2. **先理解再回应**：没听懂就问——「你担心的是哪类问题？」一句把意见从模糊变清楚；
  3. **逐条回应**：每条意见给结论——改 / 讨论 / 不改（并说明理由）；
  4. **改完要验证**：回应「改好了」之前先把验证做了——**「改好了 + 有验证 + 有测试」才是对一条意见的完整回应**。
- **意见不合三态**：有不同考虑（讲理由：性能/兼容/成本）/ 暂时不改（说清为什么、记下来）/ 听懂了但不同意（明确说明，别含糊答应又偷偷不改）。核心：**关键是不沉默、不敷衍——意见被好好拒绝，好过被假装接受**。
- **案例（MoneyRules 接口改造）**：老王意见——`assertValid` 直接抛异常，调用方想知道具体哪儿错了只能 catch 完再猜，改成返回 `Optional` 让上层决定怎么处理。判断「对（`Optional<String>` 让 API 层能精确提示『金额最多两位小数』）+ 值得改（调用方就一个）」→ 改。改法上**别让意见打架**：保留 `assertValid`（要「不合法就抛」的场景仍用），新增 `check` 返回 `Optional<String>`，各司其职；补三个测试用例（空/非正/三位小数）。
- **心态金句**：「代码是你们的共同作品，不是你的私人领地。意见砸在代码上，不是砸在你脸上。」

## 详细笔记

对应 [[Superpowers]] 18 技能协作类（与 18 篇构成审查对篇，[[Superpowers 安装与技能全景]]）。案例直接续 18 篇（老王审出的两处之一），并回填了 `MoneyRules.check` 的接口来历（代码注释自标「015 篇版本 assertValid / 017 篇按审查意见新增 check」）——**这条时间线与 17 篇正文自洽**：17 篇 defense-in-depth 第 3 层用的 `check` 正是本篇审查意见的产物。教学叙事为「人审人」；对 AI 协作场景的迁移（推论）：agent 收到 reviewer 裁决时的「逐条回应 + 改完给证据」即 subagent-driven-development 的裁决处理环（见 [[Superpowers subagent-driven-development AI 项目经理]] 两级审查）。结尾老王「把规矩固化下来，让 AI 也照着来」为 20 篇 writing-skills 埋线。

## 与已有内容的关联

- **前篇**：[[Superpowers requesting-code-review 主动求审]]（审查对篇：一求一接）。
- **MoneyRules 谱系闭环**：15 篇 assertValid（[[Superpowers root-cause-tracing 根因追踪]]）→ 19 篇新增 check → 17 篇用作纵深防御第 3 层（[[Superpowers defense-in-depth 纵深防御]]）——同一装置三次出场，系列代码前后自洽的典型样本。
- **与 v6.0 reviewer**：[[Superpowers 6.0 reviewer 只读重写]] 的 reviewer 裁决（spec/quality 两轴）对应本篇「逐条回应给结论」的裁决格式。
- **证据纪律**：第 4 步「改完给证据」与 [[Superpowers verification-before-completion 验证再收工]] 同源（验证即证据）。

## ⚠️ 矛盾 / 待澄清

- 与 wiki 既有记载无冲突。源文 URL 含 `poc_token` 参数（抓取工具附加），检索原文时以文章主体 URL 为准。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers requesting-code-review 主动求审]] · [[Superpowers defense-in-depth 纵深防御]] · [[Superpowers root-cause-tracing 根因追踪]] · [[Superpowers verification-before-completion 验证再收工]] · [[Superpowers 6.0 reviewer 只读重写]] · [[Superpowers subagent-driven-development AI 项目经理]]
