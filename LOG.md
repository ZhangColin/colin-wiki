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

## [2026-08-17] ingest | 增量 8 源（v1.2 系列 4 + AgentScope 3 + Harness Engineering 1）

处理 8/10 之后新增的 8 篇文章（含 1 篇上次被 git 一并提交但未 ingest 的 AgentScope 首篇）：

**mattpocock skills v1.2 系列（4 源，均 [[运维有术]]）**：
- [[Matt Skills v1.2 grilling 重构]] — grilling 从一次一问重构为 round-by-round frontier（13 问约 3 轮），争议设计可 opt-out
- [[Matt Pocock wizard 人墙自动化]] — 人墙步骤→交互式 bash 向导，"agent 只写脚本从不运行"，脚本化 HITL
- [[Matt Pocock 会话边界五问决策树]] — ask-matt 的 PHASE-BOUNDARIES.md：Continue/clear/handoff/subagent/compact 五问串行决策，compact 垫底
- [[Matt Pocock Skills v1.2 更新全景]] — 4 处人机边界位移 + 双平台三态 + prototype 留档

**AgentScope Java 系列（3 源，[[AI实战有术]]/Dilee，新领域）**：
- [[AgentScope Java 2.0 正式版]]（v2.0.0 GA 解读 + 三框架选型）、[[AgentScope Java 2.0 上手 Web 接口]]、[[AgentScope Java 2.0 核心架构拆解]]

**Harness Engineering（1 源，[[腾讯程序员]]/atreusliu）**：
- [[Harness Engineering 团队落地规范]] — OpenAI"Agent = Model + Harness"理念的团队级落地（6 支柱 + harness-audit）

**新建**：实体 [[AgentScope Java]]、[[AI实战有术]]、[[腾讯程序员]]；概念 [[Harness Engineering]]（跨源：腾讯落地 + AgentScope HarnessAgent + Matt v1.2 harness-neutral）。

## [2026-08-17] lint | v1.2 触发的口径更新与注记同步

- **[[mattpocock skills]] 概念页**：新增「v1.2 演进」段（8 行对照表）；一句话定义更新（212K★/13.5M 下载/双 harness）；§6 grill-me 补 7 行薄封装与依赖坑；相关页面补 4 源。
- **star 数口径更新**：7 月 170K → 8 月 212.2K（08-10 检索，全站第 19）/213.4K（08-14）；增长曲线 5 月 77K→7 月 170K→8 月 212K。
- **skill 数口径随版本演进**：v1.1=18（源码核对）→ v1.2 plugin=24 个 promoted skill——引用必须带版本号。
- **[[smart zone]] 新增口径**："约 40% context 处开始退化"（相对比例）并入阈值表；新增 Context Rot 论文机制（退化由长度本身造成）、Ralph 插件反面教材、会话预算纪律。
- **v1.2 注记同步 5 页**：[[Matt Pocock Prototype 保真度方法论]]（TUI→单文件 HTML、删除→留档）、[[grill-me 实战指南]]（grilling round-based）、[[还在用 grill-with-docs]]、[[mattpocock skills 标准工作流]] 与 [[mattpocock skills 秒杀系统实战]]（v1.1 注记升级为含 v1.2 的版本演进链）。
- **[[运维有术]]** 补 v1.2 四连发系列；**[[Superpowers 与 mattpocock skills 路线对照]]** 补社区定位印证（"grill-me is the pressure-test primitive; Superpowers is the default engineering OS" + 可组合共识）；**[[HITL 与 AFK]]** 补 wizard 脚本化 HITL 小节；**[[Skills 设计方法论]]** 补 wait-what/concision 同源证据。
- INDEX 新增 3 实体 + 1 概念 + 8 源（新开 v1.2 与 AgentScope 两个源分组）；pending 补"会话边界管理""round-by-round frontier""OpenAI"。
- 待用户后续观察：v1.2 的 round-based 是否成为标准（半年后看多少人切回一次一问）；wizard #811 symlink bug 官方修复进展。

## [2026-08-25] ingest | 去哪儿网 AI Coding 研发平台实践

- [[去哪儿网 AI Coding 研发平台实践]] — 李佳奇分享（[[爱海贼的无处不在]] 深度解读，2026-06-24）：L0-L5 自动化分级 + AI R&D Metrics（Volume×Maturity，反"出码率 KPI 竞赛"）+ **Harness 四把锁**（触发/门禁/隔离/人工审查 + 12 环节控制点）+ 三平台（QunarDevCenter 数据采集·三张表·Git Blame 出码率 / 天弦 QDO 自修复编排·JDK 升级 211 应用 / Qsuperpowers 企业定制 Superpowers·3pd→1pd 提效 66%）+ Skills Gateway 治理（50 Skills·PR 审核·种子上下文）+ 双估时机制；成果 100% 覆盖 / 75%+ 出码率 / L3 占比 30%+（自述口径）。
- 新建：实体 [[去哪儿网]]（含三大内部产品条目）、[[爱海贼的无处不在]]。
- 更新概念 3 页：[[Harness Engineering]]（第 4 源，新增"去哪儿样本"小节；注记同词侧重差异——OpenAI"赋能型运行环境" vs 去哪儿"控制型过程能力"，互补非矛盾）；[[Superpowers]]（新增"企业级定制印证：Qsuperpowers"——skill 名原样复用 + 定位边界"通用流程标准化，企业集成自建"）；[[Skills 设计方法论]]（新增"去哪儿 Skills Gateway"小节——失效点≈Gotchas 互补路径、种子上下文≈上下文工程）。
- INDEX：新增"企业级 AI Coding 实践（大厂平台）"源分组（腾讯落地规范自"方法论对比"移入）；pending 补 L0-L5 分级 / 双估时 / T-M-C 与出码率 Git Blame 法（均 1 源）。
- 待观察：去哪儿成果数字为大会自述、文章为二手解读，后续若有第三方/官方一手材料需回填核对。

## [2026-08-25] ingest | 腾讯 Agent 与 Skill 测评方案

- [[腾讯 Agent 与 Skill 测评方案]] — [[腾讯程序员]]（martinskxu，TEG 云架构平台部网关测试团队，2026-06-16）：三类评分器（确定性 > Rubric LLM-as-Judge > 人工，"能用代码判断的绝不用模型"）+ 五维度（功能/过程/效率/鲁棒/体验）+ 用例四场景（触发/核心逻辑/产物/异常容错，负向触发用例防过度触发）+ 基线机制（先跑→人工确认→即预期）+ 能力/回归双轨与用例"毕业" + pass^k vs pass@k + TPerf 实战（LCS 步骤对齐、9 场景 30+ 用例 × 7 模型、辅助分析类主动上调 0 容忍）。
- 新建概念 [[Agent 测评 Eval]]：Eval 主题已跨 3 源（Perplexity 四层 Eval + 腾讯工程化 + Anthropic 博客引用），从 [[Skills 设计方法论]] 第 5 柱独立成页；含两套框架映射（加载 Eval≈触发用例、跨模型 Eval≈模型对比报告）。
- 更新：[[腾讯程序员]]（第 2 篇源，构成"建 harness + 验 harness"闭环）、[[Skills 设计方法论]]（§5 指向新概念页）、[[Harness Engineering]]（补腾讯测评为支柱五证据）。
- INDEX：概念区 +1，"企业级 AI Coding 实践"分组 +1 源。

## [2026-08-25] ingest | Cole Medin 黑灯工厂实验

- [[Cole Medin 黑灯工厂实验]] — [[老丁888]] 对 Cole Medin 视频的解读（2026-08-19）：AI Dark Factory = "spec 进、可上线代码出"的自主仓库（第四级自主，人留两盏灯：提 issue + 发版）；三层架构（Archon harness 管道 / Claude Code / Sonnet+Haiku，"模型路由是成本最低的杠杆"）；刻意做笨的 bash 调度器（GitHub 标签即状态机，曾因 LLM 调度"幻听"改纯 bash）；三份治理文件宪法（工厂不可自改 + 禁区一碰即打回）；**holdout 盲测**（builder 不知测试、validator 不知计划）+ 蓝绿部署 + 每周自愈巡检。
- 新建：实体 [[Cole Medin]]、[[老丁888]]；概念 [[AI Coding 自主度分级]]（pending 升级——去哪儿 L0-L5 + Dan Shapiro 五级双源，L3 分水岭共识："第三级才是多数人该待的地方"）。
- 更新 4 概念页：[[Harness Engineering]]（第 5 源，个人/小团队样本小节）、[[Agent 测评 Eval]]（holdout=第三种防自欺机制，与腾讯基线互补：基线防漂移、holdout 防挟持验收标准）、[[子代理驱动开发]]（builder/validator 信息隔离与 Superpowers v6.0 怀疑论 reviewer 独立同构）、[[HITL 与 AFK]]（AFK 极端形态 + 两盏灯 + 判断类动作事前置入宪法）。
- INDEX：实体 +2、概念 +1、源 +1（新分组"AI Coding 自主化"）；pending 移除 L0-L5（已建页），补 Dan Shapiro / Archon / StrongDM holdout。
- 待观察：调度器脚本未开源（只给行为规格），"完全开源"程度打折；"一行产出代码没看过"限于可机器验证类型，勿普适化。

## [2026-08-25] ingest | TAB 大仓 Harness 工程化实战

- [[TAB 大仓 Harness 工程化实战]] — [[腾讯程序员]]（fitchzheng/leoshli，TAB A/B 实验平台，2026-07-07）：大仓（30+ 微服务）Harness 完整复盘。六层资产（Rule 几乎没写——"能判定的就别留在 Rule 里"；Skill=注意力管理而非能力增强；4 个认知 Agent"能写成 bash 的就别让 Agent 跑"；13 棒接力；7 道门禁；5 个 MCP 读多写少）；最重纪律"**下游不可改上游产物**"；**基线对比反作弊**（B−A 新增失败才阻断，剥夺"历史问题"解释权）+ 软门禁留伤疤；集成测试前置（打回 1.8→0.4）；"**模型对自己生成的东西天然没有否决欲望**"（HITL 的模型层证据）；Team Mode 撞墙复盘（"复杂度自给自足"陷阱 + YAGNI）；"团队要对齐的必须落到仓库，Memory 只放个人偏好"。数字：50+ 需求、端到端 30-75 分钟、人工介入 2-3 次。
- 更新 6 概念页：[[Harness Engineering]]（第 6 源，大仓实操小节）、[[Agent 测评 Eval]]（基线对比反作弊变体 + 软门禁伤疤）、[[子代理驱动开发]]（TAB 4-Agent 契约 + 下游不可改上游 + "方案一致性"防解释性执行）、[[HITL 与 AFK]]（无否决欲望模型层证据 + 关卡工程化度量）、[[Skills 设计方法论]]（"注意力管理"同源表述）、[[LLM Wiki]]（新增 §8 落盘优先于个人记忆——Harness 视角跨域印证）。
- [[腾讯程序员]] 实体更新为三部曲（建 + 验 + 稳）。
- INDEX：源 +1（企业级实践组）、腾讯程序员描述更新、pending 补"复杂度自给自足/YAGNI"与"仓库文档 vs Memory"。
- 注记：本篇与 [[Harness Engineering 团队落地规范]] 在 Rule 上的程度差异（起步规则共识 vs 成熟态规则脚本化）已在矛盾区标注。

## [2026-08-26] ingest | 批量 3 源：Harness 理论科普 + 十二大工程框架 + 产品经理 AI 工作流

处理 8/25–8/26 新收录的 3 篇文章：

**Harness 理论科普（1 源，新实体 [[ConardLi]]）**：
- [[Harness Engineering 三次演进与头部实践]] — code秘密花园，2026-04-03（正是腾讯落地文引用的 Harness 详解文）：Prompt→Context→Harness 三次重心迁移（表达/信息/过程问题）+ 六层拆解 + 头部实践（Anthropic Context Reset vs Compaction、generator+evaluator 分离「生产与验收必须分离」；OpenAI「修复方案从来不是更努力，而是缺了什么结构性的能力」；LangChain 不动模型仅改 Harness 榜单 52.8→66.5）。
- 更新 [[Harness Engineering]]（第 7 源）：新增「演进视角」段与头部实践段；Context Reset 与 [[smart zone]]/[[Matt Pocock 会话边界五问决策树]] 同题异构、为 compact 垫底提供头部公司印证；自评失真 = [[Agent 测评 Eval]]/holdout 盲测的理论根基（推论）。

**企业 Agent 十二大工程（1 源，新实体 [[白墨]]）**：
- [[企业 Agent 落地十二大工程框架]] — 白哥Agent落地局，2026-07-30，6.6 万字上篇（约 3.5 万字）：六纵（Prompt/Context/Skill/Agent/Harness/Loop）+ 六横（Data/Ontology/Process/Evaluation/Safety-Governance/Product-UX）；独创 Process/Agent/Harness 三方边界、Action 语义模型、Data Agent 三层次与事实-推测-行动信任链、「多 Agent 不默认优于单 Agent」。
- 更新 [[Harness Engineering]]（第 8 源）：新增白墨十二工程小节；矛盾区升级为**定义谱系四方口径**（ConardLi 最宽 Harness=Agent-Model → 腾讯 6 支柱 → 去哪儿过程控制 → 白墨最窄运行控制面）。
- 更新 [[智能问数平台]]（第 2 源印证）：Data Agent 三层次、零信任权限拦截、指标口径与五层架构高度同构；新建概念 [[Data Agent]]（双源达标，含信任链 + 底座 + 与 NL2SQL 双源一致否定）。

**产品经理 AI 工作流（1 源，新实体 [[SinKris]]）**：
- [[产品经理的 AI 工作流]] — Kris产品成长之路，2026-08-03：8 个工作流（需求/竞品/PRD/数据/项目管理/跨部门/个人成长/创意，工具+Prompt+SOP）+ 工作流=标准化+AI 自动化+可复用模板 + 80/20 分工公式 + Obsidian 点名作模板库载体。
- 关联标注：个人 Prompt 模板库 ≈ 个人版 Skills（推论）；80/20 与 [[HITL 与 AFK]] 判断类人在环呼应；INDEX 新开「个人 AI 工作流（产品管理）」分组。

**INDEX**：实体 +3（ConardLi/白墨/SinKris）、概念 +1（[[Data Agent]]）、源 +3；「企业级 AI Coding 实践（大厂平台）」组改名「企业级 AI Coding 与 Agent 落地实践」收纳非大厂方法论源；pending 更新语义层（第二次出现）、补 Action 语义模型/成熟度 L1-L5/个人模板库≈个人 Skills。
- 待观察：白墨下篇（Evaluation/Safety/Process/Product-UX + L1-L5 成熟度模型）发布后补全十二工程全貌。

## [2026-08-25] query | 「为什么配置 MCP 而非直接给 agent 数据库凭证」综合 → 沉淀 [[MCP 与直连凭证对比]]

用户读 [[Harness Engineering 团队落地规范]] §5.1 时提问：有数据库地址/用户名/密码，agent 走 Bash+CLI 就能访问，为何还要 MCP？综合作答并沉淀 synthesis：MCP 解决的不是「能不能访问」而是「受控访问」——①权限结构性收窄（工具面无写操作 + readonly_user，呼应 TAB「能判定的就别留在 Rule 里」）；②凭证不进模型上下文（${DB_PASSWORD} 只在 server 进程 env，对照源内「外部模型外传上下文」警告）；③工具面即文档 + 结构化输出省 token；④团队远程部署统一复用。何时不用：个人本地/一次性查询（源 §5.1.1 自认）。confidence: medium（凭证暴露面等部分为通用工程常识，非源内直接论述，页内已标注推论）。

## [2026-09-09] ingest | 批量 14 源：老谭教学系列 12 篇 + DeepSeek Harness 拆解 + Agent UI

上次处理（08-26）以来新增 14 篇，分两组：

**A 组：2 篇技术深拆（新作者）**
- [[DeepSeek Harness 源码拆解]] — 翟星人的实验室，2026-08-14：DSH 是 MIT 开源的开发者级 Agent 运行时底座（Everything is a plugin，[[Cordis]] 装配）；Service/Event/Effect/Scope 四概念、Profile/Bundle/Patch/Preset 四层装配、Session Event Log（事实流+多投影，反对 messages 当数据库）、子 Agent Spawn/Fork/composeFrom、自修改 cordis_define；横评 Codex/Claude Code/Pi/LangChain（向上封装 vs 向下拆开）+ 五个缺口（Benchmark 缺口与腾讯基线反作弊互证）。新建实体 [[DeepSeek Harness]]、[[Cordis]]、[[翟星人的实验室]]。
- [[从 Chat UI 到 Agent UI]] — 炼丹上岸，2026-08-30：Agent UI 设计对象从 Message 扩展为八类对象；Message Part 七种 part、Run 状态机（取消订阅≠取消执行）、HITL 真暂停、Artifact 工作区、Snapshot+Event Log+Live Stream、统一事件 Reducer + AG-UI、受控 Generative UI；五层前端架构 + 四阶段演进。新建实体 [[炼丹上岸]]。

**B 组：12 篇超级奶爸老谭 mattpocock 教学系列（08-03～08-18）**
- 主系列 7 篇（01 入门 → 02 设计灵魂 → 03 词典 → 04 访谈 → 05 流水线 → 06 反馈 → 07 组装）+ 专题 5 篇（wayfinder/loop-me/batch-grill-me/teach/improve-codebase-architecture），EShop 案例贯穿，每篇「开场故事 → 机制拆解 → 何时不要用 → 回顾」。新建实体 [[超级奶爸老谭]]。
- wiki 首个深度源补齐 4 个 skill：loop-me（in-progress 草稿，workflow spec + Push right）、teach（正式技能，七件套工作区 + 合意困难）、improve-codebase-architecture（只读体检三步 + 深浅模块/删除测试）、batch-grill-me（并入 v1.2 grilling 前快照）。
- **矛盾标注（系统性）**：老谭 12 篇全部是 v1.1 时代快照——无一提及 v1.2（wizard/wait-what/round-by-round 均无），且把 grill-me 当 coding 访谈主入口、未反映「grill-with-docs 取代 grill-me」官方演进；batch-grill-me 描述的机制与 v1.2 round-based frontier（PR #593）几乎同构，判定为并入前草稿快照。已在 [[mattpocock skills]]、[[grill-me 实战指南]]、[[还在用 grill-with-docs]]、[[diagnosing-bugs 与 tdd]]、[[Matt Skills v1.2 grilling 重构]]、[[Matt Pocock skills Wayfinder 官方文档]] 六个页面加注记，不推翻既有 v1.2 结论。

**跨源沉淀**：
- 新建概念 [[Session Event Log]]（双源达标）：DSH 后端「事实流+多投影」与 Agent UI 前端「Snapshot+Event Log+Live Stream」三组同构映射（含 presentCall↔Tool Renderer、Approval 链↔审批卡片）。
- 更新 [[Harness Engineering]]（第 9 源）：新增「开源运行时底座（DeepSeek Harness）」小节；定义谱系外补充框架级实现样本。
- 更新 [[mattpocock skills]] 概念页（第 24 源后）：新增「委托/教学/体检/批量访谈类」节；矛盾区加老谭系列错位注记。
- 更新 [[Matt Pocock]] 时间线（v1.2 + 中文教学化传播）、[[mattpocock skills 推荐工作流速查]]（扩展场景四类活）。

**INDEX**：实体 +5（超级奶爸老谭/翟星人的实验室/炼丹上岸/DeepSeek Harness/Cordis）、概念 +1（[[Session Event Log]]）、源 +14（新开「老谭教学系列」组与「Agent 运行时与 UI」组）；pending 补 Agent UI 设计（单源待第二源）、设计树/frontier/round、深浅模块/删除测试。
- 待观察：老谭系列宣称「主系列第 8 课」存在但本 wiki 未收录；翟星人预告 Cordis 下篇专题。

## [2026-09-09] update | 老谭系列口径修正：主系列为 01-07 共 7 课，系列 12 篇全部收录

用户确认：老谭系列实际发布口径为主系列 01-07（7 课）+ 专题 5 篇 = 12 篇，已全部收录，无「第 8 课」。修正上一条 ingest 条目中「待观察：第 8 课未收录」的说法——loop-me/batch-grill-me/teach 三篇文内自称「主系列 8 课」系作者笔误或未兑现计划，按 7 课计。已同步修正 [[超级奶爸老谭]]、[[mattpocock skills 八环节组装]]、[[loop-me 循环委托自动化]]、[[improve-codebase-architecture 代码库体检]]、INDEX 五处的口径注。另：用户新收文件「…一次探一步 1.md」经 diff 确认为 wayfinder 专题的重复收藏（仅 scene 参数与一张图片 watermark 差异），raw/ 只读未动，已建议用户删除重复件。

## [2026-09-10] ingest | 批量 15 源：老谭 Superpowers 教学系列（幸福账本 00–14，连载中）

处理 09-09 下午至 09-10 上午新收录的 15 篇——[[超级奶爸老谭]] 第二套系列（2026-08-21 开篇、日更 08:05），用「幸福账本」家庭记账全栈项目（Java 21 + Spring Boot 3 + H2 / Vue 3 + Element Plus）逐篇教 [[Superpowers]] 技能树。**同一作者先后系统教学"路线之争"的两条路线**（mattpocock 12 篇 → Superpowers 15+ 篇），本身是两路线可共存的作者级注脚。收录 00–14，系列规划 20+ 篇仍在连载（预告 root-cause-tracing 等 7+ 篇）。

**新建 15 个 source 页**：
- 三张入场券：[[Superpowers brainstorming 需求访谈]]（苏格拉底一次一问，四轮聊成规格）、[[Superpowers writing-plans 施工图]]（粒度双向标准）、[[Superpowers executing-plans 一步一确认]]（分批+确认+回滚）
- 测试三篇：[[Superpowers TDD 红绿循环]]（哨兵测试 novel 装置）、[[Superpowers condition-based-waiting 条件等待]]（Awaitility + 前端轮询，**wiki 首收该技能**）、[[Superpowers testing-anti-patterns 测试反模式]]（四坑）
- 基建两篇：[[幸福账本 REST API 基建]]（record/实体分层 + X-User-Id 账本切换）、[[幸福账本 Vue 前端起步]]（前后端第一次握手）
- 并行四篇：[[Superpowers using-git-worktrees 工作树隔离]]（"隔离靠目录不靠自觉"）、[[Superpowers finishing-a-development-branch 分支收尾]]（合/修/弃 + 主干再测）、[[Superpowers dispatching-parallel-agents 并行派发]]（契约先行）、[[Superpowers subagent-driven-development AI 项目经理]]（AI PM 流水线）
- 调试一篇：[[Superpowers systematic-debugging 四阶段调试]]（复现→假设→验证→根因，"报表多 57 元"案例）
- 开篇/安装：[[Superpowers 开篇 幸福账本]]、[[Superpowers 安装与技能全景]]

**概念/实体更新（5 页）**：
- [[Superpowers]]（第 9–23 源）：新增「中文教学化传播：老谭幸福账本系列」小节；⚠️ 区新增**技能数口径** 14（v5.0.7，2026-04）→ 18（2026-08 仓库：testing +2、debugging +2）——版本演进非矛盾，与 mattpocock 18→24 演进同构，引用须带时点。
- [[超级奶爸老谭]]：第二系列登记 + 时间线（两系列仅隔 3 天无缝切换）；注意本系列为 2026-08/09 当前口径（非上一系列的 v1.1 滞后快照）。
- [[子代理驱动开发]]（第 8 源）：新增「教学化口径」小节——老谭版两级审查 = 子代理自审 + 主会话复审，与 v5.x 两轮独立 review / v6.0 单 task-reviewer **三口径并排**（⚠️ 区同步）；"项目经理也在你的上下文里干活"与 [[smart zone]] 呼应。
- [[Superpowers 与 mattpocock skills 路线对照]]：结论新增第 6 条「访谈节奏分化」——v1.2 grilling 转 round-by-round 批量轮次（13 问 3 轮）vs brainstorming 坚持一次一问，两条路线在访谈节奏上正式分道扬镳。
- 小更新：[[diagnosing-bugs 与 tdd]]（补 Superpowers 4 阶段教学化展开指引，印证"不能无条件互换"）、[[2026 年再看 Superpowers：grill-me 场景选型]]（互链）。

**INDEX**：新开「Superpowers — 老谭教学系列·幸福账本」源分组（15 条）；老谭实体行更新；pending 补系列待收录篇目（root-cause-tracing / verification-before-completion / defense-in-depth / requesting-code-review / receiving-code-review / writing-skills / 组合实战 2 篇）。

**待观察**：① 系列日更中（14 篇发于今日 08:05），后续篇目收录时补 source 页；② 老谭版两级审查对应官方哪个版本，待 root-cause-tracing / requesting-code-review 篇收录后交叉核对；③ brainstorming 教学版未提"2-3 方案比较"环节，官方 SKILL 原文核对留待 lint。

## [2026-09-11] ingest | 老谭 Superpowers 系列 15：root-cause-tracing 根因追踪

- [[Superpowers root-cause-tracing 根因追踪]] — 「差一分钱」案例（报表 5793.40 vs 流水 5793.41，"账不平就是系统在说谎"）：表象（分类合计差 0.01）→ 因果链四跳（报表→接口→聚合 BigDecimal.add 没毛病→再上追一层 EntryRepository 的 `doubleValue()` 累加）→ 修根子（全程 BigDecimal）+ 双防线（MoneyRules 不变式 + 一正一反哨兵测试）。**根因判据**："修掉后能否解释之前所有相关症状"；与 systematic-debugging 分工 = **流程 vs 因果链定位**（"症状只是根因在水面的影子"）。
- 更新 4 页：[[Superpowers]]/[[超级奶爸老谭]]（系列收录至 00–15 共 16 篇）、[[Superpowers systematic-debugging 四阶段调试]]（前后篇互链，"与 root-cause-tracing 分工待核对"事项关闭）、[[Superpowers TDD 红绿循环]]（哨兵测试跨篇复用注记）；[[diagnosing-bugs 与 tdd]] 补根因判据与 falsifiable 假设的跨路线呼应。
- INDEX：系列组 +1，pending 移除 root-cause-tracing。
- 注记（推论）：哨兵测试恒绿、技术上不会失败报警——"防线"价值在文档/红线宣示层面，机器可判定的防线是 MoneyRules 与类型纪律；已在 source 页 ⚠️ 区标注，勿将其理解为会红的回归测试。
- 待观察：16 verification-before-completion 已预告（老王"AI 绕过第一道防线呢？"埋线），defense-in-depth 估计紧随其后。
## [2026-09-15] ingest | 老谭 Superpowers 系列 16：verification-before-completion 验证再收工

- [[Superpowers verification-before-completion 验证再收工]] — 改完 (implemented) ≠ 完成 (completed)：完成 = 验证过 + 有证据，证据只能是测试输出/截图/接口返回，不能是「我觉得应该没问题」。三层验证（代码层测试绿 / 行为层真实环境走一遍 / 验收对照现象非代码）+ 四步收工流程（跑前后端测试 → 浏览器真走一遍 → 验收对照 → 防回归测试钉死才提交）+ 三问清单。案例：预算标红 `null > 2000` 为 false，spent 为空按 0。**三铁律至此全部技能化展开**（①TDD 05 篇 / ②systematic-debugging 14 篇 / ③本篇）。
- 更新 4 页：[[Superpowers]]（三铁律补篇目注记 + 系列收录至 00–16 共 17 篇）、[[超级奶爸老谭]]（系列结构调试三篇 + 时间线）、[[Superpowers root-cause-tracing 根因追踪]]（debugging 类进度更新 + 老王两问归属理顺：16/17 篇分别接住）、INDEX pending 移除本技能。
- 同构注记：与 v6.0 reviewer "Do Not Trust the Report"（[[Superpowers 6.0 reviewer 只读重写]]）同一「不信任口头完成声明」精神在收工自检侧的投影。
- INDEX pending 新增观察项：「验证即证据（verification as evidence）」概念页——TDD 红绿 / finishing 合完再测 / reviewer 不信报告 / 本篇三问多源出现，待巩固后独立建页。
- 待观察：17 defense-in-depth 已预告（「防线本来就是用来一层一层叠的」），估计紧随其后。
## [2026-10-22] ingest | 老谭 Superpowers 系列 17–23+总结篇：系列完结批量收录（8 源）

- [[Superpowers defense-in-depth 纵深防御]] — 一道锁→纵深四道锁（前端表单 / @Valid / MoneyRules / 防回归测试），每层独立不赌单点；**debugging 类四技能收齐**（18 技能口径调试类全录）。
- [[Superpowers requesting-code-review 主动求审]] — 交"能审的活"：自看 diff → 自跑测试（不绿别求审）→ 自查清单 → 自写说明，"重点想请你审哪"最高效。
- [[Superpowers receiving-code-review 接受审查]] — 四步接意见：别辩护 → 先理解 → 逐条回应（改/讨论/不改）→ 改完给证据；"意见被好好拒绝，好过被假装接受"。MoneyRules.check 来历回填（15 篇 assertValid → 19 篇意见产物、17 篇已用，代码时间线自洽）。
- [[Superpowers writing-skills 规矩固化成技能]] — 元技能：description 管触发 / 规矩可执行（"注意精度"是废话、"用 compareTo"是指令）/ 踩过的坑写进去 / 验证 AI 真会用；"记性靠不住，文件靠得住"。
- [[Superpowers 组合实战1 从一句话到上线]] — 乐乐零花钱全流水线贯通（brainstorm→plans→TDD→API→前端→verification→上线）；诚实边界"上线≠部署"（H2 教学 vs 生产工程）。
- [[Superpowers 组合实战2 线上大排查]] — 三调试技能协同：先 curl 分而治之 → 三假设排除 → 根因 watch 漏 ownerId 依赖（"数据不对"实为"没刷新"）；文中防回归测试为示意骨架（作者自注）。
- [[Superpowers 结语 AI 技能树]] — 三心得：把运气变流程（验证即证据主题再 +1）/ 完整的项目是最好的老师 / 规矩靠固化不靠记性；七层技能树总图。
- [[Superpowers 全系列速查合集]] — 收官"不是随缘，是走流程"；⚠️ 抓取件疑不全（速查表多分类空缺、21 篇归类错置），待 lint 补抓核对。
- 新建 synthesis：[[writing-skills 跨路线对照]]（confidence: medium）— mattpocock 八词诊断 vs Superpowers writing-skills 四共识（description 触发器 / no-op 测试 / 坑位资产 / 写完验证）+ 分歧在姿态（减法手术 vs 加法固化）。
- 更新 3 页：[[Superpowers]]（sources +8、系列完结 25 源、debugging 类收齐）、[[超级奶爸老谭]]（系列结构全重排：调试四篇+审查两篇+元技能+组合实战+结语、时间线、待收录清零）、[[Superpowers verification-before-completion 验证再收工]]（debugging 类收齐注记）。
- INDEX：系列组标题改"已完结 24+1 篇" + 8 行；synthesis 组 +1；Pending 待收录条目清零注记；"验证即证据"观察项源数更新至 6+。
- 过程注记：一次 INDEX 编辑失误（"验证即证据"行首被误并入上一行），当场发现并修复，未留残余。
- 待办：总结篇 defuddle 补抓核对速查表原文。
