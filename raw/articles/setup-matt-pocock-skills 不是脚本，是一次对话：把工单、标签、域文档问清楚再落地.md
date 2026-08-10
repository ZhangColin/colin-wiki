---
title: "setup-matt-pocock-skills 不是脚本，是一次对话：把工单、标签、域文档问清楚再落地"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491618&idx=1&sn=c51628d09468e0432c0e4b264efd2fb2&chksm=cf405574f837dc62e8b75dc793eca7d414967428d045895bb5b193fd7cc188921df421a7e137&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-10
description:
tags:
---
运维有术 术哥无界 *2026年8月6日 08:30*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *188* 篇，AI 编程最佳实战「2026」系列第 *67* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

![[Image 96.webp|setup 信息图封面：工单、标签、域文档一次落地]]

setup 信息图封面：工单、标签、域文档一次落地

这个系列前面讲了那么多 skill 怎么调用，却一直绕着一个前提打转：每个 skill 都假设你的仓库已经装备好了。有工单系统、有 triage 标签、有 `CONTEXT.md` 和 ADR 的存放约定。但这些配置是谁装的？没人讲。

如果你刚 clone 下一个仓库，想把这套 engineering 流程全部跑起来，你会卡在第一步：到底先装什么、问什么、留下什么文件，才算"从零到能用"。

今天这篇就补上这一课。主角是 `setup-matt-pocock-skills` ，官方定位一句话： **Run once per repo before using the other engineering skills** （每个仓库跑一次，之后再用其它 engineering skill）。它不教你怎么写 issue、怎么 triage，它只做一件事：把三个配置决策一次性落地，让后面的 skill 都有确定的读写位置。

## 1\. 先想清楚：setup 为什么不是"跑一下就完事"的脚本

第一次看到这个 skill 的人容易把它当成脚手架，以为运行一次就自动生成一堆配置文件。官方文档专门纠正了这一点： **It writes config, it does not hard-code behaviour** （它写配置，不硬编码行为）。

区别在哪？脚手架是确定性的：输入参数，吐出模板。setup 是 prompt-driven 的：先 explore 真实仓库状态，把发现摆给你看，每个问题都带推荐答案，你确认后它才写入。能推断出来的它直接跳过，不废话。

所以整个 skill 的运作逻辑是一段对话，不是一个脚本。对话的起点也不在模板里，在仓库本身的真实状态： `git remote -v` 、`.git/config` 、 `AGENTS.md` 和 `CLAUDE.md` 是否存在、 `CONTEXT.md` 、`.scratch/` 目录、monorepo 信号。这些探测结果决定了它接下来问什么、怎么推荐。

还有一点值得提前说破：这个 skill 只能由你主动触发。输入 `/setup-matt-pocock-skills` ，agent 才会跑。它不会在你调用别的 skill 时偷偷自动执行，这也是 Matt Pocock 整套设计里"user-invoked 而非 model-invoked"哲学的一部分。社区里有人评价这种设计让 skill 存在本身几乎不占上下文，是有道理的。

![[Image 97.webp|确定性脚本与 prompt 对话对比图]]

确定性脚本与 prompt 对话对比图

## 2\. 完整案例：把 setup 装进一个已有团队仓库

下面用一个贯穿全文的案例来讲清楚整个流程。假设你是一名刚进团队的后端开发者，clone 下了 `team/backend` 这个仓库。仓库有一定历史，用 GitHub Issues 管理工单，但里面既没有 `AGENTS.md` 也没有 `CLAUDE.md` 。你决定让这套 engineering 流程全部可用。

### 第一步：先 explore，再开口

你在 Claude Code 里输入 `/setup-matt-pocock-skills` 。agent 没有直接问你问题，而是先做了一圈侦察。你可以看到它执行了类似这样的探测：

```
# 看 remote 指向哪，判断 issue tracker 候选
git remote -v
cat .git/config

# 看有没有既有的 agent 约定文件
ls AGENTS.md CLAUDE.md

# 看有没有域文档 / scratch 目录 / monorepo 信号
ls CONTEXT.md .scratch/ pnpm-workspace.yaml
ls packages/
```

这次探测的结果：

- `origin` 指向 `github.com/team/backend` ，GitHub 候选成立
- 没有 `AGENTS.md` ，没有 `CLAUDE.md`
- 有 `.scratch/` 目录，这是"本地 markdown issue tracker 约定已经在用"的信号
- 没有 `pnpm-workspace.yaml` ， `package.json` 里没有 workspaces 字段， `packages/` 也不存在，没有 monorepo 信号

到这里 agent 心里已经有底了。它把发现摆出来，然后开始逐个 section 确认。注意顺序：先 explore 再提问，这是 setup 和"套模板"的分水岭。它问的每一个问题，都是基于刚看到的仓库状态。

### 第二步：Section A，issue tracker 四选一

第一个问题关于工单放哪。这里有个默认姿态：如果 `git remote` 指向 GitHub，agent 会直接提议 GitHub，而不是让你从零描述。

> Agent：检测到 remote 指向 GitHub，建议使用 GitHub Issues 作为 issue tracker（通过 `gh` CLI）。可以吗？

如果这不是你的选择，还有三个分支可以走：

- **GitLab** ：remote 指向 GitLab，用 `glab` CLI
- **本地 markdown** ：写到 `.scratch/<feature>/` ，适合 solo 开发或没有 remote 的仓库
- **其它** ：Jira、Linear 这些，直接自由描述你的流程，agent 会记录成文本

在这个案例里，团队本来就重度用 GitHub Issues，你直接确认。于是第一份产物落盘：

```
# docs/agents/issue-tracker.md（示意，简化为种子模板的核心结构）

# Issue tracker: GitHub

Issues and PRDs for this repo live as GitHub issues. Use the \`gh\` CLI for all operations.

### Conventions

- **Create an issue**: \`gh issue create --title "..." --body "..."\`. Use a heredoc for multi-line bodies.
- **Read an issue**: \`gh issue view <number> --comments\`, filtering comments by \`jq\` and also fetching labels.
- **List issues**: \`gh issue list --state open --json number,title,body,labels,comments\` with appropriate \`--label\` and \`--state\` filters.
- **Comment on an issue**: \`gh issue comment <number> --body "..."\`
- **Apply / remove labels**: \`gh issue edit <number> --add-label "..."\` / \`--remove-label "..."\`
- **Close**: \`gh issue close <number> --comment "..."\`

Infer the repo from \`git remote -v\` — \`gh\` does this automatically when run inside a clone.
```

这份文件就是给 `to-tickets` 这类 skill 读的。它不需要自己猜工单在哪，读一下这个文件就知道该调 `gh` 还是 `glab` ，或者该在 `.scratch/` 里新建 markdown。

### 第三步：Section B，triage labels 只在 triage 已装时出现

第二个问题关于标签。这里有个前提条件： **只有 `triage` skill 已经安装，setup 才会问这一段** 。如果没装，它直接跳过，也不会生成 `docs/agents/triage-labels.md` 。

因为本案例里 triage 装了，agent 继续，抛出默认方案：五个 canonical 角色标签。注意源码里的行为很克制—— **它只问一个问题** ，不会自作主张去扫描仓库里已有哪些标签。

> Agent：检测到 triage skill 已安装。是否使用默认的 5 个 triage 标签？（推荐：是） `needs-triage` 、 `needs-info` 、 `ready-for-agent` 、 `ready-for-human` 、 `wontfix`

这 5 个标签不是随便起的。它们串起来就是一条 issue 流转路径：新 issue 进来挂 `needs-triage` ，缺信息就转 `needs-info` ，信息齐了变 `ready-for-agent` ，agent 做完交给人的是 `ready-for-human` ，决定不做的进 `wontfix` 。triage skill 读这份配置，按标签决定自己该对每个 issue 做什么，而不是凭空建标签。 如果你否掉默认，agent 会收集你的 override，比如你们团队习惯用 `bug:triage` 这种带前缀的命名，那就写进配置。关键是语义对齐： **标签名在配置里是什么，triage 就按什么执行** ，不重复建、不乱猜。

### 第四步：Section C，domain docs 默认 single-context

第三个问题关于域文档放哪。这一步的默认值很强： **single-context，直接写，基本不询问** 。也就是根目录一份 `CONTEXT.md` ，加上 `docs/adr/` 放架构决策记录。这个布局几乎适配所有仓库。

只有探测到 monorepo 信号时，agent 才会停下来给你多一个选项：

> Agent：检测到 pnpm workspace，存在多个独立 context。是否使用 multi-context 布局？ 根目录 `CONTEXT-MAP.md` + 每个 context 一份 `CONTEXT.md` ？

本案例没有 monorepo 信号，agent 直接推荐 single-context，你确认后，第二份产物落盘：

```
# docs/agents/domain.md（示意，简化为种子模板的核心结构）

# Domain Docs

How the engineering skills should consume this repo's domain documentation when exploring the codebase.

### Before exploring, read these

- **\`CONTEXT.md\`** at the repo root, or
- **\`CONTEXT-MAP.md\`** at the repo root if it exists — it points at one \`CONTEXT.md\` per context. Read each one relevant to the topic.
- **\`docs/adr/\`** — read ADRs that touch the area you're about to work in.

If any of these files don't exist, **proceed silently**. Don't flag their absence; don't suggest creating them upfront. The \`/domain-modeling\` skill creates them lazily when terms or decisions actually get resolved.

### File structure

Single-context repo (most repos):

    /
    ├── CONTEXT.md
    ├── docs/adr/
    │   ├── 0001-event-sourced-orders.md
    │   └── 0002-postgres-for-write-model.md
    └── src/
```

为什么 setup 要管这件事？因为 `grill-with-docs` 、domain-modeling 这些 skill 都要读 `CONTEXT.md` 才知道去哪找共享语言。没有这份约定，它们要么猜，要么每个会话重新解释一遍。README 里有个例子很能说明共享语言的价值：某个课程系统里"一节课程内的 lesson 变成真实文件"这件事，反复描述要好几句话，起个名字叫 `materialization cascade` ，之后每个会话提一次就够。这个精确术语省下的 token，每次会话都在复利。

### 第五步：写入 Agent skills 块，装完

三个 section 都确认完，agent 开始落盘。产物三件套里，issue-tracker 和 domain 是必写，triage-labels 只在前一步跑过时才写。还剩最后一步：把配置的"索引"写进仓库的 agent 约定文件。

因为本案例里 `CLAUDE.md` 和 `AGENTS.md` 都不存在，agent 停下来问你用哪个。注意它的文件选择规则： `CLAUDE.md` 存在就编辑它，否则用 `AGENTS.md` ，两者都没有就问用户，绝不两个都建，也绝不在已有 `CLAUDE.md` 时新建 `AGENTS.md` 。你选了 `AGENTS.md` （理由：团队在 Anthropic 和别家工具之间摇摆，用中立的 `AGENTS.md` 更通用），于是追加：

```
# AGENTS.md

### Agent skills

This repo is configured for the engineering skills. Read the files below before using the corresponding skill.

### Issue tracker

See \`docs/agents/issue-tracker.md\` for where and how to create issues.

### Triage labels

See \`docs/agents/triage-labels.md\` for the label vocabulary used by the triage flow.

### Domain docs

See \`docs/agents/domain.md\` for the domain doc layout (\`CONTEXT.md\` + \`docs/adr/\`).
```

到这一步，配置才真正"接通"。后面你再调用 `to-tickets` ，它读 `docs/agents/issue-tracker.md` 就知道工单放哪；调用 `triage` ，它读 `docs/agents/triage-labels.md` 就知道标签语义；调用 `grill-with-docs` ，它读 `docs/agents/domain.md` 就知道该去 `CONTEXT.md` 和 `docs/adr/` 找材料。setup 的角色就是这座桥。

![[Image 98.webp|setup 四步流程示意图：从探测到写入]]

setup 四步流程示意图：从探测到写入

## 3\. 三个 section 的默认、分支与产物

把上面的流程压成一张表，方便你对照：

| Section | 默认 | 分支 | 产物 |
| --- | --- | --- | --- |
| A. Issue tracker | GitHub（ `gh` CLI，remote 指向 GitHub 时直接提议） | GitLab（ `glab` ）/ 本地 markdown（`.scratch/<feature>/` ）/ 其它（Jira、Linear 等自由文本） | `docs/agents/issue-tracker.md` |
| B. Triage labels | 仅 triage skill 已安装时运行；默认 5 标签 | 用户否掉时收集 override，避免 triage 重复建标签 | `docs/agents/triage-labels.md` |
| C. Domain docs | single-context（根 `CONTEXT.md` + `docs/adr/` ），直接写不询问 | 仅 monorepo 信号时 offer multi-context（ `CONTEXT-MAP.md` + 每 context 一份 `CONTEXT.md` ） | `docs/agents/domain.md` |

另外还有一处跨 section 的落点： `CLAUDE.md` （优先）或 `AGENTS.md` （其次）里的 `## Agent skills` 块。它是配置的目录页，让 agent 第一次进仓库就知道去哪些文件找答案。

![[Image 99.webp|五标签状态流转图：needs-triage 到 wontfix]]

五标签状态流转图：needs-triage 到 wontfix

## 4\. 装没装好？四步检查

setup 跑完不代表装好了，给你一份可检查清单，30 秒验证：

- **三件 docs 都在** ： `docs/agents/issue-tracker.md` 、 `docs/agents/domain.md` 存在；triage 装了的仓库还要有 `docs/agents/triage-labels.md`
- **Agent skills 块在** ： `CLAUDE.md` 或 `AGENTS.md` 里有 `## Agent skills` ，且指向上面三个文件
- **标签语义一致** ：仓库里实际存在的标签，和 `docs/agents/triage-labels.md` 里写的对得上，triage 不需要临时新建
- **域文档布局确定** ： `CONTEXT.md` 和 `docs/adr/` 的位置和命名已经约定，不会每次问"放哪"

反过来，如果哪天你发现 `to-tickets` 开始猜 issue 位置、 `triage` 在套不存在的标签，说明 setup 没跑过，或者跑过但配置被删了。官方文档把这类现象当作 setup 缺失的失败信号。

![[Image 100.webp|setup 验收清单：30 秒四步检查卡片]]

setup 验收清单：30 秒四步检查卡片

## 5\. 四个反例：哪些做法会翻车

理解了设计逻辑，再看常见翻车姿势就一目了然。

**反例一：不 explore，直接套 GitHub 模板。** 新来的同学图省事，按教程模板填了一份 GitHub 的 issue-tracker 配置。但仓库其实没接 GitHub Issues，团队一直把工单写在 `.scratch/` 的 markdown 里。结果 `to-tickets` 拿着配置去调 `gh` ，一个 issue 也建不出来。setup 先看 `git remote` 和 `.scratch/` 再问，就是为了避免这种"配置与仓库事实脱节"。

**反例二：triage 没装，硬写 labels 配置。** 有人觉得 5 个标签挺好，直接把 `triage-labels.md` 写进仓库。问题是 triage skill 根本不在，这份配置没人读，标签建了也是摆设；等哪天真装了 triage，又可能和这份手写配置打架。setup 只在 triage 已装时才问标签，不是没有道理的。

**反例三：单仓库硬上 multi-context。** 看到 multi-context 布局更"高级"，在一个单体应用仓库里也搞了 `CONTEXT-MAP.md` + 一堆子 `CONTEXT.md` 。结果维护成本翻倍，agent 反而不知道该读哪份。setup 的设计是：没有 monorepo 信号就默认 single-context，直接写不询问。少即是多。

**反例四： `AGENTS.md` 已存在，又新建 `CLAUDE.md` 。** 这是 setup 明确禁止的。两个文件并存，agent 约定分裂，你都不知道自己改的是哪份。规则很简单：先看哪个已存在，优先编辑它，绝不同时新建两个。

## 6\. setup 和系列其它篇的关系

一句话： **setup 是所有 engineering flow 的 precondition** 。这个系列前面讲的 `to-tickets` 、 `triage` 、 `grill-with-docs` ，全部依赖 `docs/agents/*.md` 里的配置。这些 skill 本身是"读配置执行"的，不是"自带配置"的。没有 setup，它们就像没插电源的电器，看起来功能齐全，通电才知道哪不对。

所以 setup 跑一次就够了，不需要每次开会话都跑。重跑只在两种场景有必要：一是团队换了 issue tracker（比如从 GitHub 迁到 Linear）；二是配置乱到想推倒重来。日常微调不用碰它，直接编辑 `docs/agents/*.md` 就行。

也要说清楚它的边界。装了 setup 不解决所有工程摩擦，它只是把"工单在哪、标签叫什么、文档放哪"三个事实写对。代码质量、需求是否清晰、团队是否真的按标签流转，这些它管不了。

社区对它的批评也值得听：知乎有人指出这套配置没有全局继承机制，你有一堆仓库都用 GitHub Issues，每个仓库都得重新跑一遍 setup 来确认这个事实，多仓库场景下有点重复劳动。它是个好菜单，但要不要按自己的口味重开厨房，是你的选择。

## 总结

把今天这条链路收个尾。setup-matt-pocock-skills 是整套工程流程的第一天：先 explore 仓库状态，再逐个 section 确认，最后把三件配置落盘。它写配置不硬编码行为，是 prompt-driven 的对话，不是确定性脚本。装完的标志是三件 docs 存在、 `CLAUDE.md` 或 `AGENTS.md` 里有 `## Agent skills` 块、标签语义一致、域文档布局确定。

下次你 clone 下一个仓库，别急着调 skill。先跑一次 setup，把"工单在哪、标签叫什么、文档放哪"这三个问题一次答完，后面的流程才真的接得上。

> **说明** ：本文内容基于 Matt Pocock Skills 源码（github.com/mattpocock/skills）中 `setup-matt-pocock-skills` 、 `triage` 、 `ask-matt` 、 `grill-with-docs` 、 `to-tickets` 等 skill 的 SKILL.md 与种子模板整理而成，并结合社区对这套工程流程的公开讨论。 **文中的配置模板和参数建议仅供参考，实际效果请以你的业务数据和环境测试结果为准。** setup 只是把"工单在哪、标签叫什么、文档放哪"三个事实写对，并不会解决所有工程摩擦。如果有实际使用经验，欢迎在评论区分享交流。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录