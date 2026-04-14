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
