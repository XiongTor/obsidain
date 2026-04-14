---
title: "Lecture 11"
date: "2026-04-14"
authors: Tao Xiong
tags:
---
# 1. Lists
列表的索引比列表的长度要少1
索引正确的理解方式是：**从开始位置的偏移量**
第一个数字本身就是起始数字，所以它的偏移量为0
![](../../../imag/Lecture%2011/file-20260414191010913.png)


1. list之间的合并与复制
```python
digits * 2
# 意味着将list digits 重复2次，因此其会变成：
[1,8,2,8,1,8,2,8]
# 然后在其前面加上[2,7]，就会变成
[2,7,1,8,2,8,1,8,2,8]

```

2. list内的元素可以不是数字，它可以是任何东西，甚至是另一个list
![](../../../imag/Lecture%2011/file-20260414191416768.png)


# 2. Containers
用于判断一个数值或其它内容是否在一个list中
如：
```python
>>> digits = [1 ,8, 2, 8]
>>> 1 in digits   # 其中 in 作为了一个运算符
>>> True
>>> "1" in digits
>>> False   # 1 必须得是一个数值，而不能是一个字符
>>> [1 ,8] in digits
>>> False  # [1 ,8]并不是list digits中的一个元素
>>> [1,2] in [1, [1,2],3,4]
>>> True   # [1, 2]现在是list中的一个元素了
```

# 3.  For statements  For语句

**使用for循环==不会==产生新的frame**
![](../../../imag/Lecture%2011/file-20260414195300169.png)

### 在for语句的头部直接进行序列的解包  do sequence unpacking right inside the header of the for statement

为固定长度的sequence取了一个名字，被称之为解包
![](../../../imag/Lecture%2011/file-20260414195658164.png)

# 3 .Ranges  
另一种序列，但并不是以list的形式展示
`range()`生成一段连续的整数
它包含起始值，但是不包含结束值
例如
```python
>>> range(-2,2)
>>> range(-2,2)
# 可以使用list将range转换为list格式
>>> list(range(-2,2))
>>> [-2,-1,0,1]
```

![](../../../imag/Lecture%2011/file-20260414200237250.png)

range的应用
- 用于计算从0到n的所有正整数之和
- 用于重复某个字符固定次数
- 需要注意的是：
```python
def cheer():
	for _ in range(3):  # 此处的"_"只是一种约定俗称的写法，让大家明白这个不打算使用任何的名字，只是为了输出下面print的东西
		print("GO Bears !!! ")

```
![](../../../imag/Lecture%2011/file-20260414200731463.png)

# 4. List Comprehensions  列表推导

![](../../../imag/Lecture%2011/file-20260414201116140.png)
![](../../../imag/Lecture%2011/file-20260414201137113.png)