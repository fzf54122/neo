---
title: "Fast Generic Api：DRF 风格 FastAPI 框架的设计记录"
date: 2025-12-26
categories: [Python, fastapi]
tags: [pydantic, tortoise-orm]
pin: false
description: "DRF 风格 FastAPI 框架的设计记录：GenericAPIView + Mixins、统一响应、过滤分页与工程化取舍。"
---

> 这篇不是 README 宣传页，而是我做 **Fast Generic Api** 的工程记录：为什么做、怎么设计、做了哪些取舍、接下来准备怎么迭代。

- GitHub：https://github.com/fzf54122/fast_generic_api/
- 快速上手（最小例子）：/posts/fast-generic-api-quickstart/

---

## 背景：我为什么做它

在多个项目里反复遇到相同问题：

- CRUD 代码重复（list/create/retrieve/update/delete 大量模板化）
- 统一响应 / 分页 / 错误格式很难保持全项目一致
- 不同操作需要不同 schema（create/update/read）容易写乱
- 路由注册、权限依赖、排序过滤每个模块都要重复配置

我想抽一层“最小框架层”，让业务侧只关注：**模型、schema、少量配置**。

---

## 目标与边界

**目标**：

- 像 DRF 一样组合能力：`GenericAPIView + Mixins`
- 统一响应格式、分页结构、错误结构
- 支持常见工程能力：过滤 / 排序 / 分页 / 权限依赖
- 保持 FastAPI 的类型提示体验

**不做（至少现在不做）**：

- 不把它做成“大而全平台”
- 不追求覆盖所有 ORM / 所有复杂 query DSL
- 不把 README 的 API 参考搬进博客（博客只讲思路与关键点）

---

## 核心设计：GenericAPIView + Mixins

我采用 DRF 的经典拆法：

- **GenericAPIView**：保存通用配置与通用能力（queryset、serializer、lookup、pagination、filter、permissions...）
- **Mixins**：每个动作一个 mixin（List/Create/Retrieve/Update/Destroy）

组合后读起来非常直观：

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

我很在意这一点：看类声明就能知道这个资源支持哪些操作。

---

## 三个我最在意的工程点

### 1）统一响应（省沟通成本）

团队协作时，接口格式只要不统一，前端/调用方就会不断做兼容。

因此我做了“固定结构”的响应封装（含分页结构）。

### 2）序列化器按场景拆分（避免 schema 混乱）

- `serializer_class`：读（list/retrieve）
- `serializer_create_class`：写（create）
- `serializer_update_class`：写（update）

真实项目里，一个 schema 同时兼容读写会越来越难维护。

### 3）过滤 / 分页 / 排序抽成可替换组件

业务侧只写配置即可，复杂时再覆盖默认实现。

---

## 取舍与限制（真实记录）

- 选择 mixins：能力组合可见、可局部覆盖，适合框架层沉淀
- 默认内置分页/过滤：因为真实项目几乎必用，统一很重要
- ORM 选择：当前以异步栈为主（Tortoise ORM 更自然），未来再评估抽象层是否值得做

---

## 下一步计划（TODO）

- 更完善的 filter DSL（icontains / range / in 等）
- 权限组合更灵活（permissions 的组合模式更清晰）
- 示例与测试补齐（尤其是过滤/分页/排序的边界）
- 文档拆分：README 做参考，博客做设计与实践记录

---

## 相关链接

- GitHub：https://github.com/fzf54122/fast_generic_api/
- 快速上手：/posts/fast-generic-api-quickstart/
