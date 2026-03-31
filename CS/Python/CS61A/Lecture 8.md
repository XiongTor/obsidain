---
title: "Lecture 8"
date: "2026-03-31"
authors: Tao Xiong
tags:
---


课程内容回顾，实际例子讲解
关于：
- print()函数
- 高阶函数
- 简单脚本



# Decorators 装饰器
装饰器的本质是一个**高阶函数**，它接收一个函数作为参数，并返回一个新的函数。

可以在不修改原有函数代码的情况下，给函数动态地添加新功能（比如记录日志、计时、权限校验等）

![](../../../imag/Lecture%208/file-20260331201943685.png)

```python
import functools

def trace(func):
    @functools.wraps(func) # 保证原函数的元数据（如名字）不丢失
    def wrapper(*args, **kwargs):
        # 1. 执行函数前的动作
        print(f"--- 正在调用: {func.__name__}，参数: {args} ---")
        
        # 2. 执行原函数
        result = func(*args, **kwargs)
        
        # 3. 执行函数后的动作
        print(f"--- {func.__name__} 执行完毕，结果为: {result} ---")
        return result
    return wrapper

@trace
def add(a, b):
    return a + b

# 调用函数
add(5, 10)



# -----------------------------------------------------------
# 输出
--- 正在调用: add，参数: (5, 10) ---
--- add 执行完毕，结果为: 15 ---
```


[[lecture 9|下一讲]]