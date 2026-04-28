---
title: "Lecture 14"
date: "2026-04-17"
authors: Tao Xiong
tags:
---
# Trees
树状结构描述：
- 每一个树有一个root和一系列的branch
- 每一个branch都是一个树
- 没有branch的树被称之为leaf

树的关系的描述
- 树上的位置被称之为node
- 每一个node有一个label代表了它的值
- 每一个node可以被表述为另一个node的父节点或者子节点  
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

```

