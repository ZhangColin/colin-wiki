---
title: "MattPocock Skills v1.1 重磅更新｜重构AI编程全流程"
source: "https://mp.weixin.qq.com/s?__biz=Mzk0MzE4MzY5MQ==&mid=2247483882&idx=1&sn=57a4f1a8b77629f38f766968833613b0&chksm=c33683cef4410ad8f8749d2a1b992d6910cc080ddaa3223f41d51e05551a3adb9acba6c1b3c3&cur_album_id=4613068008494923776&scene=189#wechat_redirect"
author:
  - "[[小匠Skills]]"
published:
created: 2026-07-26
description: "mattpocock/skills 核心流程全面更名、修复盘问模块bug、补齐开发闭环，重磅工具Wayfinder上线"
tags:
---
小匠Skills 小匠Skills *2026年7月20日 21:10*

之前给大家完整拆解过 **mattpocock/skills** 这套给 AI 编程加工程纪律的标准流水线，靠 /grill-with-docs → /to-prd → /to-issues → /tdd 四步流程，解决 AI 写代码需求跑偏、上下文丢失、任务混乱的痛点。 [mattpocock/skills 标准工作流：从需求到上线](https://mp.weixin.qq.com/s?__biz=Mzk0MzE4MzY5MQ==&mid=2247483838&idx=1&sn=5b84309ee90514cc1f45cc39854ce229&scene=21#wechat_redirect)

时隔不久作者正式发布 **v1.1 大版本迭代** ，核心流程全面更名、修复盘问模块大量 bug、补齐完整开发闭环，更推出重磅新工具 **Wayfinder** ，专门解决大型项目单会话承载不下的规划难题。本文结合官方更新日志与原视频讲解，一次性讲清全部改动、新版工作流、升级操作与使用场景。

01

PART

核心技能更名：to-prd / to-issues 正式下线

RENAME · SPEC & TICKETS

本次最基础、影响所有老用户的改动是两大流程指令重命名，官方给出清晰更名逻辑：

1

/to-prd → /to-spec：原工具产出的文档不只是产品需求 PRD，还包含技术架构、领域模型、约束规范等综合内容，叫“Spec（规格文档）”更精准；保留兼容提示，打开文档会标注“Spec（规格文档）”，降低学习成本。

2

/to-issues → /to-tickets：合并原 to-plan 能力，统一将 Spec/对话需求拆分为垂直切片式任务工单 Ticket，废弃旧指令，后续全部使用 /to-tickets 做任务拆分。

升级注意事项

旧版本技能缓存会产生冲突，提供两种升级方案任选其一：

1

命令行重装方案：直接执行安装命令覆盖更新。

bash

npx skills@latest add mattpocock/skills

2

CCSwitch 可视化工具方案（推荐）：CC Switch v3.13.0 起内置了 Skills 自动更新检测——基于 SHA-256 哈希对比本地与远端版本，有更新时卡片自动显示"有新版本"标识，点击"更新"按钮即可，支持单项和批量更新，无需卸载后重新安装。

02

PART

Grill 盘问体系全面修复，解决交互混乱痛点

GRILL · BUG FIXES

/grill-me、/grill-with-docs 作为需求对齐入口，修复了长期反馈的几类核心 bug，新增强制校验机制：

1

修复重复提问、一次性抛出多问题、Agent 自询自答逻辑漏洞。

2

区分 Facts（事实）与 Decisions（决策）：代码库可读取的客观信息 Agent 自动查询，架构选型、业务规则等人为决策必须询问用户，杜绝 AI 擅自假设需求。

3

新增 确认校验闸门：完整规划生成后，必须等待用户确认，才能进入代码实现环节，避免未经同意直接开发。

4

优化领域模型同步逻辑，自动更新 CONTEXT.md、ADR 文档，全程统一项目术语。

03

PART

补齐完整软件开发生命周期

LIFECYCLE · COMPLETE

旧版流程仅有 TDD 单路径，v1.1 打通从规划到落地、质检的完整闭环：

1

全新技能 /implement：提供除 TDD 外的标准化实现路径，适配快速迭代、小型功能开发，同样遵循垂直切片独立可验证原则。

2

Code Review 能力大幅升级：引入 Martin Fowler 代码坏味检测体系，双维度审查——规范轴校验仓库编码标准、重复代码、长链路等坏味道；需求轴核对代码是否严格匹配 Spec/Ticket 需求，实现需求与代码双向绑定校验。

04

PART

v1.1 王牌新工具：Wayfinder

WAYFINDER · NEW TOOL

专门解决大项目超出单 Agent 会话上下文的行业痛点

这是本次版本最大亮点，专门解决大型项目超出单 Agent 会话上下文的行业痛点：

1

核心定位：大型项目、全新基建、多模块复杂功能专用规划入口，不替代基础四步流程。

2

配套子技能：内置 /research（权威资料调研，输出带引用 Markdown）、/prototype（低成本验证交互/逻辑原型）。

3

任务拆分机制：自动把巨型需求拆解为独立工单，划分调研、盘问、原型、开发多类型 Ticket，每张工单体量适配单会话，支持增量迭代、跨会话交接。

4

适用场景：全新开源项目、多端联动大功能、大规模代码重构。

05

PART

TDD 技能逻辑优化 + 全流程路由升级

TDD · ROUTING

1

TDD 循环规则微调，和 /implement 形成两条并行实现路径，按需选用。

2

辅助指令 /ask-matt 路由逻辑更新，自动推荐新版标准链路：需求盘问 → /to-spec → /to-tickets → /implement / /tdd。

3

Wayfinder 定位为场景化进阶入口，基础小需求仍沿用经典四步流程，不强制替换原有工作流。

06

PART

新版完整标准工作流（基础版 + 进阶变体）

WORKFLOW · ALL PATHS

基础标准流程（日常中小型需求）

![[Image 3.webp|图片]]

三大进阶变体（对应不同开发场景）

1

大型新项目/复杂功能：/wayfinder → 子技能 research/prototype → /to-spec → /to-tickets → 实现

2

代码库重构优化：/improve-codebase-architecture → prototype → /to-spec → /to-tickets

3

Bug 修复闭环：/diagnosing-bugs → /tdd 补充回归测试

自动兜底质检技能（无需手动调用）：/code-review、/domain-modeling、/codebase-design，代码提交、需求变更时自动触发，保障工程规范。

07

PART

官方项目数据与配套学习预告

DATA · RESOURCES

1

仓库数据：目前仓库收获 160K 星标、750 万次下载，是 Claude Code 生态最主流 AI 工程化工具集。

2

配套课程：作者推出低价 AI 编程速成课，系统讲解整套 Skills 落地实操。

3

上手建议：先跑通基础四步流程，熟练后再尝试 Wayfinder 大型规划能力。

///

LAST

写在最后

SUMMARY · CONCLUSION

v1.1 不是简单功能增补，而是对整套 AI 编程工程体系的规范化重构：统一术语、修复交互缺陷、补齐开发闭环、攻克大型项目规划难题。

如果你之前在用旧版 to-prd/to-issues，建议尽快清空旧技能完成升级；做中小型开发沿用基础流程，接手大型项目直接使用 Wayfinder，彻底告别 AI 写代码需求失控、上下文断裂的老问题。

仓库地址：github.com/mattpocock/skills

我是 小匠，热衷于分享 AI 观察与干货。

如果你觉得今天这篇有收获，欢迎点赞、在看、转发三连，我们下篇见。

既然看到这里了，如果觉得有用，随手点个赞、在看、转发三连吧。

点赞

推荐

转发

THANKS FOR READING

**微信扫一扫赞赏作者**

mattpocock/skills · 目录