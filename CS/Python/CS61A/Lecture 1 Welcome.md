---
course: CS61A
lecture: Lecture 1 - Welcome
topic: 表达式 (Expressions)
date: 2026-03-10
tags:
  - CS61A
  - Python
  - 表达式
---

# Lecture 1: 表达式 (Expressions)

---

## 1. 什么是表达式？

表达式是**可以求值的代码片段**。

### 日常例子
```python
18 + 90      # 加法
12 * 8       # 乘法
12 / 3       # 除法
12 ** 3      # 幂运算
```

> 💡 数学中的 `f(x)` 就是表达式的典型形式

### Python 中的表达式
![Python表达式示例](../../../imag/Lecture%201%20Welcome/file-20260310203132723.png)

- 最后一行是**嵌套表达式** (Nested Expression)

---

## 2. 调用表达式的结构 (Call Expression)

```python
add(1, 2)
```

- **运算符 (Operator)**：开括号前的部分，如 `add`
- **操作数 (Operands)**：括号内逗号分隔的部分，如 `1`, `2`

### 求值过程
1. 先明确 **operator**（运算符）
2. 再按顺序求值 **operands**（操作数）
3. 最后将 operator 应用于 operands

> 🔁 嵌套表达式同样遵循此规则，从operator开始逐步往内读，如果读到另一个表达式，则按照同样的顺序先解析清楚这个“子表达式”。所以其本质还是不同单个表达式的逐步解读。

---

## 3. 表达式树 (Expression Tree)

将嵌套表达式可视化为树形结构：

![表达式树](../../../imag/Lecture%201%20Welcome/file-20260310204333441.png)


---


---

## 相关链接

- [README](README.md)
- [[Lecture 2 names|下一讲]]
