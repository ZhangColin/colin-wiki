# 日志

> append-only 时间线。格式：`## [YYYY-MM-DD] <op> | <标题>`。
> 查最近 N 条：`grep "^## \[" LOG.md | tail -N`。只追加，不改写历史。

## [2026-07-24] init | 初始化 wiki 结构

按 `llm-wiki.md` 方法论搭建三层架构（raw / wiki / schema），建立规则文件（CLAUDE.md + AGENTS.md）、四种页面模板、INDEX/LOG 与 Bases 视图，并把 Obsidian 附件目录指向 `raw/assets`。

## [2026-07-24] ingest | LLM Wiki 方法论 gist

摄入根目录 `llm-wiki.md`（[[Andrej Karpathy|Karpathy]] 方法论原文，本 wiki 宪法）为 source 页 [[LLM Wiki 方法论 gist]]。与中文教程同源、互为补充；为 [[LLM Wiki]] 概念页提供第二来源。

## [2026-07-24] ingest | Karpathy 的 LLM Wiki 搭建实战

摄入 [[运维有术]] 的中文实战教程（微信公众号，2026-07-03）。建 source 摘要页，并据此建实体 [[Andrej Karpathy]]、[[Obsidian]]、[[Vannevar Bush]]、[[Dataview]]、[[运维有术]] 与核心概念 [[LLM Wiki]]。显式记录文章与 colin-wiki 的本地化取舍（Schema 双镜像、扁平 wiki/、英文 type、synthesis 第四类）。pending 登记 RAG/Ingest/Query/Lint/Memex/Schema 等，待后续不同脉络源触发独立建页。

## [2026-07-24] update | llm-wiki.md 归位至 raw/articles/

将 Karpathy LLM Wiki gist 原文从仓库根目录移至 `raw/articles/llm-wiki.md`，使其符合三层架构「源在 raw」规范。同步更新 `CLAUDE.md` + `AGENTS.md`（方法论依据引用）、[[LLM Wiki 方法论 gist]] 的 `source_path`、[[Karpathy 的 LLM Wiki 搭建实战]] 的原文链接。另删除根目录 0 字节空占位文件 `LLM Wiki.md`（命名歧义致 Obsidian 误建，使 `[[LLM Wiki]]` 链接错误指向空文件，已消除歧义）。

## [2026-07-26] ingest | mattpocock skills 专题（4 源批量）

批量摄入 [[mattpocock skills]] 主题 4 篇新源，建 concept 统领页 + entity [[Matt Pocock]]、[[小匠Skills]]、[[AI随风随风]]：
- [[Matt Pocock 完整工作流视频]] — 一手源（YouTube M6mYodf0dJM），Matt 本人演示 main flow 全流程
- [[mattpocock skills 标准工作流]] — 小匠Skills，v1.0 主流程与变体总览
- [[MattPocock Skills v1.1 重磅更新]] — 小匠Skills，v1.1 更新（命令更名 / Wayfinder / SDLC 闭环）
- [[mattpocock skills 秒杀系统实战]] — AI随风随风，秒杀系统 demo 实战（第二视角）

⚠️ 数据质量修复：第 3 篇 B 站搬运版 transcript 因抓取工具字幕匹配错误，内容为韩综《GOING SEVENTEEN》造船一期（与视频无关）；用 web reader 重抓 YouTube 官方 transcript，整理为正确字幕版新源文件补入 `raw/articles/`（原脏数据文件保留不动，守 `raw/` 只读红线）。source 页置顶警示块说明双文件关系。

⚠️ 版本演进标注：第 2、4 篇用 v1.0 旧命令名（`/to-prd`、`/to-issues`），v1.1 后已被 `/to-spec`、`/to-tickets` 取代——concept 页「v1.1 演进」表与各 source 页「⚠️ 矛盾」区块显式标注。concept 页另含与本 vault 自用 superpowers skills 的本地化对照（grill vs brainstorming 等）。

## [2026-07-27] ingest | mattpocock skills v1.1 官方源 + 推荐工作流速查

补抓两篇 v1.1 官方英文一手源（填补中文圈对 Wayfinder 操作细节的缺口），并沉淀本 wiki 首张 synthesis 速查页：
- [[Matt Pocock skills v1.1 官方更新日志]] — aihero.dev 官方 changelog（main flow 官方图、命令改名理由、implement/code-review/tdd 原话、Fowler smells 清单）
- [[Matt Pocock skills Wayfinder 官方文档]] — github `wayfinder/SKILL.md` 原文（chart/work 两套流程、map/ticket/frontier/fog-of-war 概念、四类 ticket、"一 session 一 ticket"铁律）
- [[mattpocock skills 推荐工作流速查]] — synthesis：按"活的大小/类型"给推荐路径（主线四步 / wayfinder 超大活 / 重构 / bug 修复），新手入口、常查导向

更新 [[mattpocock skills]] concept 页：frontmatter sources 与正文「证据/来源」段补两篇官方源、Wayfinder 段扩充操作细节并指向新页、相关页面加 synthesis。INDEX synthesis 区从"（暂无）"开张；pending 的 Wayfinder 因已有专门 source 页覆盖而移除。

抓取注记：defuddle 抓 changelog 成功（metadata 提取报非致命 URL 错，正文文件完整 239 行）；wayfinder SKILL 因 `raw.githubusercontent.com` 返回 text/plain 被 defuddle（仅处理 HTML）拒收，改用 web reader 提取 github blob 正文写入 raw——属 defuddle 不适用时的合理替代，未改源内容。

## [2026-07-28] ingest | mattpocock skills Handoff 官方文档

摄入 [[Matt Pocock skills Handoff 官方文档]]（aihero.dev/skills-handoff 一手）。补 [[mattpocock skills]] concept 页"辅助与交接"段 /handoff 的操作细节（何时用：context 风险/收工/换 agent；产出：live thread + suggested skills + references not copies + redacted secrets；续接：新 session 贴 handoff.md 作 bootstrap prompt）。handoff 是轻量跨 session 交棒（compaction），与 [[Matt Pocock skills Wayfinder 官方文档|Wayfinder]] 的结构化多 session 规划形成对照。

抓取注记：defuddle 抓 aihero.dev 成功（metadata 报非致命 URL 错，正文完整 41 行）；SKILL.md 原文（skills/productivity/handoff/）因 raw text/plain 被 defuddle 拒收、web reader 当前 500 未能抓取，官方文档页已含 SKILL 核心阐释（What it does/When/What it carries/Where it fits），作为源充分。

## [2026-08-10] ingest | 积压清理（27 源批量 + 13 新页）

用户要求批量清理自 7/28 起积压的 28 篇文章（实际 27 篇 ingest + 1 篇判定脏数据跳过）。按主题分 7 簇并行派发 subagent 抽取要点，统一撰写。新增 7 实体 + 6 概念，更新 [[mattpocock skills]]/[[运维有术]]/INDEX。

**mattpocock skills 机制深读系列（15 源，均 [[运维有术]] 除标注）**：
- 总览/主线：[[Matt Pocock 工作流五步主线鸟瞰]]（[[鸟窝]]外部视角）、[[MattPocock Skills 21 个 skill 工程纪律体系]]、[[Matt Pocock main flow 五环节]]、[[Matt Pocock 6 个 skill 工程师本能]]、[[Matt Pocock 三个 skill 结构词汇]]、[[Matt Pocock writing-great-skills 八词诊断]]、[[Matt Pocock setup-matt-pocock-skills 配置落地]]
- wayfinder/handoff/分流/并行/原型：[[Matt Pocock wayfinder handoff 接力协议]]、[[Matt Pocock 三条 on-ramp 分流]]、[[Matt Pocock 后台 research 与主线程并行]]、[[Matt Pocock Prototype 保真度方法论]]
- grill/调试/Git 护栏：[[grill-me 实战指南]]、[[还在用 grill-with-docs]]、[[diagnosing-bugs 与 tdd]]、[[Matt Pocock 的 Git 护栏]]

**Superpowers 跟踪系列（5 源）**：[[Superpowers 5万 Star 工程纪律框架]]（v4.3）、[[Superpowers 实战指南 7步流程14技能]]（v5.0.7）、[[Superpowers 7步闭环工作流深度指南]]、[[Superpowers 7阶段交付SKU库存扣减]]、[[Superpowers 6.0 reviewer 只读重写]]（v6.0）

**gstack 与插件搭配（3 源）**：[[gstack 最佳实战]]、[[Superpowers + gstack 搭配实战]]、[[Claude Code 双插件搭配 superpowers 与 gstack]]（[[飞哥]]，2026-04-07 最早提出搭配）

**方法论对比（3 源）**：[[12 行 vs 689 行：mattpocock skills 与 superpowers 的路线之争]]、[[2026 年再看 Superpowers：grill-me 场景选型]]、[[Anthropic 与 Perplexity 的 Skills 设计方法论]]

**数据/架构新领域（1 源）**：[[智能问数平台设计：五层架构与十步落地]]（开启 [[智能问数平台]] 新 domain）

**跳过（1）**：`双语字幕 mattpocockskills end-to-end` 判定为脏数据串台（正文为韩综字幕，与 [[Matt Pocock 完整工作流视频]] 已登记的串台情况一致），不建 source 页。

**新建实体（7）**：[[Superpowers]]（concept，本 vault 自用）、[[gstack]]、[[Jesse Vincent]]、[[Garry Tan]]、[[鸟窝]]、[[飞哥]]、[[Anthropic]]、[[Perplexity]]。
**新建概念（6）**：[[smart zone]]、[[HITL 与 AFK]]、[[子代理驱动开发]]、[[Skills 设计方法论]]、[[智能问数平台]]。
**更新**：[[mattpocock skills]]（补 12 源、skill 数 18vs21 口径、grill-me 取代、smart zone 独立）、[[运维有术]]（升格为被引最多作者 + 三大系列登记）、INDEX（全量重组）。

## [2026-08-10] lint | 数据冲突与待办标注

本次批量 ingest 显式标注的矛盾/过时/数据冲突（详见各页「⚠️ 矛盾」区块）：
- **smart zone 阈值未收敛**：120k / 125-150k / 140k 三处一手源不一 → 沉淀 [[smart zone]] 取区间"约 120k–150k（有争议）"。
- **Superpowers star 数三方冲突**：51,400(2月) → 36.6K(4-09) → 147,000+(4-13) 倒挂暴涨，高度存疑 → [[Superpowers]] 设「Star 数溯源」表，须 GitHub 实时核对。
- **mattpocock skill 数 18 vs 21**：v1.1.0 源码核对为 18（12+6），早期流传 21 → 概念页与 [[MattPocock Skills 21 个 skill 工程纪律体系]] 标注。
- **v6.0 合并 reviewer**：v5.x"两阶段审查"描述在 v6.0 后过时 → [[Superpowers 6.0 reviewer 只读重写]] 标注，旧页注 v6.0 变化。
- **grill-me 已被取代**：Matt 改推 grill-with-docs，新链路 `/grill-with-docs → /to-prd → /to-issues → tdd` → [[grill-me 实战指南]]/[[还在用 grill-with-docs]] 标演进链。
- **安装命令/marketplace 名多源不一**：superpowers（`@claude-plugins-official` vs `@superpowers-marketplace`）、gstack（marketplace vs git clone）两路径并存 → 各实体页标注。
- **colin-wiki 方法论缺口（推论）**：相对 [[Skills 设计方法论]]（Anthropic/Perplexity）缺 Eval 体系与 Gotchas 飞轮；"LLM 自写 Skills 平均无收益"与本 wiki 自动 ingest 有张力 → [[Skills 设计方法论]] 标注，建议 Lint 流程补待办。

**待用户拍板**：① 是否建 synthesis《Superpowers vs mattpocock skills 路线对照》《superpowers+gstack 大脑手脚搭配》（已具备 ≥2 源，本轮未建以控规模）；② 现有 [[mattpocock skills 标准工作流]] 等旧页是否补 v1.1 注记或标 stale；③ Superpowers star 数是否 web 核实订正。

## [2026-08-10] update | 建 2 synthesis + v1.0 旧页加 v1.1 注记

应用户决定补完收尾：
- **新建 synthesis（2）**：[[Superpowers 与 mattpocock skills 路线对照]]（强制 vs 最小化两条路线分歧 + 共存结论，confidence: high）、[[Superpowers 与 gstack 搭配（大脑与手脚）]]（双插件搭配范式，[[飞哥]] 与 [[运维有术]] 独立得出一致结论，confidence: high）。
- **v1.0 旧页加 v1.1 升级注记**：[[mattpocock skills 标准工作流]]、[[mattpocock skills 秒杀系统实战]] 顶部加醒目 🔖 注记块，指向 v1.1 新内容（命令更名/wayfinder/implement/双轴 review），正文保留作历史记录不重写。
- INDEX 综合 区登记 2 新页；[[Superpowers]]/[[gstack]] 反向链接到新 synthesis。
- 用户决定：star 数不 web 核实（保持存疑标注）。
