# 1. Multiple Environments
   环境（environment）是一系列栈帧（frame）的顺序 ![|550](../../../imag/Lecture%203/file-20260317193302742.png)

当我们开始调用用户自定义的函数的时候，就会在 **全局环境（global frame）** 的基础上创建出frame，得到**多框架环境（multi-frame environment）**，即同时具有 **local frame** 和**global frame** 。
   ![|550](../../../imag/Lecture%203/file-20260317194032183.png)
   下图一共构成了三个环境，分别是：  
   - Global frame
   - f1+global frame    global frame是f1的父级  
   - f2+global frame    global frame是f2的父级  
   ![|525](../../../imag/Lecture%203/file-20260317194424442.png)

值得注意的是，我们必须知道，**没有这些环境存在，其中的names(即 square mul 这些)本身就是没有意义的**，因为这些name的定义是在他们所在的环境中才生效的，所以这些环境某种程度上我们也可以称之为**作用域**


name的定义是在其最早被找到的环境中定义的
根据下图的执行顺序
- **第一步（内层调用）：** 系统发现要算 `square(square(3))`，得先算里面的 `square(3)`。
    - 创建 `f1` 帧，`x=3`。
    - `f1` 调用 `mul(3, 3)` 得到 `9`。
    - **`f1` 返回 `9` 并消失。**
        
- **第二步（外层调用）：** 现在表达式变成了 `square(9)`。
    - 创建 `f2` 帧，`x=9`。
    - `f2` 调用 `mul(9, 9)`。注意：此时查找 `mul` 是通过 `f2 -> Global` 这个链条完成的。
    - 得到结果 `81`。
    - **`f2` 返回 `81` 并消失。**
    - 
- **最后：** Global frame 接收到最终结果 `81`

![|600](../../../imag/Lecture%203/file-20260317195118143.png) 




# 2. Miscellaneous Python Features（Python的特性）

## 2.1 检查函数运行(doctest)
例如  ex.py的内同如下所示
```python  ex.py
from operator import floordiv,mod

def divide_exact(a,b):

    """Retrun the quotient and remainder of a divided by b

    >>> q, r = divide_exact(10,3)

    >>> q

    3

    >>> r

    1

    """

    return floordiv(a,b),mod(a,b)
```
当我们运行
```python
python -m doctest -v ex.py

##会得到如下输出：
    q, r = divide_exact(10,3)
Expecting nothing
ok
Trying:
    q
Expecting:
    3
ok
Trying:
    r
Expecting:
    1
ok
1 item had no tests:
    ex
1 item passed all tests:
   3 tests in ex.divide_exact
3 tests in 2 items.
3 passed.
Test passed.
```

当也可以尝试将其中的q改成错误答案后，例如改成2后，观察输出有什么变化

# 3. 条件语句(Conditional Statements)
statement,语句是解释器执行的一些操作，例如将一些名称(name)绑定到具体的数值，或着def定义一个新的函数等等
复合语句，一般具有以下结构：
- 第一个header定义了整个语句(statement)的类型，让你知道这个语句在干什么
- 从句(Clause)控制了随后的主体(Suite)
 ![|775](../../../imag/Lecture%203/file-20260317210844344.png)

### 条件语句

通过布尔上下文来判断值的真假，而不关心值本身的大小
![|775](../../../imag/Lecture%203/file-20260317211735355.png)


# 4. Iteration
## while语句，通过上述的布尔文本来进行判断循环
![|775](../../../imag/Lecture%203/file-20260318220207874.png)


## 相关链接
- [README](README.md)
- [[Lecture 4|下一讲]]