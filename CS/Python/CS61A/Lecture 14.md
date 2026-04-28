---
title: "Lecture 14"
date: "2026-04-17"
authors: Tao Xiong
tags:
---
# 1. Trees
树状结构描述：
- 每一个树有一个root和一系列的branch
- 每一个branch都是一个树
- 没有branch的树被称之为leaf

树的关系的描述
- 树上的位置被称之为node
- 每一个node有一个label代表了它的值
- 每一个node可以被表述为另一个node的父节点或者子节点  

下图的例子本身是一个斐波那契树
![|750](../../../imag/Lecture%2014/file-20260428185701522.png)

### 树的抽象化
![|151](../../../imag/Lecture%2014/file-20260428190249438.png)
```python 
>>> tree(3,[tree(1),
>>> 		tree(2,[tree(1),
>>> 				 tree(1)])])

# 可以看到其中包含了一个list
# tree是一个函数，下面我们将会开始定义这个函数

def tree(label,branch=[]):
	for branch in branches:
		assert is_tree(branch), 'branch must be a tree'
	retrun [label] + list(branches)

def label(tree):
	return tree[0]

def brancher(tree):
	return tree[1:]

def is_tree(tree):
	if type(tree) != list or len(tree) < 1:
		return False
	for branch in branches(tree):
		if not is_tree(branch):
			return False
		return True

def is_leaf(tree):
	return not branches(tree)

tree(1,[tree(5,[tree(7)]),tree(6)])
```

# 2. Tree processing
以tree为输入的或者输出的函数，其本身通常都是tree递归的
**处理树的叶子，基本上是处理树的基本情况**，意思是一般以处理树的叶子为处理树的常规操作

```python
# 简单的例子，计算一个tree的leaves数量
def count_leaves(t):
	if is_leaf(t):
		return 1
	else:
		branch_counts = [count_leaves(b) for b in branches(t)]
		return sum(branch_counts)
 
# 第二个例子，实现一个leaves函数，使得它可以返回树的叶子标签列表
# sum函数计算list的总和时的特性，其会得到一个总的list,包含所有list的元素，eg：
# sum拼接列表的时候需要指定一个空list来作为起始
>>> sum([[1],[2,3],[4]],[]) # 注意这里套了一个list  [[]]
>>> [1,2,3,4]
>>> sum


def leaves(tree)：
	if is_leaf(tree)：
		return [label(tree)]
	else:
		return sum([leaves(f) for f in branches(tree)],[])


# 第三个例子，给树的所有leaves加一，或者给所有的nodes加一
def increment_leaves(t):

```