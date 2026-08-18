---
title: "Agent 撞到人墙就卡住，Matt Pocock 的 wizard 把人工步骤变成可复用 bash脚本"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491668&idx=1&sn=bc442ff4db7c4b0125b720b2fd37a62c&chksm=cf405502f837dc14083fb02452e1cd2eccda50c0a0e027cc8ba98b55d45fca59452207561ff5&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-12
description:
tags:
---
运维有术 术哥无界 *2026年8月12日 08:28*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *193* 篇，AI 编程最佳实战「2026」系列第 *70* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

![[Image 112.webp|wizard skill 信息图封面：人机交接全景]]

wizard skill 信息图封面：人机交接全景

假设你让一个 coding agent 给仓库接上第三方支付服务。它读完了 README，写好了调用代码，跑通了本地测试，然后在某个瞬间停了下来：

**这个 API key 只有你能拿到。**

它在控制台里，登录你的账号，点开 Developers 页面才看得见。agent 没有你的账号，没有你的浏览器，也没有你的 2FA。它把一串编号步骤丢进聊天框，等你复制粘贴回去。

这是 agent 自动化的边界：自动化停在哪，人就得从哪接手。Matt Pocock 在 skills v1.2 里毕业的 `wizard` skill 专门处理这段交接。它不替你做这些步骤，它把步骤写成一个交互式 bash 向导，你跑一遍，把只有你能做的部分做完，agent 再继续。

这篇文章拆的就是这个 skill 的源码和设计，以及它为什么值得你在部署 CI、接第三方服务时给 agent 留一条这样的路。

## 1\. 一个撞墙现场

先把贯穿全文的案例定下来：给一个仓库配上第三方支付（就叫 Acme Pay）的测试密钥，并设置成 GitHub Actions 的 secret，让 CI 能跑集成测试。

这个任务可以拆成三块：

- agent 能做的：读文档、写代码、改配置、提交
- 只有你能做的：登录 Acme Pay 控制台，创建 API key，复制出来
- 卡在中间的：这个 key 既要进本地 `.env` ，又要进 GitHub Actions

agent 做到第二块就停了。按 wizard 的设计，它不会把步骤话术丢进聊天框等你一句句回。它触发 `wizard` ，生成一个 `setup-acme-pay.sh` ，然后告诉你： **跑这个脚本，跟着它点** 。

你运行脚本，节奏是这样的：脚本打开 Acme Pay 控制台，告诉你点哪个按钮。你粘贴 key，脚本写进 `.env` ，问你确认。最后调 `gh secret set` 写进 GitHub Actions，收尾时打印完成清单。全程不需要回到聊天框解释你做了什么。

跑完回到 agent，它继续读 `.env` 和 CI 配置，确认 key 已就位，接着往下做。整个流程分四段： **撞墙，触发 wizard，人工部分，回主链路** 。

这个交接能成立，是因为 wizard 把 **人要做的事** 从聊天记录里搬出来，变成一份可运行、可重复、可版本化的脚本。官方文档里有一句点得很透： **agent 只写脚本，从不运行它；脚本由你在自己的机器上运行** （aihero.dev/skills-wizard）。

![[Image 113.webp|四段式人机交接流程图]]

四段式人机交接流程图

## 2\. wizard 是什么：四类触发，一条反向约束

`wizard` 在 v1.2.0 从 `in-progress/` 毕业进入 Engineering bucket，同时改成 model-invoked（CHANGELOG #680）。它自己的 SKILL.md 开头写得很直白：

> Generate an interactive bash wizard that walks a human through steps only they can perform.

翻译过来：生成一个交互式 bash 向导，逐步骤引导真人完成只有他们能做的步骤。触发分支有四个（截至 2026 年 8 月，v1.2.3）：

1. **provisioning infrastructure** ：配置基础设施
2. **setting up credentials or CI secrets** ：设置凭据或 CI secrets
3. **walking an unfamiliar third-party dashboard** ：走一遍陌生的第三方面板
4. **a one-off migration or cutover** ：一次性迁移或切换

四类场景的共同点：都有人必须亲自参与的环节，而且这些环节手动做很繁琐，每次都要重新向 AI 解释一遍。

更关键的是反向约束，SKILL.md 原话： **Don't invoke this for steps the agent can perform itself** 。CHANGELOG #680 说得更狠： **Work an agent can do, an agent should do** ，向导只服务于那些你不会交给 agent 的点击、审批和面板操作。

这条约束是 wizard 的边界感所在。它不抢主链路的活，只在人墙处出现。

### model-invoked 到底意味着什么

这是 v1.2.0 改动里容易被误解的一点。model-invoked 不是说 `/wizard` 手动输入失效了。CHANGELOG #680 原话： **Typing `/wizard` works exactly as before - model-invocation only ever adds the agent's reach** 。

手动触发照旧可用，model-invoked 只是多了一条 agent 主动触发的路。agent 在实施中撞到人墙时，自己会伸手去拿 wizard，而不是等你想起还有这个 skill。这个改动还顺带绕开了一个实际问题：Claude 的桌面/网页端在 #693 issue 里把 user-invoked skills 从技能列表里删掉了。model-invoked 的 wizard 不受影响，仍然能触发。

![[Image 114.webp|wizard 四类触发场景与反向约束]]

wizard 四类触发场景与反向约束

## 3\. scope the procedure：先读仓库，再问人

wizard 流程的第一步叫 scope the procedure（界定流程），这一步特别容易做错。它要求先读仓库，而不是冷启动提问。

Agent 要读的文件是固定的：`.env` 、`.env.example` 、`.env.*` 、README、 `docker-compose*` 、框架配置、`.github/workflows/*` 。读这些文件的目的很具体： **每个 `secrets.*` / `vars.*` 引用都是一个 wizard 必须产出的值** 。

比如 workflow 里有这么一行：

```
env:
  ACME_PAY_API_KEY: ${{ secrets.ACME_PAY_API_KEY }}
```

那么 `ACME_PAY_API_KEY` 就是 wizard 必须产出的值。不是建议提供，是必须。scope 完成的标准是：每个 captured value 都清楚三点：

- **人从哪里拿到它** ：哪个控制台、哪个页面
- **写到哪里** ：`.env` 、GitHub secret、两者都写，或者哪里都不写（有些 stage 是纯动作，比如点一个开关）
- **是否机密** ：机密值用隐藏输入，公开值正常显示

这三点可以做成一张对照表，拿 Acme Pay 场景举例。按我读源码的理解，不少配置事故出在 **值写错了地方** ，而不是值拿错了：

| 值 | 人从哪拿 | 写到哪里 | 是否机密 |
| --- | --- | --- | --- |
| Acme Pay 测试 key | 控制台，API keys，Reveal | `.env`  \+ GitHub secret | 机密 |
| Webhook secret | 控制台生成 | 只写 GitHub secret（CI 用） | 机密 |
| 测试/生产模式开关 | 控制台页面 | 哪里都不写（纯动作） | 公开 |
| 已有但不需要动的值 | 仓库 `.env` 里已有 | 不产出 | 公开 |

注意表格最后一行：scope 不是把所有值都收一遍，而是把 CI 真正引用的值收齐。哪些值进 `.env` 、哪些进 secret、哪些临时持有或直接丢弃，由 **CI 引用什么** 决定，不由我们有什么决定。

这里要区分源码事实和作者实践建议：源码只定义了流程，判断哪个值写哪里，靠的是对 CI 配置的阅读。wizard 本身不会替你做这个判断。

scope 的收尾一步是把 stage 列表展示给用户确认，可增删、可重排。这个确认在 agent 中途触发时兼任 proposal。你看到的 stage 列表就是 wizard 给你的提案。

## 4\. map each stage's journey：路径要落到陌生人能照做

scope 定了要产出什么，第二步 map each stage's journey 定人具体怎么走。

要求很硬：每个 stage 必须落到陌生人能照做的具体指令。调研报告里给了一个标准路径示例： **Dashboard → Developers → API keys → Reveal test key → copy** 。每一步都是动作，没有 **去设置里找找** 这种含糊指令。

对应的代码形态是这样：

```
open_url "https://dashboard.acmepay.dev/developers/api-keys"
say "Log in if needed, then:"
step "Click Create API key"
step "Choose the Test environment"
step "Copy the key (it starts with ak_test_)"
```

`open_url` 跨平台打开浏览器（wslview → explorer.exe → xdg-open → open，失败则 warn 手动访问）， `step` 带蓝色前缀逐条列出动作。

这里有一条红线，SKILL.md 明确写了： **不知道当前 UI 或确切命令时，明说并询问用户或查文档，绝不编造可能不存在的步骤** 。这条红线我单独拎出来说，因为它是 wizard 这类工具翻车的重灾区，反例部分会细说。

map 完成的标准：每个 stage 都能让一个从没进过这个控制台的人照着走完。

## 5\. author the wizard：template.sh 的 library 与 STAGES

第三步是写脚本。wizard 不是从零写 bash，而是复制自带的 `template.sh` （204 行），替换示例 stage。template.sh 的设计核心是 library / STAGES 分离：

- `STAGES` marker 以上是 wizard library，文件头直接写着 **do not hand-edit it** （template.sh:6-7）

为什么 library 永不手改？SKILL.md 里有一句： **The library above the `STAGES` marker is identical in every wizard; that consistency is the point** 。一致性本身就是目的。每个 wizard 的交互逻辑完全一样，你用过一个就会用所有。library 是经过验证的公共设施，作者只填业务内容。v1.2.3 还去掉了时间估算， `stage` 只接受名字，进度按 stage 计数。

library 提供的 helpers 各管一件事：

- `stage "Name"` ：清屏，显示 `▸ Stage N/M · Name` ，屏幕只保留当前步骤
- `open_url URL` ：先开 URL 再问值，人不用切窗口
- `ask` / `ask_secret` ：可见/机密输入，后者 `read -rs` 隐藏回显，重跑时 `.env` 现值作为默认值
- `write_env KEY VALUE` ：幂等写入 `.env` ，删除旧行、追加新行
- `set_secret` / `set_var` ：写 GitHub secret / variable，gh 不可用或未认证时降级为 warn 并记入 `SKIPPED`
- `confirm "question"` ：y/N 门，任何不可逆动作前都要过它
- `finish` ：汇总 `WRITTEN_ENV` 、 `WRITTEN_SECRET` 、 `SKIPPED` ，列出仍需手工处理项

拿上面的场景，一个 wizard 大概长这样（骨架）：

```
TOTAL_STAGES=3
banner "Connect Acme Pay to your repo"

# 每个 stage 一个任务，按依赖顺序排列
stage "Get API key from Acme Pay dashboard"
# ... 打开控制台、指引、捕获值的代码 ...

stage "Save key to .env"
# ... 写 .env 的代码 ...

stage "Set GitHub Actions secret"
# ... 设 CI secret 的代码 ...

finish
```

单个 stage 展开后是这样：

```
stage "Get API key from Acme Pay dashboard"

open_url "https://dashboard.acmepay.dev/developers/api-keys"
say "Log in if needed, then:"
step "Click Create API key"
step "Choose the Test environment"
step "Copy the key (it starts with ak_test_)"

API_KEY=""
ask_secret API_KEY "Paste your Acme Pay test key"
```

有几个设计点单独展开说。

**一个 stage 一个任务，屏幕只保留当前步骤。** `stage` 会清屏，人永远只看到眼前这一步，不会被后面几步吓到。这是 **驱动流程并持有状态的程序** 和 **指令清单** 的差别。官方文档专门强调过这个定位。

**`set_secret` 只写 CI 真正需要的值。** 不是所有值都进 `.env` 或 secret。比如 webhook secret 只在 CI 里用，就不该进本地 `.env` ；反过来，本地开发要用的 key 如果只写进了 secret，本地就跑不起来。

**wizard 默认是 ephemeral 的。** SKILL.md 明确：默认一次性，为一次运行构建，任务完成后删除。只有用户想要可复用的 setup 路径时才 commit 进仓库、写进 README，让下一个人运行脚本而不是再问 AI。这个默认值很克制，大部分配置任务是一次性的，不该污染仓库。

![[Image 115.webp|template.sh 的 library 与 STAGES 结构示意]]

template.sh 的 library 与 STAGES 结构示意

## 6\. verify and hand off：不端到端自跑

第四步验证，是 wizard 特别反直觉的地方： **不做端到端自跑。**

常规脚本写完总要跑一遍确认。但 wizard 不能跑： `open_url` 会弹出浏览器， `ask_secret` 会阻塞等人输入。跑一遍等于把整个人工流程在 agent 手里重演一遍，直接卡死。所以验证改为三层静态检查：

- `bash -n <script>` ：语法检查
- 有 shellcheck 就再跑一遍 shellcheck
- `chmod +x <script>` ：确保可执行

然后做静态追踪：从 stage 1 开始，逐个值追踪去向。每个值都被捕获了吗？落到了它该落的地方吗？每个 `set_secret` 的名称和 CI 里的 `secrets.*` 引用精确匹配吗？这一步是 wizard 的质量关口： **脚本可以没有真实跑过，但每个值的来龙去脉必须在纸上走通。**

这个取舍有意思。主流 HITL 框架的做法是 agent 运行时暂停等确认（Haystack 的 confirmation policy、LangGraph 的 halt/resume、ag2 的 `human_input_mode` 都是这个路子）。wizard 反着来：把人类操作编排成一个可执行程序，人自己跑。

产物可复用、可提交、可版本化，这是 **脚本化 HITL** 和运行时 HITL 的差别。代价是 wizard 不能像运行时 HITL 那样即时反馈。它用严格的静态追踪补这个缺口。

## 7\. 用错 wizard 的四种姿势

读源码和社区讨论的时候，我整理出四种典型的误用，每一种都对应一个设计边界。

**把步骤话术丢进聊天框。** 这是 wizard 想消灭的原罪。CHANGELOG 写设计意图时原话是 **instead of dumping numbered instructions into the chat and hoping you follow them** 。话术丢聊天框的下场是：这次跑完，下次还要重新解释一遍；步骤一多，人大概率在某一步漏掉复制。wizard 把话术变成脚本。重跑时 `.env` 已有值直接作为默认值，Enter 跳过，流程本身变成了资产。

**agent 能自己做，却误触 wizard。** 反向约束的另一面。如果步骤 agent 能执行（改文件、跑命令、调 API），走 wizard 就是把人的手拖进本不该人参与的环节。判断标准就一句：这一步人不在场，agent 能不能独立完成？能，就不该上 wizard。

**编造 UI 路径。** map 阶段的红线。某个面板的按钮实际叫别的名字，或者根本不存在，wizard 却写 **点击那个红按钮** ，人照着点就会卡住，然后对脚本失去信任。SKILL.md 的处理是：不知道就说不知道，问用户或查文档。宁可让用户补一句，也不要写一个假步骤。

**把所有值都 `write_env` 。**`.env` 膨胀的根源。scope 时收了一堆值，author 时不分青红皂白全写进 `.env` ：CI 用不到的值、纯动作产生的值、本应只进 secret 的值，全堆进本地文件。结果 `.env` 越来越大，密钥散落各处，轮换时找不到谁在用。正确做法是回到那张对照表：CI 引用什么，就产出什么。

## 8\. 边界与坑：社区反馈里的真实问题

wizard 不是没有争议。官方文档和 GitHub issue 里有一些真实的边界（截至 2026 年 8 月，issue 均处于 Open 状态）：

**没有 back button。** launch week 就有用户反馈： **loved it! One thing though - is there a way to go back and correct what you've entered?** 官方承认这是设计取舍：stage 输错只能 Ctrl-C 重跑。好在重跑很廉价，`.env` 已有值作为默认值，Enter 一路跳过。

**Issue #741：箭头键失效。** `ask` 的 prompt 用 `read -r` 而不是 Readline，按左右箭头会插入 `^[[D` / `^[[C` 而不是移动光标。Backspace 可用，箭头键不行。issue 里建议的修复是 `read -e -r -p` 。

**Issue #811：symlink `.env` 被替换。** 这是目前讨论度较高的一条实质批评。 `write_env` 以 `mv "$tmp" "$ENV_FILE"` 收尾，当 `.env` 是指向中央 secrets 存储的 symlink 时， `mv` 会用普通文件替换链接而不是写穿它。影响全是静默的：repo 与中央存储脱离、key 轮换失效、存储里所有 secrets 被复制进工作树、通过 symlink 解析的工具读到冻结快照。关键点在于这个 bug 位于 template 的 library 半区——也就是 skill 明确告诉作者 **永不手改** 的部分。wizard 作者没法绕过。

**工具链偏 GitHub 生态。** 据 Tosea.ai 的第三方评测，整个 mattpocock/skills 偏 TypeScript / Node 工作流， `set_secret` 依赖 `gh` ，Django + GitLab 团队只能获得部分价值。Discussions #177 请求支持 Gitea，也印证了这一点。

另外要说明：CHANGELOG 里描述的是 **设计意图** ，不是实测效果。wizard 的目标是让 **人要做的事** 不再每次重新解释，至于部署事故会不会变少、时间能不能省下来，官方没有给过任何数据。值不值得用，取决于你的场景里 **人墙步骤** 出现得有多频繁。

![[Image 116.webp|wizard 的边界与坑：社区反馈四类问题]]

wizard 的边界与坑：社区反馈四类问题

## 9\. 它在体系里的边界：只接人墙，不抢别的活

wizard 不是孤立的。把它放回 Matt Pocock 的整个 skill 体系里看，边界很清楚：它只接"人墙"这一段，不抢别的 skill 的活。

- **和实施链路** ： `implement` 按 spec/tickets 构建，走 tdd、code-review。wizard 不在 implement 的文档里，它是 model-invoked——实施过程中 agent 撞到人墙时自动触发，是运行时的隐性交接，不是实施文档里的显式步骤
- **和第一天配置** ： `setup-matt-pocock-skills` 负责仓库的第一天配置（issue tracker、triage labels、domain docs 布局）。wizard 属于 Engineering 而不是 Productivity，原因之一就是它会读 `.env*` 、 `docker-compose*` 、框架配置和 `.github/workflows/` 里的 secrets/vars 引用来 scope 自己。一个管仓库骨架，一个管配置流程里人必须亲手做的部分
- **和 grilling 那类** ：wizard 是 **执行型人机交接** ，把 **只有人能做** 的步骤编排成脚本；grilling 那类在我看来是 **决策型对齐** ，在动手前把需求、约束、取舍对齐。一个管 **人怎么参与** ，一个管 **做什么、为什么**

一句话总结 wizard 的定位：它坐在自动化停止、人类必须点击的那条线上（官方文档原话： **sitting at the line where automation stops and a human has to click** ）。它不扩大自动化的范围，它只是把自动化边界处的人工操作，变成了一份能反复用的资产。

如果你的 agent 经常在 **配密钥、走面板、做迁移** 这类地方停下来，值得给 wizard 留一条路。它不会让 agent 变强，但会让 **人该出手时怎么出手** 这件事，不再靠聊天记录。

> **说明** ：本文内容基于 Matt Pocock 在 GitHub 开源的 `skills` 仓库中 `wizard` skill 的 SKILL.md、 `template.sh` 、CHANGELOG 与 README 源码，以及 AI Hero 官方页面 `aihero.dev/skills-wizard` 分析整理而成（本地仓库快照，2026-08-11）；社区边界来自 GitHub 公开 issue。 **文中的配置模板和参数建议仅供参考，实际效果请以你的业务数据和环境测试结果为准。** 如有不准确之处，欢迎在评论区指出，我会回到原仓库核对修正。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录