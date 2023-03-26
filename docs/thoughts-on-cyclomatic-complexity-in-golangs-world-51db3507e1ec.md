# 关于戈朗世界圈复杂度的思考

> 原文：<https://medium.com/hackernoon/thoughts-on-cyclomatic-complexity-in-golangs-world-51db3507e1ec>

![](img/fad7768bc4149c00189d5ce6f35e91cb.png)

圈复杂度有助于我们避免编写更容易出错、测试更复杂、推理更困难的复杂代码。我会特别关注后者，因为它意味着可读性低和维护成本高。例如，if/else 语句通常用作:

```
if THIS then
 do some logic in order to return THATif THAT then
 do some logic in order to return THISif SOMETHINGELSE then
 do some logic in order to return THAT and THIS
else
 do some logic in order to return THIS and THAT
```

通常每个块都包含一些逻辑，以便做一些事情。自然，每个逻辑都需要关注，因为你必须理解一个模块在做什么，它的目的是什么，以及预期的结果是否是正在产生的。现在想象一个有 20 个条件语句的函数，每个语句产生一个不同的结果，你必须解释(并记住)这个函数在什么条件下返回所有的值。可能你已经明白了。

但是，请注意圈复杂度并不试图确定控制流图中包含的每条路径有多复杂。它并不试图理解程序架构的每个模块，相反，它关注于大多数程序是如何编写的，并假设所有情况都是一样的。为了有所帮助，它假设所有的执行路径都包含一个特定的和唯一的逻辑，大多数时候都是这样。从我开始在 Go 中[编程](https://hackernoon.com/tagged/programming)开始，我就一直在思考那一点。按照我们在 [Golang](https://hackernoon.com/tagged/golang) 中使用的错误处理模式，检查另一个例子:

```
 THIS, ERR := fnGetThis()
if ERR then
 return errorTHAT, ERR := fnGetThat()
if ERR then
 return errorSOMETHINGELSE, ERR := fnGetSomethingElse()
if ERR then
 return errorWHATEVER, ERR := fnGetWhatever()
if ERR then
 return errordo some logic in order to produce the expected result using THIS, THAT, SOMETHINGELSE and WHATEVER 
```

在我看来，只返回一个错误而没有额外逻辑的 if 语句没有一个是容易出错的，也没有一个是很难推理的，也没有一个是很难解释整个代码块的目的的。它做一件事，一旦它需要的一切工作。

如果我们可以选择放弃返回一个错误是唯一要做的事情，而不是其他事情的情况，也许会出现一个更好的圈复杂度分析。因为产生这些错误的真正复杂性并没有写在那里，而是写在被调用的函数中。

我希望听到更多关于这个主题的想法，因为我在这里的目标是讨论它。所以，请随意发表评论😉。