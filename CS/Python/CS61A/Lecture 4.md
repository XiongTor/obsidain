# 1. Iteration example（迭代）

## 1.1 While循环与The Fibonacci seqence （斐波那契数列）
每一个树是前两个数之和的数列
比较重要的是确定每一个数的位置，即index 索引 
例如0就是第0个斐波那契数，1是第1个，2是第4个，以此类推
```python
0,1,1,2,3,5,8,13,21,34,55,89,144,233,377
```

使用while循环帮我们找到第n个斐波那契数是多大，即index为n的时候，对应的数值
![|550](../../../imag/Lecture%204/file-20260320191255078.png)

# 2. Control控制语句
 if 和while这些都被称之为控制语句
尝试使用**function**实现与if的控制语句相同的功能

if statement   `VS`  if call expression
![|575](../../../imag/Lecture%204/file-20260320194242313.png)

调用表达式不允许你跳过对调用表达式的部分进行评估，在函数被调用之前，所有的部分都会被始终的进行评估
控制语句会选择执行语句的哪些部分以及跳过哪些部分

举一个例子：
```python
# if statement，返回一个数字的真实的平方根，对于负数，其平方根的实数部分为0，所以返回0
def real_sqrt(x):
	""""Return the real part of the square root of x"""
	if x>= 0:
		return sqrt(x)
	else:
		return 0

# 此时针对数字16和-16
>>> real_sqrt(16)
4
>>> real_sqrt(-16)
0



# if expression
def real_sqrt(x):
	return if_(x>=0,sqrt(x),0)
# 此时针对数字16和-16
>>> real_sqrt(16)
4
>>> real_sqrt(-16)
报错

#结合之前学过的表达式的执行顺序，对于表达式内的嵌套表达式，需要先执行运算，计算出具体数值后，再逐层传递给外层的函数，因此在这里先直接运算了sqrt(-16)导致了数学运算错误，所以会直接输出报错
```


# 3. Control Expressions (logical operators     and ; or)

![](../../../imag/Lecture%204/file-20260320200111810.png)

例如下面这个例子：

### and
特性是一假恒假
对于and这个逻辑符，为了避免sqrt函数输出负数导致报错，我们加了一个x>0的判断在左侧
此时当x<0时，左侧直接输出FALSE,由于and的特性，所以右侧就不需要再次计算，会直接输出FALSE
此时如果我们呼唤`x>0`和`sqrt(x)`的位置的话，如果输出-4，就会直接报错

### or
特性是一真恒真
当我们判断一个数是否合理的时候，即其大小会不会超过python的最大精度显示，其代码如下
其中`n == 0`是为了防止n作为被除数会显示数学运算错误。因此加了一个前提条件

![](../../../imag/Lecture%204/file-20260320200728685.png)


# 4. Higher-Order Functions（高阶函数）

在python中，函数是“一等公民”（First-class citizen）。这意味着函数不仅可以被调用，还可以像整数、字符串一样被操作。
一个函数只要满足以下**任意一个条件**，就是高阶函数：
- **接收一个或多个函数作为参数**（就像你的 `summation` 接收 `cube` 或 `identity`）
- **返回一个函数作为结果**（例如闭包或装饰器）

例如下面的例子中，summation函数就是一个高阶函数
高阶函数的主要作用就在于解耦或者说泛化，用于减少脚本编写中的重复
主要就是逻辑抽象与代码复用
```python
def cube(k):

    return pow(k,3)


def summation(n,term):

    """ Sum the first n terms of a seqence.

    >>> summation(5, cube)

    225

    """

    total,k = 0,1

    while k < n:

        total,k = total+term(k),k+1

    return total
```
![|625](../../../imag/Lecture%204/file-20260320214536217.png)

如何理解高阶函数可以返回一个函数（闭包）
它的优势是可以**让函数创造函数**，而不需要去做频繁的更改
```python
def make_similarity_filter(threshold):
    def filter_logic(seq):
        return seq.identity >= threshold  # 核心逻辑只写一遍
    return filter_logic

# 创造具体的工具
strict_filter = make_similarity_filter(0.99)
loose_filter = make_similarity_filter(0.80)

#具体使用:
## 按照严格的标准筛选
strict_filter(seq)

## 按照宽松的标准筛选
loose_filter(seq)
```

### 高阶函数的意义：
#### 1. Express general methods of computation（表达通用的计算方法）
这对应的是 **“框架感”** 。写一个 `summation`，就相当于写了一万个求和函数。
#### 2. Remove repetition from programs（消除程序的重复）
#### 3. Separate concerns among functions（分离函数的关注点）
- **理解：** 一个好的函数应该只负责一件事。
    - `summation` 只负责**迭代和累加**（Iterating）。
    - `cube` 只负责**数学运算**（Math）。 
- **解耦的威力：** 这种分离让测试变得极其简单。你可以单独测试 `cube(3)` 是不是 27，也可以单独测试 `summation` 的边界条件。它们互不干扰，像乐高积木一样插拔。
```python
# 也可以被称之为闭包
def make_adder(n):
    """Returns a function that takes on argument k and return n + k.
    >>> add_three = make_adder(3)
    >>> add_three(4)
    7
    """
    def adder(k):
        return n + k
    return adder
```
![](../../../imag/Lecture%204/file-20260320215401128.png)
![](../../../imag/Lecture%204/file-20260320215501352.png)
![](../../../imag/Lecture%204/file-20260320215629618.png)



## 相关链接
- [README](README.md)
- [[Lecture 5|下一讲]]