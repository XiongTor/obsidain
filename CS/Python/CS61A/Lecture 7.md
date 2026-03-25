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
==**Global frame:**  ==
a = 1
f func f(g)

此时我们执行表达式`f(lambda y: a + y)(a)`
其运算符为f是一个函数，操作数为表达式`lambda y: a + y`
然后评估`lambda y: a + y`，其会创造一个新的函数，其parent为global frame
然后我们开始执行f(g)这个函数，其中g是要求作为一个函数输入的

==**f1 frame**==
a = 2
g 为 func `lambda y: a + y` 即我们最开始的表达式中的操作数的那个lambda函数，此时a = 1
同时return 新函数 `lambda y: a * g(y)` 即 `2 * g(y)`其被return到最开始的 `f(lambda y: a + y)(a)`中作为函数`f(lambda y: a + y)`的执行结果，此时a=2，y作为变量，绑定的是global frame里的a = 1
然后执行新函数

==**f2 frame**==
因为我们return的新函数是在f1 中生成的，所以其父级为f1
y = 1 绑定的是globel frame 里的a，即`f(lambda y: a + y)(a)`中最右侧的a
此时的具体函数为 2 * g(y)

==**f3 frame**==
g(y)即`lambda y: a + y`
其中a 为global frame中的 a = 1 
y 即 表达式最右侧的a ,也是主环境中的 a = 1
此步返回值为2

最终代入到2* g(y) = 2 * 2 = 4 
![](../../../imag/Lecture%207/file-20260325202536746.png)


# 2. return

执行函数时候，当我们到达return的时候，函数结束
 