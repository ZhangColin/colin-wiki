---
type: synthesis
title: "MCP 与直连凭证对比"
domain: [AI编程, 软件工程]
tags: [MCP, 数据库, 权限控制, 安全, Harness Engineering]
sources:
  - "[[Harness Engineering 团队落地规范]]"
  - "[[TAB 大仓 Harness 工程化实战]]"
created: 2026-08-25
updated: 2026-08-25
status: active
confidence: medium
aliases:
  - 为什么用 MCP
  - MCP vs 直连数据库
---

# MCP 与直连凭证对比

> 一句话结论：给 agent 数据库凭证 + CLI 确实「能」访问，但 MCP 把访问从「靠模型自觉」变成「结构性约束」——权限收窄、凭证不进上下文、工具面即文档；个人本地场景直连合理，团队/生产场景 MCP 收益才兑现。

## 问题

读 [[Harness Engineering 团队落地规范]] §5.1 时的自然疑问：既然给了数据库地址、用户名、密码，agent 通过 Bash 跑 `mysql -e "..."` 就能查库，为什么还要费劲配置 DB MCP Server？

## 对比 / 分析

| 维度 | 给凭证 + Bash 跑 CLI | 配置 DB MCP |
| --- | --- | --- |
| 权限范围 | 全量 SQL：SELECT 能跑，DROP/UPDATE 也能跑 | 工具面只暴露 `list_tables` / `describe_table` / 只读查询，写操作物理上不存在 |
| 约束方式 | 靠 Rules 文本（软约束，模型可能违反） | 靠工具结构（硬约束，无入口可违反） |
| 凭证去向 | 必然进命令行 → 模型上下文 / shell history / `ps` 进程列表 | 只在 MCP server 进程 env（源示例 `"MYSQL_PASSWORD": "${DB_PASSWORD}"` + `readonly_user`），模型看不到密码 |
| agent 上手 | 试错客户端环境、转义、解析文本表格 | 启动即注入工具清单 + 参数 schema，返回结构化 Schema/类型 |
| 权限审批粒度 | Bash 模式匹配难区分 SELECT 与 DROP | 按工具逐个 allow/deny，server 层可审计/限流 |
| 团队维度 | 每人手里一份生产凭证 | 远程 streamable-http MCP 一次部署全队复用（Knot MCP 市场） |

## 结论

1. **直觉没有错，但混淆了「能访问」与「受控访问」**：MCP 解决的是后者。这正是 [[Harness Engineering]] 主题「把标准写进系统，让 AI 在约束下干活」的数据库场景具象化——源 §1.2 把「安全边界：权限控制、审计日志」列为 Harness 核心职责。
2. **约束的结构性 > 文本性**：把「只读 Schema」写进 Rule 是愿望，做进工具面才是事实。与 [[TAB 大仓 Harness 工程化实战]]「能判定的就别留在 Rule 里」同一原则——权限可用工具结构判定，就不留给自然语言解释（推论，跨源连接）。
3. **凭证进上下文即失控**：源 §4.1 自己警告外部模型会外传代码上下文——Bash 直连等于把生产密码寄给模型服务商；MCP 方式凭证只在本机进程。（凭证暴露面为通用工程常识，非源内直接论述。）
4. **对 agent 也更省**：DB MCP 目标是「自动读取实时 Schema，避免 AI 写出不存在的字段」（源 §支柱二）；结构化输出省去裸 CLI 的环境试错与文本解析——是反模式「MCP 过度接入 Token 暴增」的反面：该用的 MCP 反而省 token。
5. **何时不用 MCP**：源 §5.1.1 明确——简单脚本直调 API、纯逻辑推理、复杂度大于问题。个人本地开发库 / 一次性查询，直连完全合理；MCP 收益在**团队 + 生产 + 敏感凭证**场景兑现。

## 引用

- [[Harness Engineering 团队落地规范]] — §支柱二（DB MCP 表格）、§5.1.2（mcp.json 示例：readonly_user / ${DB_PASSWORD}）、§5.1.1（何时不该用 MCP）、§1.2（安全边界职责）、§7（红线「MCP 优先」）、§8.1（反模式 4）
- [[TAB 大仓 Harness 工程化实战]] — 「能判定的就别留在 Rule 里」
- [[Harness Engineering]] — 支柱六约束与恢复

## 相关页面

- [[Harness Engineering 团队落地规范]] · [[Harness Engineering]] · [[TAB 大仓 Harness 工程化实战]] · [[Skills 设计方法论]]
