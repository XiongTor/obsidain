
# 1. Environment for Higher-Order Functions (高阶函数的环境)
重点在于理解高阶函数被调用的时候 的环境变化，以下是一些要点：
- 调用函数会创建frame，函数在哪个frame被定义 ，那个frame就是该函数的parent
> 注意是调用，不是定义函数的时候，定义函数不会生成frame，也不会执行函数主体
在下图的例子中，我们可以看到adder(k)的函数父级是f1所以当我们调用新的函数adder()的时候，会创建新的frame f2，此时f2的父级就不是globel frame 而变成了f1 ,之后当我们运算add_three(4)的时候，形参k会绑定到3，而在f2中我们无法找到形参n的value，

![](../../../imag/Lecture%205/file-20260324192724316.png)