---
title: "DeepSeek Harness 源码拆解：看看DeepSeek怎么理解Agent，怎么设计Harness"
source: "https://mp.weixin.qq.com/s/UmwwoCpQS5gLnORreMVnzQ"
author:
  - "[[翟星人的实验室]]"
published:
created: 2026-08-26
description: "从源码拆解DeepSeek Harness的插件运行时、工具链、会话与权限设计。"
tags:
---
翟星人的实验室 翟星人的实验室 *2026年8月14日 19:00*

![[Image 202.webp|DeepSeek Harness 源码分析：模型、工具、状态和权限怎样被装进同一套框架]]

DeepSeek Harness 源码分析：模型、工具、状态和权限怎样被装进同一套框架

反对本本主义，Agent 自会证明。  
大家好，这里是翟星人的实验室。

DeepSeek Harness（下文简称DSH）终于发布了。

这款承载着国民级AI产品的Agent框架昨晚亮相，MIT协议开源，目前80k stars，很猛。当然现在只是开发预览版，体验学习为主。梁圣的风格就这样——亮大招，蚕食，碾压。

DSH一出来，各大评测就吵成了一锅粥。哈哈，新东西承载了太多期待，不同人群的视角、用法不同，心理落差自然也不同。有人觉得它就是下一个Codex，打开一看界面简陋直接差评；有人看到速度快就恨不得吹上天；也有人翻了两眼源码就急着下结论。

官方最亮眼的一句话： **Everything is a plugin** 。

结合它的名字其实也能品出来——DeepSeek Harness的定位首先是一个运行时框架。所谓Harness，就是模型外面那层让想法变成现实的系统工程：任务怎么继续、工具怎么执行、权限怎么拦、进程崩了怎么恢复。（详细的Harness 相关概念，可以看下我以前写的这篇文章 [Prompt、Context之后，AI工程进入Harness Engineering时代。）](https://mp.weixin.qq.com/s?__biz=MzI0MjMwMTgyMw==&mid=2247485465&idx=1&sn=64236ff7d64f01133c1f5abfad23d617&scene=21#wechat_redirect) 至于它能不能做Coding、能不能做Design，那都是底层能力的映射，都是后话。在DSH中，模型适配、工具注册、会话日志、Agent Loop、沙箱、子智能体——全部是插件，全部可以通过配置替换，是的没错，就连Agent都还能换掉。

当然了，编程还是主场，但重心明显偏向框架。

所以今天不聊部署，也不聊怎么用，这东西到处都有也很简单。今天就从源码出发结合使用，纯从技术角度拆一拆：一个做出国民级AI产品的团队，究竟怎样理解Agent，怎样理解Harness，又是怎样把这套工程落下来的。

本文脉络，按需选看

整体框架 → 架构全景 · 底座 · 装配  
运行内核 → 循环 · 上下文 · 会话  
状态与工具 → 工具执行链 · 权限与沙箱  
多Agent与自修改 → 子Agent · 入口 · 创造模式  
横评与判断 → 和Codex/Claude Code/Pi的架构差异 · 缺口

结论：就我使用和从技术角度分析看来，现阶段它不是面向普通用户的2C产品，更像是一套面向开发者的Agent底座。

好不好玩全看个人。好了，看文章，关键概念我都颜色标记。可以快刷。

## DSH的架构全景

一个公式：

> **Agent = Model + Harness**

这是Agent领域的朴素共识。Model负责“想”——判断下一步做什么，生成文本、代码或工具调用。

Harness负责把想法变成现实。Prompt组装、工具执行、状态保存、权限控制、失败恢复，全是Harness的活。

最简单的Agent谁都能写——一个 `messages` 数组加一个工具Map就能跑。但产品级Agent的麻烦，全藏在这个循环的缝隙里。

举个例子：用户说“把项目里的端口配置改名，跑一遍测试，失败就继续修”。这句话进来，系统得决定：当前装了哪些工具，读哪份系统提示，允许写哪个目录，Shell在本机还是远程执行，工具结果怎么落盘，中途点停止哪些进程要杀掉，崩溃后怎么知道那条命令到底执行过没有。模型给方向，Harness负责把方向变成一串可控制、可观察、能恢复的动作。那DSH是咋做的呢？来先看下DSH的整体的运行时架构：

![[Image 203.webp|图片]]

看这张图。Cordis Runtime是中间那根主干——管插件怎么装上、对谁可见、什么时候激活、拔掉时怎么收尾。Agent Loop没有什么“内核特权”，它跟Tools、Session、Shell、Subagent一样，都是插在主干上的一块能力。

最上面是入口。Web是浏览器界面；headless没有界面，跑完一次任务就结束；ACP面向编辑器或自动化工具；SDK给外部程序调用。四种入口都能发起任务，不是各养一套Agent内核。

入口下面是装配层，这里几个词解释一下：Profile是一次启动的“总装配方案”，它决定叠哪些Bundle。Bundle就是一组可复用的插件配置包。Patch是覆盖层，可以针对某个插件替换配置；Preset再决定某个会话里的Agent具体带哪些工具、Prompt和能力。没有那个包揽所有依赖的巨型构造函数。

Cordis运行时管插件之间的服务、事件、作用域和生命周期。Agent、Agent Loop、Session、System Prompt、Tools都挂在这里。

第四层是能力与策略。文件系统、Shell、Subprocess、终端、LSP、Web、Skill、Subagent各有实现；Sandbox、Permission、Approval、Guard决定这些能力什么时候能用、怎么拒绝、什么时候找人确认。

最底下是Session Event Log。模型看过的消息、工具调用和结果、Turn与Step、权限变化、压缩后的上下文，最终都回到这条事实流里。Web页面、恢复、Fork、遥测和下一次模型请求，再从日志投影出各自需要的视图。这个就是典型的观测回溯了。

如果你准备跟着往下拆，先别满仓库乱翻。主线基本就在这几处：

```
packages/core/agent/             Agent 身份、Inbox 和状态
packages/core/agent-loop/        默认 Loop，Turn / Step 在这里跑
packages/core/system-prompt/     Prompt Section 的注册与组装
packages/core/session/           Event Log、恢复、Fork 和投影
packages/core/tools/             工具 Registry 与执行链
packages/preset/agent-presets/   每个 Agent 怎么装配
packages/subagent/               子 Agent 的 Activation 与所有权
packages/extensions/             动态 Cordis 扩展
```

记住这张图和这条路线再往下看，后面很多零碎的包才不会乱掉。

## 底座：是插件，但是更是一个插件系统

不少Agent也支持插件。通常做法是留一个 注册插件的入口函数， `registerTool()` ，外部传进名称、参数和执行函数，插件系统就算完成了。

但DSH说的插件深得多。深到是一套完整的系统。

官方架构文档写得很直白：模型适配器是插件，工具注册表是插件，Session Log是插件，Agent Loop也是插件。没有一个不可碰的“至尊内核”。新行为应该挂到已有扩展点旁边，不是每次都去Loop里加代码。

这个想法靠Cordis落地。啥是Cordis？这也是一个开源项目，后边会专门开一张分享下。你也可以理解DeepSeek是基于这套插件核心来完成了SDH的搭建和开发。是不是有点类似Openclaw以Pi Agent为核心，上层封装，理是这么个理，但是东西真不一样，它不只是一层依赖注入，还要管插件组合和生命周期。DSH最常用的四样东西：

### 1\. Service：能力从哪里来

工具消费者依赖 `ctx.shell` ，不直接import本地Shell类。今天Provider用Node子进程执行，明天换成远程沙箱，消费者还是同一个，模型看到的工具也不用跟着重写。

### 2\. Event：谁能在中途插手

模型请求前、工具执行前后、Turn 停止前，都有带顺序语义的事件。有些是普通通知，有些是 Waterfall：插件处理完要调用 `next()` ，后面的处理者才能继续。有些是单调 Guard，一旦拒绝，后面的插件不能又把它改成允许。权限系统最怕大家都能改最终答案，最后谁覆盖谁全看运气。

### 3\. Effect：卸载以后谁收尾

注册一个工具、监听器、计时器或 UI 区域时，同时登记清理动作。插件撤走，贡献也跟着撤走；Cordis 还会等它拥有的活动真正停下来。否则所谓热更新，跑几次就会长出重复工具、幽灵监听器和杀不掉的后台进程。

### 4\. Scope：这项能力对谁可见

全局工具、某个 Preset 的工具、某个 Agent 临时挂上的工具可以同时存在，查找时按 `agent → preset → global` 逐层解析。两个会话可以共用一套插件实例，又不会把 Session 状态混在一起。

![[Image 204.webp|Cordis 插件的四个核心概念：Service 提供能力，Event 介入流程，Scope 决定可见范围，Effect 管理卸载清理]]

Cordis 插件的四个核心概念：Service 提供能力，Event 介入流程，Scope 决定可见范围，Effect 管理卸载清理

普通插件系统关心“装了什么”，Cordis 还要回答“什么时候生效、对谁生效、撤掉时谁负责收尾”。Agent运行时比编辑器插件麻烦就麻烦在这——一个插件手里可能正握着子进程、审批请求和半个模型流，不能从数组里删个名字就假装它消失了。

当然Scope不是安全沙箱。某个Agent看不到一项服务，只说明组合上不可见，不代表同一进程里的恶意代码绝对碰不到它。组合隔离和安全隔离不是一回事，后面看动态插件时还会遇到这条边界。

到这里需要强调一件事：Cordis是Harness的底座，不是Agent本身。很多人看到DSH就默认它是"DeepSeek出的Coding Agent"，拿产品体验去评价它，起点就歪了。Harness管的是模型外面那套系统工程，Agent是跑在Harness上面的东西。DSH的价值不在于它今天作为产品好不好用，在于它把Harness这层该怎么做摊开了。

这里多说点。用"新范式"形容DSH我觉得不过分。LangChain那一代框架的设计目标是降低Agent开发门槛——向上封装，Chain、Tool、Memory、Retriever这些高度抽象把运行时细节藏起来，让开发者少操心。好处是上手快，代价是模型本身已经是黑盒，框架再叠一层封装黑盒，调试和优化就得穿两层不透明。

DSH反过来。它的设计目标不是降低开发门槛，而是降低Agent体验和成本优化的复杂度。插件化颗粒度拆到极细，Session Event Log记录一切事实，Agent的每一次运行都有迹可循、可回放、可分叉、可审计。黑盒越少，看得越清，优化空间越大。开发门槛确实更高——你得理解更多底层细节。但换来的是：在模型本身解释性弱的现实下，至少框架这层是透明的。

目标不同，架构就不同。LangChain范式向上封装，DSH范式向下拆开。哪个会走得更远？不知道。但都很牛批。

## 装配：一个Agent怎么组装出来的

理解DSH，不能一上来就去按照传统思维去先钻进 `agent-loop` 找入口。它启动的第一件东西是一棵Cordis插件树。

![[Image 205.webp|DeepSeek Harness 的四层 Agent 装配流程：Bundle 提供插件配置，Profile 叠加 Bundle，Patch 覆盖配置，Preset 决定 Agent 最终携带的能力]]

DeepSeek Harness 的四层 Agent 装配流程：Bundle 提供插件配置，Profile 叠加 Bundle，Patch 覆盖配置，Preset 决定 Agent 最终携带的能力

DSH的agent是通过四层配置叠出来的，准确的来讲是它先用 `Profile + Bundle + Patch` 搭好整个运行环境，再用 `Preset` 决定某个 Agent 带什么工具和提示词。前一套决定 DSH 怎么启动，后一套决定 Agent 以什么形态工作。

- Bundle：提供一组可以复用的插件和基础能力。
- Profile：选择并组合多个 Bundle，决定 DSH 以 Web、headless 等哪种形态启动。
- Patch：覆盖 Profile 组装后的具体配置，完成用户自己的定制。
- Preset：在 DSH 启动后，决定某个 Agent 携带哪些工具和提示词。

所以打开DSH的Web界面新建会话时，能选四种模式——standard、minimal、code、cordis。它们不是四个不同二进制，只是四份 `agent.cordis.yml` 配置，决定这个Agent继承哪些工具和Prompt。所以也造就了产品侧提供的四种运行模式。当然这个也是可以增减装配的。

![[Image 206.webp|DeepSeek Harness 官方 Web 界面的 Agent 模式菜单，列出标准模式、PTC 模式、极简模式和创造模式及其说明]]

DeepSeek Harness 官方 Web 界面的 Agent 模式菜单，列出标准模式、PTC 模式、极简模式和创造模式及其说明

这里边，在深层次挖掘下，官方默认Coding Agent，就是一行行插件配置拼出来的：

```
- id: tool-bash
  name: '@deepseek-ai/dsh-tool-bash'

- id: tool-fs
  name: '@deepseek-ai/dsh-tool-fs'

- id: tool-skill
  name: '@deepseek-ai/dsh-tool-skill'
```

看到这里感觉似曾相识。这不就是配置式Agent么！你能换掉的东西，和DeepSeek自己在用的东西，是同一份配置。没有内部特供版。不少框架嘴上说插件化，官方功能走内部通道，第三方走插件API，两条路。DSH不是。

这里展开说一下。其他Agent Harness里常见的MCP、Skill、Hook，DSH里全都有——但它们的存在形态完全不同。在Codex或Claude Code里，MCP、Skill、Hook是三套独立的扩展机制，各有各的配置文件、加载方式和生命周期。在DSH里，它们全部是Cordis插件：MCP Client是个插件，把外部MCP Server的工具注册到 `ctx.tools` 上；Skill Provider是个插件，负责发现和加载Skill目录；Hook Bridge也是插件，把Claude Code和Codex的 `hooks.json` 翻译成Cordis的拦截点。同一套插件生命周期，同一套装载/卸载/清理规则，不用为每种扩展机制单独写一套管理逻辑。

正在运行的会话不会因为Preset文件刚被修改就半路换工具。Preset在Agent出生前挂好，运行中锁死。空白会话可以切换，一旦产出过内容就锁住——历史里出现过的工具调用，不能突然换到一套不认识这些工具的配置上。切换本身也写进Session Log。

这套装配跟“在设置里勾几个工具”完全不是一个量级。企业可以不Fork主循环，拼出只读审计Agent、带远程沙箱的Coding Agent、无Web的自动化Agent，或者直接把默认Loop换掉做实验。

代价也明显：排错从“哪个函数错了”变成“当前Profile叠了什么Bundle、哪层Patch替换了配置、Agent属于哪个Preset、服务落在哪个Scope”。框架把扩展空间打开了，排查复杂度也跟着上来了。自由从来不免费。

## DSH的AgentLoop：Turn、Step和三种输入

具体看一个例子：修改配置项，跑测试，根据失败结果继续修。

入口先创建Agent。创建过程不是先把半成品塞进全局列表再慢慢挂工具。 `ctx.agents.create()` 会建一个局部Scope，在未发布状态下安装Preset、Persona、工具限制和权限覆盖。全部成功，Agent才进入Registry。中途失败整体回滚，外面永远不会看见一个只装了一半能力的Agent。

Agent 和 Agent Loop 在源码里是两个包。Agent负责身份、输入队列、状态和生命周期；ReactLoopAgent只是驱动它工作的默认实现。换Loop不用重造Session、Tools和Shell——“Loop Engineering”在DSH里是真的能换，不只是概念。

一次用户任务从收到到彻底完成，源码里叫Turn。Turn 里模型可能请求多次：先看文件，接着编辑，再跑测试，最后总结。每一次“组装上下文、请求模型、执行这一轮工具”叫一个 Step。一个 Turn 可以没有 Step，也可以有很多个 Step。

![[Image 207.webp|DeepSeek Harness 的 Turn 与 Step 流程：一个 Turn 包含多次模型请求，每个 Step 根据工具结果继续推进，直到模型返回最终文本]]

DeepSeek Harness 的 Turn 与 Step 流程：一个 Turn 包含多次模型请求，每个 Step 根据工具结果继续推进，直到模型返回最终文本

直接看 `packages/core/agent-loop/src/agent.ts` 里的三个方法：

```
followup(input) { this.send(input, 'next-turn', true) }
steer(input)    { this.send(input, 'next-step', true) }
inject(input)   { this.send(input, 'next-step', false) }
```

`followup()` 放到下一Turn，用户追加了一件完整任务；

`steer()` 放到下一Step，当前任务不停，后续动作换方向；

`inject()` 同样等下一 Step，但不主动唤醒Agent，适合后台任务完成后补一条上下文。

三个方法只差两个参数，管的是“什么时候让模型看见这条消息”。

![[Image 208.webp|followup、steer 和 inject 三种输入路由对比：进入 next-turn 或 next-step，以及是否主动唤醒 Agent]]

followup、steer 和 inject 三种输入路由对比：进入 next-turn 或 next-step，以及是否主动唤醒 Agent

每个 Step 开始时，Loop 先拿走等待中的 Step 消息，再至多拿一条 Turn 消息。这样，后台结果和用户 Steering 能尽快进入下一次模型请求，而多个完整任务仍然按顺序处理。输入的插入、认领和取消都写 Session Event，程序如果死在“收到消息”和“发起模型请求”之间，恢复后不会凭空吞掉用户一句话。

Loop从System Prompt Registry取稳定提示段和工具定义，从Session Log投影历史，再把当前运行环境、Sandbox和Approval状态作为Runtime Context加进去。模型返回普通文本，Turn可能结束；返回工具调用，Tools Runtime接手执行，结果记入日志，Loop再开下一Step。

用户点停止时，AbortSignal沿模型流、工具和子进程向下传。 `dispose()` 也不是发完信号就宣布胜利，它要等活动真正静止。源码里反复出现quiescence——清理函数返回以后，别让上一个Agent的Bash还在后台偷偷烧机器。

## 上下文：谁进来，什么时候进来

System Prompt在DSH里不是一条写死的长文本。插件向Registry注册带名称和顺序的Section，Agent Scope可以覆盖全局同名Section；模型名、工作目录这类值通过变量插值进入文本。工具Schema和Prompt Section在同一次Assembly里收集——“告诉模型它能做什么”和“真的给了哪些工具”必须对齐。这一点其实和claudecode源码设计原理一样，都是动态拷贝，变量替换。

每个Step都重新Assembly，但不等于每次都把系统提示搅成新花样。Section顺序、工具顺序、Persona和变量渲染保持一致时，前缀稳定，模型侧还能复用KV Cache。插件临时改变工具集合或提示段，缓存从第一个变化的Token开始失效。DSH的插件化不是只顾动态能力，它也在意动态以后推理成本会不会炸。

Runtime Context没有硬塞回System Prompt。Sandbox模式、Approval状态、工作区规则这些动态事实，以user-role snapshot进入耐久历史。System Prompt保持稳定，变化事实追加在后面。后一次请求能重建当时的环境，已有前缀也不用作废。看到这里明白没，网上很多用户说的缓存高的工程点上其实就是这儿！因为前缀尽量不变、利于缓存复用。

![[Image 209.webp|DeepSeek Harness 每个 Step 的上下文组装：稳定的 System Prompt Registry 与动态 Runtime Context 分别进入模型请求，动态事实同时写入 Session Event Log]]

DeepSeek Harness 每个 Step 的上下文组装：稳定的 System Prompt Registry 与动态 Runtime Context 分别进入模型请求，动态事实同时写入 Session Event Log

项目规则由 `agent-instructions` 处理。它读取用户全局与项目目录中的 `AGENTS.md` 、 `CLAUDE.md` 及本地覆盖文件，越具体的目录规则优先保留。规则有明确字节预算：先丢更宽泛的文件，最后才截断最具体的那份，还会把省略情况告诉模型。不会把整棵仓库规则无限塞进上下文，也不会让大模型先总结一遍再决定该听什么。

Skill走按需加载。模型先看到名称和有上限的简介，需要时再调用工具读取正文与资源位置。目录负责发现，正文负责执行，生命周期分开。Agent装了上百份Skill，不代表每次请求都要背上百份md。道理很简单：目录常驻，正文按需。这一点跟其他的设计一样。

## 会话数据：messages 数组为什么不够

很多Agent项目把 `messages` 数组当数据库。用户说什么、模型答什么、工具返回什么，全塞进去。等要恢复、Fork、压缩上下文、做UI流式回放时，就在一个数组上疯狂打补丁。做agent的同学是不是都是这么玩的。

DSH反过来：Session先保存发生过的事实，给模型的Messages只是其中一种投影。

日志里有 `turn/start` 、 `step/start` 、 `user/message` 、 `assistant/chunk` 、 `assistant/message` 、 `tool/call` 、 `tool/result` ，还有Inbox变化、Sandbox模式切换、Preset选择、Compaction等事件。流式Chunk和最终消息都保留——UI可以还原流式过程，模型历史只需要稳定的最终结果。

`deriveMessages()` 从这条日志生成下一次请求需要的消息。Web Transcript、遥测、恢复、Fork 也各自从同一条流投影。谁都不应该偷偷维护一份“差不多同步”的第二真相。

![[Image 210.webp|DeepSeek Harness 的 Session Event Log：用户消息、模型消息、工具调用与结果形成追加式事实流，再投影成模型消息、Web 界面、恢复、Fork 和遥测]]

DeepSeek Harness 的 Session Event Log：用户消息、模型消息、工具调用与结果形成追加式事实流，再投影成模型消息、Web 界面、恢复、Fork 和遥测

DSH的铁律：模型可见的东西，必须能被日志重建。harness的中药领域之一，可观测性。

比如 Sandbox 模式发生变化，不能只改内存变量，然后在下一次请求里告诉模型“现在能写文件了”。模式切换本身要进入 Session，下一次 Runtime Context 再从日志推导。程序重启以后，模型看到的权限事实才不会和当时发生过的历史对不上。

工具调用尤其需要这种较真。系统先记录 `tool/call` ，再真正执行。命令已经启动、结果还没写入、进程崩了——恢复时不能把这次调用删掉，更不能无脑重跑。写文件、发请求、创建资源都可能已经产生副作用。更诚实的状态是：这件事开始过，结果未知，需要检查现场。

Compaction不是把旧消息删了了事。摘要生成后，Session记录一次Surface Replacement：哪段旧表面被新摘要替换。原始事实仍在日志里，模型投影改用压缩后的表面，上下文长度控制了，审计和恢复依据也还在。

内存里的Session还要落到Persistence。DSH把持久化做成能力面，JSONL和SQLite Provider共用一套写入协调器。事件先进批次， `flush` 是耐久屏障； `append` 返回时这个批次必须已经持久化。批处理窗口有最大等待时间，避免每个流式Chunk都单独刷盘，也不会因为后续事件持续到来就把截止时间无限后拖。

冷加载和热加载语义不一样。正在运行的 Session 如果 Turn 还开着，读取方不能擅自给它补“中断”结尾；冷会话最后一个 Turn 没闭合，恢复会保留已经写下的调用，再追加合成的错误结果和 `turn/end {interrupted}` 。日志中间损坏直接拒绝，只有从未完整写成的尾部碎片可以丢掉。会话格式目前仍是预发布版本，官方没承诺广泛向后兼容——设计严谨和格式稳定是两回事。

上下文质量取决于哪些事实稳定、哪些按需加载、哪些旧内容能被摘要替代，不取决于往Prompt里塞了多少东西。DSH里Prompt Registry、Skill目录、工具结果裁剪和Compaction各管一段，没搞"万能上下文优化器"。能用确定性规则做的先做掉，让大模型判断每条日志该不该保留是浪费。

## 工具：一条调用为什么要走这么长的链

模型产生工具调用后，DSH先把 `tool/call` 写进Session，然后进入 `tools/pre-execute` ，Hooks、权限和Sandbox在这里决定允许、拒绝或请求确认。Approval完成后还要过单调Guard——Guard一旦拒绝，后面的插件不能翻案。

![[Image 211.webp|DeepSeek Harness 工具执行流水线：先记录 tool/call，再经过 pre-execute、approval、guard、execute、post-execute、finalize 和通知，最后记录 tool/result]]

DeepSeek Harness 工具执行流水线：先记录 tool/call，再经过 pre-execute、approval、guard、execute、post-execute、finalize 和通知，最后记录 tool/result

允许执行的调用进入 `tools/execute` Waterfall。超时、重试、指标这类包住真实执行的逻辑放在这里。结果出来后， `tools/post-execute` 可以阻断、替换或添加上下文；注册表做规范化，工具自己的 `finalizeContent` 执行最后约束；冻结后的权威结果先发 `tools/result` 通知，再写成唯一的 `tool/result` Session Event。

同一Step里模型可以一次给出多个工具调用。DSH允许并行执行，但按模型给出的ordinal固定结果顺序。并行解决等待时间，稳定序号解决回放和Provider消息协议——不能因为第二个命令跑得快就让模型看到一份顺序随机的历史。取消也要等整批工具收口，已发生的结果照样记账，没发生的才返回取消。

工具还提前声明UI呈现意图。普通结果、终端输出、文件Diff需要不同卡片， `presentCall` 和 `presentResult` 只根据参数与结果生成展示数据，不把 UI 状态藏进执行函数。Web 可以画专用卡片，ACP 和 SDK 仍拿到同一个权威结果。一个工具“执行了什么”和“用户怎样看见它”有关联，却不该绑成一坨。

第一眼看到这条链确实觉得重。但审批、权限、超时、日志，哪个砍掉你敢上生产？全塞进Bash工具内部倒是简单，别的工具又得复制一遍。

DSH把能力拆成Definition、Provider、Consumer三个角色。

Definition规定一项能力长什么样；Provider真正实现它；模型工具是Consumer，把能力翻译成模型能调用的Schema。按官方架构，真正定义执行世界的是Filesystem Provider和Subprocess Provider——它们共享同一个执行环境，Shell、PTY、LSP是上层消费者。把这两个Provider指向远程沙箱，上面的Bash、终端、LSP就跟着一起过去了，不用每个消费者单独适配。

![[Image 212.webp|DeepSeek Harness 能力接缝的三个角色：Definition 定义能力，Provider 实现能力，Consumer 把能力接到 Shell、PTY、LSP 或模型工具]]

DeepSeek Harness 能力接缝的三个角色：Definition 定义能力，Provider 实现能力，Consumer 把能力接到 Shell、PTY、LSP 或模型工具

文件系统、Shell、Subprocess、Terminal分开也是这个原因。Subprocess管进程树和信号，Shell管命令语义，Terminal管持久PTY，FS管读写和编辑策略。经常一起出现，但不是一个生命周期。

Sandbox要注意。当前 `SandboxMode` 的vocabulary是 `read-only` 、 `workspace-write` 、 `danger-full-access` ，它约束的是文件副作用。官方 README 明确写着：网络和进程策略不在这个词表里。也就是说，“文件只读”不自动等于“不能联网”或“不能启动进程”。这是一条文件策略，不是一只把所有风险都扣住的宇宙级玻璃罩。

权限也有软硬两层。System Prompt 告诉模型不要做什么，是软约束；Guard、文件策略、进程隔离和操作系统机制才是硬约束。软约束帮助模型少撞墙，硬约束负责模型真撞上去时墙还在。把“提示词里写了禁止”当安全设计，约束力很有限。

PTC Code Mode走同一套Tools Runtime。模型不逐个发原生工具调用，而是生成一段TypeScript风格的程序，通过 `run_code` 传输组合子调用。能写条件、循环和并发，减少模型来回往返；每个子调用仍然过权限、Guard、日志和结果规范化，不是另开后门。

## 子Agent：消息和共享队列式的通信机制

演示Subagent其实很简单——主Agent调一个函数，等子Agent返回。但产品要让子Agent后台运行、稍后继续、能被中断、重启后还能找到，事情就复杂了。像ClaudeCode的Mail Box通信机制都不能很好的处理主子和子子的通信，issue有一箩筐。

在 DSH 中，子 Agent 不会共享父 Agent 的消息数组。每个子 Agent 都有独立的 Session、Inbox 和运行状态，父子之间通过消息与结算通知通信。

整体流程大概如下：

父 Agent 每启动一次子 Agent 工作，都会创建一个 Activation，也就是一次子 Agent 的运行实例。它可以通过 Spawn 创建一个没有历史记录的新 Agent，也可以通过 Fork 复制父 Session 某个时间点以前的历史，让子 Agent 带着已有上下文继续工作。

子 Agent 启动时，会通过 这个函数 `composeFrom()` 绑定父 Agent 当前正在使用的 Preset 版本。这里的Preset继承的不只是提示词，还包括工具和其他 Agent 配置。它不会重新读取磁盘上最新的 Preset，否则父 Agent 工作到一半时配置文件刚好发生变化，子 Agent 就可能拿到另一套工具，父子的上下文也会对不上。

Spawn 和 Fork 决定子 Agent 从哪里开始；one-shot 和 continuable 决定它完成当前任务后是否还能继续。One-shot 是一次性任务，结果返回后结束；continuable 完成当前任务后进入空闲状态，父 Agent 后面还可以继续给它发送 follow-up。

任务完成后，子 Agent 会向父 Agent发送 Settlement，也就是结算通知。父 Agent 正忙时，通知先进入 Inbox；父 Agent 空闲时，这条通知可以唤醒下一轮处理。

![[Image 213.webp|DeepSeek Harness 子 Agent 生命周期：父 Agent 通过 Spawn 或 Fork 创建有独立 Session、Scope 和 Preset 记录的孩子，再以 one-shot 或 continuable Activation 运行并结算回父 Inbox]]

DeepSeek Harness 子 Agent 生命周期：父 Agent 通过 Spawn 或 Fork 创建有独立 Session、Scope 和 Preset 记录的孩子，再以 one-shot 或 continuable Activation 运行并结算回父 Inbox

DSH 还把几种看起来都像“后台任务”的能力分开了：Jobs 负责普通后台作业的收集与停止，Workflow 负责多步骤编排，Subagent 负责子 Agent 的身份、权限、消息和生命周期。它们都可以异步运行，但所有权并不相同。

子 Agent 默认也不能自行扩大权限。它出生时拿到的授权范围是固定的，需要更高权限时只能把限制报告给父 Agent，再由父 Agent 或用户决定。后台 Agent 没有人一直守着审批弹窗，允许它无限等待或者自行提权都不可靠。

当然，这套机制还没有达到分布式 Agent 平台的程度。运行中的 Activation 和所有权关系目前仍主要保存在进程内；多进程共享持久化库时，还缺少耐久邮箱和任务租约；消息被接收但尚未写入日志时，进程崩溃仍可能造成丢失；远程 ACP 子 Agent 目前也更偏向一次性执行。

DSH 已经把多 Agent抽象的概念换成了明确对象：Session、Activation、Owner、Inbox、Settlement、Quiescence。对象有了，机制也连起来了；不过目前跨进程可靠性还没跟上，也是目前的通病。

这个阶段比“一个按钮拉起五个Agent”更加透明，也离真正的分布式Agent平台还有点工程距离。能不能走到那一步，交给时间。

## 入口：多个客户端，一套执行

再说入口，不是重点，简单提一下。DSH当前提供Web UI、headless、ACP自动化入口、JSON-RPC与Python SDK。共享Agent、Tools和Session，不各自维护一套循环。目前还是初级形态。

Web端通过Typert生成的类型图和RPC Gateway访问Host服务，浏览器负责会话界面、工具卡片、审批和动态UI；真正的工具执行与事实日志仍在Host。

ACP面向编辑器或自动化客户端，把外部协议操作映射到同一组Agent生命周期，不是把Web页面套个无头浏览器。JSON-RPC SDK用一行一个Frame的协议驱动会话，Python SDK再包装这条线，适合评测和外部系统接入。

这种统一很重要。一个工具在Web里显示卡片，在ACP里变成协议事件，在SDK里变成结构化结果——但执行政策和Session事实不能各写一份。否则同一条命令从网页发起要审批，从SDK发起却直接跑，安全边界变成入口抽奖。

目前这些入口成熟度不齐。Web仍是开发者预览形态，ACP与SDK的取消、远程子Agent和部分交互能力还有缺口。底层统一保证问题有一个归属，但不会自动把每个客户端磨成成熟产品。统一是起点，不是终点。

## 自演进：Agent现场给自己加能力

DSH有一个很不一样的能力：让Agent检查自己正在运行的Cordis——有哪些服务、工具、事件和UI插槽，然后定义一份动态Package，用户允许后挂进当前进程。Bash负责使用现成能力，这套机制允许Agent临时改变能力集合。这就是DSH最大的生态靓点。

动态Package可以只有Host Half，也可以同时带Browser Half。Host Half注册工具、监听事件、提供服务；Browser Half往Web UI的Slot里挂组件。Agent不只是使用固定工具，它能看见运行时的扩展接口，现场补一个临时工具或界面，再继续任务。

有点自进化软件的味道了。但在源码中，管得相当克制，以后不知道会不会奔放起来。

`cordis_define` 只做语法检查和登记，不马上执行。 `cordis_run` 才真正启动Host Half；有Browser Half时还要由页面明确允许。 `cordis_stop` 撤销运行效果， `cordis_undefine` 再删除定义。注册走Cordis Effect，停止时工具、监听和UI能一起收回。

![[Image 214.webp|DeepSeek Harness 自修改生命周期：cordis_define 只登记，cordis_run 挂载 Host 或 Browser Half，cordis_stop 撤销效果，cordis_undefine 删除定义]]

DeepSeek Harness 自修改生命周期：cordis\_define 只登记，cordis\_run 挂载 Host 或 Browser Half，cordis\_stop 撤销效果，cordis\_undefine 删除定义

动态定义只存在进程内存。重启全丢，哈哈哈，给你玩但不给你存档。不会自动写Plugin文件，不会修改 `cordis.yml` ，不会悄悄把一次试验升级成永久配置。要保留就回到正常开发流程写一个正式插件。

边界也要讲清。Host Half跑在 `node:vm` 中，官方文档明确说这不是恶意代码安全边界。同步执行有时间上限，异步代码可以逃过；可用服务又能影响真实运行时。工具文档的建议很直接：把这项能力当Bash权限来授予。

Browser Half也不是完整安全沙箱。只接受普通JavaScript函数体，没有TypeScript、JSX和import转换；客户端的API Guard更接近约束插件写法，不负责抵挡恶意代码。没有页面连接时，带Browser Half的运行请求会一直等到当前Turn被取消，目前没有超时机制。

所以不要理解成“Agent已经能无限自我升级”。DSH给的是一套可检查、可挂载、可撤销、默认不持久化的运行时实验台。自修改从科幻口号变成了具体的四步生命周期。安全边界和产品交互还没补完，但方向是对的。

## 横评：和Codex、Claude Code、Pi的架构差异

我看了一圈围绕DSH的讨论，很多人夸它的人在夸速度快、缓存命中率高，这一点我也认同，缓存能力确实依赖模型和API侧，但Harness的上下文策略和前缀稳定性也直接影响命中率，源码中清晰可见的设计，其次就是深度适配的DeepSeek 模型，官方源头，速度那是嗷嗷叫。骂它的人呢也不少，骂界面简陋、体验不好用。。。不过大哥，预览版，还要啥自行车。这是拿成熟产品的标准去要求一个裸奔的框架。还有一批人直接拉功能清单：谁有Skill，谁有MCP，谁有Subagent。更有用的问题：模型循环、状态、工具和权限归谁拥有，外部开发者能改到哪里。

以下是和主流和高度相似的工具的比较：

![[Image 215.webp|Claude Code、Codex、Pi 和 DeepSeek Harness 的运行时所有权对比：从官方产品内核集中拥有到外部可组合的 Cordis 插件树]]

Claude Code、Codex、Pi 和 DeepSeek Harness 的运行时所有权对比：从官方产品内核集中拥有到外部可组合的 Cordis 插件树

### 1\. Codex：产品内核集中

好用，今年最好的Agent产品，桌面AGI。Codex 的开源实现并不“封闭”。在codex的cli源码中， `codex-core` 集中拥有业务逻辑、会话和Sandbox Policy，app-server对外提供Thread、Turn、Item、Approval、Skill、Plugin、Hook、MCP等接口。强产品+强模型。更容易保证CLI、IDE、桌面和云端体验一致。所以他的感觉就是把Agent做到拥有极致的护城河。它的Harness可以叫做Codex。

### 2\. Claude Code：成熟产品加扩展面

Claude Code的核心循环目前最新版本不知道有没有更新，以前泄露的版本中，可以看到就是一个query的while循环。拥有完整的Harness运行时的组件，Session、Subagent、Hooks、Skills、MCP、权限与多种运行形态都已覆盖。跟codex一样，同样是强产品内核+强模型，技术路线都一样。

### 3\. Pi：轻量、直接、好嵌入

说到这个，我本人是Pi的深度玩家，目前负责的几个智能体产品都是以Pi SDK为核心展开，运行也快一年了，只能说稳如老狗。好多朋友看到DSH这种设计，第一时间想到Pi的极简设计。但是Pi更接近轻量可塑的Coding Agent。它本质执行场景，那运行时的东西本质上是从他的提示词编排出来的一套流程，配合插件系统，来构成的。并没有完善的Harness的内容。所以它写代码，轻量，快速，Token成本比其他的Agent产品要少。它清晰的Extension API，能注册工具、命令、事件与UI，适合嵌入别的应用。优势是小、直接、容易改。而DSH，偏框架，所以往下再拆了一层：工具Registry、Session、Agent、Loop和能力Provider都进Cordis组合，代价是包更多、生命周期语义更重、学习成本高得多。这一点要区分。

### 4\. DSH：默认产品也走插件系统

DSH的差异在默认产品本身：官方装配也走插件系统，没有偷偷绕开。换Loop、Session Projection或Shell Provider，理论上不需要维护私有主循环。普通用户没有立刻可见的好处，对做Agent平台、企业执行环境和研究型Harness的团队很关键。

反过来，Codex和Claude Code把更多核心收在官方手里也没问题。大多数用户要的是一个今天就好用的编程助手，不想先学Profile、Bundle、Preset、Scope这些概念。集中内核更容易统一交互、指标和兼容性。架构开放度与产品完成度是两根轴，别拿一根替另一根领奖。

## 缺口：距离成熟产品还差什么

DSH目前是Developer Preview，官方明确说会有兼容性破坏。现在就拿它的API押上生产，那就有点冲动了。

目前已经做成的：默认产品由插件装配；Agent与Loop分离；Preset能改变每个Agent的工具和Prompt；Session Event Log接住上下文、恢复、Fork和UI投影；工具调用从日志、权限、执行到结果连成闭环。

拿它做二次开发、研究新Loop、接远程执行环境、拼企业内部Agent，落点都很清楚。

问题也很直接：距离“成熟通用 Coding Agent”，它还缺什么？

### 产品解释力

插件化以后，用户需要看懂当前装配、能力来源、权限拒绝和失败链路。今天很多答案在README和源码里，界面上不够直观。框架内部把责任拆得很清楚，出错时如果只弹一句tool failed，前面的设计等于白费。

### 安全边界

文件Sandbox还不是网络与进程统一策略，动态代码也不是恶意代码隔离。企业真把它接到仓库、凭据和远程环境，需要更强的OS隔离、网络出口控制、密钥引用、审计和插件信任机制。

### 耐久状态

Session主线已经扎实，但Subagent Activation、动态Package、Jobs的一部分仍依赖进程内存；跨进程租约、可靠邮箱、Attachment与大结果的生命周期还要继续统一。单机恢复能做，不等于多实例平台成立。

### Benchmark

缺一套能复现Harness贡献的评测。仓库里的 `BENCHMARK.md` 目前只说明怎样用Python SDK跑任务，没有公开结果、任务轨迹、工具权限、Token预算和失败口径。模型强不强、Harness好不好，不能只看最后一条回答。至少要固定模型版本、Preset、工具、Sandbox、最大轮数、超时、重试和完整轨迹。否则换一层Harness，分数涨了还是权限放宽了，谁也说不清。

### 默认产品

Codex和Claude Code的优势不神秘：错误解释、审批交互、终端体验、会话管理、IDE联动、任务恢复，这些碎活做久了就是护城河。DSH选了更开放的底层路线，但无聊又要命的产品活仍然得干完。架构不会替用户按下那个更顺手的确认按钮。

## 小结

首先得搞清楚一件事：Harness不等于Agent。DSH不是"DeepSeek出的Claude Code"，拿产品体验去评价它方向就错了。说它难用简陋的人，是在用成熟产品标准衡量一个开发框架的Developer Preview。说它快、缓存命中率高的人，只看到了一半——模型和API服务决定能不能缓存，但Harness决定请求前缀稳不稳定、能不能命中。DSH在这方面确实有设计，但把功劳全归Harness或全归模型，都不准确。

作为Agent开发者造车的底座，它的选择很明确：模型、工具、状态、权限、执行环境和UI，全回到同一套插件生命周期里，默认产品自己也遵守这套规则。

回头看，DSH真正在做的事是把模型外面的系统工程——任务怎么继续、工具怎么执行、状态怎么留、权限怎么管——落成一套能运行、能替换、也会暴露自身缺口的实现。

最有意思的一点：它愿意让“内核”失去特权。这件事能不能真正长成 Agent 插件生态，还要看兼容性、文档、分发、安全和默认产品。

这条路还很长。插件化做到这个颗粒度是颠覆性的设计，但也意味着走的是生态路线——需要时间让开发者进来、让社区长出来、让实践验证哪些抽象真的成立。生态路线从来不快。

不过。。。这可是DeepSeek。

最后期望，快迭代，快迭代。

下篇，拆一下Cordis。点点关注！

**版权声明：本文由AI技术博客原创，转载请注明出处。**

智能体 · 目录

作者提示: 个人观点，仅供参考