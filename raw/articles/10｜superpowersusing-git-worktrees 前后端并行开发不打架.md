---
title: "10｜superpowers/using-git-worktrees 前后端并行开发不打架"
source: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484291&idx=1&sn=77c53d602d4fc095028a745be917f750&chksm=ffe5d3aec8925ab8261a09f10b2baa5cc5e9d05cb0887e451511b1e2559c8309a4d7fde2dde8&cur_album_id=4599400782759952387&scene=189#wechat_redirect"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-09
description: "一个仓库里前后端同时开工，两个会话互相覆盖代码。用 git worktree 开多个工作区，各干各的互不打扰。"
tags:
  - "superpowers"
---
超级奶爸老谭！ 共渡时艰 *2026年9月4日 08:05*

## 前文回顾

上一篇 [09｜基建篇 - Vue 前端起步：把账本跑进浏览器](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484288&idx=1&sn=b5e880394f3619d533e35910373f1479&scene=21#wechat_redirect) 前后端第一次握手成功，艳姐能在网页上记账了。基建齐了，幸福账本开始有"活项目"的样子。

老王这时候问了句专业的：「前后端现在是一起改的。我想同时开两个活——前端做报表页，后端加个新接口—— **一个仓库两个会话，会不会互相踩？** 」

「会，」我答，「而且我已经踩过了。」

## 场景：一个仓库，两个会话，覆盖事故

那是幸福账本第一次"两个活并行"的日子。

我在终端开两个 Claude Code 会话：会话 A 改前端的报表页，会话 B 加后端的报表接口。俩会话都工作在同一个目录—— `202608-superpowers/` 。

结果可想而知：会话 A 改了 `Reports.vue` 存盘，会话 B 的编辑器一刷新，发现工作区怎么多了个文件？再一碰，两边对同一个目录的写入互相覆盖，改好的代码说没就没。

**一个工作目录只能承载一个"正在改的上下文"。** 想让多个活并行不打架，得让它们各自有一个不互相干扰的工作区。

这就是 `using-git-worktrees` ： **用 git 的工作树（worktree），一个仓库开出多个独立目录，各干各的。**

## 技能是什么：worktree 是"一个仓库，多份工作区"

`git worktree` 是 git 自带的命令，不是新东西，但很少人拿来当协作工具。它的模型：

- 一个仓库（`.git` ）可以挂 **多个工作目录** ；
- 每个工作区可以 **checkout 不同的分支** ；
- 各自改、各自存、各自跑测试， **互不干扰** ；
- 都在同一个 `.git` 里，提交、分支、历史是共享的。

打个比方：以前你一份代码只有一间屋子，几个人挤在一起动手；worktree 给每个分支开了 **单独的套间** ，代码还是那一栋楼，但各住各的，谁也别碰谁的家具。

## 怎么用：给前后端并行各开一间房

### 建工作树：一个分支一个目录

现在我们要并行干两件事：前端报表页 + 后端报表接口。先各自建一个分支，再各自开一个工作区：

```
# 进主仓库，先建两个分支
cd 202608-superpowers
git branch report-frontend      # 前端报表页
git branch report-backend       # 后端报表接口

# 为两个分支各开一个工作区（放在别处，别占着主工作区）
git worktree add ../hl-fe-report report-frontend
git worktree add ../hl-be-report report-backend
```

这样我们就有了三个互不干扰的目录：

- 202608-superpowers/（主工作区， `main`
	分支）
- ../hl-fe-report/（前端报表页， `report-frontend`
	分支）
- ../hl-be-report/（后端报表接口， `report-backend`
	分支）

会话 A 去 `hl-fe-report` 干活，会话 B 去 `hl-be-report` 干活—— **各改各的目录，谁也碰不到谁。**

### 验证工作区真的独立

两个会话各自改完，先看各自工作区的状态：

```
# 会话 A（前端）
cd ../hl-fe-report
git status
# 只有前端报表页的改动，干净

# 会话 B（后端）
cd ../hl-be-report
git status
# 只有后端接口的改动，干净
```

**这就是隔离的全部意义** ：A 的改动不会出现在 B 的工作区里，B 的改动也不会把 A 顶掉。代码还是那一份历史，但"正在写的现场"各是各的。

### 提交、合并、清理

两边各自提交：

```
cd ../hl-fe-report
git add src/views/Reports.vue
git commit -m "feat: 报表页渲染月度汇总"

cd ../hl-be-report
git add src/main/java/com/laotan/ledger/web/ReportController.java
git commit -m "feat: 报表月度汇总接口"
```

然后各自合回主线（具体怎么安全合，是下一篇 `finishing-a-development-branch` 的事）：

```
cd 202608-superpowers
git merge report-frontend
git merge report-backend
```

干完了，把工作区拆掉，免得留着占地方：

```
git worktree remove ../hl-fe-report
git worktree remove ../hl-be-report
git branch -d report-frontend report-backend
git worktree list   # 确认只剩主工作区
```

## 注意事项

1. 一个工作区只服务一个正在进行的活。 开工作区的成本低，别省——每个并行任务一个，干完即拆。
2. 工作区目录放仓库外面。 `git worktree add ../xxx`
	放同级，别塞进主工作区里面，否则又乱套。
3. 主工作区保持干净。 让主工作区当"主干仓库"，日常只做合并、提交、发布，不做并行的开发活。
4. 别在同一个工作区开两个会话。 worktree 隔离的是"目录"，不是"会话"。同一个目录开两个会话，照样互相覆盖——隔离靠目录，不靠自觉。

## 本文小结

`using-git-worktrees` 用一个仓库开出多个独立工作区，让前后端并行开发互不打扰：各自的分支、各自的目录、各自的提交，最后合并回主干。 **并行开发不打架，靠的不是默契，是隔离。**

两个报表活都干完了，合也合回来了。老王盯着 `git log` 里的两个新提交，问：「合回来是合回来了，但你怎么知道合得对？要是合出一堆冲突怎么办？」

「所以合之前要有规矩。」我说，「合完要检查、要验证。这个，是下一篇的事。」

下一站： `finishing-a-development-branch` ——干完活，安全合回主线。

11｜finishing-a-development-branch，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #git #协作

共渡时艰

邀请你前往腾讯公益一起捐

广州公交AED项目

✨ 人工智能 · 目录