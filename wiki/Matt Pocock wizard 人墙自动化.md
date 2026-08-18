---
type: source
title: "Matt Pocock wizard 人墙自动化"
domain: [AI编程]
tags: [mattpocock-skills, wizard, HITL, bash 脚本, 人机交接, v1.2]
sources: []
source_path: "raw/articles/Agent 撞到人墙就卡住，Matt Pocock 的 wizard 把人工步骤变成可复用 bash脚本.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491668&idx=1&sn=bc442ff4db7c4b0125b720b2fd37a62c"
author: "[[运维有术]]"
date_published: "2026-08-12"
created: 2026-08-17
updated: 2026-08-17
status: active
aliases: [wizard skill, 人墙自动化, 脚本化 HITL]
---

# Matt Pocock wizard 人墙自动化

> 一句话要点：[[运维有术]] 源码级深拆 [[mattpocock skills]] v1.2 毕业的 `wizard`——agent 撞到"只有人能做的步骤"（API key 在你的控制台里）时，自动生成一个交互式 bash 向导，**agent 只写脚本、从不运行它；脚本由你在自己的机器上运行**——把人机交接从聊天记录变成可复用资产。

## 关键要点

- **撞墙现场**：接第三方支付，agent 读写代码全通，卡在"API key 只有你能拿到"；wizard 触发后生成 `setup-acme-pay.sh`，四段式交接：撞墙→触发 wizard→人工部分→回主链路。
- **四类触发**：provisioning infrastructure / setting up credentials or CI secrets / walking an unfamiliar third-party dashboard / a one-off migration or cutover。**反向约束**："Don't invoke this for steps the agent can perform itself"——agent 能做的走 wizard 是把人拖进不该人参与的环节。
- **model-invoked 澄清**：手动 `/wizard` 照旧可用，"Typing /wizard works exactly as before - model-invocation only ever adds the agent's reach"；顺带绕开 Claude 桌面/网页端 #693（user-invoked skills 从列表被删）的问题。
- **流程四步**：①**scope the procedure**——先读仓库（`.env*`/README/`docker-compose*`/框架配置/`.github/workflows/*`），**每个 `secrets.*`/`vars.*` 引用都是必须产出的值**；三点对照表（人从哪拿/写到哪/是否机密）；收尾把 stage 列表展示给用户确认（兼任 proposal）。②**map each stage's journey**——路径必须落到陌生人能照做（"Dashboard → Developers → API keys → Reveal test key → copy"）；红线：**不知道当前 UI 或确切命令时明说并询问，绝不编造可能不存在的步骤**。③**author the wizard**——复制 `template.sh`（204 行）；**library/STAGES 分离，library 永不手改**（"that consistency is the point"）；helpers：`stage`（清屏只留当前步骤）/`open_url`/`ask`/`ask_secret`（隐藏回显，重跑时 .env 现值作默认）/`write_env`（幂等）/`set_secret`（gh 不可用降级 warn）/`confirm`（y/N 门）/`finish`（汇总 WRITTEN_ENV/WRITTEN_SECRET/SKIPPED）。④**verify and hand off**——**不做端到端自跑**（open_url 弹浏览器、ask_secret 阻塞等人）；改三层静态检查（bash -n + shellcheck + chmod）+ 逐值流向静态追踪（"脚本可以没真跑过，但每个值的来龙去脉必须在纸上走通"）。
- **安全卖点**："Nothing is sent to an agent while it runs"——脚本运行时开浏览器阻塞等人类，密钥不经 agent 直接写 `.env`/secrets；与其给 agent computer-use 权限，不如给一个确定性脚本。
- **脚本化 HITL vs 运行时 HITL**：主流框架（Haystack confirmation policy、LangGraph halt/resume、ag2 human_input_mode）是 agent 运行时暂停等确认；wizard 反着来——把人类操作编排成可执行程序，人自己跑；产物可复用/可提交/可版本化，代价是没有即时反馈，用静态追踪补。
- **默认 ephemeral**：为一次运行构建，用完删除；用户要可复用 setup 路径才 commit。
- **四种误用**：步骤话术丢聊天框（原罪，"instead of dumping numbered instructions into the chat"）；agent 能做却误触；编造 UI 路径（翻车重灾区）；把所有值 write_env（.env 膨胀、密钥散落）。
- **社区边界（issue 均 Open）**：无 back button（stage 输错 Ctrl-C 重跑，重跑廉价）；#741 箭头键失效（`read -r` 非 Readline，建议 `read -e -r -p`）；**#811 symlink `.env` 被 `mv` 替换**——`write_env` 以 `mv "$tmp" "$ENV_FILE"` 收尾，symlink 被普通文件替换（repo 与中央存储脱离、key 轮换失效、secrets 被复制进工作树），且 bug 在"永不手改"的 library 区，wizard 作者绕不过；工具链偏 GitHub/TS 生态（set_secret 依赖 gh；Discussions #177 请求 Gitea）。
- **体系定位**：官方原话 "sitting at the line where automation stops and a human has to click"——不扩大自动化范围，只把边界处的人工操作变成资产。与 grilling 对照：**wizard=执行型人机交接（人怎么参与），grilling=决策型对齐（做什么、为什么）**。
- 诚实声明：CHANGELOG 描述的是设计意图非实测效果；值不值得用取决于你的场景里"人墙步骤"出现频率。

## 与已有内容的关联

- 相关概念：[[mattpocock skills]]、[[HITL 与 AFK]]（wizard 是"脚本化 HITL"新形态）
- 相关实体：[[Matt Pocock]]、[[运维有术]]
- 相关源：[[Matt Pocock Skills v1.2 更新全景]]（wizard 毕业的版本背景）、[[Matt Pocock setup-matt-pocock-skills 配置落地]]（第一天配置 vs 运行时人墙）、[[Matt Pocock 后台 research 与主线程并行]]（HITL/AFK 划线）

## ⚠️ 矛盾 / 待澄清

- 无源间矛盾；#811 symlink 问题是当前最有实质性的批评（静默故障），使用中央 secrets 存储的团队须注意。
- 与 [[HITL 与 AFK]] 概念页的四道边界机制互补：wizard 处理的是"人墙处人怎么参与"，与 claim/分支/指针/契约（防并发踩脚）是两类边界。

## 相关页面

- [[mattpocock skills]] · [[HITL 与 AFK]] · [[Matt Pocock Skills v1.2 更新全景]] · [[Matt Pocock setup-matt-pocock-skills 配置落地]] · [[Matt Pocock]] · [[运维有术]]
