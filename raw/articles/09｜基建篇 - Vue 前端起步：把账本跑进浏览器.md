---
title: "09｜基建篇 - Vue 前端起步：把账本跑进浏览器"
source: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484288&idx=1&sn=b5e880394f3619d533e35910373f1479&chksm=ffe5d3adc8925abba190d0665d5f9633216e19b510696c207ed1a78f68d439aafa52802a2c44&cur_album_id=4599400782759952387&scene=189#wechat_redirect"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-09
description: "基建篇第二弹：Vue 3 + Vite + Element Plus 搭起前端，从请求封装、账本切换，到第一屏「记账 + 账目列表」，完成前后端第一次握手。"
tags:
  - "superpowers"
---
超级奶爸老谭！ 共渡时艰 *2026年9月3日 08:05*

## 前文回顾

上一篇 [08｜基建篇 - REST API：把账本逻辑变成接口](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484285&idx=1&sn=86aac4dd4fd823e86be007e88aa1000e&scene=21#wechat_redirect) 我们把领域变成了接口：数据落 H2、 `X-User-Id` 切账本、错误统一 JSON、curl 一打全通。后端完整了。

艳姐把手机往我面前一放，指着我敲的命令行：「你给我看的这个，我可不认识。 **我要的是点开就能用的页面。** 」

## 场景：接口是给程序看的，页面是给人看的

后端好了，可它只会说 HTTP。艳姐不会 `curl` ，她要的是 **浏览器里的网页** 。

前端工程 `happy-ledger-web` 在安装篇就建了骨架，现在把它填起来：Vue 3 + Vite + Element Plus，把 `/api` 那堆接口，变成五个能点的页面。

这一步做完，前后端就 **第一次握上手** 了：浏览器点一下 → 前端发请求 → 后端查 H2 → 回 JSON → 前端渲染成表格。这趟链路，就是"完整项目"的命脉。

不是 superpowers 的技能篇，还是 **基建篇** 。今天先做最小闭环：账目列表 + 记账表单，两屏跑通。

## 怎么用（一）：前端骨架三件套

### 请求封装：所有请求自动带账本头

前端和后端约定用 `X-User-Id` 头切账本。这个头得是 **自动的** ，不然每个页面都得手写一遍。封装一个 `api/request.js` ：

```
const BASE_URL = '/api'
const USER_HEADER = 'X-User-Id'

/** fetch 封装：统一 baseURL、自动带账本头、非 2xx 抛后端返回的 message。 */
export function api(path, options = {}) {
  const { ownerId, ...rest } = options
  const headers = { 'Content-Type': 'application/json', ...(rest.headers || {}) }
  if (ownerId) {
    headers[USER_HEADER] = ownerId
  }
  return fetch(BASE_URL + path, { ...rest, headers }).then(async (response) => {
    if (response.status === 204) {
      return null
    }
    const body = await response.json()
    if (!response.ok) {
      throw new Error(body.message || '请求失败')
    }
    return body
  })
}

export const entries = {
  list: (month, ownerId) => api('/entries?month=' + month, { ownerId }),
  create: (data, ownerId) => api('/entries', { method: 'POST', body: JSON.stringify(data), ownerId }),
  remove: (id) => api('/entries/' + id, { method: 'DELETE' })
}
```

要点： **`ownerId` 传入就自动变 `X-User-Id` 头** ；非 2xx 直接抛后端给的 `message` ，页面 `catch` 到就提示用户。

### 账本状态：一个共享的"当前账本"

顶栏切换账本，所有页面都要感知。用组合式函数（composable）做一个单例状态：

```
import { computed, ref } from 'vue'

export const LEDGER_FAMILY = { id: 1, label: '家庭账本' }
export const LEDGER_KID = { id: 2, label: '乐乐零花钱' }

// 单例账本状态：切换后所有页面共享
const current = ref(LEDGER_FAMILY)

export function useLedger() {
  const ownerId = computed(() => current.value.id)

  function switchTo(ledger) {
    current.value = ledger
  }

  return { ownerId, switchTo }
}
```

页面里 `const { ownerId } = useLedger()` ，把 `ownerId.value` 传给请求封装，账本切换自动生效。

### 顶栏 + 侧栏布局

`App.vue` ：左侧菜单（五个页面），顶栏放标题 + **账本切换下拉** ：

```
<template>
  <el-container class="app">
    <el-aside width="200px">
      <el-menu :default-active="route.path" router>
        <el-menu-item index="/">账目</el-menu-item>
        <el-menu-item index="/entry">记账</el-menu-item>
        <el-menu-item index="/reports">报表</el-menu-item>
        <el-menu-item index="/budgets">预算</el-menu-item>
        <el-menu-item index="/allowance">零花钱</el-menu-item>
      </el-menu>
    </el-aside>
    <el-container>
      <el-header class="header">
        <span>{{ route.meta.title }}</span>
        <el-select :model-value="ledger.id" class="ledger-switch" @change="onSwitch">
          <el-option v-for="item in ledgers" :key="item.id" :label="item.label" :value="item.id" />
        </el-select>
      </el-header>
      <el-main>
        <router-view />
      </el-main>
    </el-container>
  </el-container>
</template>
```

顶栏这个下拉，就是"免登录切换账本"的真身——切一下， `ownerId` 变，后面所有请求的 `X-User-Id` 跟着变。

## 怎么用（二）：第一屏"账目列表"

账目页：选月份 → 拉数据 → 表格渲染。这是前后端第一次握手的地方：

```
<template>
  <div>
    <el-date-picker v-model="month" type="month" :clearable="false" />
    <el-table :data="list" stripe>
      <el-table-column prop="occurredAt" label="时间" width="180" />
      <el-table-column prop="category.name" label="分类" width="120" />
      <el-table-column prop="amount" label="金额" width="120" />
      <el-table-column prop="note" label="备注" />
    </el-table>
  </div>
</template>
```

`watch([month, ownerId], load)` 是这个小页面的灵魂： **账本一换、月份一改，列表自动跟着变** 。你切到"乐乐零花钱"，表格立刻变成乐乐的两笔账——前后端握手完成。

## 怎么用（三）：第二屏"记账表单"

记账页：选类型、选分类、填金额备注，点"记一笔"POST 到后端：

```
<template>
  <el-form label-width="80px" style="max-width: 460px">
    <el-form-item label="类型">
      <el-radio-group v-model="form.type">
        <el-radio-button value="EXPENSE">支出</el-radio-button>
        <el-radio-button value="INCOME">收入</el-radio-button>
      </el-radio-group>
    </el-form-item>
    <el-form-item label="分类">
      <el-select v-model="form.categoryId" placeholder="选个分类">
        <el-option v-for="item in categoryOptions" :key="item.id"
                   :label="item.name" :value="item.id" />
      </el-select>
    </el-form-item>
    <el-form-item label="金额">
      <el-input-number v-model="form.amount" :min="0.01" :precision="2" :step="10" />
    </el-form-item>
    <el-form-item label="备注">
      <el-input v-model="form.note" placeholder="比如：一杯奶茶" />
    </el-form-item>
    <el-form-item>
      <el-button type="primary" @click="submit">记一笔</el-button>
    </el-form-item>
  </el-form>
</template>
```

注意 `catch (error) { ElMessage.error(error.message) }` ——后端"金额必须大于 0"这种提示，会原样弹给艳姐看。 **这就是上一篇统一错误 JSON 的回报** ：前端拿到就能提示，不用自己猜。

## 怎么用（四）：验证前后端握手

前端配了 `/api` 代理到后端（安装篇的 `vite.config.js` ），所以只要两端都起：

```
# 终端 1：后端
cd happy-ledger && mvn spring-boot:run
# 终端 2：前端
cd happy-ledger-web && npm run dev
```

浏览器打开 `http://localhost:5173` ：

- 账目页：看到 seed 的 8 笔家庭账（时间、分类、金额、备注）；
- 记账页：选"支出 → 吃饭"，金额 9.90，备注"加班奶茶"，点"记一笔"→ 成功提示 → 回账目页刷新，多了这一笔；
- 顶栏切到"乐乐零花钱"：账目页立刻只剩乐乐那两笔。

**从浏览器点击到后端落库、再到回显，一条链路走通了。** 艳姐第一次亲手记上账的时候，我听见她嘀咕了一句：“这还像回事。”

## 注意事项

1. `watch([month, ownerId], load)` 是响应式的关键。 账本/月份一变，列表自动刷新。忘了它，切换账本后页面还停留在旧数据，那是前后端最尴尬的假握手。
2. 请求封装统一管理头。 别在组件里手写 `fetch`
	带 `X-User-Id` ——封进 `request.js` ，全站一个约定，改一处生效全局。
3. 错误要弹给人看。 后端 `{code, message}`
	的错误 JSON，前端 `catch` 后直接 `ElMessage.error(message)` 。吞掉异常 = 艳姐点了没反应。
4. 别在组件里直接写"魔法 URL"。 `/api/entries`
	这种路径统一放 `request.js` ，页面只调 `entries.list(...)` ，语义清楚、好改。

## 本文小结

基建第二步完成：前端从骨架长成两屏——账目列表 + 记账表单。请求封装自动带账本头，账本切换联动全站，前后端第一次握手成功。 **艳姐终于能在网页上记账了。**

老王在旁边看得直点头，然后问了个关键问题：「现在前后端是一起改的。要是我想同时开两个活——比如前端的报表页和后端的新接口——一个仓库两个会话，会不会互相踩？」

「会，而且我已经踩过了。」我笑了笑，「下一篇，讲怎么让它们不打架。」

下一站： `using-git-worktrees` ——一个仓库，多个工作区。

10｜using-git-worktrees，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #AI编程 #Vue #前端

共渡时艰

邀请你前往腾讯公益一起捐

传递温暖关爱老人

✨ 人工智能 · 目录