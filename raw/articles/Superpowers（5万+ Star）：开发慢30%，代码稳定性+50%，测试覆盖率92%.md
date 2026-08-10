---
title: "Superpowers（5万+ Star）：开发慢30%，代码稳定性+50%，测试覆盖率92%"
source: "https://mp.weixin.qq.com/s?__biz=Mzg4MzcyOTQ2NQ==&mid=2247489092&idx=1&sn=3d13e3573d3373431bafed997526ad11&chksm=cf43a312f8342a04e3e9cabebc56bd5a69351fb92e6107f4b99032212b3a1ad7593b42575a43&scene=178&cur_album_id=4354638012475867143&search_click_id=#rd"
author:
  - "[[运维有术]]"
published:
created: 2026-08-01
description:
tags:
---
运维有术 术哥无界 *2026年2月15日 10:09*

> 🚩 2026 年「术哥无界」系列实战文档 X 篇原创计划 第 *28* 篇，AI 编程最佳实战「2026」系列第 *2* 篇
> 
> 大家好，欢迎来到 **术哥无界 | ShugeX ｜ 运维有术** 。
> 
> 我是 **术哥** ，一名专注于 AI 编程、AI 智能体、Agent Skills、MCP、云原生、Milvus 向量数据库的 **技术实践者与开源布道者** ！

> **Talk is cheap, let's explore。无界探索，有术而行。**

![[Image 13.webp|Superpowers 信息图封面]]

Superpowers 信息图封面

你有没有遇到过这种情况：让 AI 帮你写个功能，它二话不说就噼里啪啦敲出一堆代码。跑起来发现逻辑有问题，改了半天还是不对劲。回头一看，它压根没理解你的需求就开始动手了。

这不是 AI 的问题，是缺乏 **规范的工作流程** 。

今天要介绍的这个工具叫 Superpowers，它能让你的 AI 编程代理学会"三思而后行"。简单说，就是强制 AI 先做需求分析、先写测试、再写代码。听起来很烦？但用过的人都说香。

## 你将学到什么

- ✅ Superpowers 是什么，为什么能让 AI 变得更靠谱
- ✅ 核心功能和工作原理
- ✅ 如何在 Claude Code 中安装配置
- ✅ 实战案例：从零开发一个功能
- ✅ 使用技巧和常见问题

## 开始之前

### 你需要准备

- 一台电脑（Mac、Windows 或 Linux 都可以）
- 已安装 Claude Code（如果没有，后续会教你）
- 基本的命令行操作能力
- 一点点耐心

### 预计时间

⏱️ 完成本教程大约需要 20-30 分钟（阅读 + 动手实践）

### 难度等级

⭐⭐ 入门级 - 有点技术基础就能跟上

## 为什么需要 Superpowers？

说实话，我第一次用 AI 编程工具的时候挺兴奋的。输入需求，等几秒，代码就出来了。多爽啊！

但用多了就发现问题来了。

### AI 编程的常见坑

**坑一：不问清楚就动手**

你让它实现一个"用户登录功能"，它直接开始写代码。等到你发现它做的是"邮箱密码登录"，而你要的是"手机验证码登录"，已经浪费半小时了。

这种事我遇到过不下十次。每次都想着"这次应该能理解吧"，结果每次都被打脸。

**坑二：跳过测试直接写代码**

写完代码能跑就万事大吉？没有测试，后期改一个 bug 引出三个新 bug。这种事我见得太多了。

有一次我让 AI 写了个支付模块，当时能跑就上线了。结果后来发现一个边缘情况没处理，用户付了钱但订单没生成。排查了整整一天。

如果一开始就写测试，这个问题早就暴露出来了。

**坑三：代码质量参差不齐**

有时候生成的代码很漂亮，模块化、有注释、命名规范。有时候又像屎山，变量名 a、b、c，逻辑绕来绕去。

你永远不知道下一次会是什么样。

![[Image 14.webp|传统 AI 编程 vs Superpowers 流程对比]]

传统 AI 编程 vs Superpowers 流程对比

*图：传统 AI 编程 vs Superpowers 流程对比*

### Superpowers 的解决思路

Superpowers 的核心理念很简单： **不让 AI 想跳就跳** 。

它设置了一系列"技能"，每个技能都是一个强制性的检查点。AI 想写代码？不好意思，先把需求理清楚。想提交代码？不好意思，先过测试。

这听起来像是在限制 AI，实际上是在 **保护你的项目** 。

就像开车系安全带，虽然麻烦，但关键时刻能救命。

## Superpowers 是什么？

![[Image 15.webp|Superpowers 7 阶段工作流程]]

Superpowers 7 阶段工作流程

*图：Superpowers 7 阶段开发流程*

用一句话概括：\*\*Superpowers 是 AI 编程代理的"工作规范框架"\*\*。

打个比方，AI 编程代理就像一个刚毕业的程序员，技术能力有，但缺乏经验。你让它干活，它可能做得很快，但质量参差不齐。

Superpowers 就是给这个程序员配了一个"老师傅"——每做一步都要汇报，老师傅点头了才能继续。

### 背景故事

Superpowers 由 **Jesse Vincent** 创建。这个人可不是无名之辈，他是知名开源项目 RT（Request Tracker）的作者，也是多个热门 npm 包的贡献者。

他在用 Claude Code 开发项目时发现：AI 确实能写代码，但经常会"自作聪明"跳过一些重要步骤。比如不写测试就提交代码，或者没理解需求就动手。

于是他开发了这套技能系统，把最佳实践变成"硬规则"。不是"建议你这样做"，而是"必须这样做"。

截至 2026 年 2 月，这个项目在 GitHub 上已经获得 **51,400+ stars** ，有 16+ 位贡献者参与维护。社区贡献了 30 多个额外的技能，覆盖了各种开发场景。

### 核心工作流程

Superpowers 把软件开发拆成了 7 个阶段，每个阶段都有对应的技能：

```
1. 头脑风暴（Brainstorming）
   ↓ 先问清楚要做什么，形成设计文档
   
2. 工作树隔离（Git Worktrees）
   ↓ 创建独立的开发环境，互不干扰
   
3. 编写计划（Writing Plans）
   ↓ 把大任务分解成 2-5 分钟的小任务
   
4. 子代理开发（Subagent Development）
   ↓ 每个任务由独立的 AI 代理处理
   
5. 测试驱动开发（TDD）
   ↓ 先写测试，再写代码，必须通过
   
6. 代码审查（Code Review）
   ↓ 双重检查：功能符合性 + 代码质量
   
7. 完成分支（Finishing Branch）
   ↓ 合并代码或创建 PR
```

这套流程看起来繁琐，但每一步都有它的道理。而且 Superpowers 的设计很智能，简单任务会自动跳过一些步骤，复杂任务才会走完整流程。

下面我们详细说说几个关键环节。

## 核心功能详解

### 1\. 头脑风暴技能（Brainstorming）

这是 Superpowers 重要的关卡，也是我觉得最有价值的部分。

当你提出一个需求时，AI 不会直接动手，而是先启动"头脑风暴"技能。它会做这些事：

**探索项目上下文**

- 读取你的 README、配置文件
- 查看最近的 Git 提交记录
- 了解项目的技术栈和架构

**逐个澄清问题**

- 一次只问一个问题，不会一股脑抛给你十个问题
- 根据你的回答调整后续问题
- 不会假设答案

**提出多个方案**

- 给出 2-3 个可行的实现方案
- 说明每个方案的优缺点
- 让你做选择，而不是帮你做决定

**保存设计文档**

- 把讨论结果整理成文档
- 后续实现时可以随时参考
- 防止做到一半忘了最初的设计

**硬性规定** ：在用户批准设计之前， **禁止写任何代码** 。

```
<HARD-GATE>
Do NOT invoke any implementation skill, write any code, 
scaffold any project, or take any implementation action 
until you have presented a design and the user has approved it.
</HARD-GATE>
```

这就像你跟产品经理沟通需求，先把需求理清楚再开工。省得做了半天发现理解错了。

我之前让 AI 做一个"搜索功能"，它直接写了一个简单的模糊匹配。后来才发现我要的是"带拼音搜索和同义词扩展的全文检索"。如果一开始就问清楚，哪会有这些弯路。

### 2\. 测试驱动开发技能（TDD）

这个技能让我又爱又恨。

爱的是，它真的能保证代码质量。恨的是，有时候写测试比写代码还费劲。但用过一段时间后，我真心觉得这个约束太有必要了。

Superpowers 的 TDD 流程严格遵循经典的 RED-GREEN-REFACTOR 循环：

```
RED     → 写一个会失败的测试
验证     → 运行测试，确认它真的失败了
GREEN   → 写最少的代码让测试通过
验证     → 运行测试，确认它通过了
REFACTOR → 重构代码，保持测试通过
重复     → 回到第一步，写下个测试
```

**铁律** ：如果写了代码但没有先写测试？ **删掉代码，重新开始** 。

别觉得夸张。这个规则救了我好几次。之前写了一个"能跑"的功能，结果后来发现边缘情况没处理，空数组会报错。如果一开始就写测试，这个问题在开发阶段就暴露出来了。

Superpowers 的 TDD 技能还有一个特点：它会 **验证测试确实失败了** 。有时候你以为测试会失败，结果因为各种原因通过了（比如测试写错了）。Superpowers 会强制你确认每个测试的预期结果。

### 3\. 子代理协作技能（Subagent Development）

这个功能挺有意思，也是 Superpowers 区别于其他 AI 工具的地方。

假设你要开发一个"用户系统"，里面有注册、登录、密码重置、个人资料等功能。传统方式是让一个 AI 顺序处理所有功能。

Superpowers 可以把每个功能拆成独立的子任务，让多个 AI 代理并行处理。每个子任务完成后，还要经过两轮审查：

**规格合规审查**

- 实现是否完全符合设计文档
- 有没有添加需求以外的功能
- 有没有遗漏需求里的功能

**代码质量审查**

- 命名规范是否一致
- 测试覆盖率是否达标
- 是否有重复代码
- 是否有潜在的性能问题

这样可以防止 AI "过度构建"——加了需求里没有的功能。也可以防止"偷工减料"——漏掉需求里的功能。

审查者是另一个独立的 AI 代理，相当于"第二双眼睛"。两个代理互相制衡，质量更有保障。

### 4\. Git Worktree 隔离技能

如果你用过 Git 的 worktree 功能，应该知道它的好处。

Superpowers 会为每个开发任务创建一个独立的工作目录。这样你可以同时处理多个任务，互不干扰。

举个例子：

- 主分支在 `/project/main`
- 功能 A 在 `/project/feature-a`
- 功能 B 在 `/project/feature-b`

三个目录共享同一个 Git 仓库，但各自有独立的工作区。就算 AI 把功能 A 搞砸了，也不会影响功能 B 的代码。

这个功能对团队协作特别有用。多人同时开发不同功能，各有各的隔离环境，最后统一合并。

## 如何开始使用？

好了，说了这么多，该动手了。下面是详细的安装步骤。

### 第一步：安装 Claude Code

如果你还没装 Claude Code，先装这个。

Claude Code 是 Anthropic 官方出的命令行工具，让 Claude 直接在你的终端里帮你写代码。它支持读取本地文件、执行命令、创建文件等操作，是 Superpowers 运行的基础。

**Mac / Linux 安装：**

```
# 使用 npm 安装（需要先装 Node.js）
npm install -g @anthropic-ai/claude-code
```

**Windows 安装：**

```
# 使用 npm 安装
npm install -g @anthropic-ai/claude-code
```

安装完成后，验证一下：

```
claude --version
```

✅ 如果你看到版本号（比如 `1.0.0` ），说明安装成功了！

\*\*如果提示 "command not found"\*\*：

1. 确认 Node.js 已正确安装（运行 `node --version` ）
2. 检查 npm 全局路径是否在 PATH 中
3. 尝试关闭终端重新打开

### 第二步：配置 Anthropic API Key

Claude Code 需要 API Key 才能工作。

如果你还没有 Anthropic 账号，去官网注册一个： https://www.anthropic.com/

注册后在控制台创建 API Key（注意：API Key 只会显示一次，记得保存）。

拿到 API Key 后，运行：

```
claude login
```

按提示粘贴你的 API Key。系统会自动保存，下次启动不需要重新输入。

### 第三步：安装 Superpowers

现在到重头戏了。打开 Claude Code，运行以下命令：

```
# 1. 注册 Superpowers 市场
/plugin marketplace add obra/superpowers-marketplace
```

等待几秒钟，会看到类似这样的输出：

```
Marketplace added: obra/superpowers-marketplace
```

然后安装插件：

```
# 2. 安装 Superpowers 插件
/plugin install superpowers@superpowers-marketplace
```

安装完成后，重启 Claude Code：

```
# 3. 重启 Claude Code
/exit
```

重新进入 Claude Code：

```
claude
```

### 第四步：验证安装成功

怎么确认 Superpowers 已经装好了？

试着让 AI 帮你做点什么：

```
帮我实现一个简单的计算器功能
```

如果你发现 AI 开始问你一堆问题，而不是直接甩代码，说明 Superpowers 已经在工作了！

示例输出：

```
在开始实现之前，我想先了解一些细节：

1. 这个计算器是命令行工具还是 Web 应用？
2. 需要支持哪些运算？（加减乘除、括号、科学计算？）
3. 数据精度有要求吗？

请回答这些问题，我会据此设计实现方案。
```

✅ 恭喜你，安装成功！

## 实战案例：开发一个待办事项功能

光说不练假把式。我们来走一遍完整流程，感受一下 Superpowers 的工作方式。

![[Image 16.webp|待办事项功能开发流程]]

待办事项功能开发流程

*图：待办事项功能完整开发流程*

### 场景设定

假设你有一个 Node.js + Express 项目，想加一个"待办事项"功能。

### 第一步：提出需求

在 Claude Code 里输入：

```
我想在项目里加一个待办事项功能，用户可以添加、删除、标记完成
```

### 第二步：头脑风暴阶段

Superpowers 启动后，AI 会开始探索项目并提问：

```
我注意到你的项目使用了 Express 框架和 SQLite 数据库。
在设计待办事项功能之前，我想确认几个问题：

1. 待办事项需要哪些字段？（标题、描述、截止日期、优先级？）
2. 是否需要分类或标签功能？
3. 用户认证是否需要？（目前是单用户还是多用户？）
4. 是否需要 API 文档？

请逐一回答，我会根据你的回答设计方案。
```

你可以这样回答：

```
1. 只需要标题和完成状态两个字段
2. 暂不需要分类
3. 暂时是单用户，不需要认证
4. 需要 API 文档
```

AI 会继续追问：

```
明白了。还有几个细节：

1. 删除是物理删除还是软删除（标记为已删除）？
2. 标题有长度限制吗？
3. 需要支持批量操作吗（批量删除、批量标记完成）？
```

这样的问答会持续几轮，直到需求足够清晰。

### 第三步：确认设计文档

问答结束后，AI 会生成一份设计文档：

```
## 待办事项功能设计文档

### 1. 数据模型
| 字段 | 类型 | 说明 |
|------|------|------|
| id | INTEGER | 主键，自增 |
| title | TEXT | 待办标题，最长 200 字符 |
| completed | BOOLEAN | 完成状态，默认 false |
| created_at | DATETIME | 创建时间 |
| updated_at | DATETIME | 更新时间 |

### 2. API 设计
| 方法 | 路径 | 说明 |
|------|------|------|
| POST | /api/todos | 创建待办 |
| GET | /api/todos | 获取待办列表 |
| GET | /api/todos/:id | 获取单个待办 |
| PUT | /api/todos/:id | 更新待办 |
| DELETE | /api/todos/:id | 删除待办 |

### 3. 文件结构
- routes/todos.js - 路由定义
- controllers/todoController.js - 业务逻辑
- models/todo.js - 数据模型
- tests/todos.test.js - 测试用例
- docs/todos-api.md - API 文档

请确认是否按此方案实施？
```

你需要明确回复"确认"或提出修改意见。

### 第四步：测试先行

确认设计后，AI 会先写测试：

```
// tests/todos.test.js
const request = require('supertest');
const app = require('../app');

describe('Todo API', () => {
  beforeEach(async () => {
    // 清空测试数据库
    await db.run('DELETE FROM todos');
  });

  describe('POST /api/todos', () => {
    test('应该创建新的待办事项', async () => {
      const response = await request(app)
        .post('/api/todos')
        .send({ title: '学习 Superpowers' });
      
      expect(response.status).toBe(201);
      expect(response.body.title).toBe('学习 Superpowers');
      expect(response.body.completed).toBe(false);
    });

    test('标题为空时应该返回错误', async () => {
      const response = await request(app)
        .post('/api/todos')
        .send({ title: '' });
      
      expect(response.status).toBe(400);
      expect(response.body.error).toBeDefined();
    });

    test('标题超长时应该返回错误', async () => {
      const longTitle = 'a'.repeat(201);
      const response = await request(app)
        .post('/api/todos')
        .send({ title: longTitle });
      
      expect(response.status).toBe(400);
    });
  });

// 更多测试用例...
});
```

AI 会运行这些测试，确认它们全部失败（因为还没写实现代码）。这一步很重要，确保测试真的能检测问题。

### 第五步：实现功能

测试确认失败后，AI 才开始写实现代码：

```
// models/todo.js
const db = require('../db');

class Todo {
staticasync create(title) {
    if (!title || title.trim() === '') {
      thrownewError('标题不能为空');
    }
    if (title.length > 200) {
      thrownewError('标题不能超过200个字符');
    }

    const result = await db.run(
      \`INSERT INTO todos (title, completed, created_at, updated_at)
       VALUES (?, ?, datetime('now'), datetime('now'))\`,
      [title.trim(), false]
    );
    
    returnthis.findById(result.lastID);
  }

staticasync findById(id) {
    const row = await db.get(
      'SELECT * FROM todos WHERE id = ?',
      [id]
    );
    return row || null;
  }

// 更多方法...
}

module.exports = Todo;
```

每完成一个功能，AI 就运行相关测试，确保通过后再继续。

### 第六步：代码审查

所有功能完成后，AI 会自动触发代码审查：

```
## 代码审查报告

### 规格合规检查 ✅
- [x] POST /api/todos 实现完整
- [x] GET /api/todos 实现完整
- [x] PUT /api/todos/:id 实现完整
- [x] DELETE /api/todos/:id 实现完整
- [x] 未添加设计文档以外的功能

### 代码质量检查
- [x] 命名规范一致
- [x] 错误处理完善
- [x] SQL 使用参数化查询，无注入风险
- [x] 测试覆盖率 92%

### 建议（非阻塞）
1. 可以添加日志记录，方便调试
2. 考虑添加分页功能（后续版本）

审查结果：通过 ✅
```

### 第七步：完成提交

审查通过后，AI 会帮你提交代码：

```
git add .
git commit -m "feat: add todo API with TDD

- Add todo model with CRUD operations
- Add API routes for todo management
- Add comprehensive test cases (92% coverage)
- Add API documentation

Co-authored-by: Claude <claude@anthropic.com>"
```

整个过程虽然比"直接让 AI 写代码"慢一些，但产出物的质量高很多。而且因为有完整的测试和文档，后期维护成本大大降低。

## 最佳实践与技巧

用了几个月 Superpowers，我总结了一些经验分享给你。

### 技巧一：给 AI 提供足够的上下文

Superpowers 的头脑风暴阶段会读取你的项目文件。但如果项目结构混乱，AI 也摸不着头脑。

**建议：**

- 保持 README 更新，写清楚项目用途和结构
- 关键文件加注释，说明模块职责
- 配置文件写清楚每个配置项的含义
- 保持合理的目录结构

一个清晰的项目结构可以让 AI 更好地理解上下文，生成更符合项目风格的代码。

### 技巧二：不要急于求成

刚开始用 Superpowers 会觉得慢。以前 5 分钟能出代码的事，现在要 20 分钟。

但想想后期省下的调试时间，还是值得的。

稳住，这是在投资代码质量。就像建房子打地基，前期花时间，后期才稳固。

我用了一个月后统计了一下，虽然单个功能的开发时间增加了 30%，但整体项目交付时间反而缩短了 20%。因为后期返工大幅减少。

### 技巧三：善用"简化模式"

Superpowers 默认会走完整流程，但不是所有任务都需要。

如果只是改个文案、修个样式、加个日志，可以告诉 AI：

```
这个小修改，使用简化流程
```

Superpowers 会智能跳过部分步骤，只在必要时启用完整工作流。这样既保证了质量，又不会太繁琐。

### 技巧四：定制你自己的技能

Superpowers 的技能是可以定制的。你可以在项目里创建自己的技能，满足特定需求。

比如，你的团队有特定的代码规范，可以创建一个"代码规范检查"技能：

```
.superpowers/
└── skills/
    └── team-code-style/
        ├── skill.md
        └── templates/
```

技能文件示例：

```
# team-code-style

## 触发条件
当创建或修改 JavaScript/TypeScript 文件时

## 检查规则
1. 使用 const/let，禁止 var
2. 使用单引号字符串
3. 函数必须有 JSDoc 注释
4. 禁止 console.log（使用 logger）
```

这样每次写代码都会自动检查团队规范。

### 技巧五：利用子代理处理大型任务

当任务比较复杂时，可以让 Superpowers 拆成子任务并行处理。

比如重构一个大模块：

```
把 auth 模块从 JavaScript 迁移到 TypeScript，拆成子任务处理
```

Superpowers 会把任务拆成：

- 迁移类型定义
- 迁移工具函数
- 迁移核心逻辑
- 迁移测试用例
- 更新依赖

每个子任务独立处理，互不阻塞。最后统一整合。

## 常见问题

### Q1：Superpowers 会拖慢开发速度吗？

刚开始会感觉慢。但这是把时间花在了前期设计和测试上，后期返工的概率会大大降低。

我用了一个月后统计了一下，单个功能的开发时间增加了约 30%，但整体项目交付时间缩短了约 20%。因为 bug 少了，后期维护成本低了。

### Q2：支持哪些 AI 编程工具？

目前官方支持：

- **Claude Code** （推荐，体验出色）
- **OpenAI Codex**
- **OpenCode**

如果你用的是 Cursor、Windsurf、Continue 等 IDE 内置的 AI，暂时用不了 Superpowers。因为这些工具没有开放插件接口。

### Q3：收费吗？

完全免费，MIT 开源协议。你可以自由使用、修改、分发。

### Q4：如果不装 Superpowers，Claude Code 还能用吗？

当然可以。Superpowers 只是一个"增强包"，不影响 Claude Code 本身的功能。

你可以把它理解成 VS Code 的插件——装了有额外功能，不装也不影响基本使用。

### Q5：团队协作怎么用？

Superpowers 特别适合团队协作。它提供了：

1. **统一的代码质量标准** ：无论谁开发，都遵循相同的流程
2. **代码审查机制** ：双重审查确保代码质量
3. **设计文档** ：每个人都能看到功能的设计意图
4. **Git Worktree 隔离** ：多人并行开发不冲突

新成员加入团队后，跟着 Superpowers 的流程走，很快就能上手项目规范。

### Q6：可以关闭某些技能吗？

可以。在项目根目录创建 `.superpowers/config.json` ：

```
{
  "disabledSkills": [
    "using-git-worktrees"
  ]
}
```

这样 Git Worktree 技能就会被禁用。

### Q7：遇到问题去哪里求助？

1. **GitHub Issues** ：https://github.com/obra/superpowers/issues
2. **Reddit 社区** ：r/ClaudeAI 板块搜索 "superpowers"
3. **查看文档** ：项目 README 写得很详细

社区响应挺快的，通常 1-2 天内会有回复。

## 总结

说到底，Superpowers 做的事情很简单： **让 AI 编程代理变得更专业** 。

它不是让 AI 更聪明，而是让 AI 更守规矩。就像给一个技术能力强但缺乏经验的新人配了个老师傅，每一步都把关。

**Superpowers 的核心价值：**

1. **强制最佳实践** ：TDD、代码审查、文档先行，这些"应该做但总被跳过"的事情变成了硬性要求
2. **提高代码质量** ：测试覆盖率高，bug 少，维护成本低
3. **规范化流程** ：无论谁开发，产出物的质量都一致
4. **可扩展性** ：可以定制自己的技能，适应不同项目的需求

**适合谁用：**

- 经常被 AI 生成代码坑过的开发者
- 想提高代码质量的个人或团队
- 需要标准化开发流程的项目
- 愿意在前期多花时间换取后期省心的"懒人"

**可能不太适合：**

- 只是想快速验证想法的原型开发
- 简单的一次性脚本
- 不想改变现有工作流程的人

如果你经常被 AI 生成的代码坑过，或者想要更稳定的开发流程，试试 Superpowers。花 20 分钟安装配置，可能帮你省下无数小时的调试时间。

*本教程基于 Superpowers v4.3 版本编写，不同版本可能略有差异*

**好啦，谢谢你观看我的文章，如果喜欢可以点赞转发给需要的朋友，我们下一期再见！敬请期待！**

**扫码关注，获取更多 AI 工具的实战经验和最佳实践。不错过每一篇干货！**

![[Image 17.webp|联系方式]]

**微信扫一扫赞赏作者**

AI编程最佳实战「2026」 · 目录