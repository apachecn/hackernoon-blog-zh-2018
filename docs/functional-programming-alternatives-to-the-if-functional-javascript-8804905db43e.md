# 函数式编程:IF #Functional #JavaScript 的替代方案

> 原文：<https://medium.com/hackernoon/functional-programming-alternatives-to-the-if-functional-javascript-8804905db43e>

有几次我被问到“在函数式编程中，你会怎么做 X？”我绝对喜欢这类问题。

我尽我所能回答每个问题，但我认为有许多问题足够有趣，值得他们自己的文章。

因此，在这篇文章中，我想展示如何以一种更实用的方式重新创建这个命令式函数。

这个函数有一个没有`else`的`if`语句。因此，虽然一个`ternary`操作符可以工作，但并不理想。

这里，我们只需要在有“值”时运行“调度”,否则我们什么也不做。

一种选择是使用短路操作器:

短路和三进制，这两种方法都可以解决这个问题，但是让我们再看几个。

# 解决方案 A: ifVal 辅助函数

为了用一种更实用的方式来做这件事，我将创建一个助手函数。我会先写这个旧学校的帮助者，然后一点一点地把它浓缩，这样每个人都能容易地理解它。

现在我们可以修改我们的`someAction`函数，使用`ifVal`代替传统的`if`块。

下面是一个简单快捷的 twitter 截图对比:

## 进一步阅读

看看 Ramda 的[当](http://ramdajs.com/docs/#when)、[除非](http://ramdajs.com/docs/#unless)和 [ifelse](http://ramdajs.com/docs/#ifElse) 其他有用的类似功能。

# 解决方案 B:函子

我们也可以使用**或者键入**。Maybe 类型由一个保存值的`Just`类型或一个确切表示值的`Nothing`类型组成。

对于这个例子，我将使用来自 [Sanctuary](https://sanctuary.js.org/) 库的 Maybe 类型。

它看起来有点像这样:

也许类型非常简单，我之所以想用它是因为`map`和`Maybe`配合得非常好，它被赋予了一个特殊的名字，一个**仿函数**。

一些库，如 [ramda-fantasy](https://github.com/ramda/ramda-fantasy) 有流畅的语法，但是本文使用的是 Sanctuary。不过，很高兴知道这两者是等价的。

示例:

所有加在一起的`doSomething`也许会是这个样子:

这里我们有三个函数，`getItem`、`toMaybe`和`map`，它们可以组合成一个新的函数。我们可以将值传递给这个新函数，它将首先流入`getItem`，然后是`toMaybe`，最后这个值将流入我们的`map`。

最后一张完美的推特图片:

# 结束

我希望你喜欢这个小小的功能之旅。如果你这样做了，如果你迷路了，如果你有任何问题，或者如果你想要更多像这样的文章，请在评论中告诉我。如果你真的想激励我，请在这里关注我或 [Twitter](https://twitter.com/joelnet) 如果你**喜欢**这篇文章，请四处传阅！

干杯！

附:这篇文章最初是[发布在 dev.to](https://dev.to/joelnet/functional-programming-how-would-you-if-no-else-javascript-59ai) 上的。