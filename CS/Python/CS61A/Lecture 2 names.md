---
date: 2026-03-12
---
------------
# 1. names
绑定names与values的三种方式
1. 通过`import`来实现，要求是只能是**内置函数**，可以看到pi被赋值为了圆周率常数
```python
from math import pi
pi
3.1414926
```
2. 通过自定义直接赋值
```python
length=10
```
3. 通过**def**语句来实现，创建自定义函数，注意retrun所在的行需要tap缩进才能正常运行
```python
def area():
	return radius*radius*pi
```

# 小结
指明一些表达式的类别
![|700](../../../imag/Lecture%202%20names/file-20260312200128609.png)

# 2 . define functions 定义函数
```python
def<name>(<formal parameters>):
	return <returun expression>
```
其中def之后到"："为止的部分被称之为**函数签名（Function signature）** 他定义了函数的名字
return所在的行是**函数体（Function body）** 他定义了函数到底如何执行，再次重申，此行需要tab缩进
![|500](../../../imag/Lecture%202%20names/file-20260312201238139.png)
# 3. calling user-defined functions  调用自定义函数
调用自定义函数三步（注意不是写自定义的函数）:
1. 创建了一个本地**frame(栈帧)**,是变量作用域的意思,用完就作废，相当于为你当前定义的函数的执行指定了一个运行的地方，可以称之为local frame，而在此之前你一直处于全局的环境中，可以称之为globel frame
2. 将形式参数（形参，formal parameter），即下列图片中的x，与参数值进行绑定，即`x与-2`绑定
3. 运行函数主体在这个新的frame中，即开始计算mul(x,x)
![|700](../../../imag/Lecture%202%20names/file-20260312211445262.png)
#  4. 环境是一个个栈帧的序列
序列意味着是有顺序的，调用函数会**从当前执行点开始，一层层往外推**
- **环境（Environment）= 帧的链条（Chain of Frames）：** 环境不是一个孤岛，它是由“当前帧 + 它的所有父帧”组成的一条线。
- **查找逻辑是“就近原则”：** Python 只要在链条的任何一环找到了这个名字，就会立即停止搜索。这意味着**内部环境的名字会遮蔽外部环境的同名变量**。

在一个frame中一个name只能绑定一个值，它会被最新的赋值给覆盖
![|650](../../../imag/Lecture%202%20names/file-20260312213755197.png)  

## 小工具
[Online Python Tutor](https://pythontutor.com/visualize.html#mode=edit)
用于解析python的每一步都是如何运行的

# 5.print and none
使用print与直接在python命令行中键入存在一定的区别例如：
```python
>>> -2
-2
>>>print(-2)
-2
>>>"go home!"
"go home!"
>>> print("go home!")
go home!   ### 使用print就不会直接打印出引号
>>> print(1,2,3)
1 2 3 ###此时会直接打印出1 2 3而不会打印出逗号，逗号被默认为分隔符
>>>print(None)
None
>>>None #当我们直接输入None时，不会显示任何内容，因为none意味着什么也没有
### 此时
```
## 纯函数（pure function）与非纯函数(Non-pure function)
![|675](../../../imag/Lecture%202%20names/file-20260312215536139.png)

print()就是一个典型的非纯函数，其有一个副作用，即不知一个输出
其中输出-2是print()函数的特性，其会打印出其中的内容，同时他还会return一个None

所以我们可以看一个例子：
其中表达式的执行是从左往右，所以会在第一个print(1)的时候直接print出一个1，一次类推
然后内部的两个print都会返回一个None，而print是可以直接打印出None的，所以最左侧的print的打印出两个None
同时最左侧的print还会retune一个None，但是在python中默认不会显示直接输出的None，所以最后一个None并没有显示。
![](../../../imag/Lecture%202%20names/file-20260312215832453.png)


# 6. 总结

![](../../../imag/Lecture%202%20names/file-20260316185202940.png)
## 相关链接

- [README](README.md)
- [[Lecture 3|下一讲]]