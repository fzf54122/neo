---
title: "🚀 mini-search：高性能、易扩展的轻量级搜索引擎"
date: 2026-01-12
categories: [Python, 搜索引擎]
tags: [FastAPI, BM25, 全文搜索, 异步]
pin: true
description: "🎯 基于 FastAPI 和 BM25 算法的轻量级搜索引擎，支持中英文全文搜索、文档管理和索引自动构建。"
---

<div align="center">
  <img src="./assets/img/search-icon.svg" alt="Search Icon" width="80" height="80" style="margin-bottom: 15px;">
  <!-- <h1 style="margin: 0;">mini-search：高性能、易扩展的轻量级搜索引擎</h1> -->
  <!-- <p style="font-size: 1.1rem; color: #666; margin: 10px 0 20px 0;">🎯 基于 FastAPI 和 BM25 算法，支持中英文全文搜索、文档管理和索引自动构建</p> -->
</div>

<!-- > 🌟 **mini-search** 是一个现代化的轻量级搜索引擎，旨在提供高性能、易扩展的全文搜索解决方案，同时保持开发体验的简洁与优雅！ -->

- 📁 **GitHub**：https://github.com/fzf54122/mini-search/

[//]: # (- 📚 **文档地址**：http://localhost:8000/docs)

---

## 背景：为什么需要 mini-search

在日常开发中，我们经常需要为应用添加搜索功能。传统的数据库模糊查询😫性能差，而大型搜索引擎如 Elasticsearch 又🥵过于笨重，不适合小型项目或快速原型开发。

我在多个项目中反复遇到相同问题：
- 🤦‍♂️ 需要简单易用的搜索功能，但不想引入复杂的搜索引擎
- 🔄 CRUD 代码重复，难以保持统一的响应格式和错误处理
- ⚙️ 并发任务需要根据场景选择线程池或进程池，增加了开发复杂度

基于这些痛点，我开发了 **mini-search**，它集成了三个核心组件：
1. **mini-search 主体**：🔍 轻量级搜索引擎实现
2. **fast-gener-api**：⚡ DRF 风格的 FastAPI 框架，减少 CRUD 代码重复
3. **smart-pool**：🤖 智能并发工具，自动选择线程池或进程池

---

## 设计目标与边界

**目标**：
- 🚀 提供高性能的全文搜索功能，支持中英文混合搜索
- 🎨 保持 API 简洁易用，降低开发门槛
- 🔄 集成自动化 CRUD 接口，减少重复代码
- ⚡ 智能并发处理，优化不同类型任务的执行效率

**不做（至少现在不做）**：
- ❌ 不追求 Elasticsearch 级别的复杂功能
- ❌ 不支持分布式部署（单机高性能优先）
- ❌ 不替代专业的搜索引擎解决方案

---

## 核心特性

mini-search 具备以下核心功能，让搜索变得简单又强大：

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 20px; margin: 30px 0;">

  <div style="background-color: #f8fafc; padding: 25px; border-radius: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.05);">
    <h3 style="display: flex; align-items: center; gap: 10px; margin-top: 0;">
      📚 **文档管理**
    </h3>
    <ul style="list-style-type: disc; padding-left: 20px;">
      <li>✅ 完整的 CRUD 操作支持</li>
      <li>🚀 异步数据库访问 (Tortoise ORM)</li>
      <li>🗂️ 文档状态与软删除管理</li>
    </ul>
  </div>

  <div style="background-color: #f8fafc; padding: 25px; border-radius: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.05);">
    <h3 style="display: flex; align-items: center; gap: 10px; margin-top: 0;">
      🔍 **全文搜索**
    </h3>
    <ul style="list-style-type: disc; padding-left: 20px;">
      <li>📈 基于 BM25 算法的高性能搜索</li>
      <li>🌐 支持中英文混合搜索</li>
      <li>🎯 精确的相关性排序</li>
      <li>⚙️ 可配置的搜索结果数量</li>
    </ul>
  </div>

  <div style="background-color: #f8fafc; padding: 25px; border-radius: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.05);">
    <h3 style="display: flex; align-items: center; gap: 10px; margin-top: 0;">
      📊 **索引管理**
    </h3>
    <ul style="list-style-type: disc; padding-left: 20px;">
      <li>🔄 自动索引构建与更新</li>
      <li>⚡ 多线程索引构建支持</li>
      <li>📸 索引快照与历史记录</li>
      <li>📊 索引大小与性能监控</li>
    </ul>
  </div>

  <div style="background-color: #f8fafc; padding: 25px; border-radius: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.05);">
    <h3 style="display: flex; align-items: center; gap: 10px; margin-top: 0;">
      ⚡ **高性能设计**
    </h3>
    <ul style="list-style-type: disc; padding-left: 20px;">
      <li>🚀 异步处理架构</li>
      <li>📦 Redis 缓存优化</li>
      <li>🤯 支持 GIL 禁用的 Python 3.13+</li>
      <li>🔧 可扩展的多线程搜索</li>
    </ul>
  </div>

  <div style="background-color: #f8fafc; padding: 25px; border-radius: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.05);">
    <h3 style="display: flex; align-items: center; gap: 10px; margin-top: 0;">
      🛡️ **安全与可靠性**
    </h3>
    <ul style="list-style-type: disc; padding-left: 20px;">
      <li>🚦 接口限流保护</li>
      <li>🛡️ 完整的错误处理</li>
      <li>📝 详细的操作日志</li>
      <li>💾 数据完整性保障</li>
    </ul>
  </div>

</div>

---

## 技术架构

mini-search 采用清晰的分层架构设计，像搭积木一样构建功能：

```
┌─────────────────────────────────────────────────────────────┐
│                        API 层                              │
│  (application/app_search/apis/) - 路由、参数验证、响应封装 │
│  基于 fast-generic-api 实现自动化 CRUD 接口                 │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                      Service 层                            │
│  (application/app_search/services/) - 业务逻辑处理          │
│  SearchService 封装索引构建、搜索算法和文档处理             │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                      Model 层                              │
│  (application/app_search/models/) - 数据模型定义             │
│  Tortoise ORM 模型，支持异步数据库访问                     │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                      Commons 层                            │
│  (commons/) - 公共组件：缓存、日志、响应格式化等            │
│  提供跨模块复用的核心功能                                 │
└─────────────────────────────────────────────────────────────┘
```

### 技术栈详情

| 组件类型 | 技术框架 | 版本要求 | 用途说明 |
|---------|---------|---------|---------|
| **后端框架** | FastAPI | >= 0.110.0 | 高性能异步 Web 框架 |
| **ORM** | Tortoise ORM | >= 0.20.0 | 异步数据库访问 |
| **搜索引擎** | 自定义 BM25 | - | 全文搜索算法实现 |
| **中文分词** | jieba | >= 0.42.1 | 中文文本分词处理 |
| **API 自动化** | fast-generic-api | >= 0.1.6 | 自动生成 CRUD 接口 |
| **并发处理** | smart-pool | - | 智能选择线程池/进程池 |
| **缓存** | Redis | >= 7.1.0 | 高性能缓存服务 |
| **数据库** | SQLite/MySQL/PostgreSQL | - | 数据持久化存储 |

---

## 两大核心工具

mini-search 集成了两个自主开发的核心工具，它们是项目高性能和易用性的关键！✨

### 1. ⚡ fast-gener-api：DRF 风格的 FastAPI 框架

fast-gener-api 采用了 DRF 的经典设计模式：`GenericAPIView + Mixins`，让业务侧只关注**模型、schema 和少量配置**，告别重复代码！

**核心设计**：
- **GenericAPIView**：保存通用配置与通用能力（queryset、serializer、lookup、pagination、filter、permissions...）
- **Mixins**：每个动作一个 mixin（List/Create/Retrieve/Update/Destroy）

**使用示例**：
```python
class UserViewSet(
    mixins.ListModelMixin,
    mixins.CreateModelMixin,
    mixins.RetrieveModelMixin,
    mixins.UpdateModelMixin,
    mixins.DestroyModelMixin,
    GenericAPIView,
):
    ...
```

**三大亮点**：
- 📋 统一响应格式、分页结构、错误结构
- 📝 序列化器按场景拆分（create/update/read）
- ⚙️ 支持过滤 / 排序 / 分页 / 权限依赖

### 2. 🤖 smart-pool：智能并发工具

smart-pool 的目标很明确：
> 用一个统一的入口执行参数化任务，让“线程池/进程池选择”变成默认行为！

**核心能力**：
- 🚪 **统一 API**：线程池与进程池共用同一调用方式
- 🧠 **auto 模式**：free-threaded 运行时优先线程池，否则使用进程池
- 📋 **结果策略**：按输入顺序或按完成顺序返回
- ⏱️ **超时控制**：支持单任务超时与整体超时
- 📊 **轻量统计**：可选 `RunStats` 收集执行信息

**使用示例**：
```python
from smartpool import ThreadUtils

result = ThreadUtils.run_parameterized_task(
    work,
    [1, 2, 3, 4],
    mode="auto",  # 智能选择并发模型
    timeout=None,
    ordered=True,
)
```

---

## 快速开始

### 📋 环境要求
- 🐍 Python 3.12+
- 📦 UV (推荐) 或 pip
- 🗄️ Redis 6+ (可选，用于缓存)
- 📊 SQLite/MySQL/PostgreSQL (数据库)

### 🛠️ 安装部署

1. **安装依赖** 📥
   ```bash
   # 安装 UV (推荐)
   curl -LsSf https://astral.sh/uv/install.sh | sh

   # 安装项目依赖
   uv install
   ```

2. **环境配置** ⚙️
   创建 `.env` 文件：
   ```env
   DATABASE_URL=sqlite://db.sqlite3
   REDIS_URL=redis://localhost:6379/0
   APP_TITLE=mini-search
   APP_DESCRIPTION=轻量级搜索引擎 API
   VERSION=1.0.0
   ```

3. **数据库初始化** 📊
   ```bash
   uv run aerich init-db
   ```

4. **启动服务** 🚀
   ```bash
   uv run uvicorn application:app --reload --host 0.0.0.0 --port 8000
   ```

5. **访问应用** 🌐
   - 📚 **API文档**: http://localhost:8000/docs
   - 🔍 **搜索接口**: http://localhost:8000/api/search/search/

---

## API 使用示例

### 1. 📚 文档管理

**创建文档** ✏️
```bash
curl -X POST "http://localhost:8000/api/search/documents/" \
  -H "Content-Type: application/json" \
  -d '{"title": "测试文档", "content": "这是一个用于测试的文档内容", "author": "测试作者", "source": "测试来源"}'
```

**响应示例** 📋
```json
{
  "code": 200,
  "message": "success",
  "data": {
    "id": "uuid-string",
    "title": "测试文档",
    "content": "这是一个用于测试的文档内容",
    "author": "测试作者",
    "source": "测试来源",
    "status": true,
    "is_deleted": false,
    "created_at": "2026-01-12T10:00:00",
    "updated_at": "2026-01-12T10:00:00"
  }
}
```

### 2. 🔍 搜索功能

**创建索引** 🔧
```bash
curl -X POST "http://localhost:8000/api/search/search/" \
  -H "Content-Type: application/json" \
  -d '{"workers": 4, "index_name": "default"}'
```

**搜索文档** 🔍
```bash
curl -X GET "http://localhost:8000/api/search/search/?q=测试文档&limit=10"
```

**响应示例** 📋
```json
{
  "code": 200,
  "message": "success",
  "data": {
    "query": "测试文档",
    "total": 5,
    "limit": 10,
    "results": [
      {
        "doc_id": "uuid-string",
        "title": "测试文档",
        "content": "这是一个用于测试的文档内容...",
        "author": "测试作者",
        "source": "测试来源",
        "created_at": "2026-01-12T10:00:00",
        "updated_at": "2026-01-12T10:00:00",
        "score": 0.95
      }
    ],
    "took": 0.01
  }
}
```

---

## 性能优化

### 1. 🤯 禁用 GIL (Python 3.13+)

在 Python 3.13+ 中，可以禁用全局解释器锁 (GIL) 以获得更好的多线程性能：

```bash
PYTHON_GIL=0 uv run uvicorn application:app --reload
```

### 2. 🔧 调整线程数

根据服务器 CPU 核心数调整索引构建线程：

```bash
curl -X POST "http://localhost:8000/api/search/search/" \
  -H "Content-Type: application/json" \
  -d '{"workers": 8, "index_name": "default"}'
```

### 3. 📦 缓存优化

确保 Redis 缓存配置正确，可显著提升搜索性能：

```env
REDIS_URL=redis://localhost:6379/0
REDIS_POOL_SIZE=50
REDIS_TIMEOUT=30
```

---

## 命令行工具

mini-search 提供了丰富的命令行工具，方便开发和测试：

### 📊 生成测试数据

```bash
# 生成 10000 个中英文混合文档
uv run python cli.py gen-docs --n 10000 --out data/docs.jsonl --mode mix
```

### ⚡ 性能基准测试

```bash
# 测试不同线程数下的索引构建性能
uv run python cli.py bench --docs data/docs.jsonl --workers 1,2,4,8,16 --repeat 3
```

**输出示例** 📈：
```
Python: 3.13.0 | GIL: OFF | CPU: 8
Loading docs: data/docs.jsonl ...
Loaded docs: 10000
workers=1  median=1.234s speedup=1.00 vocab=5000
workers=2  median=0.654s speedup=1.89 vocab=5000
workers=4  median=0.345s speedup=3.58 vocab=5000
workers=8  median=0.198s speedup=6.23 vocab=5000
workers=16 median=0.123s speedup=10.03 vocab=5000
```

---

## 项目结构

```
mini-search/
├── application/          # 🚀 应用核心代码
│   ├── app_search/      # 🔍 搜索应用模块
│   │   ├── apis/        # 📡 API 视图层
│   │   ├── models/      # 📊 数据模型层
│   │   ├── serializers/ # 🔄 数据序列化层
│   │   └── services/    # 💼 业务逻辑层
│   ├── __init__.py      # 🎯 应用入口
│   └── apis.py          # 🗺️ API 路由注册
├── commons/             # 🧰 公共组件
├── conf/                # ⚙️ 配置目录
├── data/                # 📚 测试数据
├── images/              # 🖼️ 项目图片资源
├── logs/                # 📝 日志文件
├── cli.py               # 🛠️ 命令行工具
└── README.md            # 📖 项目说明
```

---

## 总结与未来计划

mini-search 是一个现代化的轻量级搜索引擎，它集成了 fast-gener-api 和 smart-pool 两个核心工具，提供了高性能的全文搜索功能和优雅的开发体验！

**已实现功能** ✅：
- 🔍 中英文全文搜索
- 🚀 自动化 CRUD API
- ⚡ 智能并发处理
- 🏗️ 异步架构设计
- 📚 文档管理功能
- 🔄 索引自动构建

**未来计划** 📅：
- 🔄 支持更多搜索算法（如 TF-IDF、向量搜索）
- ⚡ 优化中文分词性能
- 🎯 提供更丰富的搜索过滤选项
- 📦 支持索引压缩和增量更新
- 📊 完善监控和告警机制

---

## 贡献与致谢

欢迎提交 Issue 和 Pull Request！项目遵循严格的代码规范，确保代码质量和一致性。

**致谢** 🙏：
- ⚡ **FastAPI** - 高性能的异步 Web 框架
- 📊 **Tortoise ORM** - 优雅的异步 ORM
- 📝 **jieba** - 强大的中文分词库
- 📦 **uv** - 现代化的 Python 包管理器
- 🌟 **所有贡献者** - 感谢你们的支持和贡献！

---

## 许可证

Apache License 2.0

---

<div align="center" style="margin: 40px 0 20px 0;">
  <img src="./assets/img/search-icon.svg" alt="Search Icon" width="60" height="60" style="margin-bottom: 15px;">
  <p style="color: #6b7280; margin: 0;">mini-search - 让搜索变得简单而强大</p>
</div>