---
title: "从 Chat UI 到 Agent UI：如何设计一个真正可用的智能体交互界面"
source: "https://mp.weixin.qq.com/s/p34PXv5ZFT0ie_aiLI0WQQ"
author:
  - "[[炼丹上岸]]"
published:
created: 2026-09-03
description: "前言很多团队在开发 Agent 产品时，最先做出来的通常是一个聊天页面：左边是用户消息；右边是 AI 回复；底"
tags:
---
炼丹上岸 炼丹上岸 *2026年8月30日 00:00*

![[Image 216.webp|图片]]

## 前言

很多团队在开发 Agent 产品时，最先做出来的通常是一个聊天页面：

- 左边是用户消息；
- 右边是 AI 回复；
- 底部是输入框；
- 后端通过 SSE 返回流式文本。

从界面上看，它和 ChatGPT 很像，因此也很容易让人产生一种错觉：

> 只要把聊天页面做得更漂亮，就能得到一个好的 Agent UI。

但真正进入 Agent 场景后，很快就会发现，传统聊天界面的能力远远不够。

一个普通 Chatbot 的主要任务，是理解用户问题并返回一段回答。一个 Agent 则可能需要先制订计划，再调用工具，读取文件，查询数据库，启动子任务，等待审批，生成文档，并在几分钟后返回最终结果。

因此，Agent UI 面对的不是一次简单的问答，而是一个持续运行、不断产生状态变化的执行过程。

用户真正关心的，也不再只是“AI 回答了什么”，而是：

- Agent 当前正在做什么；
- 为什么还没有完成；
- 已经执行了哪些步骤；
- 调用了哪些工具；
- 工具调用是否成功；
- 是否需要自己确认；
- 能否暂停、取消或者重试；
- 刷新页面之后还能不能继续；
- 最终生成的文档、表格和图表在哪里。

所以，Agent UI 的核心并不是聊天气泡，而是：

> 如何让用户理解 Agent 的执行过程，参与关键决策，并最终获得可操作的业务结果。

---

## 一、Chat UI 和 Agent UI 的本质区别

传统 Chat UI 主要围绕消息展开。

它通常只需要处理三类状态：

```js
用户消息Assistant 消息正在生成
```

一次交互的过程也比较简单：

```js
用户输入   ↓发送请求   ↓模型生成文本   ↓前端流式展示   ↓生成完成
```

这类界面很适合：

- 智能客服；
- FAQ 问答；
- 知识库检索；
- 简单文本生成；
- 日常聊天。

Alibaba ChatUI、Chatscope 等组件库，主要解决的就是这一类问题，包括消息列表、输入框、快捷回复、响应式布局和移动端适配。

但 Agent 的执行过程更接近一个后台任务系统：

```js
用户提出目标   ↓Agent 创建 Run   ↓分析任务   ↓制订计划   ↓调用工具   ↓处理工具结果   ↓更新计划   ↓等待用户确认   ↓继续执行   ↓生成最终结果
```

这个过程可能持续几十秒，也可能持续几十分钟。

页面可能关闭，用户可能切换会话，网络可能断开，工具可能失败，Agent 也可能暂停等待用户输入。

因此，Agent UI 需要围绕以下对象来设计：

```nginx
ConversationMessageRunStepTool CallApprovalArtifactEvent
```

其中：

- Conversation 表示一整段会话；
- Message 表示用户或 Agent 的消息；
- Run 表示 Agent 的一次完整执行；
- Step 表示执行计划中的某个步骤；
- Tool Call 表示一次工具调用；
- Approval 表示等待用户确认的操作；
- Artifact 表示生成的文档、表格、图表等成果；
- Event 表示执行过程中发生的一次状态变化。

这意味着 Agent UI 不应该只是一个 Message List，而应该是一个围绕 Run 展开的工作界面。

---

## 二、Agent UI 应该围绕“执行过程”设计

传统聊天页面的中心是消息。

Agent UI 的中心则应该是执行过程。

用户发送：

```js
请分析这份招标文件，并生成投标建议。
```

背后可能发生：

```js
读取文件   ↓解析 PDF   ↓提取资格条件   ↓提取评分办法   ↓检索历史案例   ↓分析风险   ↓生成投标建议
```

如果页面只显示一个“正在思考”的动画，用户很难判断：

- 系统是否卡住了；
- 当前执行到了哪里；
- 是否真的读取了文件；
- 是否调用了企业知识库；
- 预计还需要多久；
- 某一步失败后会不会继续。

更合理的页面应该把执行过程转换为用户可以理解的状态。

例如：

```js
正在分析招标文件✓ 文件读取完成✓ 已提取资格条件● 正在分析评分办法○ 等待匹配历史案例○ 等待生成投标建议
```

这类展示不是为了暴露模型内部的完整思维过程，而是为了呈现可解释的执行状态。

用户不需要看到模型每一步隐含推理，但需要知道：

- Agent 当前处在哪个阶段；
- 已经做了什么；
- 下一步准备做什么；
- 是否出现错误；
- 是否需要用户参与。

因此，Agent UI 的第一原则应该是：

> 把 Agent 的内部执行状态转换为用户可以理解、可以判断、可以操作的界面状态。

---

## 三、一个成熟的 Agent UI 应该是什么结构

一个复杂 Agent 产品，通常不适合只使用单栏聊天页面。

更合理的是三栏或者两栏半布局：

```js
┌──────────────────────────────────────────────────────────┐│ Agent 名称 / 当前状态 / 模型 / 停止 / 重新执行           │├──────────────┬──────────────────────────┬────────────────┤│              │                          │                ││ 会话列表     │       对话与执行区       │   工作区       ││              │                          │                ││ 历史会话     │ 用户消息                 │ 文档           ││ 运行中会话   │ Agent 回复               │ 表格           ││ 失败会话     │ 执行计划                 │ 图表           ││ 等待确认     │ 工具调用                 │ 文件预览       ││              │ 审批卡片                 │ 代码预览       ││              │                          │                │├──────────────┴──────────────────────────┴────────────────┤│ 输入框 / 附件 / 技能 / Agent / 模型 / 发送 / 停止       │└──────────────────────────────────────────────────────────┘
```

左侧负责会话管理，中间负责交流和执行过程，右侧负责展示复杂成果。

这样的布局能够把“聊天”和“工作”分开。

聊天区域适合展示：

- 用户提问；
- Agent 的解释；
- 执行计划；
- 工具调用摘要；
- 审批请求；
- 最终结果摘要。

工作区适合展示：

- 长文档；
- 数据表格；
- 图表；
- 代码；
- PDF；
- HTML 页面；
- 可编辑报告；
- 数据分析结果。

例如 Agent 生成了一份 5000 字的风险报告。

不应该把整份报告全部塞进消息气泡，而可以在聊天区显示：

```css
已生成《项目风险分析报告》。4
[打开报告] [导出 Word] [继续修改]
```

用户点击后，在右侧工作区打开完整报告。

这样页面职责更加清晰：

```js
聊天区负责沟通和解释工作区负责承载实际成果
```

---

## 四、Agent UI 的核心不是 Message，而是 Message Part

很多聊天页面的数据模型是：

```css
interface Message {  role: "user" | "assistant";  content: string;}
```

这对普通聊天足够，但对 Agent 来说过于简单。

因为 Agent 的一条回复中，可能同时包含：

- 文本；
- 工具调用；
- 工具结果；
- 执行计划；
- 来源引用；
- 审批请求；
- 生成文件；
- 错误信息。

因此更合理的数据结构是：

```nginx
Message   ↓多个 Message Part
```

例如：

```css
interface AgentMessage {  id: string;  role: "user" | "assistant";  status: "pending" | "streaming" | "completed" | "failed";  parts: MessagePart[];}
```

其中：

```bash
type MessagePart =  | TextPart  | PlanPart  | ToolPart  | ApprovalPart  | ArtifactPart  | SourcePart  | ErrorPart;
```

一条 Agent 消息可以是：

```css
Assistant Message├── Text Part│   “我会先分析文件结构。”├── Plan Part│   5 个执行步骤├── Tool Part│   正在解析 PDF├── Tool Part│   正在查询案例库├── Artifact Part│   风险分析报告└── Text Part    “已经完成分析。”
```

这种设计带来的最大好处是：

> 前端不再把所有内容都当成字符串，而是根据内容类型选择专门的组件。

例如：

```javascript
function MessagePartRenderer({ part }: { part: MessagePart }) {  switch (part.type) {    case "text":      return <MarkdownContent text={part.text} />;    case "plan":      return <PlanCard plan={part} />;    case "tool":      return <ToolCard tool={part} />;    case "approval":      return <ApprovalCard approval={part} />;    case "artifact":      return <ArtifactCard artifact={part} />;    case "error":      return <ErrorCard error={part} />;  }}
```

assistant-ui 和 Vercel AI SDK UI 都采用了类似思想：消息不是简单文本，而是由多个结构化 Part 组成。

这是从 Chat UI 走向 Agent UI 最重要的一次数据模型升级。

---

## 五、工具调用是 Agent UI 的核心内容

Agent 和普通聊天机器人最大的区别之一，就是 Agent 会调用工具。

例如：

```nginx
search_documentsquery_databaseparse_filegenerate_chartsend_emailcreate_document
```

最原始的实现通常把工具调用直接显示为 JSON：

```json
{  "tool": "search_documents",  "args": {    "keyword": "评分办法",    "top_k": 20  }}
```

这种方式虽然方便开发，但对普通用户非常不友好。

用户并不关心内部工具名称，也不关心参数结构。

他真正关心的是：

```js
正在搜索企业案例库关键词：评分办法搜索范围：当前企业已找到 12 个相关文档
```

因此工具 UI 应该分成两个层次。

默认层面展示业务含义：

```js
✓ 已完成案例检索  找到 12 个相关项目  耗时 1.8 秒
```

展开后再展示技术详情：

```javascript
工具名称：search_documents参数：{"keyword":"评分办法","top_k":20}Trace ID：trace_10001
```

同时，不同工具应该拥有不同的展示组件。

例如：

```js
搜索工具    → 搜索结果列表数据库查询    → 数据表格图表生成    → 图表组件文件解析    → 文件进度卡片文档生成    → Artifact 卡片发送邮件    → 邮件预览与确认按钮
```

不应该所有工具都使用同一张 JSON 卡片。

更合理的方式是建立 Tool Renderer Registry：

```javascript
const toolRenderers = {  search_documents: SearchResultCard,  query_database: DataTableCard,  generate_chart: ChartCard,  create_document: DocumentArtifactCard,  send_email: EmailApprovalCard,};
```

前端根据工具名称选择专用渲染器。

这种模式也是 Vercel AI SDK、assistant-ui 和 CopilotKit 中非常重要的一类设计。

---

## 六、执行计划应该成为用户理解 Agent 的入口

复杂 Agent 往往会先生成执行计划。

例如：

```js
分析招标文件├── 读取文件├── 提取资格要求├── 提取评分办法├── 匹配历史案例└── 生成投标建议
```

如果计划只是 Agent 内部数据，用户看不到，就失去了很大价值。

一个好的 Plan UI 应该显示：

```js
执行计划                              3 / 5✓ 读取招标文件✓ 提取资格条件● 分析评分办法○ 匹配历史案例○ 生成投标建议
```

它可以让用户快速知道：

- 任务被拆成了哪些步骤；
- 当前执行到哪一步；
- 哪些步骤已经完成；
- 哪一步失败；
- 是否还有后续步骤。

Plan 的状态可以设计为：

```bash
type StepStatus =  | "pending"  | "running"  | "completed"  | "failed"  | "skipped";
```

需要特别注意，Plan 和 Todo 不应该被混为一谈。

Plan 是：

```js
展示给用户看的执行计划
```

Todo Tool 是：

```nginx
Agent 自己可以调用和更新的任务管理工具
```

Todo 的变化可以投影成 Plan UI，但二者在架构中仍然是两个概念。

---

## 七、Agent UI 必须支持 Human-in-the-loop

Agent 的能力越强，越需要人工确认。

如果 Agent 可以：

- 删除文件；
- 发送邮件；
- 修改数据库；
- 提交审批；
- 发布内容；
- 创建生产任务；
- 执行付款；

就不能允许它在没有用户确认的情况下自动执行。

此时 Agent 应该进入等待状态：

```sql
running   ↓waiting_user   ↓用户确认   ↓running
```

页面需要展示 Approval Card：

```css
需要你的确认Agent 准备向 126 位客户发送通知邮件。主题：产品升级通知接收人数：126发送时间：立即发送[查看邮件内容] [取消] [确认发送]
```

这类交互的重点，不只是提供两个按钮，而是要让后台 Agent 真正暂停。

用户确认后，Agent 从暂停位置继续，而不是重新运行整个任务。

因此后端需要支持：

```cs
approval.requiredapproval.approvedapproval.rejected
```

前端收到 `approval.required` 后：

1. 显示审批卡片；
2. 将 Run 状态设置为 `waiting_user` ；
3. 停止展示“正在执行”；
4. 等待用户操作；
5. 用户确认后发送结果；
6. 后端恢复 Run。

Human-in-the-loop 是 Agent 产品从“自动生成内容”走向“参与真实业务操作”的关键能力。

---

## 八、复杂结果应该进入 Artifact Workspace

Agent 经常会生成复杂结果。

例如：

- 一份报告；
- 一个表格；
- 一张图表；
- 一段代码；
- 一个 HTML 页面；
- 一份投标文件；
- 一个数据分析看板。

这些内容不适合只作为聊天消息存在。

因此需要引入 Artifact 概念。

```css
interface Artifact {  id: string;  type: "document" | "table" | "chart" | "code" | "html";  title: string;  status: "creating" | "ready" | "failed";  version: number;}
```

聊天区只负责说明：

```js
已经为你生成销售分析报告。
```

Artifact 区负责真正展示：

```js
报告正文可编辑内容版本历史导出按钮分享按钮继续修改
```

这种设计会让 Agent 更像一个工作助手，而不是一个只能输出文字的聊天机器人。

Artifact 还可以支持版本迭代：

```js
销售分析报告 v1   ↓用户要求补充区域对比   ↓销售分析报告 v2   ↓用户要求加入图表   ↓销售分析报告 v3
```

对话负责描述修改意图，Artifact 负责保存真实成果。

---

## 九、Agent UI 的状态必须围绕 Run 管理

普通聊天页面通常只有：

```nginx
idlestreamingcompleted
```

但 Agent Run 的状态会复杂得多：

```nginx
queuedrunningwaiting_toolwaiting_userpausedcompletedfailedcancelled
```

顶部状态区域可以展示：

```js
● 正在分析文件已运行 01:32已完成 3 / 6 个步骤[停止] [在后台继续]
```

当 Agent 正在执行时，用户切换到其他会话，当前 SSE 可以关闭，但 Run 不能自动停止。

必须区分：

```js
取消页面订阅≠取消 Agent 执行
```

用户再次进入时，页面应该重新恢复：

```js
会话消息Run 状态Plan 状态工具状态Artifact 状态
```

而不是重新执行用户的任务。

所以 Agent UI 和普通聊天 UI 的一个重要差别是：

> Agent Run 是独立于当前页面连接存在的后台执行对象。

---

## 十、Agent UI 不能只依赖 SSE

SSE 很适合实时推送 Agent 事件。

例如：

```cs
run.startedassistant.deltatool.startedtool.completedartifact.createdapproval.requiredrun.completed
```

但 SSE 只是传输通道，不应该是状态的唯一来源。

如果用户刷新页面：

```nginx
SSE 连接消失前端内存消失本地流式文本消失
```

如果后端没有保存状态，页面就无法恢复。

因此，一个可靠的 Agent UI 应该采用：

```diff
Snapshot+Event Log+Live Stream
```

其中：

- Snapshot 用于恢复当前完整界面；
- Event Log 用于补发离线期间的事件；
- Live Stream 用于接收最新事件。

进入会话时的标准流程是：

```js
加载 Conversation Snapshot   ↓恢复消息、计划、工具和 Artifact   ↓识别 activeRun   ↓读取 lastEventId   ↓重新连接 SSE   ↓补发遗漏事件   ↓继续实时接收
```

这也是 Agent UI 和普通 Chat UI 在架构上的关键差异。

普通聊天可能只需要流式文本。

Agent UI 必须考虑：

```js
页面刷新切换会话网络断开多标签页后台执行事件补发事件去重
```

---

## 十一、前端应该使用统一事件 Reducer

很多项目会在 SSE 监听代码中直接修改页面状态：

```javascript
source.addEventListener("tool.started", () => {  // 修改工具组件});source.addEventListener("assistant.delta", () => {  // 修改消息内容});
```

随着事件越来越多，这种方式会变得非常混乱。

更合理的方式，是将所有事件交给统一 Reducer：

```cs
function applyAgentEvent(  state: AgentState,  event: AgentEvent,): AgentState {  switch (event.type) {    case "run.started":      return startRun(state, event);    case "assistant.delta":      return appendText(state, event);    case "plan.updated":      return updatePlan(state, event);    case "tool.started":      return startTool(state, event);    case "tool.completed":      return completeTool(state, event);    case "approval.required":      return requireApproval(state, event);    case "artifact.created":      return createArtifact(state, event);    case "run.completed":      return completeRun(state, event);    default:      return state;  }}
```

这样做有几个好处：

- 实时事件和补发事件使用同一套逻辑；
- 页面刷新后可以重新构建状态；
- 可以根据 eventId 去重；
- 可以检测事件顺序；
- 状态变化容易测试；
- 后端框架发生变化时，前端改动更小。

AG-UI 的重要价值就在于，它尝试定义一套 Agent 和前端之间的统一事件协议。

前端不需要直接理解 LangGraph、OpenAI Agents 或自研 Runtime 的所有私有事件，而是先通过 Adapter 转换成统一 UI Event。

架构可以设计为：

```nginx
LangGraph Events ─┐OpenAI Events ────┤自研 Runtime ─────┼→ UI Event Protocol → Reducer → Components其他 Agent ───────┘
```

---

## 十二、Generative UI 应该怎么做

Generative UI 是 Agent UI 的重要方向。

它意味着 Agent 不只返回文字，还可以决定页面应该显示什么结构化组件。

例如用户说：

```js
帮我比较这三种服务器配置。
```

普通 Chat UI 可能返回一个 Markdown 表格。

Generative UI 可以直接生成：

```js
服务器配置对比组件├── CPU 对比├── 内存对比├── 价格对比├── 推荐标签└── 选择按钮
```

但 Generative UI 不应该等于让模型随意生成和执行 HTML。

企业系统更适合两种方式。

第一种是受控组件：

```javascript
const componentRegistry = {  risk_list: RiskList,  project_table: ProjectTable,  approval_form: ApprovalForm,  comparison_chart: ComparisonChart,};
```

Agent 只能选择已有组件，并传递参数：

```json
{  "component": "risk_list",  "props": {    "items": []  }}
```

第二种是声明式 UI Schema：

```json
{  "type": "card",  "children": [    {      "type": "heading",      "text": "风险分析"    },    {      "type": "list",      "items": []    }  ]}
```

前端根据 Schema 渲染已有设计系统中的组件。

不推荐在企业系统中直接执行模型生成的任意 HTML 或 React 代码，因为会带来：

- XSS 风险；
- 样式不可控；
- 性能不可控；
- 组件质量不稳定；
- 与设计系统不一致。

---

## 十三、如何借鉴主流 Agent UI 项目

目前主流方案各自解决的问题不同，不适合简单地互相替代。

## ChatUI

ChatUI 更适合参考基础聊天体验：

- 消息列表；
- 输入框；
- 快捷回复；
- 移动端布局；
- 会话基本组件。

它适合作为 Agent UI 的基础层，但无法单独解决复杂 Agent Run、工具调用和状态恢复。

## assistant-ui

assistant-ui 更适合参考组件化和 Runtime 设计。

它将页面拆成：

- Thread；
- Message；
- Composer；
- Message Part；
- Attachment；
- Action Bar；
- Branch Picker。

这种 Primitive 化的思路非常值得借鉴。

不要只做一个巨大的 `<AgentChat />` 组件，而是把行为和视觉拆成可组合组件。

## Vercel AI SDK UI

Vercel AI SDK UI 更适合参考：

- UIMessage；
- Message Parts；
- 工具调用流；
- 前后端消息协议；
- Generative UI；
- 数据流处理。

它对 Next.js 和 React 项目尤其友好。

## CopilotKit

CopilotKit 更适合参考 Agent 如何深度嵌入业务页面。

重点能力包括：

- Generative UI；
- Shared State；
- Human-in-the-loop；
- Agent 与 React 组件交互；
- Agent 读取和修改页面状态。

如果产品不是一个独立聊天页面，而是希望 Agent 操作现有业务系统，CopilotKit 的方向更值得参考。

## LangChain Agent Chat UI

它更适合参考 LangGraph 场景中的：

- 工具调用展示；
- Run 中断；
- 状态恢复；
- Thread；
- Time Travel；
- 调试。

## AG-UI

AG-UI 更适合参考协议层设计。

它解决的问题是：

```js
不同 Agent Runtime    ↓如何使用统一事件与前端通信
```

如果团队计划长期建设自研 Agent 平台，统一 UI 协议会比单纯选择某一个组件库更重要。

---

## 十四、一个推荐的 Agent UI 前端架构

完整前端可以划分为五层：

```sql
┌─────────────────────────────────────────┐│              页面与组件层               ││                                         ││ Thread / Composer / Tool / Plan         ││ Approval / Artifact / Sources           │├─────────────────────────────────────────┤│                状态层                   ││                                         ││ Conversation State                      ││ Run State                               ││ Artifact State                          │├─────────────────────────────────────────┤│              Event Reducer              │├─────────────────────────────────────────┤│               Transport                 ││                                         ││ SSE / Fetch Stream / WebSocket          │├─────────────────────────────────────────┤│           Agent Protocol Adapter        │├─────────────────────────────────────────┤│ LangGraph / OpenAI / 自研 Agent Runtime │└─────────────────────────────────────────┘
```

目录可以设计为：

```bash
agent-ui/├── components/│   ├── thread/│   ├── message/│   ├── composer/│   ├── plan/│   ├── tool/│   ├── approval/│   ├── artifact/│   └── source/├── runtime/│   ├── reducer/│   ├── transport/│   ├── recovery/│   └── adapters/├── store/│   ├── conversation-store.ts│   ├── run-store.ts│   └── artifact-store.ts├── protocol/│   ├── event.ts│   ├── message.ts│   └── tool.ts└── renderers/    ├── message-part-renderer.tsx    ├── tool-renderer.tsx    └── artifact-renderer.tsx
```

这样的结构能够避免：

- UI 组件直接依赖 SSE；
- SSE 直接修改页面；
- 前端强绑定某一个 Agent 框架；
- 所有工具逻辑堆在 Message 组件中。

---

## 十五、从现有 Chat UI 演进到 Agent UI

如果当前已经有一个基本的聊天页面，不需要一次性全部重做。

可以分四个阶段建设。

## 第一阶段：完善基础聊天体验

先把基本能力做好：

- Message Part；
- Markdown；
- 代码块；
- 附件；
- 停止生成；
- 重试；
- 历史会话；
- 自动滚动；
- 错误提示。

这个阶段可以重点参考 ChatUI 和 assistant-ui。

## 第二阶段：增加 Agent 执行展示

开始展示：

- Run 状态；
- Plan；
- Tool Call；
- Tool Result；
- Progress；
- Error；
- Cancel；
- Retry。

这个阶段，产品就从普通 Chat UI 开始进入 Agent UI。

## 第三阶段：实现可靠状态恢复

加入：

- Snapshot；
- Event Log；
- eventId；
- SSE 重连；
- 切换会话恢复；
- 页面刷新恢复；
- 后台 Run；
- 事件去重。

这个阶段决定系统是否真正能够进入生产环境。

## 第四阶段：建设 Agentic UI

最后加入：

- Human-in-the-loop；
- Artifact Workspace；
- Subagent；
- Shared State；
- Generative UI；
- UI Protocol Adapter。

最终的演进路线可以概括为：

```nginx
Chat UI   ↓Tool-aware Chat UI   ↓Agent Runtime UI   ↓Agentic Application UI
```

---

## 结语

传统 Chat UI 的目标，是让用户和模型进行自然对话。

Agent UI 的目标则更复杂：

> 让用户能够理解、控制并参与一个持续运行的智能执行系统。

因此，一个成熟的 Agent UI 不应该只有：

```diff
消息列表+输入框+流式输出
```

它应该至少具备：

```js
结构化 Message PartAgent Run 状态执行计划工具调用展示人工确认复杂成果工作区状态恢复统一事件协议
```

ChatUI 可以帮助我们解决基础聊天体验。

assistant-ui 可以帮助我们理解组件 Primitive 和 Runtime。

Vercel AI SDK UI 可以帮助我们设计 Message Part、工具流和 Generative UI。

CopilotKit 可以帮助我们理解 Shared State、Human-in-the-loop 和业务页面协同。

AG-UI 可以帮助我们建立 Agent Runtime 与前端之间的统一协议。

但真正决定 Agent UI 质量的，不是选择了哪个组件库，而是是否真正围绕 Agent 的执行过程设计。

最终，一个好的 Agent UI 应该让用户清楚地知道：

```nginx
Agent 正在做什么为什么这样做已经完成了什么哪里需要自己参与最终成果在哪里
```

只有做到这一点，Agent UI 才不再只是一个“更复杂的聊天窗口”，而是用户与智能体共同完成工作的操作界面。

**微信扫一扫赞赏作者**