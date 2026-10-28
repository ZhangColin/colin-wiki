---
title: "DeltaBox Lite：面向终端 AI Agent 的轻量文件沙箱"
source: "https://mp.weixin.qq.com/s/uFswgOfOJK-yrYwZslTDaA"
author:
  - "[[IPADS]]"
published:
created: 2026-10-22
description:
tags:
---
IPADS 上海交大并行与分布式系统研究所 *2026年10月16日 16:54*

专为 AI Agent 打造的轻量级文件系统运行时，提供原生事务级 Diff · Commit · Abort 语义与审批机制。

DeltaBox Lite 已通过插件和扩展支持 DeepSeek Harness（含桌面版）与 Pi Agent，也可便捷集成到其他 Harness 系统中。

随着大语言模型快速迈向“自主执行的Agent”，开发者与 AI 的协作模式正在发生根本性变革。以 Claude Code、Cursor、DeepSeek Harness 以及 Pi 为代表的智能体，能够调用工具链、运行 Shell 脚本、编辑本地代码库并安装依赖。然而，在 AI 获得真实“动手能力”的同时，每一位在本地环境运行 Agent 的个人开发者，都不可避免地面对着对Agent（可能）失去掌控的焦虑：

**改动黑盒与盲区：** Agent 在幕后连续执行了多步工具调用，究竟修改了哪几个文件？改动了哪几行？是否破坏了未受版本控制的临时文件或配置？

**脏工作区难以回滚：** 一旦 Agent 生成了不符合要求的代码，原本干净的工作区变脏。开发者不得不靠人脑记忆、git status、git checkout 甚至手工比对来试图复原。而遇到未跟踪的新文件时，版本控制工具往往无能为力。

**环境隔离的困境：** 传统容器（Docker）或虚拟机（VM）虽然提供了隔离，但过于沉重、冷启动迟缓，且与宿主开发环境割裂。

**开发者需要将 Agent 执行的最终决定权握在手中。** Agent 可以尝试，但每一次改动都应该 **可验证、可保留、可撤销** 。

为此，我们开源了专为个人开发者与终端AI Agent场景打造的轻量级系统原生沙箱运行时：DeltaBox Lite，在 Linux 与 macOS 上为个人用户带来毫秒级的事务化文件系统状态管理体验。

![[Image 228.webp|图片]]

## 核心设计：偏好提交的写时复制

在操作系统与沙盒文件系统的设计中，写时复制（Copy-on-Write, CoW）是实现变更隔离与版本管理的核心技术。

然而，不同的 CoW 数据流向，其实对应了不同的操作成本。

理解了 **Copy-Up** 与 **Copy-Down** 的机制差异，也就理解了为什么传统沙箱在面对 AI 智能体时往往会显得笨重。

![[Image 229.webp|图片]]

Copy-Up 与 Copy-Down 架构对比

### 偏向撤销的传统 Copy-Up 模式

传统 OverlayFS、容器或基于标准分层写时复制的沙盒实现往往采用Copy-Up架构：

宿主工作区被挂载为底层的只读模板（Lower Layer）。当沙盒内的命令尝试写某个文件时，内核在底层文件被修改前触发Copy-Up，该文件自底层向上被完整复制到上层（Upper Layer），后续所有写操作均发生在上层。

恰恰这种操作，决定了该架构是 **偏向撤销（Abort-Biased）** 的：

**撤销极其高效：** 如果当前任务执行失败，或者开发者不满意而想要撤销，系统只需直接丢弃私有 Upper 层，底层宿主并未发生变动。撤销成本为 O(1)。

**提交开销高昂：** 该架构会在提交写入的关键路径上引入显著开销。当 Agent 完成一次任务操作，并且用户确认修改后，沙箱系统需要将此前保存在上层中的改动，写回到底层，这会导致 Agent 阻塞并等待文件更新，才能继续后续的执行，导致关键路径上的时延开销。

**这决定了此类架构的适用场景：** 在端到端任务中，如果大部分执行结果都会被 Abort，Copy-Up 的性能会更高；反之则可能引入显著开销。

### 偏向采纳的 Copy-Down 模式

然而在日常个人编程实践中，上述假设并不完全成立：开发者让 Agent 编写代码、重构模块或修复 Bug，绝大多数正确的生成结果最终都是要被合并采纳的。哪怕用户对于一次执行不满意，很多时候也会选择采纳当前修改，并且给出进一步的命令，而不是选择撤销当前修改。

这意味着，如果依然采用 Copy-Up，开发者就要为每次大概率发生的提交承受高昂的写放大开销。为此，DeltaBox Lite 首次提出了 Commit-Biased Copy-on-Write (CB-CoW) 理念，采用 Copy-Down 范式。

![[Image 230.webp|图片]]

Copy-Down 模式：原地写入、日志退役与撤销恢复

写入操作直接向下（Down）作用在真实的宿主工作区。但是在覆盖写发生之前，引擎在底层 FUSE 拦截层中，仅将即将被覆盖的旧数据块范围和元数据记录为 Undo Log。Copy Down是偏向提交的：

**提交高效：** 因为新生成的代码在写入时已经直接落在工作区，开发者确认后，系统只需在元数据中将对应 Undo 日志标记退役并异步删除。没有文件搬运，提交操作可在 O(1) 时间内完成。

**修改透明：** 因为数据直接落盘，宿主机的 VS Code、语言服务（LSP）、热重载服务以及单元测试工具可以实时捕捉文件变化，使用体验无割裂感。

系统把零开销的路径留给了大概率发生的提交，只有当且仅当开发者决定放弃这轮尝试时，引擎才沿着回滚日志逆序重放旧范围，恢复到原始工作区。

**这种方式更适合** 个人日常自主编程、持续迭代与高频交互的场景。

这次开源的DeltaBox Lite 沙箱系统原生支持两种机制：

![[Image 231.webp|图片]]

DeltaBox Lite 文件系统模式对比

开发者可在会话启动时通过 `--fs-mode` 参数选择相应模式。

## 具体怎么工作？

DeltaBox Lite 将Agent 工具调用封装为如下状态机：

![[Image 232.webp|图片]]

DeltaBox Lite 会话状态机

**EXEC（执行任务）：** Agent 在受策略约束的隔离视图中运行命令与文件编辑，支持执行 Shell 脚本、代码重构、包安装等真实操作；

**DIFF（审查改动）：** 汇总整个会话或本轮交互产生的变更。不仅能高亮展示标准的文本统一差异（Unified Diff），还能对大文件改动、二进制资产更新输出清晰的标记，一目了然；

**COMMIT（接受结果）：** 审查通过后，一键保留所有改动，当前状态冻结为新的可信基线，继续后续步骤；

**ABORT（撤销尝试）：** 审查不满意时，一键撤回。底层引擎自动恢复所有受影响路径的原始状态，完全清除未跟踪的脏文件，让环境完美回到本轮尝试之前。

DeltaBox Lite 内部根据不同审查范围建立不同的回滚点：

**CLI 模式：** 以单次命令或用户显式保存点为基线；

**Task 级：** 以单次对话为边界，Agent 无论内部发生多少次循环推理与工具执行，最终只向用户呈现该任务的累积修改；

**Tool 级：** 以单次敏感工具调用（如 bash、write\_file）为边界，提供细粒度的逐操作审查。

即使 Agent 连续执行多条命令，调用 Abort 依然能全局回滚到任务开始前的基准状态。

## 使用方式与插件生态

DeltaBox Lite 提供了开箱即用的命令行工具以及主流 Agent 框架的深度整合。

### 1\. 独立 CLI

![[Image 6.gif|图片]]

DeltaBox Lite 独立 CLI 演示

```
# 试运行复杂任务，验证不满意则撤销
```
```
deltabox run --abort -- sh -c 'python setup.py build && rm -rf legacy/'
```
```
# 开启持久化会话，多步执行后统一 Diff 和 Commit
```
```
session_id=$(deltabox session start .)
```
```
deltabox exec "$session_id" -- git checkout -b feature/refactor deltabox exec "$session_id" -- npm run codegen
```
```
deltabox diff "$session_id"      # 审查所有生成的代码差异
```
```
deltabox commit "$session_id"    # 确认保留
```
```
deltabox session stop "$session_id"
```

### 2\. DeepSeek Harness（DSH）插件

在面向通用自主编程的 DeepSeek Harness（包含 Web 与 Desktop 客户端）中，安装 dsh-deltabox 插件后，效果如下：

![[Image 7.gif|图片]]

DeepSeek Harness 插件演示

Agent 每完成一轮代码改动或执行外部命令，界面下方的 审批控制台（Approval Panel） 自动弹出；

界面直观列出当前轮次的所有受控文件增删、改写 diff；

开发者点击 “允许（Allow Once）”，修改就地固化；如果点击 “拒绝（Reject）”，DeltaBox 会回滚该轮所有改动，并反馈给 Agent。

### 3\. Pi Coding Agent 扩展

针对以终端交互为主的轻量编码智能体 Pi，pi-deltabox 扩展直接嵌入到 Node 运行时内：

![[Image 8.gif|图片]]

Pi Coding Agent 扩展演示

用户可以使用 `/sandbox-review` 、 `/sandbox-commit` 和 `/sandbox-abort` 进行操作。

任务执行完毕后，Pi 会主动提醒开发者对暂存的改动做出决定。

## 性能表现

实测数据证明，DeltaBox Lite 凭借精细化的设计，在各项指标上远超传统沙盒方案。

### 1\. 与 Bubblewrap + Git 对比

将 DeltaBox Lite 与业界常用的轻量隔离组合 Bubblewrap + Git 对比：

![[Image 233.webp|图片]]

DeltaBox Lite CB-CoW 与 Bubblewrap + Git 性能对比

### 2\. 局部写入开销对比

在 Linux 6.18（ext4）基准测试中，将 DeltaBox Lite cbcow-through 与基于传统 Copy-Up 的 Sandlock 处理延迟进行对比（p50 / p95）：

![[Image 234.webp|图片]]

DeltaBox Lite CB-CoW 与 Sandlock 局部写入开销对比

此外，DeltaBox Lite 空指令调用开销在 13~14 ms 左右，且不随工程中文件数量增加（10 个 v.s. 1000 个）而发生退化。

## 把尝试交给 Agent，把选择留给你

随着 AI Agent 能力越来越强，如何让 Agent 按照预期、可控地完成工作和任务变得十分重要。智能体沙箱是支撑 AI Agent 可靠执行的关键系统能力。与面向云端、RL 等场景的传统方案不同，在终端侧，轻量化和系统原生是保障用户体验的关键。DeltaBox Lite 结合终端 Agent 的行为特征，同时支持传统 Copy-Up 和新提出的 Copy-Down 模式。

让你的每一次决定，都有后悔药可吃；让 AI Agent 的每一次改动，都有清晰的选择。

**欢迎体验并参与 DeltaBox Lite 开源社区建设！**

**GitHub 仓库：**  
https://github.com/delta-box/deltabox-lite

**项目主页：**  
https://delta-box.github.io/

**文档与协议：**  
https://deepwiki.com/delta-box/deltabox-lite

，时长00:52

<video src="https://mpvideo.qpic.cn/0bc374aa6aaag4aozfor6rvfb76db77qadya.f10002.mp4?dis_k=998fcdbcf504b062fb19c804e0f44b15&amp;dis_t=1792568542&amp;play_scene=10120&amp;auth_info=epm5qcUPPC1Jv+S8lQkMTzhEYUwBLWFOUEc8dmNeLhtDbE9wNj8GJhw+Vjs6bk92N1A3&amp;auth_key=d94a22fff4c77a07f3b1184384c7b3ec&amp;vid=wxv_4740368178801950730&amp;format_id=10002&amp;support_redirect=0&amp;mmversion=false" controls="">您的浏览器不支持 video 标签</video>