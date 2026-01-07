---
title: "smartpool：智能选择线程池/进程池的轻量并发工具"
date: 2026-01-07
categories: [Python]
tags: [concurrency, threadpool, processpool, performance, smartpool]
pin: false
description: "用一个统一 API 运行参数化任务，自动选择线程池或进程池，附带超时、结果策略与轻量统计。"
---

# ⚙️ smartpool：让并发选择更聪明

## 📚 背景

并发执行在日常脚本和服务中非常常见：
- IO 密集任务（请求、读写）适合线程池
- CPU 密集任务（计算、压缩）更适合进程池

问题在于：**每次都要自己判断并发模型**。smartpool 的目标很明确：
> 用一个统一的入口执行参数化任务，让“线程池/进程池选择”变成默认行为。

## 🎯 设计目标与边界

smartpool 面向轻量批处理与脚本场景，保持 API 小而清晰。

- ✅ 适合：IO 密集、CPU 密集、小批量任务
- ❌ 不适合：分布式调度、持久队列、复杂重试流程

## ✨ 核心能力

- **统一 API**：线程池与进程池共用同一调用方式
- **auto 模式**：free-threaded 运行时优先线程池，否则使用进程池
- **结果策略**：按输入顺序或按完成顺序返回
- **超时控制**：支持单任务超时与整体超时
- **轻量统计**：可选 `RunStats` 收集执行信息
- **资源保护**：`max_tasks` 限制任务规模

## 🧩 API 一览

```python
from smartpool import ThreadUtils

ThreadUtils.run_parameterized_task(
    task,
    params,
    *,
    mode="auto",          # auto | cpu | io | thread | process
    max_workers=None,
    thread_name_prefix="default",
    timeout=None,
    timeout_total=None,
    ordered=True,
    result_order=None,    # "input" | "completed"
    chunksize=1,          # process + ordered=True 时 map 路径有效
    max_tasks=None,
    return_exceptions=False,
    stats=None,           # RunStats
)
```

## 🚀 快速开始

### ✅ 1) 自动选择执行器

```python
from smartpool import ThreadUtils

def work(x: int) -> int:
    return x * 2

result = ThreadUtils.run_parameterized_task(
    work,
    [1, 2, 3, 4],
    mode="auto",
)
print(result)
```

### ✅ 2) 结果顺序控制

```python
result = ThreadUtils.run_parameterized_task(
    work,
    [0.2, 0.0, 0.1],
    mode="thread",
    result_order="completed",
)
```

### ✅ 3) 超时与异常返回

```python
from smartpool import RunStats

stats = RunStats()
result = ThreadUtils.run_parameterized_task(
    work,
    [1, 2, 3, 4],
    mode="process",
    timeout=0.5,
    timeout_total=2.0,
    return_exceptions=True,
    stats=stats,
)
print(stats)
```

## 🧠 行为说明

- 进程池要求 `task` 为顶层函数，参数可序列化（pickle）。
- `ordered=True` 时，进程池默认使用 `map`，逐项超时不可用。
- 进程池 ordered 模式启用超时/return_exceptions/timeout_total 会切换为 `submit`，并忽略 `chunksize`。
- `timeout_total` 为全局超时限制，触发时抛出 `TimeoutError`。

## 🧪 适用场景小结

| 场景 | 推荐模式 | 说明 |
|------|----------|------|
| IO 密集批处理 | thread / io | 线程更适合 I/O 等待 |
| CPU 密集批处理 | process / cpu | 进程绕过 GIL |
| 不确定场景 | auto | 运行时自动选择 |

## 🎓 小结

smartpool 的定位不是替代复杂调度系统，而是**让并发选择变得简单**。
如果你希望保持轻量、无需引入额外依赖，又想得到合理的并发模型选择，这个小工具会很合适。

## 📎 参考

- 项目代码：`core/smartpool/thread.py`
- 文档：`README.md`

