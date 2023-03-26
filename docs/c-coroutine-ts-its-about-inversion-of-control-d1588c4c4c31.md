# C++协程 TS——它是关于控制反转的！

> 原文：<https://medium.com/hackernoon/c-coroutine-ts-its-about-inversion-of-control-d1588c4c4c31>

![](img/44206b5402ac9606ad5ffe99c14ca37e.png)

“person flip skiing above snow during daytime” by [Jörg Angeli](https://unsplash.com/@joerga?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

许多人看到协程 TS 时会想，这有什么好大惊小怪的？

本文给出了一个激励性的例子，在这个例子中，惰性序列使您能够*分离您的关注点*，并且在不增加复杂性的情况下最大化代码重用。

# **举例:近似黄金比例**

每个学数学的学生都知道如何通过斐波那契数列逼近黄金分割率:

```
fib(n+1) / fib(n) -> φ = 1.6180… 
```

或者使用 C++…

函数`golden`计算给定近似值的黄金比例。在每一次迭代中，我们都更接近黄金比例，一旦我们足够接近，函数就会返回。

这很简单，但是如果我们对`golden`有一些额外的用例呢？

*   也许我们想限制最大迭代次数
*   或者我们想在每次迭代后打印
*   或者我们想返回一个`{ fib(n+1), fib(n) }`的元组
*   或者可能有一个我们还没有想到的用例，但是有人可能已经想到了

为了满足所有这些要求，我们需要修改算法并提供一些定制点。我们可以添加一组配置选项，或者使用通用功能:

我们可以看到，当我们将其一般化以处理更多用例时，这个函数的复杂性增加了。这个版本的`golden`可读性比原版差很多！

另一种方法是为每个特定的用例编写这个函数的变体。以下是打印每个迭代的版本:

这应该是可读的，但是现在我们必须维护多个函数！

> 我们如何实现可重用性和简单性？

问题来了。模板化的版本很灵活，但并不简单。函数变量很简单，但不灵活。我们如何实现可重用性和简单性？

## 我们为什么要在它们之间做出选择呢？

这个问题是由算法控制序列的*迭代和序列*的*表示引起的。这些问题应该分开。*

那么，我们需要什么样的抽象才能两全其美呢？

# 经由协程 TS 的控制反转

协程 TS 使您能够定义惰性序列。惰性序列只是一组有序的值，其中每个值只在被请求时才被计算。

您可能会认为惰性序列是一个具有两个属性的容器:

*   `bool hasNext()`这个序列里还有别的值吗？
*   `T takeNext()`返回序列中的下一个值，并前进 1。

因此，我们能够分离计算和表示，同时保持对序列的完全控制。

协程 TS 是相当低级的，所以下面的例子是使用我们的[管道](https://github.com/loopperfect/conduit)库构建的。这个库为我们提供了各种原语，我们可以用它们来创建惰性序列。我们不会在这里深入讨论实现细节(GitHub 上有完整的[源代码)，而是看看所有这些是如何有用的！](https://github.com/LoopPerfect/conduit)

好的。因此，使用[管道](https://github.com/loopperfect/conduit)我们可以为斐波那契定义一个惰性序列:

由于黄金比例是斐波那契数的一种变换，我们可以使用`scan`来计算它们:

这里我们使用该序列打印前 10 个斐波那契比率:

现在我们有了一个 iterable，我们可以像以前一样通过计算一个 delta 来控制迭代:

它的美妙之处在于，我们可以只取序列的一个定义，但在多个地方使用它。与我们之前看到的模板化解决方案不同，代码仍然紧密地映射到数学定义。

如果你想知道如何使用协程 TS，
实现懒惰序列，请查看我们的 [GitHub](https://github.com/loopperfect/conduit) 。

# 结论

协程 TS 允许我们将控制权交还给算法的消费者。这使得实现者不必向最终用户提供定制挂钩来修改算法的行为。

# 既然你在这里…

我们最近发布了 [BuildInfer](https://buildinfer.loopperfect.com/) ，这是一款优化 C/C++构建脚本的新工具。[看一看](https://buildinfer.loopperfect.com/)！

![](img/5b29e7d89e98091a3ce03d7088438ce9.png)

# 您可能也会对…感兴趣

[](https://hackernoon.com/error-handling-in-c-or-why-you-should-use-eithers-in-favor-of-exceptions-and-error-codes-f0640912eb45) [## C++中的错误处理或:为什么你应该使用这两者之一来支持异常和错误代码

### TL；速度三角形定位法(dead reckoning)

hackernoon.com](https://hackernoon.com/error-handling-in-c-or-why-you-should-use-eithers-in-favor-of-exceptions-and-error-codes-f0640912eb45) [](https://hackernoon.com/value-ptr-the-missing-c-smart-pointer-1f515664153e) [## value_ptr —丢失的 C++智能指针

### TL；速度三角形定位法(dead reckoning)

hackernoon.com](https://hackernoon.com/value-ptr-the-missing-c-smart-pointer-1f515664153e)