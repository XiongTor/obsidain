---
title: "Lecture 12"
date: "2026-04-14"
authors: Tao Xiong
tags:
---
# 1. Box-and-Pointer Notation
方框和指针指示法，用于在环境图中表示列表的一种方法

## The Closure proprety of data types
数据类型的封闭属性

- 重点例子是，列表可以包含列表
![](../../../imag/Lecture%2012/file-20260414202107788.png)

### Box-and-Pointer Notation in environment diagrams
在环境图中，列表被表示为一行带有索引标签的相邻方框，每个方框对应一个元素
每个方框要么包含一个原始值，要么指向一个符合值
![|725](../../../imag/Lecture%2012/file-20260414202524333.png)

# 2. Slicing 切片
是一种可以对list和range等序列执行的操作
 ![|725](../../../imag/Lecture%2012/file-20260414203101366.png)

## slicing creates new values
slicing总是会创造一个新的变量
![|650](../../../imag/Lecture%2012/file-20260414203255357.png)

# 3. Processing Container Values 处理容器值

## Sequence aggregation 序列聚合
接受可迭代参数并将它们聚合成单个值的函数  `sum`
![](../../../imag/Lecture%2012/file-20260414204946691.png)

值得注意的是`sum`还可以对list求和，要求输出一个空的list作为start
![](../../../imag/Lecture%2012/file-20260414205110105.png)
其它的如：
max
all
![](../../../imag/Lecture%2012/file-20260414205526292.png)


# 4. strings 字符串
![|800](../../../imag/Lecture%2012/file-20260414205654424.png)

### **当我们执行下列的这个‘curry......’的函数的时候，实际上我们创造了一个新的函数**
![|800](../../../imag/Lecture%2012/file-20260414211046165.png)


strings 是一个序列，意味着我们可以计算它的长度，并选择其中的元素。
但是部分行为与其它的序列也有所差异，例如在“in”和“not in”中
![](../../../imag/Lecture%2012/file-20260414211504405.png)


# 5. Dictionaries 字典
是python中的一种内置数据类型，用于存储键值对(key),键(key)用于查找某个值，而对应的值即为要查找的内容。  
在代码中以及作为值显示的时候，它们的书写方式都使用花括号和冒号来分隔键和值：
限制：
- 一个key只能对应一个数值，如果想要对应多个数值，可以使用list
- key本身不能是list或者字典
```python
{'Dem':0}
```
![|700](../../../imag/Lecture%2012/file-20260414212949921.png)

同样的字典中的value也可以为一个list


## Dictionary comprehensions  字典推导

可以理解为通过一个推导式来生成一个新的字典

![](../../../imag/Lecture%2012/file-20260414215306051.png)
![](../../../imag/Lecture%2012/file-20260414215317970.png)


[[Lecture 13|下一讲]]
