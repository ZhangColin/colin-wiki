---
type: entity
title: DeltaBox Lite
domain: [AI编程, 系统工程, AI工具]
tags: [DeltaBox, 文件沙箱, CB-CoW, Copy-Down, 事务, FUSE, 开源]
sources:
  - "[[DeltaBox Lite 终端文件沙箱]]"
created: 2026-10-28
updated: 2026-10-28
status: active
aliases: [deltabox-lite, DeltaBox]
---

# DeltaBox Lite

> 一句话定义：[[IPADS]] 开源的终端 AI Agent 轻量文件沙箱运行时（Linux/macOS，毫秒级）——原生事务级 Diff / Commit / Abort + 审批机制，核心创新 **Commit-Biased Copy-on-Write（Copy-Down）**。

## 关键属性 / 事实

- **仓库**：`github.com/delta-box/deltabox-lite`（开源，文档 deepwiki；项目主页 delta-box.github.io）。
- **CB-CoW**：Copy-Down 直接写宿主工作区 + 覆盖前记 Undo Log——提交 O(1)、撤销才重放；与传统 Copy-Up（撤销 O(1)、提交写回）成本互补，两种模式 `--fs-mode` 可选。
- **状态机**：EXEC → DIFF → COMMIT / ABORT；回滚点三级（CLI 命令 / Task 对话 / Tool 敏感调用）。
- **生态**：独立 CLI + DeepSeek Harness 插件（dsh-deltabox，审批控制台）+ Pi 扩展（/sandbox-*）——「也可便捷集成到其他 Harness 系统」。
- **性能**：空指令调用 13–14ms，不随工程文件数退化；超 Bubblewrap+Git（官方自测口径）。
- 名称中的「Lite」暗示存在/规划中的完整版 DeltaBox（推论，源内未展开）。

## 观察点

- 与 [[DeepSeek Harness]] 插件生态的整合深度；完整版（非 Lite）形态。
- 「Agent 沙箱」概念第二源触发时建概念页（见 [[INDEX]] Pending）。

## 相关页面

- [[IPADS]] · [[DeltaBox Lite 终端文件沙箱]] · [[DeepSeek Harness]] · [[HITL 与 AFK]]
