---
title: "从 Skills 到黑灯工厂，一次把开发交给 AI 的实验"
source: "https://mp.weixin.qq.com/s/pn__FFm0WCKxaTJWflrccg"
author:
  - "[[老丁888]]"
published:
created: 2026-08-24
description: "他搭的东西叫 AI Dark Factory（AI 黑灯工厂）。名字带点玩笑，意思却很直接，一个会自己出货代码的仓库。他把这个实验完整开源在了 GitHub 上，仓库名叫 dark-factory-experiment。网址请向下翻到本文结尾。"
tags:
  - "AI开发平台"
---
老丁888 钉子之家 *2026年8月19日 06:56*

## 从 Skills 到黑灯工厂，一次把开发交给 AI 的实验

> 深夜，一个 GitHub 仓库还在自己忙活。有人新开了一个 issue，几个小时后，新功能部署上线，全程没有人在中间盯。这是 Cole Medin 的一个构想，他从自己开源的 30 多个 skills 出发，给它起名叫 AI 黑灯工厂，并且动手搭了出来，做成一个开源的小实验。

![[Image 134.webp|图片]]

这一次，他不满足于「让 AI 帮你写得更快」。他想让整个代码仓库，自己写代码、自己出货。

他搭的东西叫 **AI Dark Factory（AI 黑灯工厂）** 。名字带点玩笑，意思却很直接，一个会自己出货代码的仓库。他把这个实验完整开源在了 GitHub 上，仓库名叫 dark-factory-experiment。网址请向下翻到本文结尾。

Cole 把整座工厂的全貌画成了下面这张架构图，五个组件、三份治理文件、一条独立性的边界，都在这一页里。先看全景，再往下逐块拆解。

![[Image 135.webp|图片]]

## 一、这个名字，从八十年代的机器人车间来

「黑灯工厂」这个词不是 Cole 现编的，它有个出处。

它来自 Dan Shapiro（Glowforge 的创始人），灵感是 FANUC 在 1980 年代那种「黑灯工厂」。那种工厂里，机器人 24 小时造机器人，车间里连灯都不用开，因为没有一个人类工人在场。把这个概念搬到软件上，就是一句很朴素的话， **规格说明进去，软件出来** 。

![[Image 136.webp|图片]]

Cole 给黑灯工厂下的定义，也是这个意思。一个会自己出货代码的仓库。开发者只做一件事，把下一步想构建的东西写成规格说明（spec，一份讲清楚「要做什么」的文档）送进去，工厂吐出来的，是已经经过完整评审和验证、可以直接上线的代码。

![[Image 137.webp|图片]]

为什么这件事现在才变得靠谱？Cole 给了三个同时发生的理由。大语言模型在变强，编码 agent 在变强，围绕 agent 搭的 harness 也在变强。三者叠在一起，「放手让 agent 去干」这件事，才对一大类工作变得现实。

## 二、先看成品，他连一行产出代码都没看

光讲道理不够，Cole 先端出了一个已经跑通的成品。

这个成品，就是 dark-factory-experiment 这个仓库里维护的那个应用，一个深色主题的 AI 聊天应用。读者可以向它问问题，它会去检索一位创作者的全部视频，从转录文本里找出相关段落，给出带引用来源的回答。这个应用的网址是 chat.dynamist.ai，现在就能打开用。

![[Image 138.webp|图片]]

整个构建过程，他「连它产出的一行代码都没看过」。

上一篇文章那套 skills，本质还是「AI 帮你写得更快」，人仍然守在环里做规划、做验证。而这座黑灯工厂，直接跳到了「人基本不用在场」。

不过 Cole 紧接着划了一条线。他说，这 **不** 是 vibe coding（凭感觉把代码库整个交给 AI 的做法）。

![[Image 139.webp|图片]]

表面上看，黑灯工厂也是把代码库交给 agent 管理。真正的区别，在于工程精力花在了哪里。

vibe coding 把赌注押在「模型这一刻发挥得好不好」上。黑灯工厂把大量工程精力 **前置** ，花在搭 harness 上。有了这套约束骨架，agent 才能好好规划，并且由一个独立的 agent 去批判主构建者的产出。

一句话，可靠性靠提前搭建，不靠那一刻的运气。

Cole 也坦白交代了成本。这套系统他花了数百万 token、好几个月去搭，踩遍了所有坑。

> **他也不承诺这座工厂能为任何代码库产出生产级代码，很多事还没到那一步。**

> **这座工厂只处理能够被机器验证对错的代码。依赖手感、审美、可读性的工作，不在其范围之内；认证、支付这类影响面大、一旦出错难以承受的功能，同样被排除在外。**

但它是一个起点，一扇通往 AI 编码未来的窗。

## 三、五级自主，Cole 停在了第四级

要把这件事讲清楚，得先借一张地图。

Cole 引用了 Dan Shapiro 的一篇文章，用车辆做类比，把「AI 替开发者干多少活」分成了清晰的五档。

> Dan Shapiro《The Five Levels: from Spicy Autocomplete to the Dark Factory》，danshapiro.com，2026 年 1 月。

![[Image 140.webp|图片]]

五档从低到高，这样排。

- **第零层，手动挡**
	　最手动，AI 只做「火辣补全」（spicy autocomplete，Cole 的自嘲说法），大部分代码还是开发者自己写。
- **第一、第二层，结对程序员**
	　AI 像结对同事，会自主生成一些样板代码。
- **第三层，人当经理**
	　编码 agent 写大部分代码，开发者基本放手，但仍深度在环，规划和最终验证亲自下场。
- **第四、第五层，连方向盘都没有**
	　只剩一个控制台给高层方向，比如一份 PRD。开发者定目的地，agent 自己弄明白怎么到、怎么上线。

这里有个诚实到值得单独拎出来的细节。在 README 里，Cole 白纸黑字写着，这座工厂跑在 **第四级，不是第五级** 。

第五级意味着工厂连 issue 都自己写，而 Cole 的工厂不写自己的任务单。人类仍然做两件事，提交 issue，以及发布新版本。这座黑灯工厂里，其实还留着两盏灯，一盏在入口，一盏在出口。

他为什么这么克制？后面讲「指引层」的时候会看到，他给这套系统划了一片工厂永远碰不得的边界。

## 四、4 条命令，从零搭一座黑灯工厂

讲完理念，进入落地环节。本节给出从零搭建一座黑灯工厂的具体步骤与命令，均来自 Cole 开源的仓库，读者可照此复现。

![[Image 141.webp|图片]]

Cole 把搭建黑灯工厂的整套方法封装成一个 skill，并把它与全部 AI 编码工作流所用的 skill 一起，放在一个公开仓库里，地址是 github.com/coleam00/skills。这套 skill 里包含他反复讲的 PIV 循环（计划、实现、验证）、规划、worktree 与若干元技能。

**第一步，安装 skill。** 若使用 Claude Code，在对话中执行下面两条命令，即可把全部 32 个 skill 作为只读托管插件装进来。

```
/plugin marketplace add coleam00/skills
 /plugin install skills@cole-medin
```

装好后，skill 以命名空间形式调用，例如 `/skills:piv-implement` 。后续更新用 `/plugin marketplace update` 。若第一条命令报 `Permission denied (publickey)` ，改用 HTTPS 地址 `https://github.com/coleam00/skills.git` 重试即可。

若使用其他编码 agent（如 Pi、Codex），则把 agent 指向上述仓库地址，让它自行安装并适配自身环境。

**第二步，安装 Archon（可选）。** Archon 是 Cole 开源的 harness 引擎，负责把一个个编码 agent 的会话编排成确定性的工作流。至于底层跑哪个编码 agent，build-dark-factory 并不限定，Claude Code、Codex、Cline 等都可用；Cole 个人用 Archon 做这套编排层，读者若也想用，按下面命令安装即可。

```
npx skills add coleam00/Archon

 # 或走 CLI 全量安装
 curl -fsSL https://archon.diy/install | bash
 archon init
```

**第三步，克隆参考工厂。** 作者的原始实验仓库公开了全部工作流文件，可直接克隆作为参照。

```
git clone https://github.com/coleam00/dark-factory-experiment.git
```

工作流文件位于仓库的 `.archon/workflows/` 目录，任务状态用 GitHub 标签管理，这两点在后面章节展开。

**第四步，调用 skill，启动采访。** 真正需要准备的只有一份 PRD。第一步装好的 `build-dark-factory` 无需再复制，直接在 Claude Code 里调用它，把 PRD 的路径作为参数传进去。

![[Image 142.webp|图片]]
```
/build-dark-factory product.prd.md
```

skill 收到 PRD 后，先不急着写代码，会先抛出一连串问题，问清三件事，包括想要的自主度、偏好的工作流与应用验证策略。采访结束，skill 再按回答把整座工厂建进仓库。

![[Image 143.webp|图片]]

这套「先问清再开工」的设计，是黑灯工厂区别于普通脚本的关键。它拒绝通用模板，转而搭一套贴合开发者本人习惯的 harness，方便日后继续演进。

**常用命令。** 若采用了 Archon，工厂建好后用它查看与运行工作流。

```
archon workflow list
 archon workflow run archon-fix-github-issue --branch fix/issue-10 "Fix issue #10"
```

## 五、为什么用游戏来测

讲到这里，Cole 先展示了一样东西，一个用黑灯工厂建出来的塔防小游戏。

![[Image 144.webp|图片]]

敌人开始涌进来，可以用武器攻击，有音效，能升级属性、进入下一波。Cole 自己评价，这是个挺好玩的游戏。

为什么偏偏是游戏？他的解释很有意思。

> **测试黑灯工厂，需要一个能不断加难度的载体。游戏恰好满足这一点，想压极限就加功能。而聊天应用不行，功能加多了会「功能臃肿」。这个细节本身，就是一个关于「如何验证系统」的方法论。**

## 六、底层三层，谁是手、谁的大脑

讲完现象，Cole 抛出一张架构图，开始拆底层机制。这张图是整条视频的主线。

![[Image 145.webp|图片]]

读完 README 会看清，这座工厂底下其实叠着三层东西，每一层干的事都不一样。

第一层是 **harness，Archon** 。这是整个实验能跑起来的前提。Archon 是 Cole 自己写的开源工作流引擎，它能把一个个编码 agent 的会话，用确定性的步骤串成一条端到端的流程，跑脚本、调用 gh、解析输出、按结果分叉。黑灯工厂那套「先分诊这些 issue，再实现这一个，再验证 PR，再合并」的逻辑，在 Archon 里就是 `.archon/workflows/` 目录下的几个工作流文件。Cole 的说法很实在，没有 Archon 这种东西，人要么一步一提示地去戳 agent，要么就得写一个又大又脆的脚本把它们包起来。Archon 干的事，是把「AI 有时能做到」变成「工厂每隔几小时自己就能做到」。

第二层是 **编码 agent，Claude Code** 。在每个 AI 节点里，Archon 拉起一个 Claude Code 当 agent。Claude Code 握着那些工具，改文件、跑 bash、调 gh、抓网页，跑完整个循环，执行提示词要它干的活。

第三层是 **模型，Claude Sonnet，便宜节点用 Haiku** 。哪个节点用哪个模型，是逐个工作流节点配的，也就是说，路由是一个配置决定，不是工厂的固有属性。Claude Code 是包着模型的那层壳，模型才是真正在推理、在写代码的大脑。

Cole 在这里给了一个十分锐利的判断，模型路由是全系统里成本最低的杠杆。

因为 agent 和模型都是可以换的零件，而它们外面的那套管道是不动的。他在实验的不同阶段，让工厂跑过 MiniMax M2.7，也跑过 Kimi K2.6（通过 Pi 这个 agent）。现在 `.archon/workflows/` 里声明的是 claude 这个 provider，推理节点用 Sonnet，便宜的抽取节点用 Haiku。换模型的时候，设计里的其他东西一点不动。这恰恰是重点，能被替换的零件，和不能被替换的管道，得分清楚。

## 七、一个每半小时醒一次的哑巴调度器

那么多 spec 排队进来，谁来管？答案是自动化，而且是一套刻意做笨的自动化。

Cole 用一个 cron 任务每 30 分钟触发一次调度器。这个调度器不在这仓库里，它放在 VPS 上，是一个大约 100 行的 bash 脚本，不保存自己的任何状态，也不让任何大模型来决定跑什么。

![[Image 146.webp|图片]]

这套调度器刻意做笨，源于 README 里记录的一次教训。早先的版本里，Cole 让一个模型决定派发什么任务，结果模型「幻听」出一些根本不存在的工作去执行。他随即改为纯 bash，让调度器只读 GitHub 上的标签和状态，最多并行开四个工作流，按固定优先级行动，先修 PR，再验证 PR，再实现 issue，最后分诊。

> **这套调度脚本本身没有开源，README 只给了它的行为规格。读者照着规格，或跟随 build-dark-factory 这个 skill 的引导，都能把这层调度重建出来。**

这里的关键，是标签就是状态机。工厂自己不记状态，它读 GitHub 的标签来决策。

![[Image 147.webp|图片]]

issue 这条线是这样走的， `factory:triaging` （待分诊）到 `factory:accepted` （已接受）到 `factory:in-progress` （进行中），最后要么开出 PR，要么 `factory:rejected` （拒绝，附上理由）。

PR 这条线是， `factory:implementing` （实现中）到 `factory:needs-review` （待评审）到 `factory:approved` （自动合并），或者 `factory:needs-fix` （要修，最多修两次），再不行就升级成 `factory:needs-human` （需要人）。

分诊还会给每个接受的 issue 打上优先级，critical、high、medium、low，让调度器先挑影响最大的活干。

并行度是一个可以拧的旋钮。Cole 最多开四个并行任务，因为并行太多会很快撞上速率限制。这是成本与效率之间的一个现实权衡。

## 八、让 agent 别跑偏的三份治理文件

到这里，视频进入它自认「压轴」的部分。第一块是指引层，也就是让 agent 在完全自主时仍按开发者意图行事的那套东西。它由三个核心文件构成。

![[Image 148.webp|图片]]

第一个文件是 **global rules（全局规则）** ，在仓库里叫 CLAUDE.md，记录开发者总想让 agent 遵守的约束与约定，无论是否在黑灯工厂里工作都适用。

第二个文件是 **factory rules（工厂规则）** ，在仓库里叫 FACTORY\_RULES.md，是 agent 在更自主运行、尤其在黑灯工厂里才遵守的边界，而且更严格。Cole 举的例子是「一次只处理一个 bite-size（一口大小）的任务」。为什么？因为当 agent 要自查、而人完全不在环时，任务一旦太大，可靠性就会下滑。

第三个文件是 **mission.md** ，在仓库里叫 MISSION.md，内容很多源自 PRD，写的是应用的目标，以及更重要的、明确 out of scope（超出范围）的东西。

这三份文件，Cole 管它们叫 governance files（治理文件）。它们是这片系统的宪法，而宪法有一条铁律，工厂永远不能改它自己。

这个设计催生了一个反直觉的机制，agent 有权拒绝。Cole 说，他不希望把喂进来的 spec 当作不容置疑的真理，他想要一个机制，让编码 agent 反过来纠正开发者。有时候 agent 会对着一个 spec 说，这超出应用范围了，或者这太复杂，得先拆开。这套「允许拒绝」的机制，等于给自主系统装了一个刹车。

比刹车更狠的，是他给工厂画的那片禁区。认证、流量控制、部署配置，再加上那三份治理文件，这几样东西任何一个 PR 只要碰了，会在评估其他任何东西之前就被自动打回。Cole 对这句话的分量很清醒，一个自主系统有多可信，取决于它对自己有哪些事永远改不了。

## 九、最硬核的一块，盲测场景

视频的重头戏，是验证 harness。Cole 坦言，这是整个黑灯工厂里「最酷也最复杂」的部分。

![[Image 149.webp|图片]]

工厂里其实有两个 agent 在协作，一个 **builder（构建者）** ，一个 **validator（验证者）** 。

为什么需要两个？因为编码 agent 在实现过程中会积累大量偏见，它会对刚写的东西心软。所以需要一个完全独立的评审，用挑剔的眼光看待产物，而且这个评审不知道代码怎么实现的，也不知道最初的计划是什么。

于是有了 Cole 最得意的发明， **holdout scenarios（盲测场景）** 。

它的思路是这样的。开工之前，先抛开所有实现细节，只从应用的高层问一句，成功到底长什么样？把这套「成功标准」写成一组测试场景。关键在最后一步，这组场景 builder 永远看不到。看不到，它就无法为了通过场景而专门设计应用，也就无法「挟持」验收标准。

README 里把这个模式点名归功给了 StrongDM 的「holdout」做法。Cole 还提到另外几位前人，Spotify Honk、Steve Yegge 的 Gas Town，说他的这些规则，是从每一个失败过的黑灯工厂尝试里研究出来的。

把这个机制再抽象一层，就是一条原则，builder 与 validator 彻底分离。builder 不知道测试，validator 不知道计划。两个 agent 各自独立运行，合起来才让黑灯工厂可靠。

![[Image 150.webp|图片]]

Cole 说得直接，没有这套盲测，他连把黑灯工厂当实验来跑都不敢。

## 十、最后一块拼图，上线与自愈

另有一处常被忽略的细节，部署。

![[Image 151.webp|图片]]

Cole 说，如果 PR 合并后不自动上线，那搭的只是个「PR 生成器」，不是黑灯工厂。

他用的策略是 **蓝绿部署（blue-green deployment）** ，任何时刻都保留两份应用，一份对用户在线，一份待命。合并后的代码先进到 main 分支，自动部署到预览环境，由人定期把预览升成正式版本。这样用户几乎感受不到停机，而「从 spec 到上线」这条路，才算走通。

另有一个让整座工厂「活」起来的零件，藏在 README 的末尾，一个每周跑一次的回归测试工作流。

它每个星期启动一次应用，用 agent-browser 跑四个端到端的浏览器场景，模拟真实用户操作，最后合成一份报告，把发现的问题重新提成一个 GitHub issue。也就是说，工厂会自己找出自己的 bug，再把 bug 排进自己的队列。这是整件事里最像科幻的一环，它会自己出货代码，也会自己巡检。

## 十一、小结

把这条视频和这个仓库摊开，一座 AI 黑灯工厂其实由这样几块拼成。

- 一条处理每个 spec 的工作流，agent 以无界面模式自主运行
- 一个每半小时醒一次的哑巴调度器，用 GitHub 标签管理任务状态
- 三份治理文件（CLAUDE.md、FACTORY\_RULES.md、MISSION.md），划定 agent 永远碰不得的边界
- 一组验证装置（builder 与 validator 分离，外加盲测场景），挡住 agent 自欺
- 一套蓝绿部署加每周自愈巡检，让上线和找 bug 都自动完成

> **Cole 给这套系统定的位很克制。他承认黑灯工厂仍是实验性的，自己停在了第四级而非第五级，第三级才是多数人该待的地方，但同时又认定这就是 AI 编码自主度的高阶形态，是「未来的窗口」。**

回想上一篇文章那套 skills，它的核心是把流程做可重复。这一篇的黑灯工厂，是在可重复之上，进一步把「人在不在场」这件事也工程化了。两篇文章连起来，是一条清晰的进阶路线，先把单点环节交给 AI，再把整个系统交给 AI。

对想动手的读者，一个务实的起步建议是，先别急着搭一整套。先只搬一个零件，比如盲测场景。开工前把「成功长什么样」写下来，并且让构建代码的 agent 永远看不到这份标准，然后看看产出是不是更可信了。这一个零件，是整条视频里性价比最高的一课。若要直接动手，可从第四节的四条命令开始，先用 `/plugin marketplace add coleam00/skills` 把 skill 装进 Claude Code，再克隆参考仓库走一遍。

---

**附录　术语对照与来源说明**

本文基于视频《How to Build the Most Powerful System for AI Coding (Full Breakdown)》（作者 Cole Medin，时长 24 分 33 秒）及他开源的 dark-factory-experiment 仓库改写

| 术语 | 说明 |
| --- | --- |
| AI Dark Factory | AI 黑灯工厂，会自己出货代码的仓库 |
| Archon | Cole 开源的工作流引擎，harness builder |
| spec / PRD | 规格说明 / 产品需求文档 |
| harness | 给编码 agent 套的约束、流程与验证装置 |
| coding agent | 编码智能体，能读写代码、执行命令的 AI 助手 |
| headless mode | 无界面模式，agent 作为后台任务自主运行 |
| PIV 循环 | 计划（Plan）、实现（Implement）、验证（Validate） |
| CLAUDE.md / FACTORY\_RULES.md / MISSION.md | 三份治理文件 |
| builder / validator | 构建者 / 验证者，两个分离的 agent |
| holdout scenarios | 盲测场景，builder 永远看不到的验收标准 |
| blue-green deployment | 蓝绿部署，双版本并存、切换零停机 |
| RAG | 检索增强生成，从资料里检索相关内容再生成回答 |
| vibe coding | 凭感觉把代码库交给 AI 的做法 |

**素材来源** 　仓库信息取自 github.com/coleam00/dark-factory-experiment、github.com/coleam00/skills 与 github.com/coleam00/Archon。Dan Shapiro 五级框架一文见 https://www.danshapiro.com/blog/2026/01/the-five-levels-from-spicy-autocomplete-to-the-software-factory/。

#AI编程 #ClaudeCode #AIAgent #智能体 #自动化开发 #Archon #GitHub #AI编码 #编程效率 #开发工具

**微信扫一扫赞赏作者**

AI观点 · 目录