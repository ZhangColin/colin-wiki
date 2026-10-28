---
type: source
title: "Superpowers verification-before-completion 验证再收工"
domain: [AI编程, 软件工程, 测试]
tags: [Superpowers, 老谭superpowers系列, verification-before-completion, 验证, 验收对照, 防回归测试]
sources: []
source_path: "raw/articles/16｜superpowersverification-before-completion 验证完成再收工.md"
source_type: article
source_url: "https://mp.weixin.qq.com/s/gZa98ORbO073GS_8NlGt4w"
author: "[[超级奶爸老谭]]"
date_published: "2026-09-14"
created: 2026-09-15
updated: 2026-10-22
status: active
aliases: [verification-before-completion, 验证再收工, 验证完成再收工]
---

# Superpowers verification-before-completion 验证再收工

> 一句话要点：`verification-before-completion` 把「完成」重新定义为「验证过」——**宣布完成之前，必须拿出证据证明它真的完成**。改完 (implemented) ≠ 完成 (completed)；证据只能是测试输出 / 截图 / 接口返回，不能是「我觉得应该没问题」。

## 关键要点

- **三铁律收官**：本技能 = [[Superpowers]] 三条铁律之③「没有新鲜验证证据就不做完成声明」的技能化展开。至此三铁律在老谭系列中全部展开：① TDD（05 篇）→ ② systematic-debugging（14 篇）→ ③ 本篇（16 篇）。老谭自称这是 superpowers 调试类里「我最心疼的一条」——他在上面栽过最大的跟头：改两行代码直接 `git commit` 宣布「修好了」，结果修好 A 场景弄坏 B 场景（改完压根没跑测试、没在页面验证）。
- **核心区分**：「改完了 (implemented)」= 代码动了、自认为对；「完成了 (completed)」= 验证过了、有证据证明对。没有验证证据，宣布的「完成」只是「改完」的客气说法。
- **三层验证**（宣布完成前逐一过）：
  1. **代码层**：测试跑绿、编译通过、lint 无报错；
  2. **行为层**：在真实运行环境里把改动的功能**实际走一遍**——「不是在脑子里过一遍，是在真实的页面上点一遍」；
  3. **验收对照**：对着需求/计划的验收标准逐条核对——对着「现象」而不是对着「代码」（艳姐报「吃饭没标红」，要验证的就是「现在红了」，不是「我改了 xxx 行」）。
- **案例（预算标红）**：预算页「吃饭」超支没标红——前端条件 `spent > limitAmount`，某分类没账时后端 `spent` 返回 `null`，`null > 2000` 为 false 自然不标红。修复：前端把 `spent` 为空按 0 处理。
- **四步收工流程**（案例演示）：
  1. 改完先跑代码层验证（`npm test` 前端 Vitest / `mvn test` 后端 JUnit；本版只动前端，后端测试是「顺带确认没碰坏」）；
  2. 行为层验证：起前后端，浏览器真走一遍——发现「吃饭」实际未超支，**现造超支场景**（预算调成 50 < 支出 103.5），刷新后标红 ✓ + 顶部超支提示 ✓；
  3. 验收对照：复现路径核对 ✓ + 顺带检查没改坏的地方（没超支的不红 ✓、其他操作正常 ✓）；
  4. 防回归 + 提交：加前端测试 `isOverBudget(null, 2000) === false` 钉死，绿了才 `git commit`——「这一次提交时是真的敢说完成，因为每一步都有证据」。
- **三问清单**（宣布完成前自问）：① 测试绿了吗（输出贴出来）？② 在真实环境走过吗（页面点过 / 接口 curl 过，不只是想想）？③ 对照验收标准逐条过了吗？三个都是「是」才算完成；任何一个「我猜应该没问题」都回去补验证。
- **注意事项**：a) 改完和完成是两回事；b) **测试绿 ≠ 行为对**——自动化覆盖不到的渲染 / 交互 / 真实数据要用手在页面走一遍，前端尤其如此；c) 验收对着现象不对着代码；d) 防回归测试别偷懒（「修完不加测试，等于告诉 bug：欢迎再来」）。

## 详细笔记

对应 [[Superpowers]] 18 技能 debugging 类第 3 个（2026-08 仓库口径，[[Superpowers 安装与技能全景]]；v5.0.7 14 技能口径里亦属调试类）。至此 debugging 类四技能已全部收录（systematic-debugging / root-cause-tracing / 本篇 / [[Superpowers defense-in-depth 纵深防御]]）。系列叙事位置：调试三连（14/15/16）第三篇——systematic-debugging 给**流程**、root-cause-tracing 给**因果链**、本篇给**收工标准**。15 篇老王画圈之问（「跑一遍测试、验证修对了——是不是有专门的说法？」）由本篇接住；结尾老王新问（「AI 绕过第一层防线，第二层还能兜住吗？」）+「防线本来就是用来一层一层叠的」为 17 篇 defense-in-depth 埋线。

隐含要点（推论）：验收对照的前提是**可复现的现象**——案例中「吃饭」实际未超支，老谭现造超支场景（调预算至 50）才完成行为层验证；无法构造可观察现象的改动，验证本身就不完整。

## 与已有内容的关联

- **前篇**：[[Superpowers root-cause-tracing 根因追踪]]（老王提问的直接回答）；其「不留防线的修复等于没修」与本篇「防回归测试别偷懒」同旨——「修完加测试钉死」模式在 05/15/16 篇第三次出现（[[Superpowers TDD 红绿循环]] 哨兵测试一脉相承）。
- **同构对照**：与 [[Superpowers 6.0 reviewer 只读重写]] 的 "Do Not Trust the Report"（reviewer 只信自己读到的 diff，不信子代理报告）是同一「不信任口头声明」精神在 **AI 审 AI** 与 **收工自检** 两侧的投影；[[Superpowers subagent-driven-development AI 项目经理]] 两级审查同源。
- **流程衔接**：[[Superpowers finishing-a-development-branch 分支收尾]] 的「合完主干再测」即分支级的一次 verification-before-completion；[[Superpowers TDD 红绿循环]] 是过程内的持续验证，本技能是交付前的最终验证。
- **跨路线**：与 mattpocock `/diagnosing-bugs` 的 red-capable command（能红的命令才算反馈，[[diagnosing-bugs 与 tdd]]）同属「证据纪律」家族。

## ⚠️ 矛盾 / 待澄清

- 与 wiki 既有记载无冲突；该技能为此前「待收录」项，本篇收录后 pending 已更新。
- 本篇口径为**手测优先**（行为层 = 人手在页面点一遍），未讨论 E2E 自动化（Playwright 类）与手测的分工边界——后续篇目是否补充待观察。

## 相关页面

- [[Superpowers]] · [[超级奶爸老谭]] · [[Superpowers root-cause-tracing 根因追踪]] · [[Superpowers systematic-debugging 四阶段调试]] · [[Superpowers TDD 红绿循环]] · [[Superpowers finishing-a-development-branch 分支收尾]] · [[Superpowers 6.0 reviewer 只读重写]] · [[Superpowers subagent-driven-development AI 项目经理]] · [[Superpowers defense-in-depth 纵深防御]] · [[diagnosing-bugs 与 tdd]]
