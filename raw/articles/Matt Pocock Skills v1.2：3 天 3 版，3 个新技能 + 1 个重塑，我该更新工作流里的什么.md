---
title: "Matt Pocock Skills v1.2：3 天 3 版，3 个新技能 + 1 个重塑，我该更新工作流里的什么"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491686&idx=1&sn=69445fc6cb6e6050ce5703a19910522a&chksm=cf405530f837dc261a71434b2e7aa06b7e6b37cac8bebf3a123f6c9f0addfc738c255bdabd8e&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-17
description:
tags:
---
运维有术 术哥无界 *2026年8月14日 08:30*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *195* 篇，AI 编程最佳实战「2026」系列第 *72* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

![Matt Pocock Skills v1.2 全家桶变更地图封面](https://mmbiz.qpic.cn/mmbiz_png/icibtH5FrDwPdqwewWEsUBpRhZTWpjpI7R16zEqibfBh6vCtJ7LFoUmS1hqUjpvUfiaEoYnoRqB3Vd6DWpud1bfdf3wBvZ9FgHgSC46rx1wzNrM/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

Matt Pocock Skills v1.2 全家桶变更地图封面

Matt Pocock Skills 在 2026-08-05 发布 v1.2.0，同一天又发 1.2.2，次日再发 1.2.3。只看发布说明，这像一次食品全家桶更新：grilling 追问提速、wait-what 和 to-questionnaire 毕业、wizard 转正、prototype 改了玩法。仓库在 GitHub 上已经 213.4k star，算是社区里相当有分量的一套 Agent Skills。

但把 v1.2.0 的 CHANGELOG 和源码拆开看，真正值得开发者跟进的是 4 处 **人机分工边界的位移** ：

- **wizard** ：只有人能做的步骤（配密钥、开 dashboard、跑一次性迁移），现在 agent 会自动伸手
- **to-questionnaire** ：挖别人脑子里的知识，有了固定的问卷产物
- **wait-what** ：对话卡壳时，用七个词纠偏，而不是翻聊天记录
- **prototype** ：探索产物从用完删除，变成归档到 primary source 分支

这篇文章就回答一个问题： **v1.2 我到底该更新工作流里的什么。**

## 1\. v1.2 变更地图

先给全景，再拆细节。仓库自己分 5 个 bucket（分类目录）：engineering 和 productivity 是正式频道；misc 保留少用；in-progress 是 beta 频道（公开但不随 plugin 发布）；deprecated 已弃用。 **毕业** 就是技能从 in-progress 挪进正式 bucket、随 plugin 一起发布。

| 技能 | v1.1 行为 | v1.2 行为 | 变化性质 |
| --- | --- | --- | --- |
| wait-what | 不存在 | 新增 7 行 user-invoked | 结构性 |
| to-questionnaire | in-progress | 毕业进 Productivity bucket | 结构性 |
| wizard | in-progress | 毕业进 Engineering，转 model-invoked | 结构性 |
| prototype | throwaway 用完删除 | 归档到 throwaway 分支 + 单文件 HTML | 结构性 |
| writing-for-agents | 旧名 writing-great-skills | 重命名，转 model-invoked | 结构性 |
| 双平台 | 只有 Claude | plugin + Codex 元数据 + skills.sh 三态 | 结构性 |
| 全仓修补 | 无 | Redact 去敏、harness-neutral、wizard 去时间估算 | 小改动 |

版本线：1.2.0 是结构大版本，1.2.2 当天跟进、1.2.3 次日跟进，1.2.1 没有独立记录，合并进 changeset 管理。grilling 的 round-by-round 重构（13 问压到约 3 轮）也属于 1.2.0，但内部运作上一篇文章拆过，这里只当背景。

另外两件顺手的事。1.2.0 删掉了 6 个旧 skill（ubiquitous-language 并进 domain-modeling、design-an-interface 并进 codebase-design、qa 拆成 triage/to-tickets 等）。它还顺手给 wayfinder 引入了 decision ticket 概念：research tickets 用 subagent 并行烧掉，落在 `research/<name>` throwaway 分支 + context pointer。这个模式跟下面 prototype 的留档思路是同一套。

下面按 5 条主线展开。每条讲清楚触发方式和产物形态，顺带指出升级后还按旧习惯用的反例。

## 2\. wizard：人墙步骤自动化

wizard 这次从 in-progress 毕业进 Engineering bucket，跟其他技能不同的地方在触发方式：它是 **model-invoked** 。你不用主动叫它，是 agent 在对话里遇到“只有人才能做的步骤”时自动伸手。官方原话说得直白： **Work an agent can do, an agent should do; the wizard is for the clicks, approvals and dashboard trips you would not hand to one。**

触发分支有四类：provisioning infrastructure、setting up credentials or CI secrets、walking an unfamiliar third-party dashboard、running a one-off migration or cutover。反过来，agent 自己能做的步骤是明确的 non-trigger，它不该拿 wizard 包装自己能跑的命令。

产物是一个交互式 bash 脚本，一步步带人走完手工流程。template.sh 的结构是关键：

- `STAGES` 以下是作者区：只写 `TOTAL_STAGES` 和每个 `stage`

v1.2.3 之后 `stage` 只接收名字，时间估算整个被删掉，进度按阶段数计。作者区长这样（示例结构，以官方 template.sh 为准）：

```
TOTAL_STAGES=3

stage "打开 Stripe 控制台，导出 restricted key"
  open_url "https://dashboard.stripe.com/apikeys"
  say "登录后进入 API keys，点 Create restricted key，勾选 charges 权限"
  pause "弄好了按回车继续"

stage "把 key 写入本地 .env"
  STRIPE_KEY=$(ask_secret "粘贴 restricted key（输入不回显）")
  write_env ".env" "STRIPE_RESTRICTED_KEY" "$STRIPE_KEY"

stage "同步到 GitHub Actions secrets"
  set_secret "STRIPE_RESTRICTED_KEY"
```

wizard 的读取范围有讲究：`.env*` 、 `docker-compose*` 、framework config、`.github/workflows/*` 里每个 `secrets.*` / `vars.*` 引用，都是 wizard 必须产出的值。

**安全卖点值得单独说** 。第三方解读里有个很准的概括：与其给 agent computer-use 权限让它自己点，不如给一个确定性脚本。wizard 运行时会开浏览器、阻塞等人类输入，agent 自己不能端到端跑，只能做静态 trace（ `bash -n` + shellcheck + 值流向检查）；脚本里输入的密钥也不经过 agent，直接写进 `.env` 或 GitHub secrets。 **Nothing is sent to an agent while it runs。**

wizard 默认 ephemeral：built for one run，存 scratch 或 `scripts/` ，用完删除；用户要求可重复的 setup 路径时才 commit。

升级到 v1.2 后要是还手动开终端、复制粘贴、自己拼.env，等于放着 wizard 的自动伸手不用。它的触发条件是 agent 判断这步只有人能完成，不是人命令它。

![wizard template.sh 的 STAGES 两层结构示意图](https://mmbiz.qpic.cn/mmbiz_png/icibtH5FrDwPfTPpo3sMHU9JArh7dTqHV6te8rviaYpvEiaJXxT2caJ68waEibfic9QAVDiavnr0auibciafbljx4kGWvanT3gWfKibibwFic811keYqfZg/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

wizard template.sh 的 STAGES 两层结构示意图

## 3\. to-questionnaire：挖出别人脑子里的知识

to-questionnaire 从 in-progress 毕业进 Productivity bucket。它解决的是另一处分工问题： **你无法回答的问题，不要 grill 主题，只 interview 发送对象** 。grill 的定位是逼问文档和代码，但对方想要什么、卡在哪个环节，这类答案不在文档里，只在别人脑子里。

核心 move 叫 **grill the send, not the subject** ：先想清楚收件人是谁（角色、专长、关系），再想你需要拿回什么（具体决策或事实），针对 gap 写问卷。

产物是 `to-questionnaire-<slug>.md` ，Markdown 问卷，可异步填写或会议中共同填写。结构固定：Purpose / From-To-How your answers will be used / Context / How to answer / 按主题分 `##` 的 question 区块（most-important-first，一问一义，答案 stub 在下，必要时 one-line why this matters）/ Anything else?。示意：

```
**to-questionnaire: onboarding 评审**

**收件人和用途**
- 收件人：后端负责人（做过 3 次 onboarding 重构）
- 用途：决定是否立项 onboarding 可观测性改造
- 回答方式：异步填写，约 10 分钟

**问题（按重要度排序）**
- 1. 新员工第几天能独立跑通本地环境？
  （答案留空，给收件人填）
- 2. 上一次 onboarding 卡住的是哪个环节？
  （为什么问：我们怀疑是环境配置，需要你的数据佐证）
```

定位上，它和 `/grill-me` 互为镜像：grill-me 挖你自己脑子，to-questionnaire 挖别人脑子。ask-matt 里它作为 Standalone route 出现。

第三方解读说得更直白：to-questionnaire **openly described as a patch for the fact that agents are still hard to collaborate around** 。说白了，agent 协作能力还不行，问卷是把人拉回协作流程的补丁。这不是贬义，是对工具边界的诚实定位。

容易走的老路是：遇到只有别人能答的问题，先问 agent、grill 文档。答案根本不在文档里，白费一轮。正确姿势是直接生成问卷发给那个人。

## 4\. wait-what：七个词的一词纠偏

wait-what 是这次发布里体感很轻、设计很巧的一个。完整内容只有三行 frontmatter 加一行正文：

```
---
name: wait-what
description: Stop. That last message did not land — re-pitch it.
disable-model-invocation: true
---

Wait — I don't understand where you've got to here. Re-pitch that: give me a little bit of context, talk in ASD-STE100 Simplified Technical English, and use the ubiquitous language from \`CONTEXT.md\`.
```

关键设计在命名： **它命名的是听者的状态（没听懂），不是输出形态** 。 `/tldr` 、 `/no-fluff` 这类命令让模型裁剪词语，容易把上下文一起裁掉；wait-what 让模型做的是重新讲一遍，用 STE100 简化技术英语、用 `CONTEXT.md` 里的领域通用语言（ubiquitous language）。它复用的还是全局 `CLAUDE.md` 里已有的 leading words，跟 `CONTEXT.md` 的词汇对齐，不另起一套话术。官方给的解释是 **concision skills fail by growing** ：写 400 行的精简指令，模型照样 verbose，所以保持极短。 边界也划得很清楚： **wait-what 修复一条消息，不预防下一条** 。预防靠 `/grill-with-docs` 提前建共享语言。官方原话： **The cure for jargon is a shared language built upfront with /grill-with-docs。**

发布几天内 shadcn/ui 作者发推玩梗： **One of you models is about to get a lot of /wait-what. You know who you are.** 这个词能引起主流前端开发者的共鸣，说明命名切中了真痛点。

**反例** ：agent 输出听不懂时，还是翻聊天记录或发 `/tldr` 让它压缩。wait-what 的正确用法是让它按你的领域词汇重讲一遍，而不是再压缩一次。

![wait-what 一词纠偏与 /tldr 裁剪的对比图](https://mmbiz.qpic.cn/sz_mmbiz_png/icibtH5FrDwPcezseZicibBbryyU0rwkSWicKoiaibic0JQ1vA9VnWDx2iarT6zGQrhdAg8ic3W95dGqlUw0ibYRN0CtUA1fJHYfV1gVrGXMicibzWDZLb3w/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=2)

wait-what 一词纠偏与 /tldr 裁剪的对比图

## 5\. prototype：throwaway 从删除到留档

prototype 重塑是 v1.2 里反直觉的一条。v1.1 的设定是 throwaway = 用完删除；v1.2 改成 **prototype 是 primary source，throwaway 不再等于删除** 。

两个核心 idea：

**一是 demo 是单个可分享 HTML 文件** （logic 分支）。纯 HTML/CSS/JS，无 build、无 server，双击即开，非开发者也能用领域语言驱动：带标签的 state panel + 常驻 free-play buttons + tabbed guided walkthroughs（每个 scenario 一个 tab，下面列出要按的按钮顺序）。可移植的纯逻辑模块（pure reducer、state machine、纯函数集、方法面清晰的类）提升到真实代码，HTML shell 才是 throwaway。

这里有个 v1.1 到 v1.2 的方向变化：旧版 LOGIC.md 是纯逻辑终端小程序加 TUI shell，面向开发者自己；新版改成单文件 HTML，明显更面向非开发者演示。

**二是验证完的问题归档** ：原型问完问题后不删除，提交到 `prototype/<name>` throwaway 分支（out of main），在实现 issue 上留 context pointer；主分支只保留被验证的决策。结论（verdict + question）持久化在 issue / ADR / commit。UI 分支走 `?variant=` URL 参数切换，上限 5 个变体，胜出变体 fold 进真实代码，输家和 switcher 一起上 throwaway 分支。

这里要分清一个常见误解： **归档不等于生产化** 。原型依旧无测试、无错误处理、无泛化、一次跑完；它只是被保留为可供追溯的 runnable evidence，不是把 throwaway 代码打磨成生产代码。这个区分很重要：很多人一听保留原型，就把原型代码当生产代码伺候，v1.2 明确说了不。

**反例** ：原型验证完还按老习惯直接删掉。决策没有留档，下次评审又得从零解释。正确姿势是推 `prototype/<name>` 分支、issue 上留 pointer。

## 6\. 双平台：plugin 与 Codex 的实际边界

v1.2 的安装方式分叉成两条路线，哲学完全不同：

| 路线 | 安装方式 | 哲学 | 更新方式 |
| --- | --- | --- | --- |
| Claude Code plugin | `claude plugins install mattpocock-skills` | 订阅（subscribe） | 作者发布自动更新 |
| skills.sh | `npx skills@latest add mattpocock/skills` | 抱走（fork） | `npx skills update`  手动拉取 |

README 里说得清楚：一条是 **you subscribe rather than fork** ，另一条是 **Nothing updates behind your back** 。两条路线互斥：装两份，每个 skill 就会出现两次。

plugin 侧：`.claude-plugin/plugin.json` 显式列出 24 个 promoted skill，精确控制发布集合，排除 misc / in-progress / deprecated / personal。2026-08-05 起被 Claude Code 官方 marketplace 收录， `claude plugins install mattpocock-skills` 成为文档化安装路径，不用先 add marketplace。

Codex 侧的边界要泼盆冷水： **native Codex plugin 还没上线** 。ADR 0002 给了明确理由：Codex 的 `.codex-plugin/plugin.json` 的 `skills` 只接受单个路径字符串，不接受数组，没法从 bucket 布局里挑选 promoted 集合；symlink 方案在安装时又会被 Codex 丢弃。所以现在 Codex 用户面对的是三态：

- **Claude plugin** ：托管只读 bundle，24 个 skill，自动更新
- **Codex 元数据** ：每个 SKILL.md 旁一个 `agents/openai.yaml` （携带 Codex UI 元数据 + invocation policy）， `AGENTS.md` 是 `CLAUDE.md` 的 symlink，让 Codex 读同一份仓库指令

invocation 语义在双平台下是严格对齐的：user-invoked 的 skill 在 Claude 侧设 `disable-model-invocation: true` ，在 Codex 侧设 `policy.allow_implicit_invocation: false` ，两边都只有人显式调用才能触发；model-invoked 的 skill 两边都省略这段配置。还有个规则值得记：user-invoked skill 可以调用 model-invoked skill，但永远不能调用另一个 user-invoked skill。

- **skills.sh** ：把文件复制进项目，可编辑，手动更新

还要分清专属与通用： **Claude Code 专属** 的是 plugin 玩法和官方 marketplace 收录； **仓库通用** 的是 SKILL.md 本身、skills.sh、AGENTS.md / CLAUDE.md、openai.yaml 元数据。所以即使你不用 Claude Code，这套技能的核心资产照样能拿。

![Claude Code 与 Codex 三态安装边界示意图](https://mmbiz.qpic.cn/sz_mmbiz_png/icibtH5FrDwPfBvtib0gkalGiaqmKQN7iaP4bGA15ayPzJBqlicPZcEEzFB82albm9ItpgtfrIwUOMtVicANtiaurc6PXWpIDkPPXJaLt3THyU1xECo/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=3)

Claude Code 与 Codex 三态安装边界示意图

## 7\. v1.2.2-1.2.3：修修补补也有信息量

这几天的 patch 里藏着一个信号：v1.2 的主战场其实是 **让同一套技能在不同 harness 上行为一致** 。

- **#766（1.2.2）** ：writing-for-agents 在 Codex 里恢复 model-invokable。1.2.0 给它设了 `policy.allow_implicit_invocation: false` ，结果 Codex 直接从 model-visible 列表里把它过滤掉，只有显式 `$writing-for-agents` 才触发，在 Codex 上等于哑火，1.2.2 紧急修正
- **#779（1.2.3）** ：diagnosing-bugs 新增 Redact 章节。展示命令、输出、捕获产物时一开始就遮蔽敏感信息，用环境变量建 loop 让凭据留在环境里，只引用捕获产物中带信号的行
- **#781（1.2.3）** ：从 code-review / codebase-design / improve-codebase-architecture 删掉 Claude Code 专用工具名和 agent-type 名，让步骤在 Codex 和其他 harness 可跟随
- **#783（1.2.3）** ：wizard 模板删掉时间估算， `stage` 只接收名字，进度按阶段数计

## 8\. 我该跟进哪条

如果只记一张表，按身份对号入座：

| 读者身份 | 优先跟进 | v1.2 价值点 |
| --- | --- | --- |
| Claude Code 用户 | plugin 安装 + wizard + prototype | 自动更新；人墙步骤自动伸手；探索产物留档 |
| Codex 用户 | skills.sh + wait-what + openai.yaml | 三态边界；七词纠偏；理解元数据作用 |
| 只想学人墙步骤自动化 | wizard | template.sh 结构可直接抄进自己的脚本 |
| 想建 domain vocabulary | wait-what + CONTEXT.md | 纠偏话术 + 共享语言对齐 |

说句实话： **不要指望更新到 v1.2 就自动提速** 。wizard、to-questionnaire 是能力新增，不是速度优化；grilling 的提速（13 问压到约 3 轮）属于 1.2.0，而且已经有专门文章拆过。更新的价值在边界，不在速度：哪些活现在可以交出去、哪些话术有了解法、哪些产物不再丢。

社区里已经有新手反馈 **I couldn't build anything** 。全家桶的编排成本是真实痛点，这也是为什么这篇只按身份给跟进建议，而不是让你把 24 个 skill 全部重学一遍。

![v1.1 到 v1.2 四条人机分工边界位移图](https://mmbiz.qpic.cn/mmbiz_png/icibtH5FrDwPftsCJjAfiaMDOnvJQmBBB4xO3PqB0Dn21HFYRZleDSq8J3chgFn3Ts8cETLtcJRreG5libuElyua7qRJE57BxbV0icP0Jf923ce4/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=4)

v1.1 到 v1.2 四条人机分工边界位移图

## 总结

回到开头那个问题：v1.2 我到底该更新工作流里的什么？

我的答案是 4 处边界 + 1 个通道：让 wizard 接走人墙步骤、让 to-questionnaire 替你挖别人脑子、让 wait-what 随时纠偏、让 prototype 的探索产物留档，再用 plugin 或 skills.sh 其中一条路线接收这套变化。原型从删除变成留档，是这次相当反直觉、也值得记的一条： **throwaway 定义的是命题，不是代码本体。**

选择安装路线前先想清楚你要的是订阅还是抱走。装两条，得到的只是每个 skill 出现两次。

> **说明** ：本文内容基于 Matt Pocock Skills 仓库（mattpocock/skills）v1.2.3 源码（CHANGELOG、wizard/template.sh、to-questionnaire、wait-what、prototype 等）及社区反馈分析整理而成。源码分析基于笔者本地仓库版本（2026-08-06 的 v1.2.3），尚未在生产环境中完成全场景验证。 **文中的配置模板和参数建议仅供参考，实际效果请以你的业务数据和环境测试结果为准。** 如果有实际使用经验，欢迎在评论区分享交流。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录