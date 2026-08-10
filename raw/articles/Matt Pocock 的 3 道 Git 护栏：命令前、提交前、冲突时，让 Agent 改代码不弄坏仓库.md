---
title: "Matt Pocock 的 3 道 Git 护栏：命令前、提交前、冲突时，让 Agent 改代码不弄坏仓库"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491588&idx=1&sn=4e48c79f48c8d019cfed92eadfaaacb7&chksm=cf405552f837dc4474b5b224b52f2c8d1fb23722f62fd58c9714236c0ac5801183e9a6d5a4e8&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-07
description:
tags:
---
运维有术 术哥无界 *2026年8月3日 08:30*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *185* 篇，AI 编程最佳实战「2026」系列第 *65* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

让 Agent 在真实仓库里写代码，有一个问题大多数教程都跳过了： **Agent 并不天然理解哪些 git 命令是危险的** 。它只会按最大似然执行你交代的事。于是你常常看到这些场面：

- Agent 要 push，发现远端有分歧，直接 `git push --force` ，把同事还没合并的提交覆盖了
- Agent 改完代码，自我感觉良好， `git commit -m "done"` ，其实 typecheck 压根没跑过
- 两个分支改了同一行，rebase 冲突，Agent 挑了一边，挑错的那边恰好是需求要的

这三个场景，发生在三个不同的时间点：命令执行前、commit 之前、冲突出现时。Matt Pocock 的 skills 仓库里，正好有三样东西分别守着这三个点： `git-guardrails-claude-code` （预防）、 `setup-pre-commit` （提交）、 `resolving-merge-conflicts` （冲撞）。

这篇文章不打算逐个介绍这三个 skill，那是产品文档干的事。我想用一条主线把它们串起来： **三层护栏，各自拦住一类 Git 事故** 。

![[Image 35.png|三道 Git 护栏信息图封面：命令前、提交前、冲突时]]

三道 Git 护栏信息图封面：命令前、提交前、冲突时

## 1\. 先看事故现场：一个 TS 项目的三个 Agent

假设你带着 A、B、C 三个同事在同一个 TypeScript 项目里干活，每人配了一个 Claude Code 风格的 Agent。

- A 让 Agent 写新功能：支付模块。Agent 干了一下午，本地 commit 了一堆，准备 push。
- B 让 Agent 修一个类型报错。Agent 改完就提交，没跑任何检查。
- C 接手 main 分支做 release，需要把 feature 分支 rebase 到 main 上，rebase 到一半，冲突了。

三个人的 Agent，分别可能制造三类事故。

A 的 Agent 在 push 时发现远端有别人推的新提交，它不停下来问，而是直接 `git push --force` ，把还没被 push 的远端提交覆盖了。

B 的 Agent 改完类型错误就直接 commit，其实改动让另外两个文件 typecheck 爆了，没人跑过，代码进了仓库。

C 的 Agent 在 rebase 冲突时看到 `<<<<<<<` 标记，选了看起来比较新的那边，可需求要的是另一边的逻辑。

这三类事故的共同点：都发生在 Agent 对 git 的行为失控时。而且每一类，都对应一个可以拦截的时间点：

```
命令执行前          commit 前          冲突出现时
    │                  │                  │
 git push --force    commit 脏代码     rebase 挑错一边
    │                  │                  │
 [预防层]            [提交层]           [冲撞层]
 git-guardrails      setup-pre-commit   resolving-merge-conflicts
```

下面按时间顺序，一层一层看它们各自拦住了什么。

![[Image 36.png|三层护栏时间线：预防、提交、冲撞三个时间点]]

三层护栏时间线：预防、提交、冲撞三个时间点

## 2\. 预防层：危险命令在执行前就被拦下

先回到 A 的事故。 `git push --force` 这种命令，能拦的窗口只有一个： **命令真正执行之前** 。

Claude Code 在这个位置有一个专门机制： `PreToolUse` hook。它在 Claude 决定调用 Bash 工具之后、工具执行之前触发，标准输入会收到一段 JSON，里面带着 Claude 准备执行的完整命令。

Matt 的 `git-guardrails-claude-code` 就守在这个位置。核心脚本 `block-dangerous-git.sh` 逻辑很简单：

```
INPUT=$(cat)
COMMAND=$(echo "$INPUT" | jq -r '.tool_input.command')
DANGEROUS_PATTERNS=( "git push" "git reset --hard" "git clean -f" "git clean -fd" "git branch -D" "git checkout \." "git restore \." "push --force" "reset --hard" )
for pattern in "${DANGEROUS_PATTERNS[@]}"; do
  if echo "$COMMAND" | grep -qE "$pattern"; then
    echo "BLOCKED: '$COMMAND' matches dangerous pattern '$pattern'. The user has prevented you from doing this." >&2
    exit 2
  fi
done
exit 0
```

从 stdin 读 JSON，用 `jq` 取出 `tool_input.command` ，正则匹配危险模式。命中就向 stderr 打一条消息，然后 **exit 2** 。除了带 `git ` 前缀的命令，它还会拦下独立模式的 `push --force` 、 `reset --hard` 。

为什么偏偏是 exit 2？因为 Claude Code 的 hooks 机制里， **只有 exit 2 才是真正的阻止信号** 。官方文档说得直白：exit 0 是成功，但不代表批准（ `staying silent doesn't approve it` ）；exit 1 是非阻塞错误，动作照常执行，官方原话是 `If your hook is meant to enforce a policy, use exit 2.`。换句话说，网上那些用 exit 1 拦截危险命令的示例，基本形同虚设。

被拦下之后，Agent 看到的是这样一条消息：

```
BLOCKED: 'git push origin main' matches dangerous pattern 'git push'.
The user has prevented you from doing this.
```

Agent 会明白自己没权限，然后停下来。这正是执行前拦截的意义所在。 **如果等到 CI 才拦，命令已经跑完了** 。CI 在 push 之后才触发，拦不住远端被覆盖，更拦不住本地 `git reset --hard` 丢掉一整天的工作。

**反例一：把 guardrails 装在 CI 而不是 agent hook 上。** 这个思路听起来合理：反正 CI 会检查。但 CI 的检查发生在 push 之后，而 `git push --force` 覆盖远端、 `git reset --hard` 丢本地改动，都发生在 CI 之前。等 CI 跑完，事故已经完成了。PreToolUse 之所以装在这里，是因为它是能在命令执行前这个时间点说话的机制。

安装时有一个选择： **项目级还是全局** 。项目级写进仓库的 `.claude/settings.json` ：

```
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "\"$CLAUDE_PROJECT_DIR\"/.claude/hooks/block-dangerous-git.sh"
          }
        ]
      }
    ]
  }
}
```

全局则写进 `~/.claude/settings.json` ，hook 路径换成 `~/.claude/hooks/block-dangerous-git.sh` 。skill 安装时会问你要哪一种；如果 settings 已存在，会把新 hook 合并进已有的 `hooks.PreToolUse` 数组，不覆盖其他配置。

这里有两个边界必须交代清楚。 **第一，这层只管 Claude Code** 。PreToolUse 是 Claude Code 的工具权限机制，别的 Agent 工具未必有同样的 hook 接口，换工具就得另想办法。

**第二，它是字符串正则匹配，不是命令解析器** 。 `git push origin main` 、 `git push -f` 都会命中 `git push` 模式，但别指望它能智能判断这条 push 是否安全。它的定位是宁可错杀，不可放过，而且它替代不了 CI 的质量关卡。

装完怎么验证？skill 给了一个很直接的冒烟命令：把 `{"tool_input":{"command":"git push origin main"}}` 用 echo 管道喂给脚本，正确结果应该是 exit 2，并向 stderr 打印 BLOCKED。这个验证步骤很重要，因为它同时确认了脚本路径、jq 依赖和退出码三件事。

![[Image 37.png|PreToolUse hook 拦截危险命令流程图]]

PreToolUse hook 拦截危险命令流程图

## 3\. 提交层：commit 之前的一道机械保险

再回来看 B 的事故。Agent 改完代码直接 commit，没跑过 typecheck。这个时间点（commit 之前）对应的护栏是 `setup-pre-commit` 。

它给仓库装上 Husky pre-commit hook，编排顺序是固定的。`.husky/pre-commit` 文件内容如下（Husky v9+ 不需要 shebang）：

```
npx lint-staged
npm run typecheck
npm run test
```

配套的 lint-staged 配置：

```
{
  "*": "prettier --ignore-unknown --write"
}
```

为什么 lint-staged 要排在全量 typecheck / test 前面？因为 lint-staged **只处理暂存文件** ，也就是这次 commit 真正会带走的文件。lint-staged 官方的理由就两条：全量跑太慢，而且全量 lint 对无关文件会产生无关结果，原话是 `Ultimately you only want to check files that will be committed.` 所以先用毫秒级的 Prettier 把暂存文件收拾干净，再跑全量的 typecheck 和 test。

反过来把全量测试放前面，中型仓库一次提交可能就要跑几分钟，人很快就想用 `git commit --no-verify` 绕过去。

**反例二：pre-commit 里跑全量测试，而不是 lint-staged。** 这会让每次提交都变慢，慢到让人想绕过它。lint-staged 的价值恰恰是快：只检查即将被提交的文件。

还有几个编排细节，都是社区踩过坑的结论：

- **typecheck 不适合放进 lint-staged 逐文件跑** 。 `tsc --noEmit` 是项目级全量语义检查；lint-staged 会把暂存文件列表追加到命令末尾，tsc 就变成单文件模式，结果不可靠。所以这里把 `npm run typecheck` 作为独立一行全量跑。
- **lint-staged v10 起会自动把修复后的文件重新 stage** ，配置里不要再手写 `git add` ，写了反而画蛇添足。
- **仓库没有 `typecheck` / `test` 脚本时，跳过对应行，而不是补一个空脚本** 。补一个 `echo "no tests"` 之类的空脚本，等于制造虚假的安全感，每次提交都通过但什么都没检查。没有测试就如实说，把质量闸门交给 CI。
- **Prettier 不该有和 ESLint 冲突的规则** 。Prettier 管格式化，ESLint 管代码质量，职责分开。如果 ESLint 里还开着 `indent` 、 `quotes` 这类格式化规则，两边会打架，用 `eslint-config-prettier` 一次性关掉冲突规则（社区统计大约 30 多条）。这里有个坑：eslintrc 的 `extends` 管不到你在 `rules` 里显式写的规则，得用 `npx eslint-config-prettier <file>` 检测出来删掉。

装上之后，第一次提交本身就是冒烟测试：skill 会让你提交一条 `Add pre-commit hooks (husky + lint-staged + prettier)` ，这次提交会真的触发新装的 hook。

回到 B 的案例。Agent 改完类型错误，准备 `git commit` 。pre-commit 先跑 lint-staged 格式化暂存文件，然后全量 typecheck 直接爆了。Agent 看到失败，回去修，再 commit，再跑，直到 typecheck 通过。这就是提交层的拦截点。

也要交代清楚边界： **pre-commit 不是安全边界，只是纪律工具** 。 `git commit --no-verify` 能绕过它，CI 才是最终关卡。它的价值是把没跑过的代码挡在 commit 之前，而不是替代 CI 做质量把关。

补充一个上下文：在 Matt 的主流程 skill `implement` 里，commit 之前本来就有 typecheck、单测、全量测试和 code-review 关卡。pre-commit 是兜底的一道机械保险，而不是全部防线。提交层护栏做得再好，也不该指望它替代 Agent 提交前的自查。

![[Image 90.webp|pre-commit 编排顺序：lint-staged、typecheck、test]]

pre-commit 编排顺序：lint-staged、typecheck、test

## 4\. 冲撞层：冲突按意图解决，而不是按默认

接下来是 C 的事故：rebase 冲突。这一层是 `resolving-merge-conflicts` ，它和前两层不一样：前两层装好之后自动生效，这一层是 **事件驱动** 的，冲突出现时才调用。它的 SKILL.md 只有 14 行，流程 5 步：

1. **看当前状态** ：确认 merge / rebase 进行到哪一步，看 git history 和冲突文件
2. **找 primary source** ：对每个冲突，搞清每处改动为什么发生，读 commit message、查 PR、查原始 issue
3. **逐 hunk 解决** ：尽量保留双方意图；实在不兼容，选匹配 merge 目标的一边，并记录 trade-off；不要发明新行为
4. **跑项目的自动化检查** ：通常是 typecheck → tests → format，修掉 merge 弄坏的东西
5. **完成 merge / rebase** ：stage 所有文件并 commit；rebase 就继续 `--continue` ，直到所有 commit 走完

**反例三：merge 冲突只看代码不看上下文。** 这是新手 Agent 常见的行为：看到冲突标记，比较两边代码谁看起来对，选一个。GitKraken 的指南里有一句话点得很透： **冲突标记告诉你改了什么，但没告诉你为什么改** 。第 2 步强制先找 primary source，就是为了让你按意图解决，而不是按看起来的样子解决。

回到 C 的案例。Agent 在 rebase 时遇到冲突，两个分支都改了同一个函数。它准备选看起来比较新的那边，但第 2 步拦住了它：读了两边的 commit message 才发现，feature 分支的改动是需求方明确要的新逻辑，main 分支的改动只是重构时的顺手调整。

按意图，应该保留 feature 的逻辑，再把 main 的重构部分合进去。这就是保留双方意图。

第 4 步同样关键。 **冲突解决后必须跑自动化检查** 。GitKraken 把 `Not Testing After Resolution` 列为常见错误，理由很硬：合并的组合可能产生双方单独都没有的新 bug。代码看着对，一跑 test 就露馅。

这里有四个边界，务必记牢：

- **rebase 中 ours / theirs 的方向和 merge 相反** 。rebase 时 ours 是你正在重放的提交所在分支，搞反了就选错一边。
- **`--continue` 之后就不能 abort 了** 。GitLab 文档明确：一旦执行 `git rebase --continue` ，rebase 就不能再回滚。想 abort 要在继续之前决定。
- **`git checkout --ours|--theirs` 会整体覆盖整个文件** ，丢掉文件里另一侧的非冲突修改。只想解决单个 hunk，得用 `git merge-file` 。这也是为什么流程要求逐 hunk，而不是整文件二选一。
- **永远不要 `--abort` 是 Matt 的个人哲学，不是社区共识** 。GitHub 和 GitLab 官方都把 `--abort` 当成合法逃生通道，社区主流观点是：abort 会丢弃已做的全部解决工作，所以应该在深入解决之前判断要不要放弃，而不是解决到一半才反悔。
![[Image 38.png|冲突解决 5 步流程图]]

冲突解决 5 步流程图

## 5\. 对照与安装顺序

三个时间点、三个 skill、三类事故，对应关系是：

| 时间点 | skill | 拦住的事故 | 安装方式 |
| --- | --- | --- | --- |
| 命令执行前 | `git-guardrails-claude-code` | force push 覆盖、reset --hard 丢工作 | 一次性安装 |
| commit 前 | `setup-pre-commit` | 提交没跑过 typecheck 的代码 | 一次性安装 |
| 冲突出现时 | `resolving-merge-conflicts` | rebase / merge 冲突挑错一边 | 按需调用 |

这里有一个分类事实值得留意： `resolving-merge-conflicts` 在 Matt 的 `skills/engineering` 主列表里，是日常推广的； `git-guardrails-claude-code` 和 `setup-pre-commit` 放在 `skills/misc` ，作者自己标注为 **保留但很少用、未在插件中推广** （Tools I keep around but rarely use — not promoted in the plugin）。这个分类透露了作者的优先级：冲突解决是他工作流里常遇到的事，所以放主列表；guardrails 和 pre-commit 是备着偶尔用的。这不代表后者没用，只代表作者的使用频率。

安装顺序上，我的建议是分三天装，而不是一次装齐：

1. **第 1 天，让 Agent 写代码之前** ：装 `git-guardrails-claude-code` 。它是三层里能拦危险命令的那一层，几分钟装完，收益立竿见影。
2. **第 2 天，Agent 开始改代码、产生提交时** ：装 `setup-pre-commit` 。lint-staged 和 typecheck 开始兜住没跑过就提交的代码。
3. **冲突出现时** ：调用 `resolving-merge-conflicts` 。它是事件驱动的，没有冲突时装了也学不会。

为什么不是一次装齐？因为前两层安装都有人工确认步骤：guardrails 要你选项目级还是全局、要自定义拦截清单；pre-commit 要检测包管理器、要确认 typecheck / test 脚本是否存在。

一次装齐，你会对每个配置项都无感确认，到头来根本不知道装了什么、拦什么。分开装，每装一层都清楚它防什么。

还要区分两类性质： **Claude Code 专属** 和 **仓库通用** 。 `git-guardrails-claude-code` 靠的是 Agent 工具的 PreToolUse hook 能力，只对 Claude Code 生效，换一个 Agent 工具就得另想办法； `setup-pre-commit` 和 `resolving-merge-conflicts` 是仓库级的，装进仓库后，任何参与 git 工作流的人都要遵守，不管是你的同事还是任何一个 Agent。

## 总结

回头看那三类事故：force push 覆盖远端、commit 没跑过的代码、rebase 冲突挑错一边。它们不在同一个时间点发生，所以也不该靠同一种手段解决。三层护栏的边界，恰恰就是 Agent 失控的三种时间窗口。

再说句老实话：这三层都不是铜墙铁壁。hook 是字符串正则匹配， `--no-verify` 可以绕过 pre-commit，按意图解决冲突也不能排除逻辑错误。

它们的价值不是消灭所有事故，而是把事故从悄无声息地发生，变成在每个时间点都被迫停下来一次：危险命令推不出去，脏代码进不了仓库，冲突得先想清楚再继续。对一个要让 Agent 在真实仓库里干活的团队，这已经是投入产出比划算的三层保险。

> **说明** ：本文内容基于 Matt Pocock skills 仓库源码（mattpocock/skills）与 Claude Code 官方 hooks 文档分析整理而成，文中涉及的拦截脚本、hook 配置和安装步骤以本地仓库版本为准，尚未在生产环境中逐项验证。 **文中的配置模板和参数建议仅供参考，实际效果请以你的业务数据和环境测试结果为准。** 如果你也遇到过 Agent 弄坏仓库的场面，欢迎在评论区分享你的踩坑和护栏方案。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录