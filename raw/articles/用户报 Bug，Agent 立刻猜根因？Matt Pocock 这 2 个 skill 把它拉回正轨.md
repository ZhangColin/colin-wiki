---
title: "用户报 Bug，Agent 立刻猜根因？Matt Pocock 这 2 个 skill 把它拉回正轨"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247491490&idx=1&sn=f8d12d71a20ff139603599bb92e1f2e1&chksm=cf43aaf4f83423e2f13bff54a4d2476e18d07cdaeb753ec7eb07af46c87cd476355dad7e5100&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-06
description:
tags:
---
运维有术 术哥无界 *2026年7月22日 10:00*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *174* 篇，AI 编程最佳实战「2026」系列第 *58* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、AIOps、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

![[Image 69.webp|信息图封面：Matt Pocock 两个 skill 修复 Agent 失败模式]]

信息图封面：Matt Pocock 两个 skill 修复 Agent 失败模式

最近在翻 mattpocock/skills 仓库（2026-07 看到的 star 数是 180k）的时候，我注意到一个有意思的现象：仓库 README 把 Agent 失败模式归为四类，其中 **#3 The Code Doesn't Work** 单独被拎出来，对应的不是某条 prompt 技巧，而是两个独立的 skill： `/tdd` 和 `/diagnosing-bugs` 。

`/tdd` 听上去老生常谈，但 `mattpocock` 把它重新组织成了一个 model-invoked skill（agent 在合适语境自动加载），核心论点是：TDD 不是为了写更多测试，而是为了让 agent 有一个 **够快、够准、能真实失败** 的反馈信号。 `/diagnosing-bugs` 走得更远，把整个调试流程拆成 6 个强制阶段，跳阶段必须显式说明理由。

读源码的时候我反复看到一个句子：

> If you catch yourself reading code to build a theory before this command exists, **stop** — jumping straight to a hypothesis is the exact failure this skill prevents.

翻译成人话就是： **在你没有一个能复现 bug 的命令之前，停下来** 。先复现，再假设。

这篇文章我想拆清楚两件事：

1. 这两个 skill 到底在解决什么具体的失败模式？为什么 **先建反馈信号** 不是一个常识，而要写进 skill 文本里？
2. 在一次真实的小改动里，怎么把 `/tdd` 和 `/diagnosing-bugs` 的核心动作落到代码、issue、PR 模板里。

> **截至 2026-07-21** ，mattpocock/skills 仓库处于 v1.0.x 阶段。v1.0 是 breaking change，部分旧 skill 名（如 `diagnose` ）已重命名为 `diagnosing-bugs` ，引用时需注意时效性。

## 1\. 失败模式不是不会写代码，是没有 ground truth

mattpocock/skills 仓库把 Agent 失败模式归成四类。 `/tdd` 和 `/diagnosing-bugs` 对应的是第三类—— `The Code Doesn't Work` ：

> Without feedback on how the code it produces actually runs, the agent will be flying blind.

仓库的修辞把这一点说得很重： **The rate of feedback is your speed limit** （《Pragmatic Programmer》引文，README 收录）。下面我把这两条 skill 看作 **给 Agent 装上反馈信号** 的具体实现来拆。

我在 Hacker News 上看到一条评论说得更直白（HN `id=48811091` ，用户 pipes）：

> I have been doing this tdd and it's a big improvement but **the code is still pretty awful**.

TDD 能让 **代码能跑** ，但管不了 **代码架构好** 。这个边界很重要 —— 后面我会专门讲。

另一个角度来自仓库的 `Issue #263` （hades200082，2026-05-27， **Open 状态** ）：在 OpenCode + Kimi K2.6 环境里跑 `/code-review` ，skill 默认行为是 review 完直接进入 fix 模式；如果是 review 一个 PR，期望是发带 inline comment 的 review，而不是改代码。报告者原话：

> It would probably mean another skill to action review comments on PRs

这个 issue 是单一来源、间接证据，不能推广到所有 agent 的普遍默认行为，但它至少提示了一条：把 **先复现/先观察→再动手** 作为纪律写到 skill 文本里是有现实意义的，不是过度工程。

所以 `/tdd` 和 `/diagnosing-bugs` 的核心不是 **教 agent 写好测试** 或 **教 agent 调试** ，而是要把 **反馈信号** 这件事从 agent 的 **默认倾向** 里剥离出来，变成 SKILL.md 文本里的强制流程约束——不依赖模型自觉。

## 2\. 一个真实感强的案例：用户报 Bug，Agent 没复现就猜根因

先讲一个场景。为方便讨论，我用登录白屏这个常见问题作为 **示例** 构造一个 issue（这是文章作者为说明流程自拟的样本，不是仓库自带案例）：

```
**Title**: 登录页点击提交后白屏

**Description**:
登录页 https://example.com/login 输入正确的账号密码，
点提交按钮后整个页面变成白屏。控制台没有 JS 报错。
```

然后你把这个 issue 喂给 agent，agent 跑了一通源码阅读，给你一段这样的回答：

> 看了一下 `LoginForm.tsx` ，问题应该是 `useEffect` 的依赖数组里漏了 `password` ，导致闭包里捕获的 `password` 是 stale value。 建议改成 `useEffect(() => { ... }, [username, password])` 。

**这是典型的没复现就猜根因** 。我看到仓库 `diagnosing-bugs` 文档里有这么一段话：

> If you catch yourself reading code to build a theory before this command exists, **stop** — jumping straight to a hypothesis is the exact failure this skill prevents. No red-capable command, no Phase 2.

按这个 skill 的要求，正确的开场不是读源码，是先做一个 **`red-capable` command** 。一个能跑、断言用户症状的命令：

```
# 1. 起一个 dev server
npm run dev &

# 2. 用 Playwright 复现用户路径
npx playwright test e2e/login.spec.ts --reporter=list
```

`e2e/login.spec.ts` 里写的不是模糊的 **能跑通** ，而是 **精确断言用户报的症状** ：

```
import { test, expect } from '@playwright/test';

test('login submit does not produce white screen', async ({ page }) => {
  // 1. 打开登录页
  await page.goto('https://localhost:3000/login');

  // 2. 输入正确凭据
  await page.fill('input[name="username"]', 'testuser');
  await page.fill('input[name="password"]', 'correct-password');

  // 3. 点击提交
  await page.click('button[type="submit"]');

  // 4. 断言『不白屏』 —— 精确对应用户症状
  // 不要写成 expect(page).toBeVisible() 之类的『没崩溃』断言
  await expect(page.locator('body')).not.toBeEmpty();
  await expect(page.locator('body')).not.toHaveCSS('background-color', 'rgb(255, 255, 255)');
});
```

这个命令必须满足 4 个判据（仓库里叫 **Phase 1 完成清单** ）：

| 判据 | 含义 |
| --- | --- |
| **Red-capable** | 断言用户确切症状（白屏），不是 **没崩溃** |
| **Deterministic** | 每次跑结论一致（flaky 的 bug 要 pin 高复现率） |
| **Fast** | 秒级，不能分钟级 |
| **Agent-runnable** | 无需人类介入（除非真的必须点击，走 HITL 模板） |

只有这条命令稳定地 **红** 了，你才有资格进入 Phase 2（复现 + 最小化）和 Phase 3（提出假设）。

这就是我看完 `/diagnosing-bugs` 最想强调的一点：\*\* `red-capable`

![[Image 70.webp|red-capable command 4 判据入场券框架图]]

red-capable command 4 判据入场券框架图

## 3\. /diagnosing-bugs 的 6 阶段：为什么是线性的

仓库里 `/diagnosing-bugs/SKILL.md` 把整个调试流程硬性拆成 6 个阶段：

| Phase | 名称 | 关键产出 |
| --- | --- | --- |
| **1** | Build a feedback loop | 一个已运行过的 red-capable command |
| **2** | Reproduce + minimise | 复现 + 最小化（每个剩余元素都是 load-bearing） |
| **3** | Hypothesise | 3-5 个 ranked、falsifiable 假设 |
| **4** | Instrument | 每个探针对应 Phase 3 的一个预测 |
| **5** | Fix + regression test | 在 correct seam 写回归测试 |
| **6** | Cleanup + post-mortem | 清理 debug log，commit/PR 写明真正根因 |

几个关键点：

Phase 3 的假设必须 falsifiable（可证伪）。仓库里给的判据是：

> 必须能陈述： **如果 X 是原因，那么改 Y 会让 bug 消失**

单凭一句 **应该是 X** 不算假设——它也没有给出可执行的预测。仓库要求每个假设都要落到一个 **能动手验证** 的小改动上。比如上面这个白屏案例，几个 falsifiable 假设可以这么写：

> 假设 1： `useEffect` 依赖漏了 `password` → 改成 `[username, password]` 后白屏应该消失 假设 2：提交按钮的 `onClick` 抛了 Promise rejection 未处理 → 改成 `async/await + try/catch` 后白屏应该消失 假设 3：登录成功后 `navigate('/dashboard')` 路由配置缺失 → 加路由配置后白屏应该消失 假设 4：浏览器扩展（如密码管理器）注入了冲突脚本 → 关闭扩展或换无痕模式后白屏应该消失

Phase 4 的探针必须对应 Phase 3 的预测。给每个假设设计一个能 **测出真假** 的小改动或日志探针。所有 debug log 都要打 tag，比如 `[DEBUG-a4f2]` ，方便后面清理。

另一点是 **Phase 5 的 correct seam** —— 在哪个位置写回归测试。

> If there's no correct seam for a regression test, that's itself a finding — you've discovered your code is untestable in the way that matters.

一句话：找不到合适的 seam 写回归测试，这件事本身就是发现 —— 说明这块代码的测试边界划得不对，得重构。

仓库里还专门为性能 bug 留了一个分支：

> Measure first, fix second. For performance, baseline first (timing harness, `performance.now()`, profiler, query plan), then bisect.

性能问题先用基线测量，不要直接 `console.log` 看时间戳。

![[Image 71.webp|diagnosing-bugs 6 阶段诊断流程图]]

diagnosing-bugs 6 阶段诊断流程图

## 4\. 为什么猜测的根因不构成反馈信号

这一节是整篇文章我最想强调的。

很多开发者（包括我自己）调 bug 时的心理路径是这样的：

> 看一眼 stack trace → 扫一下相关代码 → 脑子里蹦出一个 **应该是 X** → 改一下 → 不行就换一个 **应该是 Y** →... → 半小时过去了，commit log 一片狼藉

这个路径的致命问题在于： **我觉得根因是 X** 是一个内部状态，不是外部信号。它不告诉你 X 是不是真的对，也不告诉你改完有没有用。你只能通过 **改 → 看结果** 来验证，而这个 **看结果** 如果没有 red-capable command，全凭肉眼判断。

仓库里有一个比喻我喜欢：

> Build the right feedback loop, and the bug is 90% fixed.

这句是仓库作者的经验性比喻，没有公开的对照数据。要避免把它当成可验证的收益承诺；它的意义是把 **建反馈循环** 和 **乱猜根因** 在脑内拉开优先级。

我把这个判断标准翻译成 issue/PR 评论里能用的一句话：

> 在贴出根因之前，先贴出 **能跑、断言症状、命中 bug** 的命令及其输出。

如果你的根因前面没有这个东西，那它就只是一个 **assertion** ，不是 **evidence** 。

## 5\. /tdd 的核心：让 agent 知道往哪走，而不是写更多测试

说完 `/diagnosing-bugs` ，再讲 `/tdd` 。

仓库里 `/tdd/SKILL.md` 的官方定义：

> TDD is the red → green loop. This skill is the reference that makes that loop produce tests worth keeping: what a good test is, where tests go, the anti-patterns, and the rules of the loop.

注意它的措辞： **tests worth keeping** （值得保留的测试）。不是覆盖率高、不是数量多，是 **值得留** 。

核心概念有这么几个，我按开发者一次小改动的视角重新解释：

### 5.1 Public interface 与 Seam（缝合线）

仓库原文：

> **Public interface** is the boundary at which the test observes behavior. **Seam** is the same idea, named after the **seam** in a garment — a place where two pieces join and you can see through.

直白点说： **seam 就是你从外部观察行为的那个接缝** 。比如：

| Seam 类型 | 例子 |
| --- | --- |
| HTTP API 端点 | `POST /api/login`  的 request/response |
| CLI 命令的 stdout | `git status`  的输出 |
| Public class 的方法 | `UserService.login()`  的返回值 |
| 事件/消息订阅 | `bus.on('login:success', handler)`  收到的 payload |

测试就应该写在 seam 上， **不要写内部** 。一个反面例子：

```
// ❌ 反例：从旁路（数据库查询）断言
test('login works', async () => {
  await userService.login('test', 'pwd');
  // 从数据库查
  const user = await db.query('SELECT * FROM users WHERE email = ?', ['test']);
  expect(user).toBeTruthy();  // 测的是 db 写入，不是 login 行为
});
```

这个测试看似覆盖了 login 流程，但实际断言的是数据库有没有写。哪天 login 改成不写库、只发 token，测试会无脑挂掉。

正解：

```
// ✅ 正例：从公共接口（返回值）断言
test('login returns a valid token', async () => {
  const result = await userService.login('test', 'pwd');
  // 测的是 login 这个 seam 暴露的行为
  expect(result.token).toMatch(/^[A-Za-z0-9-_=]+\.[A-Za-z0-9-_=]+\.?[A-Za-z0-9-_.+/=]*$/);
});
```

### 5.2 Pre-agreed seam

仓库里有个容易被忽略的概念： `pre-agreed seam` ：

> Pre-agreed seam: seams you and the user agreed on before writing the test. Don't write tests on seams that weren't agreed.

为什么要先约定？因为 **不在公共接口上的测试，会偷偷变成实现细节的测试** 。一旦你测了一个内部协作者，下一次重构你就不敢动了 —— 不是因为行为错了，是因为测试挂了。

实操上，我会在 issue 或对话里先把要测的 seam 列表写出来，让用户确认：

```
**Public interface** (seam 列表)：
- \`POST /api/login\` 的 200/401 response
- \`userService.login()\` 的返回值 \`{ token, expiresAt }\`

**不在本 PR 覆盖的 seam**：
- 数据库写入内容
- 内部 hash 函数
- 任何 throw 行为（这期不在范围）
```

### 5.3 Red-green loop 与 Tracer bullet

仓库把 TDD 简化成红 → 绿：

1. **Red before green** — 先写一个失败的测试， **只** 写让这个测试通过的最少代码，不要预判未来测试
2. **One slice at a time** — 一次一个 seam、一个 test、一个最小实现
3. **Refactoring 不属于 loop** — 重构归 `/code-review` skill，不要混进 red → green

`tracer bullet` （示踪弹）这个词我喜欢：

> A tracer bullet is one vertical slice. You write one test + one minimum implementation, get a clean red→green, and use that as a probe for the next slice.

翻译： **tracer bullet 就是一颗打出去能看轨迹的子弹** 。你先打一发，看路径对不对；路径对了再打下一发；路径偏了立刻改方向。

横向切片（horizontal slicing）是反模式：

```
❌ 反例（horizontal slicing）：
- 先写完所有 login 相关的测试（10 个）
- 再写完所有 login 相关的实现
- 最后跑测试，全红 → 一次性改 10 处
```
```
✅ 正例（vertical slice / tracer bullet）：
- Slice 1: 写 1 个测试，**login 返回 token** → 写最小实现让它过 → 提交
- Slice 2: 写 1 个测试，**login 密码错返回 401** → 写最小实现 → 提交
- Slice 3: 写 1 个测试，**login 锁 5 分钟** → 写最小实现 → 提交
```

仓库里特意强调过：

> AI loves coding horizontally instead of focusing on the vertical slices.

agent 默认会 **把一类东西全做完再切换** ，这是它的惯性。如果你不在 PR 或 issue 里显式约束 **一次一个 slice** ，它大概率会一把梭。

### 5.4 Mocking 原则

仓库 `mocking.md` 里的核心原则：

> Mock at the system boundary (external APIs, databases, time/randomness, file system). Don't mock your own classes, modules, or internal collaborators.

只在系统边界 mock —— 外部 API、数据库、时间、随机性、文件系统。 **不要 mock 自己的类** 。

为什么？因为 mock 自己的类等于 **假装这是边界** ，一旦你内部重构，mock 也要跟着改。改 mock 不会带来新信息，只会带来工作量。

一个 **AI 友好** 的设计原则： **让边界 mockable，方法是依赖注入和 SDK-style 接口** （每个端点一个独立函数），不要把所有 IO 塞进一个通用 `fetch`

![[Image 72.webp|vertical slice vs horizontal slicing 正反对比图]]

vertical slice vs horizontal slicing 正反对比图

## 6\. 两种流程的边界：诊断 vs 增量实现

到这一步，一个常见的问题是： `/tdd` 和 `/diagnosing-bugs` 能不能混着用？

仓库 README 和 SKILL.md 都没说能替代对方。我的理解是：

| 维度 | `/tdd` | `/diagnosing-bugs` |
| --- | --- | --- |
| **触发场景** | 构建新功能 / 修复 bug（测试先行） | 报告 broken/throwing/failing/slow |
| **起点** | 公共接口 + 预先约定的 seam | 一个 red-capable command |
| **循环单元** | red → green（一个 vertical slice） | 6 阶段线性格 |
| **关键产出** | 一个 tracer bullet 测试 + 一个最小实现 | 一个能稳定命中的复现命令 + 一个根因 + 一个回归测试 |

**核心共性** ：两者都强调 **先建立可靠的反馈信号** 。

**但** 它们服务的场景不一样：

- **`/tdd`** 适合 **我知道我要什么行为，我增量实现它** —— 闭环是 **测试先红 → 实现 → 测试变绿 → 下一个 slice**
- **`/diagnosing-bugs`** 适合 **我不知道为什么坏，但我知道它坏了** —— 闭环是 **复现 → 假设 → 验证 → 修复 → 回归测试**

从流程结构上能看出，它们不适合直接互套。比如你正在增量实现一个新功能，硬塞 6 阶段诊断流程的开场是 **先建复现命令** ——但你还没东西可以复现，闭环卡在 Phase 1。反过来，你正在调一个 bug，硬上 red → green loop 的开场是 **测试先红** ——但你得先有 red 才能谈 green，而 red 的来源正是 diagnosing-bugs Phase 1 的产物。

obra/superpowers 的 `systematic-debugging` skill 用 4 阶段覆盖同一片领域（evidence → pattern → hypothesis → implementation）。社区里有人觉得 **superpowers 已经覆盖 TDD 了** （HN `id=47916909` ，Deeds67），也有人觉得 **它没显式产出 red-capable command 这么硬** （同贴 jghn）。

这恰好说明： **TDD/debug 流程的标准化还没收敛** ，4 阶段和 6 阶段是调研中观察到的两种主流实现，覆盖范围上仍有争议，不能无条件互换。选哪个取决于你团队最看重 red-capable command 的硬约束，还是更柔性的证据链。

## 7\. 最小可复制模板：issue / PR / Agent 会话里都能用

下面这些不是仓库的 **官方标准** ，是按它的核心思想抽出来的、能在日常工作中直接用的最小模板。 **它们是为方便协作而生的，不应该被当成公司流程强推** 。

### 7.1 给 Agent 的诊断请求模板

```
## 1. 反馈循环（必填，先于一切）

**One command**:
\\`\\`\\`bash
# 一条能跑、断言症状的命令；已在本机跑过一次
\\`\\`\\`

**Expected**:
<期望输出 / 期望行为>

**Observed**:
<实际输出 / 实际行为>
```

**重要** ： `One command` 必须满足前面 4 条判据（red-capable / deterministic / fast / agent-runnable），否则 agent 只能猜。

### 7.2 给 Agent 的 TDD 请求模板

```
## 2. 公共接口与 Seam（必填，先于一切）

**Public interface** (pre-agreed seam):
- <seam 1>
- <seam 2>

**本次只做一个 vertical slice**:
- <slice 描述，比如 **login 返回 token**>

**不在本 slice 范围**:
- <明确排除的 seam / 行为>
```

这模板的价值在于 **提前约束 agent 的工作边界** 。没有它，agent 倾向于把 **登录功能** 理解成 **用户名密码校验 + 失败重试 + 滑块验证码 + 找回密码** 全套。

### 7.3 反模式清单

仓库和社区讨论里反复出现的高频误用：

| 误用 | 为什么错 |
| --- | --- |
| **没有真正跑过的测试** | 写过但没运行 = 自欺欺人，agent 经常这么干 |
| **Tautological test**  （恒真测试） | `expect(add(a, b)).toBe(a + b)`  —— 期望值按代码方式重算，永远不会失败 |
| **测实现细节** | mock 内部协作者、测私有方法、从旁路断言 |
| **一次性写完整套测试** | horizontal slicing，集成时才发现问题 |
| **只提一个假设** | 假设不需要证伪就能 **解释一切** ，等于没解释 |
| **改完不写回归测试** | 下次同样的 bug 还会回来 |

## 8\. 边界：哪些事这两个 skill 兜不住

按调研报告里的素材， **这两个 skill 不是银弹** 。需要明确：

**1\. TDD 管不了架构质量。** HN `id=48811091` pipes 的话被引用过很多次：

> I have been doing this tdd and it's a big improvement but the code is still pretty awful.

TDD 让 **代码能跑** ，但管不了 **代码架构好** 。架构问题由 `/improve-codebase-architecture` 接住，mattpocock/skills 把它归在 #4 失败模式（We Built A Ball Of Mud）。

**2\. 6 阶段诊断流程在生产 bug 上的实测有效性没有公开数据。** 仓库 README 和 talk 都没量化 **减少 X% bug / Y% token / Z% 返工** 。社区搜索也未发现可信的实测 ROI 数据（调研报告 § 7.6）。所以别在 PR 描述里写 **用了这个流程 bug 减少 50%** 这种话 —— 没有数据支撑。

**3\. 纪律需要文本外化，光在脑子里知道没用。** 仓库的 `/code-review` skill 自身都出现过 **review-then-immediately-fix** 的 bug（Issue #263）。如果你不在 PR / issue / Agent 会话开头显式贴模板，agent 大概率会回到默认行为 —— 看到问题立刻动手。

**4\. Anthropic 官方没有等价 skill。** 截至 2026-07-21，mattpocock/skills 是该领域的 **事实上标准实现** ，但不是官方背书。引用时不要写 **Anthropic 推荐的调试流程** —— 这是错的。

**5\. v1.0 是 breaking change。** 旧的 `diagnose` / `write-a-skill` / `ubiquitous-language` 等名字已重命名为 `diagnosing-bugs` / `writing-great-skills` / `domain-modeling` 等。引用老博客或老 issue 时需注意时效性。

## 9\. 结语

回到开头的命题： **Agent 为什么经常在错误方向上努力** ？

我的回答：不是因为它不够聪明，而是因为它的 **努力方向** 在没有 ground truth 的情况下是被惯性和上下文驱动的。 **先建反馈信号** 这件事不能默认 agent 知道，必须 **显式写在 skill 文本、issue 模板、PR 描述、Agent 会话的开头** 。

`/tdd` 和 `/diagnosing-bugs` 真正做的，是把这条纪律从一个 **需要时刻提醒自己的习惯** ，降级成一个 **填表就能走对的流程** 。

至于你用了之后 bug 会不会减少、token 会不会降 —— 我没有实测数据。 **这不是一个能立竿见影的承诺，是一条长期纪律** 。

我赌你下次遇到 **用户报 bug，agent 没复现就开始猜根因** 的时候，会想翻这篇文章。

> **说明** ：本文基于 mattpocock/skills（GitHub 仓库 180k+ stars）源码分析和互联网社区讨论整理而成。文中的 6 阶段诊断流程、red-capable command 4 判据、falsifiable hypothesis 等概念均忠实于 `/diagnosing-bugs` SKILL.md 原文。 **文中的配置模板和参数建议仅供参考，实际效果请以你的业务数据和环境测试结果为准。** 登录白屏案例和 4 个 falsifiable 假设是文章作者为说明流程自拟的样本，不来自仓库或任何已公开的 issue 报告。如果你也用 `mattpocock/skills` 的某条 skill，欢迎在评论区分享你的真实使用案例。

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录