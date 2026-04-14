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


