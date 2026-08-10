---
title: "Anthropic 和 Perplexity 公开了内部 Skills 设计方法论：9 类分类、3 层成本、4 层 Eval"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491095&idx=1&sn=5cb086d7eee87743768d68025a5ae7da&chksm=cf43ab41f8342257a62b2064fcc8e503082a5250bc69a68c4325c90d491717ca24f4b053600b&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-02
description:
tags:
---
运维有术 术哥无界 *2026年6月12日 08:30*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *137* 篇，AI 编程最佳实战「2026」系列第 *41* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！
> 
> **Talk is cheap, let's explore。无界探索，有术而行。**

![[Image 38.webp|封面图]]

封面图

*图 1：Agent Skills 设计方法论全景图*

> **说明** ：本文内容基于 Anthropic 官方博客（Thariq Shihipar，Claude Code 团队）和 Perplexity Research 官方技术文章分析整理而成，源码级细节来自已公开的设计文档和工程实践分享。 **文中的配置模板和参数建议仅供参考，实际效果请以你的业务数据和环境测试结果为准。** 如果有实际使用经验，欢迎在评论区分享交流。

翻了一遍 Anthropic 和 Perplexity 的官方技术博客，有一个共识反复出现： **Skills 不是 prompt，也不是插件——它是上下文工程（Context Engineering）** 。

这不是文字游戏。Perplexity 的原话说得很直接： **如果你像写代码一样写 Skill，你会失败。** Anthropic 的 Claude Code 团队工程师 Thariq Shihipar 则专门纠正了一个常见误解——Skills 不是 **只不过是个 markdown 文件** ，而是包含脚本、资源文件、数据的文件夹，智能体可以发现、探索和使用这些内容。

两篇文章发布时间差两个月（Anthropic 2026 年 3 月，Perplexity 2026 年 5 月），面向的场景也不同——一个是开发者工具的扩展机制，一个是终端用户的 Agent 产品能力模块。但两边的设计理念几乎一模一样。

下面把两家的做法掰开揉碎说一遍。

## 1\. 认知转变：为什么 Skills 不是 Prompt

### 从 Prompt Engineering 到 Context Engineering

传统 Prompt Engineering 的核心是优化单次指令，引导模型给出正确输出。Context Engineering 关注的完全是另一个层面：在模型做决策之前，如何组织好所有相关信息——包括结构、优先级、加载时机。

打个比方：Prompt Engineering 是向专家提一个精准的问题；Context Engineering 是在专家来之前，把所有相关报告分好类、标好优先级、整齐地摆在桌上。问题问得好当然有用，但如果桌上堆着一堆无关材料、关键数据又找不到，再好的问题也白搭。

把 Skill 当 prompt 写，大概率会踩这些坑：

- 一次性把所有信息写在一个文件里
- 用描述性语言写功能说明，而不是触发条件
- 让模型自己写 Skills
![[Image 39.webp|对比图]]

对比图

*图 2：Prompt Engineering vs Context Engineering*

第四点尤其值得注意。Perplexity 的研究表明，LLM 自己写的 Skills 平均没有收益——他们的原话是 **模型无法可靠地编写它们受益的程序性知识** 。高质量的 Skill 必须由人类基于实战经验编写，再通过 Eval 套件持续验证。

### Python 之禅在 Skill 领域大面积失效

Perplexity 团队做了一个很扎心的对比。他们发现 PEP20 里至少一半的智慧，在写 Skill 时是错误的或误导性的：

| Python 之禅 | Skill 之禅 |
| --- | --- |
| 简单优于复杂 | Skill 是文件夹，不是文件。复杂度就是功能 |
| 显式优于隐式 | 激活是隐式模式匹配，采用渐进式披露 |
| 稀疏优于密集 | 上下文昂贵，每 token 要做到信号密度上限 |
| 特殊情况不足以打破规则 | Gotchas（踩坑点）就是特殊情况，而且是核心价值内容 |
| 如果实现容易解释，可能是个好主意 | 如果容易解释，模型已经知道了，删掉它 |

最后一条切中要害。很多人写 Skill 的第一反应是写教程式说明文档，但模型已经知道的东西写进去只会浪费上下文窗口。Anthropic 的第一条最佳实践也是同一个意思： **不要说显而易见的事** ，把重点放在能打破模型常规思维模式的信息上。

### 什么时候需要 Skill？什么时候不需要

两家的判断标准很明确。

**需要 Skill 的情况** ：

- 模型没有特殊上下文就会犯错
- 需要跨运行保持行为一致性
- 知识是持久的但不在训练数据中
- 涉及企业特定工作流或团队约定

**不需要 Skill 的情况** ：

- 模型本来就能做对的事情
- 信息变化速度超过维护速度
- 和系统 prompt 重复的指令

Perplexity 的设计 Skills 是个很好的例子。这些 Skills 由设计负责人 Henry Modisett 编写，指定用哪些字体、不用哪些字体、不同字体的 **感觉** 。这些判断是纯主观的品味问题，模型不可能从训练数据中学到，但用户能明显感受到输出质量的差异。

Anthropic 的 `frontend-design` Skill 也是类似思路。它不是教模型怎么写 CSS，而是通过与用户反复迭代来改进模型的设计品味，专门避免典型套路（比如过度使用 Inter 字体和紫色渐变）。Thariq Shihipar 提到这个 Skill 由一位工程师专门打磨——说明品味类 Skills 需要持续的人类投入。

## 2\. 三层成本模型：Token 经济学的工程实践

![[Image 40.webp|架构图]]

架构图

*图 3：三层上下文成本模型*

Perplexity 提出的三层上下文成本模型，是我见过的对 Skill 架构设计指导性最强的框架。它把 Skill 的上下文成本分成三层：

| 层级 | 加载内容 | Token 预算 | 何时付费 |
| --- | --- | --- | --- |
| **Index** | 每个 Skill 的 `name` + `description` | 约 100 tokens/Skill | 每个 session，每个用户，始终付费 |
| **Load** | 完整 `SKILL.md` body | 约 5,000 tokens | 加载时付费，后续每个对话轮次累积 |
| **Runtime** | `scripts/`  、 `references/` 、 `assets/` 、子技能 | 无上限 | 仅当 Agent 实际读取时付费 |

核心理念是六个字： **按需加载，分级付费** 。

### Index 层：寸土寸金

每个用户每次对话，所有 Skills 的 `name` 和 `description` 都会被加载。你多写一句废话，全世界每个用户每次对话都在替你买单。Perplexity 给了一个直白的说法： **每个 Skill 都是税（Every Skill is a Tax）** 。

对 Skill 中的每句话，都要问一个问题： **没有这条指令，Agent 会做错吗？** 如果不会，就不能留在那里。

### Load 层：预算有限，精打细算

Load 层稍微宽松，但一次对话可能同时加载 3-5 个 Skill，成本叠加。5,000 tokens 的预算意味着要在信息密度和完整性之间做权衡。跳过显而易见的内容，聚焦 gotchas 和负面示例，把重内容移到 spoke 文件中。

### Runtime 层：按需自由

脚本、详细文档、模板这些重内容放在 `scripts/` 、 `references/` 、 `assets/` 目录中，只有 Agent 实际读取时才消耗 token。这就是 **渐进式披露** （Progressive Disclosure）的核心思路——也是 Agent Skills 开放标准的三阶段加载模型：Discovery → Activation → Execution。

### Hub-and-Spoke 目录结构

三层成本模型对应的标准目录结构叫 Hub-and-Spoke：

```
my-skill/
├── SKILL.md          # frontmatter + 指令（hub - 中枢）
├── scripts/          # Agent 运行的代码，不需要重新发明的
├── references/       # 重文档，条件加载
├── assets/           # 模板、schema 和数据
└── config.json       # 首次用户设置
```

`SKILL.md` 是中枢，包含元数据和核心指令。 `scripts/` 、 `references/` 、 `assets/` 是分支，Agent 按需读取。你可以把 token 预算精准分配给不同层级的信息。

Anthropic 的最佳实践也强调同样的思路：详细函数签名拆到 `references/` 目录，输出模板放 `assets/` ，核心指令保持精简。两家公司的目录结构虽然表述不同，底层逻辑完全一致。

### 信息组织的边界：1,945 节国税法的教训

Perplexity 用一个具体案例说明了信息组织的极限。他们测试美国所得税 Skill 时，把全部 **1,945 节美国国税法** 放在单个文件夹中。结果性能比不加载 Skill 还糟糕。

解决方案是 **多级层次结构** 。当 Skill 需要覆盖 300 个主题时，让模型在 300 个中做可靠选择，目前还是未解决的挑战。但分成 20 个主题领域，模型先选领域再在 15 个中选择，准确率就上来了。Perplexity 的美国所得税 Skill 使用了三级主题嵌套。

这个案例说明了一个重要原则： **Skill 的价值不取决于包含多少信息，而取决于模型能否在正确时机找到正确信息** 。再好的知识库，索引做得烂，效果还不如没有。

你在项目中用过类似的分层信息组织方案吗？欢迎在评论区聊聊。

## 3\. 实战方法论：从分类体系到 5 步流程

![[Image 41.webp|分类图]]

分类图

*图 4：Anthropic 9 类 Skills 分类体系*

### Anthropic 的 9 类分类体系

Anthropic 内部活跃使用的 Claude Code Skills 已有几百个。梳理后归为 9 大类别：

| 类别 | 核心用途 | 典型示例 |
| --- | --- | --- |
| 库与 API 参考 | 帮助正确使用某个库/CLI/SDK | billing-lib（计费库踩坑点） |
| 产品验证 | 测试/验证代码是否正常工作 | signup-flow-driver（无头浏览器跑注册全流程） |
| 数据获取与分析 | 连接数据和监控体系 | funnel-query（转化漏斗事件 JOIN） |
| 业务流程与团队自动化 | 把重复性工作流自动化为一条命令 | standup-post（自动站会汇报） |
| 代码脚手架与模板 | 为特定功能生成框架样板代码 | new-workflow（搭建新服务） |
| 代码质量与审查 | 执行代码质量标准并辅助审查 | adversarial-review（子智能体挑刺） |
| CI/CD 与部署 | 拉取、推送和部署代码 | babysit-pr（监控 PR→重试 CI→自动合并） |
| 运维手册 | 多工具排查流程→结构化报告 | \*-debugging（现象→工具→查询模式） |
| 基础设施运维 | 日常维护和运维操作 | \*-orphans（孤立资源清理） |

9 个类别覆盖了从开发到运维的完整生命周期。几个有意思的案例值得单独说说。

`babysit-pr` Skill 展示了 Skills 编排复杂多步工作流的能力：监控 PR 状态、重试不稳定的 CI、解决合并冲突、启用自动合并，整个过程不需要人工干预。它不是简单的命令别名，而是一个有状态的工作流编排。

`/careful` Skill 通过 `PreToolUse` 钩子拦截 `rm -rf` 、 `DROP TABLE` 、 `force-push` 等危险操作，只在需要时激活。这是 Skills 与钩子系统结合的典型用法，也说明 Skills 不只是文本知识——它们可以包含行为逻辑。

`adversarial-review` Skill 的设计思路很巧妙：用一个全新的子智能体来挑刺，而不是让同一个模型既写代码又审代码。这避免了 **自己审自己** 的盲区。

### Perplexity 的 5 步开发流程

Perplexity 的开发流程有一个反直觉的特点： **第一步不是写 Skill，而是写测试** 。

| 步骤 | 核心要点 |
| --- | --- |
| **Step 0：写 Evals**  （最先做） | 从真实用户查询、已知失败、邻域混淆中采样。负面示例比正面示例更重要 |
| **Step 1：写 Description**  （公认最难） | 路由触发器，不是功能文档。好的 description 以 "Load when..." 开头，50 字以内 |
| **Step 2：写 Body** | 跳过显而易见的内容；聚焦 gotchas 和负面示例；重内容移到 spoke 文件 |
| **Step 3：搭建层次结构** | scripts/（确定性逻辑）、references/（条件加载）、assets/（输出模板） |
| **Step 4：迭代与发布** | 在分支上大量迭代，搭建 hero query 集，运行大量 evals |

Step 0 把 Evals 放在写 Skill 之前，这和 TDD 的思路一脉相承。先定义成功标准，再写实现。Perplexity 专门强调，负面示例可能比正面示例更重要——知道 Skill 在什么情况下不该触发，和知道它应该触发一样关键。

Step 1 是公认最难的一步。 **Description 不是功能描述，而是路由触发器** 。Anthropic 的 Thariq Shihipar 也强调了同样的观点： `description` 字段描述的是 **何时该触发** 这个 Skill，不是功能摘要。好的 description 读起来更像 if-then 条件。比如 `babysit-pr` 的 description 可能是 `Load when the user asks to monitor, babysit, or manage a pull request` ——用的是用户真实说法，而不是功能定义。

这个区别看着细微，但实际影响很大。如果你的 description 写的是 `This skill helps with PR management` ，模型很难判断当前对话是否需要它。但如果写的是 `Load when the user mentions babysitting a PR or monitoring CI status` ，触发准确率会高得多。

## 4\. 评估与维护：Gotchas 飞轮与四层 Eval 套件

![[Image 42.webp|流程图]]

流程图

*图 5：Gotchas 飞轮——持续迭代的核心机制*

### Gotchas 飞轮：踩坑点才是核心资产

两家公司都有一个反常识的共识： **Skill 中价值含量最高的内容不是正面教程，而是踩坑点（Gotchas）** 。

Anthropic 的做法是：每次 Agent 犯错时，在 Skill 的 Gotchas 章节追加一条。Thariq Shihipar 把这列为 8 条最佳实践的第 2 条，并明确说这是高价值内容。

Perplexity 把这个机制系统化为 **Gotchas 飞轮** ：

- Agent 在某事上失败 → 添加 gotcha
- Agent 不该加载时加载了 → 收紧 description，添加负面 evals
- Agent 该加载时没加载 → 添加关键词和正面 evals
- 系统 prompt 变化 → 检查竞争或重复

有一说一，这个机制听着简单，但 Perplexity 明确指出一个容易做错的地方： **不应该通过添加更长指令或改变 description 来解决问题** 。Gotchas 列表才是长期积累、高价值的地方。

为什么？因为加长指令会让 Load 层的 token 成本上升，影响所有用户。而 Gotchas 是有针对性的——它只在特定的失败模式上增加上下文，ROI 高得多。

### 四层 Eval 套件

Perplexity 的评估体系覆盖了 Skills 从加载到执行的完整链路：

| Eval 层级 | 测试目标 |
| --- | --- |
| **Skill 加载 Eval** | 精度（该加载的是否加载了）、召回（不该加载的是否避免了）、禁止检查 |
| **渐进加载 Eval** | Agent 是否读取了正确的 spoke 文件（references/、scripts/） |
| **端到端任务 Eval** | 完整的 Agent 循环 + LLM judge 评分 |
| **跨模型 Eval** | 在 GPT、Claude Opus、Claude Sonnet 三种编排模型上运行 |

跨模型 Eval 这事容易被跳过，但 Perplexity 的经验说明它不该被跳过。不同模型在处理 Skills 时行为差异不小。如果你的 Skill 只在 Claude 上测过，换到 GPT 上可能完全不是一回事。

Anthropic 的做法更偏实战：用 `PreToolUse` 钩子记录 Skill 使用情况，从日志里找受欢迎的 Skills 和触发频率偏低的 Skills。这和他们的内部管理策略一致——让好用的 Skills 自己冒出来，用数据说话。

管理上也有分层：小团队直接提交到代码仓库的 `./.claude/skills` 目录就行；人多了，再搭内部插件市场（Plugin Marketplace）。Skills 之间可以直接按名字引用，形成依赖链。

## 5\. 开放标准与生态：从内部实践到行业基础设施

### Agent Skills 开放标准

2025 年 12 月 18 日，Anthropic 将 Agent Skills 发布为开放标准，并捐赠给 Agentic AI Foundation（AAIF）。标准的核心是前面提到的文件夹结构加上渐进式披露三阶段加载。标准结构为：

```
my-skill/
├── SKILL.md          # Required: 元数据 + 指令
├── scripts/          # Optional: 可执行代码
├── references/       # Optional: 参考文档
├── assets/           # Optional: 模板和资源
└── ...               # 任意额外文件或目录
```

这个结构和 Perplexity 的 Hub-and-Spoke 结构高度一致。说明开放标准不是凭空设计的，而是从实践中抽象出来的。

### 生态采纳情况

合作伙伴名单很有分量： **Atlassian（Jira/Confluence）、Figma（设计→代码）、Notion（规格→任务）、Canva、Sentry、Cloudflare、Ramp、Box** 。微软在 VS Code 和 GitHub 中采纳了这个标准，Cursor 的 Nightly 版也已经支持。SDK 层面提供 Python、TypeScript 和 Java 三种语言的支持。

从时间线看发展速度不慢：2025 年 10 月 Claude Skills 功能正式上线，12 月开放标准化并引入一批合作伙伴，到 2026 年 3-5 月两家公司几乎同时发布深度技术文章分享内部经验。

### 对技术团队意味着什么

如果你是技术负责人或架构师，Agent Skills 开放标准意味着几件事。

**统一的能力扩展格式** 。团队不再需要为每个 Agent 平台写不同格式的能力模块。一个符合标准的 Skill 文件夹，理论上可以在 Claude Code、VS Code、Cursor 等多个环境中使用。

**上下文工程将成为必备技能** 。写好 Skill 的核心不是编程能力，而是对上下文成本的理解和信息组织的能力。这是一个新的技能维度。

**评估体系不可省略** 。Perplexity 把 Evals 放在 Step 0，Anthropic 用钩子系统持续监控——两家都把评估作为 Skill 生命周期的核心环节。准备建设 Skills 的团队，Eval 基础设施应该和 Skill 开发同步规划。

至于 OpenAI 和 Google 是否有类似的 Skill 系统、大规模 Skills 库的路由准确率阈值在哪、合作伙伴生态中 Skills 如何定价——这些问题当前公开资料中未发现相关依据。但从 Anthropic 和 Perplexity 已公开的方法论来看，Agent Skills 的工程化体系已经有了比较清晰的轮廓： **三层成本模型管住 token 经济学，Hub-and-Spoke 结构管住信息组织，Gotchas 飞轮管住持续迭代，四层 Eval 套件管住质量底线。**

如果你也在做 Agent 能力模块的设计，这四根柱子值得认真参考。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录