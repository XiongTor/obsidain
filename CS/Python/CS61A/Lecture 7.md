---
title: "Lecture 7"
date: "2026-03-25"
authors: Tao Xiong
tags:
---
# 1. Lambda Function Environments
```python
a=1
def f(g):
	a = 2
	return lambda y: a * g(y)

f(lambda y: a + y)(a)
```

环境解析：
**Global frame:**   
a = 1
f func f(g)

此时我们执行表达式`f(lambda y: a + y)(a)`
其运算符为f是一个函数，操作数为表达式`lambda y: a + y`
然后评估`lambda y: a + y`，其会创造一个新的函数，其parent为global frame

然后我们开始执行f(g)这个函数，其中g
f1 frame
a = 2
g 为 func `lambda y: a + y` 即我们最开始的表达式中的操作树的那个lambda函数
同时return 函数 g