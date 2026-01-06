---
title: "Python Free-Threading: 多线程 CPU 密集型性能实测"
date: 2026-01-06
categories: [Python]
tags: [threading, performance, concurrency, python3.13]
pin: false
description: "通过真实基准测试，对比启用与禁用 GIL 时 Python 多线程在 CPU 密集型任务下的性能表现，验证 Python 3.13 free-threading 带来的并行能力提升。"
---

# 🐍 Python GIL 与多线程性能实践

## 📚 背景

Python 中的**全局解释器锁（GIL）**长期以来一直是多线程性能优化的瓶颈，尤其是对于 CPU 密集型任务。本文通过一个简单的测试脚本，直观展示了在**启用**和**禁用** GIL 的情况下，Python 多线程在 CPU 密集型任务上的性能表现差异。

## 📝 测试脚本

我们使用以下脚本测试不同线程数下的 CPU 密集型任务性能：

```python
import os, time
from concurrent.futures import ThreadPoolExecutor

def cpu_work(seed: int, iters: int) -> int:
    """CPU密集型任务：使用线性同余生成器进行计算"""
    x = seed + 1
    for i in range(iters):
        x = (x * 1103515245 + 12345 + i) & 0xFFFFFFFF
    return x

def run(total_iters: int, threads: int) -> float:
    """将总工作量分配到指定数量的线程中执行"""
    per = total_iters // threads
    extra = total_iters % threads
    chunks = [per + (1 if i < extra else 0) for i in range(threads)]

    t0 = time.perf_counter()
    with ThreadPoolExecutor(max_workers=threads) as ex:
        list(ex.map(lambda args: cpu_work(*args), [(i, chunks[i]) for i in range(threads)]))
    return time.perf_counter() - t0

if __name__ == "__main__":
    cores = os.cpu_count() or 4
    total_iters = 120_000_000  # 固定总工作量：1线程=120M；4线程=每个30M

    print("cores=", cores, "total_iters=", total_iters)
    for t in [1, 2, 4, 8, 12]:
        dt = run(total_iters, t)
        print(f"threads={t:<2} time={dt:.2f}s")
```

## 🖥️ 测试环境

| 类别 | 配置 |
|------|------|
| **硬件** | 12核CPU |
| **操作系统** | Linux |
| **Python版本** | 🟢 Python 3.13.5t（支持禁用GIL）<br>🔴 Python 3.12.10（默认启用GIL） |

## 📊 测试结果

### 🟢 Python 3.13.5t（禁用GIL）

```bash
PYTHON_GIL=0 python3 test.py 
cores= 12 total_iters= 120000000 
threads=1  time=31.08s 
threads=2  time=15.45s 
threads=4  time=7.78s 
threads=8  time=5.57s 
threads=12 time=4.62s 
```

### 🔴 Python 3.12.10（默认GIL）

```bash
python3 test.py     
cores= 12 total_iters= 120000000 
threads=1  time=22.50s 
threads=2  time=25.51s 
threads=4  time=24.56s 
threads=8  time=24.77s 
threads=12 time=24.34s 
```

### 📈 性能对比表

| 线程数 | Python 3.13.5t（禁用GIL） | Python 3.12.10（默认GIL） | 性能提升 |
|--------|---------------------------|---------------------------|----------|
| 1️⃣     | 31.08s                    | 22.50s                    | ❌ 慢38% |
| 2️⃣     | 15.45s                    | 25.51s                    | ✅ 快65% |
| 4️⃣     | 7.78s                     | 24.56s                    | ✅ 快216% |
| 8️⃣     | 5.57s                     | 24.77s                    | ✅ 快345% |
| 12️⃣    | 4.62s                     | 24.34s                    | ✅ 快427% |

### 📉 执行时间对比图

```
执行时间（秒）
    35 ┼
       │                                 🟢 Python 3.13.5t (无GIL)
    30 ┼        🔴
       │        │
    25 ┼        │      🔴 🔴 🔴 🔴
       │        │      │ │ │ │
    20 ┼        │      │ │ │ │
       │        │      │ │ │ │
    15 ┼        │  🟢  │ │ │ │
       │        │  │   │ │ │ │
    10 ┼        │  │   │ │ │ │
       │        │  │   │ │ │ │
     5 ┼        │  │   │ 🟢│ │  🟢
       │        │  │   │ │ │ │  │
     0 ┼────────┼──┼───┼─┼─┼─┼──┼──
         1线程   2线程   4线程   8线程   12线程
```

## 🧐 结果分析

### 1. 🔒 GIL的影响

#### 🔴 Python 3.12.10（默认GIL）
- **单线程**：执行时间为22.50秒
- **多线程**：当线程数增加到2、4、8、12时，执行时间基本保持在24-25秒左右
- **性能表现**：多线程不仅没有带来性能提升，反而略有下降
- **原因分析**：GIL限制了同一时刻只能有一个线程执行Python字节码，CPU密集型任务无法通过多线程实现真正的并行

#### 🟢 Python 3.13.5t（禁用GIL）
- **单线程**：执行时间为31.08秒（略慢于3.12.10，可能是禁用GIL的额外开销）
- **2线程**：执行时间减少到15.45秒，接近2倍加速 ⚡
- **4线程**：执行时间为7.78秒，接近4倍加速 ⚡⚡
- **8线程**：执行时间为5.57秒，加速比约5.6倍 ⚡⚡⚡
- **12线程**：执行时间为4.62秒，加速比约6.7倍 ⚡⚡⚡⚡
- **性能表现**：随着线程数增加，执行时间持续减少，展现了良好的并行性能

### 2. 📊 并行效率分析

| 线程数 | 理论加速比 | 实际加速比 | 并行效率 |
|--------|------------|------------|----------|
| 2️⃣     | 2x         | 1.9x       | 98%      |
| 4️⃣     | 4x         | 3.99x      | 96%      |
| 8️⃣     | 8x         | 5.58x      | 70%      |
| 12️⃣    | 12x        | 6.73x      | 56%      |

> 💡 **效率下降原因**：随着线程数增加，并行效率逐渐降低，这是因为线程调度、上下文切换和内存竞争等因素的影响。

> 🎯 **关键发现**：在12线程时（与CPU核心数相同），仍能获得约6.7倍的加速，表明禁用GIL后Python可以有效利用多核CPU资源。

## 🎓 结论

### 🚩 核心发现

1. **🔒 GIL确实是Python多线程性能的主要瓶颈**
   - 对于CPU密集型任务，在默认启用GIL的Python版本中，多线程无法带来性能提升
   - 甚至可能因为线程切换开销导致性能略有下降

2. **🚀 禁用GIL可显著提升多线程性能**
   - 在支持禁用GIL的Python版本中（如3.13.5t），CPU密集型任务可以通过多线程实现真正的并行执行
   - 充分利用多核CPU资源，12线程时获得了约427%的性能提升

3. **📊 线程数与性能的关系**
   - 随着线程数增加，性能逐渐提升
   - 当线程数接近或等于CPU核心数时，性能提升最为明显
   - 超过核心数后，性能提升幅度逐渐减小（受限于硬件资源）

4. **🔄 版本差异影响**
   - 不同Python版本之间即使在相同配置下也可能存在性能差异
   - 这可能与解释器优化、GIL实现等因素有关

### 💡 关键洞察

> "禁用GIL虽然在单线程性能上有轻微损失，但在多线程CPU密集型任务中带来了革命性的性能提升。"

## 🛠️ 实践建议

### 1. 💻 对于 CPU 密集型任务

| 场景 | 建议方案 |
|------|----------|
| **使用 Python 3.13+** | ✅ 考虑禁用 GIL 以获得更好的多线程性能<br>（设置环境变量：`PYTHON_GIL=0`） |
| **使用旧版 Python** | ✅ 使用多进程（`multiprocessing`）绕过 GIL<br>✅ 使用 C 扩展（Cython / ctypes）<br>✅ 尝试 Numba 等 JIT 编译器 |


### 2. 📶 对于I/O密集型任务

- 🟢 GIL的影响较小，多线程仍然是有效的解决方案
- 🚀 也可以考虑使用异步编程（`asyncio`模块）来提高性能
- 💡 优先选择适合任务特性的并发模型

### 3. 📊 性能测试的重要性

> "没有测量就没有优化。" - 性能优化箴言

- 在实际应用中，应根据**具体任务类型**和**运行环境**进行性能测试
- 不同的硬件配置、Python版本和任务特性可能导致不同的性能表现
- 使用性能分析工具（如`cProfile`、`line_profiler`）找到瓶颈所在

## 📚 参考资料

- 📖 [PEP 703 -- Making the Global Interpreter Lock Optional in CPython](https://peps.python.org/pep-0703/)
- 🔒 [Python Global Interpreter Lock (GIL)](https://wiki.python.org/moin/GlobalInterpreterLock)
- 🚦 [Python Multiprocessing vs Multithreading](https://realpython.com/python-multiprocessing/)

---

## 🎉 总结

通过这个简单的测试，我们**直观地看到了GIL对Python多线程性能的巨大影响**，以及禁用GIL后多线程在CPU密集型任务上的出色表现。

### 🌟 未来展望

随着Python 3.13的正式发布，**禁用GIL的特性**将为Python的并行计算能力带来新的可能性，特别是对于：
- 科学计算和数据分析领域
- 机器学习和深度学习应用
- 高性能Web服务和API

Python正在变得越来越强大，能够更好地利用现代多核CPU的计算能力！🚀

---

*本文测试结果基于12核Linux系统，实际性能可能因硬件配置和任务特性而异。*