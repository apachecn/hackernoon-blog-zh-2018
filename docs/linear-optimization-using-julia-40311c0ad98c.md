# 使用 Julia 的线性优化

> 原文：<https://medium.com/hackernoon/linear-optimization-using-julia-40311c0ad98c>

## 编写您的第一个 Julia 代码来解决线性优化问题。

![](img/f997836d1f693986462f977880bc9a66.png)

在这篇文章中，我们将关注使用 Julia 建模线性优化问题。

> 想了解更多关于 Julia 的信息，请点击这里的链接。
> 想了解更多关于线性优化的知识，请点击这里的[链接。](/@jayantakshirsagar/linear-optimization-b688d87c0f28)

Julia 有一个名为 JuMP 的软件包，用于建模优化问题。
JuMP 是一种领域特定的建模语言，可用于线性、混合整数、非线性问题。

JuMP 内部使用 MathProgBase。MathProgBase 提供了独立于求解器的高级函数，可用于与 Cbc、Clp、CPLEX、Gurobi 等求解器进行交互。在这个例子中，我们将使用 Cbc 作为求解器。Cbc 是一个开源的数学求解器，支持线性以及混合整数问题。

*1。创建模型*

```
using JuMP
using CbclpModel = Model(solver = CbcSolver(seconds = 3600))
```

在我们使用 JuMP 创建模型之前，让我们确保安装了 JuMP 和 Cbc。

```
Pkg.add(“JuMP”)
Pkg.add("Cbc")
```

*2。定义变量*

```
[@variable](http://twitter.com/variable)(lpModel, x <= 5)
[@variable](http://twitter.com/variable)(lpModel, y >= 6)
```

我们正在创建两个变量，即具有预定义边界的 x 和 y。

*3。创建约束*

```
[@constraint](http://twitter.com/constraint)(lpModel, x+y <= 20)
[@constraint](http://twitter.com/constraint)(lpModel, 2x + 3y >= 28)
```

*4。创建一个目标*

```
[@objective](http://twitter.com/objective)(lpModel, Max, x + y)
```

*5。求解并阅读答案*

```
status = JuMP.solve(lpModel)

getvalue(x)
getvalue(y)
```

***我们来举个例子。***

有一次，一群 18 名学生走进了一家新开的咖啡馆。咖啡馆只有两个项目，菜单卡是这样的。

1.茶叶印度卢比 20
2。R30 中的咖啡

18 个学生中，5 个要了茶，6 个说要咖啡。剩下的 7 个对这两种饮料都没问题。
此外，他们不想花费超过 470 印度卢比。这让酒店经理很困惑。他曾经在茶叶和咖啡上分别获得 2 卢比和 3 卢比的利润。他想将利润最大化，但不知道如何去做。让我们试着帮助他。

***解***

假设，x 是要求喝茶的学生人数，y 是想要咖啡的学生人数。他们中至少有 5 个人想要茶。

x >= 5
同样，y > = 6

我们知道，总共有 18 名学生，因此，x + y = 18。总费用不能超过 470 英镑，因此

20x + 30y <= 470

目标是利润最大化，这意味着

利润最大化，用 2x + 3y 表示，因为每一杯茶将产生 2 卢比的利润，每杯咖啡将产生 3 卢比的利润。

```
 using JuMP
using CbclpModel = Model(solver = CbcSolver(seconds = 3600))[@variable](http://twitter.com/variable)(lpModel, x >= 5)
[@variable](http://twitter.com/variable)(lpModel, y >= 6)[@constraint](http://twitter.com/constraint)(lpModel, x+y == 18)
[@constraint](http://twitter.com/constraint)(lpModel, 20x + 30y <= 470)[@objective](http://twitter.com/objective)(lpModel, Max, 2x + 3y)status = JuMP.solve(lpModel)println("Number of Tea Cups: $(getvalue(x))")
println("Number of Coffee Mugs : $(getvalue(y))")
```

上面的代码应该输出

```
Number of Tea cups: 7.0
Number of Coffee Mugs : 11.0
```

也就是说，最大利润应该是

2 * 7 + 3 * 11 =印度卢比 47

现在，让我们把问题变得更有趣一点。总预算不是 470 印度卢比，我们假设预算是 475 印度卢比。

因此，我们需要修改其中一个约束。

```
@[constraint](http://twitter.com/constraint)(lpModel, 20x + 30y <= 475)
```

那很简单。让我们用修改后的约束运行整个代码，看看输出是什么。

```
Number of Tea Cups: 6.5
Number of Coffee Mugs : 11.5
```

哪里出了问题？因为，给别人上半杯茶和半杯咖啡是没有意义的，对吗？我们需要确定，x 和 y 是离散的整数变量，而不是连续的。让我们将变量声明改为

```
[@variable](http://twitter.com/variable)(lpModel, x >= 5, Int)
[@variable](http://twitter.com/variable)(lpModel, y >= 6, Int)
```

这也将导致与前面相同的输出。

```
Number of Tea Cups: 7.0
Number of Coffee Mugs : 11.0
```

*感谢阅读。非常感谢您的反馈。*