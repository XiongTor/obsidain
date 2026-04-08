---
title: "lecture 9"
date: "2026-03-31"
authors: Tao Xiong
tags:
---
# 1. Self-Reference 
> 函数可以在其体内引用自己的名字

```python
def print_all():
	print(x)
	return print_all
	
print_all(1)(3)(6)

```

# 2. Recursive function  递归函数
递归函数是一个函数，其主体要么直接调用自身，要么间接调用自身
这意味着，当执行递归函数的主体的时候，可能需要再次应用同样的函数

```python
#求和数字的所有位数的和，例如2013=2+0+1+3=6
def split(n):
	return n // 10, n % 10
	
def sum_digits(n):
	if n < 10:
		return n
	else:
		all_but_last, last = split(n)
		return sum_digits(all_but_last) + last
```

# 3. Recursion in Environment Diagrams 递归中的环境图
![](../../../imag/lecture%209/file-20260408201644939.png)


## 3.1 迭代与递归的区别  Iteration  vs   Recursion

递归某种程度上更加简洁易读，且参与的参数要更少
![](../../../imag/lecture%209/file-20260408201902783.png)

# 4. Verifying Recursive Functions  验证递归函数的正确性

## 4.1 The Recursive leap of faith  递归的信仰之越？？

1. 验证基本功能，检查一个简单的例子
2. 功能抽象化，不考虑如何实现，只考虑它应该做什么
3. 假设 fact(n - 1)是正确的
4. 验证 fact(n)是正确的

![](../../../imag/lecture%209/file-20260408202646225.png)

# 5. Mutual recursion 相互递归
指两个不同的函数的相互调用
## 5.1 Luhn算法，用于计算信用卡号的校验和
> 信用卡的最后一位数字是通过前面所有的数字计算得到的，如果最终计算对不上，就说明卡号输错了

![](../../../imag/lecture%209/file-20260408204227380.png)

# 6.  Recursion and Iteration

## Converting Iteration to Recursion
将递归函数转化为迭代函数
当我们使用while的迭代函数的时候，重点在于把我迭代中需要保持的状态是什么？即哪些需要迭代？
下例中，需要保持的状态是n和数字总和
![](../../../imag/lecture%209/file-20260408205223357.png)


[[Lecture 10|下一讲]]