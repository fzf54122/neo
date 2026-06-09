---
title: "🚀 TurboSync：面向开发者、NAS 与 Homelab 的 Rust 文件同步工具"
date: 2026-06-09
categories: [Rust, 文件同步, 桌面应用]
tags: [TurboSync, Rust, Tauri, React, QUIC, SQLite, 文件监听, NAS, Homelab, GUI, TUI]
pin: true
description: "🎯 一个用 Rust 编写的跨平台文件同步工具：内置本地 Agent、桌面 GUI、终端 TUI、SQLite 状态、文件监听与 Agent-to-Agent QUIC 传输。"
---

{: .text-center }
# ⚡ TurboSync / 飞梭同步

{: .text-center }
>  把“目录能复制”升级成“多设备之间稳定、可控、可观测地同步”。

{: .text-center }
`GUI` • `TUI` • `CLI`  
本地 Agent · SQLite 状态 · QUIC 传输

<!-- {: .text-center } -->
- 📁 **GitHub**：<https://github.com/fzf54122/turbo_sync>

---

## 🧩 背景：为什么做这个项目？

很多个人设备、NAS 和 Homelab 场景里的文件同步，都绕不开这些选择：

- `rsync` 很强，但参数和使用模型对普通用户不够友好
- Syncthing 功能完整，但首次配置要理解不少概念
- 网盘产品很方便，但不适合所有本地目录、服务器和自托管场景
- 自己写脚本能解决一时问题，但长期维护、错误恢复和状态追踪都麻烦

于是你会发现，真正的痛点不是“复制一个文件”，而是：

> ❗ 如何让开发目录、NAS 备份、服务器目录和 Homelab 设备之间的同步 **简单、稳定、可观察、可长期维护**。

`TurboSync` 就是为这个问题而生：

✅ 用 Rust Agent 负责底层同步，用 GUI/TUI/CLI 提供不同入口，让用户用更少概念完成目录同步闭环。

---

## ⚖️ 方案对比：脚本/通用工具 vs TurboSync

| 维度 | 传统脚本 / 通用工具 | TurboSync |
|------|----------------------|-----------|
| 使用入口 | 多数依赖命令参数和手写脚本 | 桌面 GUI、终端 TUI、CLI 三种入口 |
| 后台服务 | 常需自行守护进程或 cron | 本地 Agent 统一扫描、监听和同步 |
| 状态管理 | 依赖文件时间、日志或人工判断 | SQLite 保存节点、任务、索引和同步日志 |
| 文件变化 | 常需手动触发或外部 watcher | `notify` 文件监听 + 500ms 防抖 |
| 增量判断 | 各工具语义不同，脚本难统一 | 扫描索引 + `blake3` 指纹 |
| 远端传输 | 通常依赖 SSH/rsync/共享目录 | Agent-to-Agent QUIC 文件传输 |
| 用户体验 | 强工具属性，学习成本较高 | 普通用户优先用 GUI/TUI 管理任务 |
| 可观测性 | 日志分散，状态难聚合 | 最近活动、同步日志、节点健康状态 |
| 发布交付 | 不同平台需要单独处理 | GitHub Actions 三平台打包 |

> 简单说：传统工具“能力很强”，但 TurboSync 更关注个人设备同步场景里的 **少配置、可视化、可自托管、可观测**。

---

## 🎯 设计目标与边界

### 目标

- 🧱 **少概念**：用户只需要理解 Node、Task、Source、Target、Sync
- 🖥️ **多入口**：桌面 GUI、终端 TUI、CLI 都能管理同一套 Agent 能力
- 🔁 **稳定同步**：扫描、索引、文件监听、增量同步和删除同步形成闭环
- 📊 **可观测**：节点健康、同步活动、日志和文件索引都能查看
- 🧰 **可自托管**：适合开发机、NAS、Linux 服务器、Docker 和 Homelab

### 当前边界

- 不做云盘、在线预览、文档协作
- 不做团队权限、用户系统和企业管理后台
- 双向同步已有基础验证路径，但真实数据建议先从单向同步开始
- 远端同步建议运行在可信网络中，并配置证书指纹

---

## 🏗️ 当前实现到哪了？

下面是当前版本的能力快照：

| 模块 | 当前状态 | 说明 |
|------|----------|------|
| 桌面 GUI | ✅ | Tauri + React + TypeScript，产品名“飞梭同步” |
| 终端 TUI | ✅ | `tsync dashboard`，默认中文，可切换 English |
| CLI | ✅ | 节点、任务、同步、扫描、监听、日志等命令 |
| 本地 Agent | ✅ | Axum 本地控制 API，负责扫描、监听、同步和传输 |
| 本地同步 | ✅ | 本机目录到本机目录，当前最稳定主线 |
| 远端同步 | ✅ | Agent-to-Agent QUIC 文件传输 |
| 文件监听 | ✅ | `notify` + 500ms 防抖，变化后自动扫描和同步 |
| 状态存储 | ✅ | SQLite + SQLx migrations |
| 文件索引 | ✅ | `walkdir` 扫描 + `blake3` 文件指纹 |
| 同步日志 | ✅ | 运行记录、变更数量、失败数量、传输数据量 |
| 节点健康检查 | ✅ | 展示连接成功、连接失败和错误信息 |
| 三平台打包 | ✅ | Windows / Linux / macOS release workflow |

---

## ✨ 三个入口，复用同一个同步核心

### 1) 🖼️ 桌面 GUI

适合普通用户和日常桌面使用：

- 添加同步节点
- 创建同步任务
- 手动同步和重新扫描
- 开启或停止文件监听
- 查看节点健康状态
- 查看最近同步活动
- 删除任务和节点

GUI 会自动初始化本机配置，并自动启动本机 Agent。如果这个 Agent 是 GUI 自己启动的，关闭 GUI 时会自动关闭；如果 Agent 原本已经在运行，GUI 只连接使用，不会在退出时关闭它。

### 2) 🧭 终端 TUI

适合开发者、服务器和喜欢终端控制台的用户：

- `s` 同步选中任务
- `w` 开启文件监听
- `x` 停止文件监听
- `e` 编辑任务
- `f` 查看文件索引
- `l` 中文 / English 切换

第一次运行 `tsync dashboard` 时，TUI 会自动初始化本机状态，并启动本机 Agent。

### 3) 🧰 CLI

适合脚本集成、远端机器、Docker 和调试：

- `tsync init`
- `tsync agent run`
- `tsync node add/list/remove`
- `tsync task add/list/remove`
- `tsync sync/rescan/watch/logs`

底层仍然通过本机 Agent 统一执行同步能力。

---

## 🚀 运行示例

### 桌面 GUI

```bash
make gui
```

`make gui` 会先构建本机 Agent 和 CLI，再启动 Tauri 桌面端。

### 终端控制台

```bash
tsync dashboard
```

通常普通用户只需要运行这一条命令。

### 前台运行 Agent

用于远端机器、Docker、服务器或调试日志：

```bash
tsync agent run
```

### 添加远端节点

```bash
tsync node add nas 192.168.1.20:38746
```

如果远端启用了证书指纹校验：

```bash
tsync node add nas 192.168.1.20:38746 --cert-fingerprint <sha256>
```

### 创建同步任务

```bash
tsync task add documents \
  --source ~/Documents \
  --target-node <node-id> \
  --target-path /backup/Documents
```

### 同步、扫描、监听和日志

```bash
tsync sync <task-id>
tsync rescan <task-id>
tsync watch <task-id> start
tsync watch <task-id> status
tsync watch <task-id> stop
tsync logs --limit 20
```

---

## 🧠 架构怎么组织？

```text
Desktop GUI / TUI Dashboard / CLI
        |
        | localhost HTTP
        v
TurboSync Agent
  |-- Local Control API (axum)
  |-- SQLite State DB
  |-- File Watcher (notify)
  |-- Scanner / Indexer (walkdir + blake3)
  |-- Sync Engine
  |-- QUIC Transport (quinn)
```

典型工作流：

1. GUI/TUI/CLI 调用本机 Agent API。
2. Agent 扫描源目录，写入 SQLite 文件索引。
3. 同步引擎生成创建、更新、删除计划。
4. 本地目标直接复制文件。
5. 远端目标通过 QUIC 发送到目标 Agent。
6. 同步结果写入日志，并展示在 GUI/TUI/CLI 中。

---

## 📦 Workspace 拆分

```text
turbo_sync/
|-- crates/
|   |-- turbosync-agent/      # 本机 Agent 和控制 API
|   |-- turbosync-cli/        # tsync 命令行入口
|   |-- turbosync-core/       # 配置、模型、通用类型
|   |-- turbosync-gui/        # Tauri + React 桌面 GUI
|   |-- turbosync-storage/    # SQLite / SQLx 持久化
|   |-- turbosync-sync/       # 文件扫描、同步引擎、文件监听
|   |-- turbosync-transport/  # Agent-to-Agent QUIC 传输
|   `-- turbosync-tui/        # 终端仪表盘
|-- migrations/               # SQLx migrations
|-- docs/                     # 用户指南、自测流程、产品文案
|-- Makefile
`-- README.md
```

这种拆分让 UI、命令行、存储、同步引擎和传输层各自聚焦，也方便后续替换或增强某一层能力。

---

## 🪨 过程中的关键坑位

### 坑 1：普通用户不该手动管理后台服务

如果每次使用前都要求用户理解并启动 Agent，桌面同步工具的门槛会很高。

修复：GUI/TUI 启动时自动初始化并拉起本机 Agent；如果 Agent 原本已经存在，则只连接使用，不抢占生命周期。

### 坑 2：文件监听不能直接等同于最终状态

文件系统事件可能合并、重复或遗漏，直接把事件当同步事实会导致边界问题。

修复：监听事件只作为提示，真正同步前仍以扫描和 SQLite 索引为准，并使用 500ms 防抖降低抖动。

### 坑 3：远端同步需要可验证的传输身份

Agent-to-Agent 传输如果只看地址，在可信网络之外会有误连风险。

修复：支持远端证书指纹配置，添加节点时可传入 `--cert-fingerprint <sha256>`。

### 坑 4：跨平台发布不只是构建一个 Rust 二进制

桌面端需要同时带上 `tsync-gui`、`tsync` 和 `turbosync-agent`，还要处理 Linux、macOS、Windows 的不同包形态。

修复：Release workflow 在 `v*` tag 推送时构建三平台产物，Linux 提供 `.deb`，macOS 提供 `.tar.gz`，Windows 提供 `.zip`。

---

## ✅ 开发与验收

### 基础检查

```bash
make check
```

等价于：

```bash
cargo fmt --all --check
cargo clippy --workspace --all-targets -- -D warnings
cargo test --workspace
```

### GUI 检查

```bash
make build-gui
make gui-check
```

覆盖前端构建、Tauri Rust fmt/clippy/test 和 Tauri shell 编译检查。

### 手动自测

详细流程见：

```text
docs/testing.zh.md
```

覆盖：

- 本地 -> 本地
- 本地 -> 远端
- 双向同步基础验证
- TUI 快速验收

---

## 🧭 下一步计划

1. 🖥️ 继续打磨桌面 GUI：降低首次使用门槛，增强错误提示和任务编辑体验
2. 🔐 增强远端安全体验：更清晰的证书指纹展示、校验提示和连接诊断
3. 🔁 稳定双向同步：完善冲突处理、测试矩阵和真实场景验收
4. 📊 强化可观测性：更细粒度的同步进度、失败原因聚合和历史趋势
5. 📦 完善发布体验：持续优化三平台安装包和用户文档

---

## 💬 结语

`TurboSync` 的方向很明确：

> 不是再造一个复杂云盘，而是提供一个面向个人设备、NAS 和 Homelab 的轻量同步基础设施。

如果你也在维护多台设备之间的开发目录、备份目录或服务器工作目录，欢迎直接从 GUI 或 `tsync dashboard` 跑起来试一轮。🔥
