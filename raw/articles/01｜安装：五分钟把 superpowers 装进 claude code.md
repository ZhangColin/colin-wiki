---
title: "01｜安装：五分钟把 superpowers 装进 claude code"
source: "https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484230&idx=1&sn=39f4ff9211d0a43fb8af2511da2c0464&chksm=ffe5d36bc8925a7d9616e8aa98ac90f6bad9371318c6daddd8687983571b582f767c97b011d9&cur_album_id=4599400782759952387&scene=189#wechat_redirect"
author:
  - "[[超级奶爸老谭！]]"
published:
created: 2026-09-09
description: "手把手把 obra/superpowers 装进 Claude Code，认全 18 个核心技能，顺手搭起「幸福账本」前后端双工程骨架。"
tags:
  - "superpowers"
---
超级奶爸老谭！ 共渡时艰 *2026年8月24日 08:05*

## 前文回顾

上一篇 [00｜superpowers 开篇：艳姐说，我们家钱算不清了](https://mp.weixin.qq.com/s?__biz=MzYyMTQ4MDI1OA==&mid=2247484223&idx=1&sn=da68f8d58934055a5ffac2166ba99884&scene=21#wechat_redirect) 里，艳姐要一套全家能用的 **网页记账系统** ，我决定用 AI 来写。开篇讲清 superpowers 是什么、系列怎么读、技术栈与项目场景的约定。

那顿晚饭之后，我的书房门口多了个小本子。艳姐写的，扉页只有三个字： **开始吧。**

> 老谭，你说那个“苏坡破袜子”到底是个啥，装起来麻烦不？
> 
> —— 老王，邻居，最近也在用 AI 写代码

## 场景：老王问怎么装

上次开篇我说要用 superpowers 给艳姐写「幸福账本」。老王在微信上追着问：名字听着挺唬人，到底怎么装？装完我啥也看不见啊？

他那会儿正蹲在小区门口修自行车，手机举得老高，就等我回消息。我打了一行字又删掉，想了半天——这事儿确实一两句话说不清。

![01-1](https://mmbiz.qpic.cn/sz_mmbiz_png/d9Wx6XKcQmQddvRxcJicvnqxyWoLLcYxyGDYD8wPQ3ibiaYRcRZQyj4ic01xbEFBub3zMPStXsQ3yCXoGA7obyazwddZgiaqzsgLOVibkpial0ovvY/640?wx_fmt=png&from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=0)

别急，今天就把这件事讲透。装它比你装一个 Java 环境还快， **五分钟搞定** 。装完你还会看到一张“技能全景图”，以后我们每篇文章讲的技能，都在那张图里。

老王又补了一句：「老谭，我要是装完没动静，是不是就白装了？」

「你装完肯定有动静。」我回他，「动静大到你会嫌它话多。」

## SUPERPOWERS 是 “插件 + 技能库”

先纠正一个常见误会：superpowers **不是** 一个独立 App，也不是你跑起来的什么服务。

它是 **Claude Code 的插件（plugin）** ，装进去之后，给 Claude 注入一套“技能（skill）”。

技能是个啥？你可以把它理解成 **一份写给 AI 看的标准作业流程** 。好比后厨里光有食材和刀没用，得有一张张菜谱，写明先切葱还是先热油。superpowers 就是塞给 AI 的一摞菜谱。比如：

- 你提到“想个点子” → 激活 `brainstorming` 技能 → Claude 按苏格拉底式提问，一个问题一个问题地跟你聊；
- 你说“开始实现功能” → 自动激活 `test-driven-development` → Claude 先写失败测试再写代码；
- 你说“帮我查个 bug” → 自动激活 `systematic-debugging` → Claude 先查根因，绝不乱改。

Claude 装插件后， **干活方式从“你说一句它写一堆”变成了“按流程走”** 。这就是它的价值。

我跟老王打了个比方：「以前用 AI，像请了个手脚麻利但没规矩的临时工。你说啥它干啥，干完你自己检查。装完 superpowers，等于给这临时工发了一本员工手册。」

「那它会不会嫌手册太长？」老王问。

「它巴不得有手册。」我说，「AI 最怕的不是规矩多，是没规矩。」

## 怎么用：安装三步走

![01-2](https://mmbiz.qpic.cn/mmbiz_jpg/d9Wx6XKcQmRO53YW8RCbTxtmVEJB1ZOCHgU504SZAC95eX0icND8Pux6gSxHQHwn28Ek9h3NOH31ULH0HvxBbvkVeBl22Qblw3fcncAJNK5I/640?from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=1)

### 添加插件市场

在 Claude Code 里直接输入这条命令（以 `/` 开头是 Claude Code 的内置命令）：

```
/plugin marketplace add obra/superpowers-marketplace
```

这一步是告诉 Claude Code：去老欧的插件市场拉清单。

回车一敲，终端里就开始刷字了。拉清单很快，快得像去菜市场拿一份当天的报价单，几秒就完事。

### 安装插件本体

市场清单到手，把插件本体装进项目：

```
/plugin install superpowers@superpowers-marketplace
```

`superpowers@superpowers-marketplace` 意思是“从 superpowers-marketplace 这个市场装 superpowers 插件”。装完它会自动把整套技能库拉到你的项目里。

老王又冒出来一句：「老谭，这不就跟 npm install 一个包差不多？」

「心态一样，干活不一样。」我说，「npm 装的是代码，这个装的是 **干活的规矩** 。」

### 验证装好了

装没装上，一条命令见分晓：

```
/plugin list
```

如果看到 `superpowers@superpowers-marketplace` 就说明装好了。这时候你再输入 `/` ，能看到几个新的斜杠命令：

| 命令 | 激活的技能 | 用途 |
| --- | --- | --- |
| `/brainstorm` | brainstorming | 跟 AI 头脑风暴，把想法打磨成规格 |
| `/write-plan` | writing-plans | 把规格写成实施计划 |
| `/execute-plan` | executing-plans | 按计划分批执行 |

其余的技能（TDD、调试、代码审查等） **不用你手动敲命令** ，Claude 会在合适的场景自动激活。这也是 superpowers 最妙的地方： **它不是一堆命令，而是一套会自动上身的习惯** 。

> 装完如果你在项目里看不到技能文件，别慌。
> 
> 插件会在首次运行时自动把 `skills/` 目录铺好，通常在 `.claude/skills` 或 `.agents/skills` 下。你甚至可以翻开 `SKILL.md` 读它的原文。

我那天晚上真把 `SKILL.md` 翻出来读了。读到一半，乐乐探头进来：「爸爸，你是在看书吗？」

「嗯，给 AI 看的手册。」我说。

「AI 也要读书啊？」她一脸惊讶，「那我是不是不用写作业了？」

「……它读的书，比你的作业多。」乐乐瘪着嘴走了。

## 技能全景图：我们这一路要打的怪

装完之后，你面对的是一张这样的地图（按老欧仓库的分类，全系列 18 个技能全覆盖）：

```
superpowers
├── collaboration（协作）            —— 把一件事多人多机协作做好
│   ├── brainstorming                头脑风暴：想法 → 设计规格
│   ├── writing-plans                设计规格 → 实施计划
│   ├── executing-plans              实施计划 → 分批执行
│   ├── subagent-driven-development  子代理驱动：一个 AI 当项目经理
│   ├── dispatching-parallel-agents  并行派发：一次派好几个 AI
│   ├── using-git-worktrees          git 工作树：隔离并行开发
│   ├── finishing-a-development-branch 分支收尾：合还是弃
│   ├── requesting-code-review       提交审查前自检
│   └── receiving-code-review        接住审查意见
├── testing（测试）                   —— 让代码靠得住
│   ├── test-driven-development      先测试后代码，红绿循环
│   ├── condition-based-waiting      条件等待异步，不用 sleep
│   └── testing-anti-patterns        测试反模式避坑
├── debugging（调试）                 —— 出 bug 不慌
│   ├── systematic-debugging         四阶段系统化排查
│   ├── root-cause-tracing           从症状追根因
│   ├── verification-before-completion 修完必须验证
│   └── defense-in-depth             纵深防御，多层兜底
└── meta（元技能）                    —— 让技能越来越多
    ├── using-superpowers            技能库入口（自动注入）
    └── writing-skills               写你自己的技能
```

后面每篇文章，我们就从这个地图上摘一个技能，用「幸福账本」讲透。这个项目跟别人家的教程不一样的地方是： **它是一个前后端完整的项目** ——Java 后端管数据，Vue 前端跑在浏览器里。

我把截图发给老王，他盯着看了半天，回了一句：「这不是一张地图，这是一张 **体检表** ——哪块弱，补哪块。」

「你要这么理解也行。」我说，「反正这一路，你负责问为什么，我负责踩坑。」

## 顺手搭起「幸福账本」的前后端骨架

光有技能还不行，得有地儿练。现在把项目建起来——一个 `happy-ledger` （后端）+ 一个 `happy-ledger-web` （前端）的双工程。

老规矩，先跟艳姐报备一声：「艳姐，今晚我开两个工程，给咱家账本打地基。」

「打吧打吧。」她头也不抬，「别打到半夜就行。」

![01-3](https://mmbiz.qpic.cn/mmbiz_jpg/d9Wx6XKcQmQOVeGG2I7iaZfFUCTaFkftSPkg9N3ENQOh0u5ibDjCMxCLupAd0ocgMCMHMd4GF5pwSSH8ic6j03lgHMV95jf4PjAbr9e6K96rcI/640?from=appmsg&watermark=1&tp=webp&wxfrom=5&wx_lazy=1#imgIndex=2)

### 后端：Spring Boot 工程

后端 `pom.xml` （Spring Boot 3 + Java 21，数据库先用 H2）：

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.3.5</version>
        <relativePath/>
    </parent>

    <groupId>com.laotan</groupId>
    <artifactId>happy-ledger</artifactId>
    <version>0.1.0-SNAPSHOT</version>
    <name>happy-ledger</name>
    <description>幸福账本：家庭记账后端 API</description>

    <properties>
        <java.version>21</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

`application.properties` ：数据落盘到本地文件库，重启不丢，这是“完整项目”的底气：

```
spring.application.name=happy-ledger
server.port=8080

spring.datasource.url=jdbc:h2:file:./data/happy-ledger
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

spring.jpa.hibernate.ddl-auto=update
spring.jpa.open-in-view=false
```

启动类：

```
package com.laotan.ledger;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/** 幸福账本：家庭记账后端 API 启动类。 */
@SpringBootApplication
public class HappyLedgerApplication {

    /** 启动入口。 */
    public static void main(String[] args) {
        SpringApplication.run(HappyLedgerApplication.class, args);
    }
}
```

第一个领域对象—— **账目** ，用 Java 的 `record` 写，不可变、干净：

```
package com.laotan.ledger;

/** 收支类型：收入 / 支出 */
public enum EntryType { INCOME, EXPENSE }
```

接着是 **收支分类** ——每笔账要归到“吃饭、出行”这些类目上：

```
package com.laotan.ledger;

/** 收支分类：每一笔账归属的类别，如"吃饭"、"出行"。 */
public record Category(Long id, String name, EntryType type) {
}
```

再往下是 **账目本体** ——一笔收入或支出（ `ownerId` 是账本归属：1 家庭、2 乐乐）：

```
package com.laotan.ledger;

import java.math.BigDecimal;
import java.time.LocalDateTime;

/**
 * 账目：一笔收入或支出。
 * ownerId 是账本归属：1 = 家庭总账；2 = 乐乐零花钱。
 */
public record Entry(
        Long id,
        EntryType type,
        Category category,
        BigDecimal amount,
        LocalDateTime occurredAt,
        String note,
        Long ownerId
) {
}
```

配一个最简单的 JUnit 5 测试，确认骨架能跑：

```
package com.laotan.ledger;

import static org.assertj.core.api.Assertions.assertThat;

import java.math.BigDecimal;
import java.time.LocalDateTime;

import org.junit.jupiter.api.Test;

class EntryTest {

    /** 验证一笔支出记录：字段不可变、金额精确（BigDecimal）。 */
    @Test
    void entryRecordIsImmutableAndAmountExact() {
        Entry entry = new Entry(
                1L, EntryType.EXPENSE,
                new Category(1L, "吃饭", EntryType.EXPENSE),
                new BigDecimal("88.50"),
                LocalDateTime.of(2026, 8, 1, 12, 30),
                "周末下馆子", 1L);

        assertThat(entry.amount()).isEqualByComparingTo("88.50");
        assertThat(entry.type()).isEqualTo(EntryType.EXPENSE);
        assertThat(entry.ownerId()).isEqualTo(1L);
    }
}
```

跑一下： `mvn test` 。绿灯，后端骨架就成了。

注意看断言用的 `isEqualByComparingTo` —— **比较金额必须用它，不能 `isEqualTo`** 。

否则， `new BigDecimal("88.50")` 和 `"88.5"` 的 scale 不同，相等也判不等。

这是全系列的约定。奶茶 15.00、一顿饭 88.50 这种小数，要是一开始就交给 `double` ，攒到月底对账，它敢给你平白多出几分钱来。

### 前端：Vue 3 + Vite + Element Plus 工程

后端有了，但艳姐要的是 **网页** 。再建一个前端工程 `happy-ledger-web` ，用 Vite 脚手架一键生成，然后装上 `vue-router` 和 `element-plus` 。

`package.json` （前端依赖）：

```
{
  "name": "happy-ledger-web",
  "private":true,
  "version": "0.1.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  },
  "dependencies": {
    "vue": "^3.5.13",
    "vue-router": "^4.5.0",
    "element-plus": "^2.8.8"
  }
}
```

`vite.config.js` ：开发期把 `/api` 代理到后端 8080，这样前后端 **同源** ，不用处理跨域：

```
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
  plugins: [vue()],
  server: {
    port: 5173,
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true
      }
    }
  }
})
```

入口 `main.js` ：挂上 Element Plus 和路由：

```
import { createApp } from 'vue'
import ElementPlus from 'element-plus'
import 'element-plus/dist/index.css'
import App from './App.vue'

const app = createApp(App)
app.use(ElementPlus)
app.mount('#app')
```

根组件 `App.vue` ——先来一个最朴素的壳，顶上写一行字，证明前后端通着：

```
<script setup>
</script>

<template>
  <h1>幸福账本</h1>
  <p>骨架搭好了，页面后面一篇篇往里填。</p>
</template>
```

装依赖、起服务：

```
cd happy-ledger-web
npm install
npm run dev
```

浏览器打开 `http://localhost:5173` ，看到「幸福账本」四个字，前后端双工程骨架就活了。

艳姐端了杯水进来，瞄了一眼屏幕：「这就完了？账呢？」

「地基刚打好，楼还没盖呢。」我指指屏幕，「这才第一层。」

「行。」她把水放下，「那我等你的楼。」

## 注意事项

1. 命令以官方为准。 插件命令偶尔会随版本调整，装不上就去
	obra/superpowers 官方仓库 看 README，别硬背。
2. 技能会自动激活，不用你每个都敲。 记住 `/brainstorm`
	、 `/write-plan` 、 `/execute-plan` 三个常用命令就够了，其他的交给场景。这三张是入场券，剩下的 AI 自己会接。
3. 双工程要一起起。 前端 `npm run dev`
	（5173）和后端 `spring-boot:run` （8080）都要开着，前端才能通过 `/api` 代理拿到数据。别只开一个然后问“怎么没数据”。
4. 金额用 `BigDecimal` ，从第一篇就开始守规矩。 用 `double`
	记账，是后面“账对不上”bug 的万恶之源。现在偷的懒，都是月底对账时流的泪。

## 本文小结

装插件五分钟，但这五分钟之后，你跟 AI 的协作方式会变——从“指挥它”变成“跟它走流程”。工具还是那个工具，规矩变了，结果就完全两样。

技能树铺开了，前后端骨架也立起来了。老王的微信又亮了：「老谭，地图我存了，下一步呢？」

下一步，就是那三张入场券里的第一张。

下一站，老王第一个问题又来了： **“我得先说清楚我到底要啥吧？”** 对，这就是 `brainstorming` 技能要解决的事。

艳姐只扔给我们一句“做个家庭记账”，我们得把它聊成一份能写的规格——聊清楚领域模型，也聊清楚网页长什么样。

02｜brainstorming，不见不散。

> 喜欢的话点个 **关注** ，后台私信 `源码` ，本系列源代码持续更新，打包带走。

#superpowers #Claude #Java #SpringBoot #Vue

✨ 人工智能 · 目录