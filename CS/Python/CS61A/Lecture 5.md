
# 1. Environment for Higher-Order Functions (高阶函数的环境)
重点在于理解高阶函数被调用的时候 的环境变化，以下是一些要点：
- 调用函数会创建frame，函数在哪个frame被定义 ，那个frame就是该函数的parent
> 注意是调用，不是定义函数的时候，定义函数不会生成frame，也不会执行函数主体
在下图的例子中，我们可以看到adder(k)的函数父级是f1所以当我们调用新的函数adder()的时候，会创建新的frame f2，此时f2的父级就不是globel frame 而变成了f1 ,之后当我们运算add_three(4)的时候，形参k会绑定到3，而在f2中我们无法找到形参n的value，所以我们往上一级去f1中寻找，发现其父级f1 中 n被绑定为3，此时我们就得知了两个形参的具体数据，便可以执行具体的加法运算


-  每一个用户定义的函数都具有一个父级frame（通常是globel frame）
- 函数的父级frame是其被定义的frame
- 每一个local fram都有一个父级frame（通常是globel frame）
- 一个frame的父级是被调用函数的父级frame

![](../../../imag/Lecture%205/file-20260324193412497.png)

## 如何画一个环境示意图

1. 当一个函数被调用的时候，我们创建一个以被调用函数的name为title的local frame
2. 将被调用的函数的父级赋值到local frame
3. 在被创建的local frame中将形参绑定到具体的数值
4. 最后，在local frame中执行被调用函数的主体
![](../../../imag/Lecture%205/file-20260324194001909.png)

# 2. local name
函数的形参具有局部范围
> 这里也有助于进一步理解上文中提到的如何去找一个函数的父级

在下面的例子中，我们想要理解的是，为什么当我们执行`return = f(1, 2)`的时候会报错：
首先我们来看看各个环境里有些什么：
globel frame，我们定义了func f(x, y)和func g(a)，注意，g(a)的def语句，即函数签名是在globel frame中执行的，所以其父级为 globel frame而不是 func f(x, y)。这与上文不同

当我们执行f(1,2)是我们将 x = 1 , y = 2，此时返回g(1) ,此时的一个环境是 globel frame 与 f frame
然后我们执行g(1)，调用函数，创建frame g 其父级为调用函数的父级，即globel frame，此时令a = 1 ，y=?，在frame g中找不到y 的赋值，去其父级globel frame中，发现也找不到，因此报错

所以其实在执行f(x, y)的时候，我们有两个环境：
环境一：globel frame  + f frame 在这个环境中，x=1, y=2
环境二：globel frame + g frame 在这个环境中，a=1，找不到y

即**函数的形参具有局部范围，f的frame无法作用于g的frame**

![](../../../imag/Lecture%205/file-20260324195551327.png)

# 3. Function Composition 函数组合
与上文类似，注意顺序与环境
![](../../../imag/Lecture%205/file-20260324204335939.png)

# 4. Lambda Expressions
lambda表达式是评估为函数的表达式
> 通过lambda将表达式评估为函数

lambda重视创造一些简单的函数，由于它的特性
同时在python中lambda使用较少，更多的使用def
而在其它的编程语言中，lambda可能占据了一个较大的比例
![](../../../imag/Lecture%205/file-20260324204947648.png)
![](../../../imag/Lecture%205/file-20260324205046405.png)![](../../../imag/Lecture%205/file-20260324205140711.png)


### lambda与def之间的区别
仅仅在intrinsic name即函数的内在名字上有差异，def会返回函数被定义的名字，lambda直接返回lambda
这个差别会体现在画环境图中

![](../../../imag/Lecture%205/file-20260324205523125.png)
![](../../../imag/Lecture%205/file-20260324205839133.png)


# 5. Currying 柯里化

其核心机制为：链式闭包

传统函数：
```python
def volume(l, w, h):
    return l * w * h

# 调用时必须给齐三个数
print(volume(10, 5, 2))
```
柯里化：
```python
def curry_v(l):
    def with_width(w):
        def with_height(h):
            return l * w * h  # 这里的 l 和 w 来自上两层
        return with_height
    return with_width

# 拆解步骤看内存：
step1 = curry_v(10)       # 产生一个闭包，记住了 l=10
step2 = step1(5)          # 又产生一个闭包，记住了 w=5
final_result = step2(2)   # 最终计算：10 * 5 * 2
```
- **普通函数**：一手交钱，一手交货（参数全给，结果出来）
- **柯里化**：分期付款（参数一个一个给，最后给齐了再出结果）
在实际应用中，如果你发现某个函数的前几个参数在一段时间内是固定的，你可以先“固定”住它们，生成一个新的函数。


## 相关链接
- [README](README.md)
- [[Lecture 6|下一讲]]