# Kotlin 中的模拟静态方法

> 原文：<https://medium.com/hackernoon/mocking-static-method-in-kotlin-614df89482ae>

![](img/018bae2cb4c89245238826dafb0f8bc9.png)

Photo by [Susan Yin](https://unsplash.com/photos/ym3QFTHYUE4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/mock?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

静态方法不好，或者至少，当它不是**纯**的时候。

在这种情况下，纯粹意味着:

*   它不会产生副作用，不会在方法范围之外产生任何变化。
*   它是引用透明的，意味着任何引用都必须作为参数传递，否则引用值的变化将导致不确定的结果。

这有什么不好？

因为谁消费了那个不纯的静态方法，谁就不容易测试。如果静态方法是纯的，它可能是好的，因为你可以预测它的行为，你不需要嘲笑他们(少嘲笑是伟大的！)，只需要断言返回值就够了。但是不纯洁的，那就不一样了。

如果代码在你的控制之下，你可以也应该避免不纯的静态方法。但是如果你不得不使用一个库或者框架中静态方法，而且你不能避免它们，你能做什么呢？

例如，我们想使用 [Twillo](https://www.twilio.com/) 发送短信通知。

Twillo SDK 提供了静态方法`Message.creator`，这是一个构造`MessageCreator`的工厂方法。实际向 Twillo 请求发送短信的方法是`messageCreator.create(restClient)`。

虽然`Message.creator`是纯粹的，但是`messageCreator.create(restClient)`也就是`Message.creator`的结果却不是。所以我们需要通过首先清除`Message.creator`的返回值来模拟`create`方法。但是由于`Message.creator`是静态的，我们没有办法进去。

有人可能会说:我们可以围绕这个静态方法创建一个包装器类，这样我们就可以模仿这个包装器。

但是测试的目的是增加你对代码的信心。仅仅为了测试而增加另一层间接性并不会增加被测系统的可信度，反而会增加代码库的复杂性。

因此，如果添加包装器有助于您改善团队使用 API 的编码体验，并且它被大量使用，那么它可能值得投资，而不仅仅是为了测试。

那么，有没有更精益的方法呢？

## 高阶函数 FTW

我们想以某种方式创建一条路径，用别的东西来代替`Message.creator`。在科特林和其他许多语言中，函数被视为一等公民。最小的可重用组件不再是类，而是函数。所以你可以像其他物体一样传递函数。

在严格的函数和数学意义上，函数必须是纯函数。但是在科特林的上下文中，函数似乎是允许不纯的。

Kotlin 中方法和函数的区别在于，方法与对象相关联，而函数不需要。你可以看到,`fun`关键字到处引用函数，不管是方法声明还是顶级函数声明，这意味着函数是方法的超集，至少在这个上下文中是这样。

但是什么是高阶函数呢？

高阶函数基本上是指以函数为参数或返回函数的函数。

即使没有函数作为参数且不返回函数的函数被称为一阶函数，这里使用的术语高阶函数而不是二阶或三阶，因为理论上，我们可以将“具有函数作为参数或返回函数的函数”嵌套无限多个级别。所以它高于二阶或三阶。

让我们回到我们的主题。所以我们想把`Message.creator`注入到`notify`中，我们可以通过让`notify`成为一个高阶函数来实现，这样我们就可以把`Message.creator`作为一个参数传入。

为了做到这一点，第一步，我们需要得到正确的类型。

让我们弄清楚这个特殊函数的类型。下面是最初的`creator`在 SDK 中的实现:

所以它有一个类型

```
(PhoneNumber, String, String) -> MessageCreator
```

这意味着参数类型在箭头“`->`”的左边，返回类型在右边。

现在让我们定义我们的`typealias`来引用这个函数类型，并为了可读性而命名它。

但是现在，我们只是改变了我们的接口，这不是很好，因为通常我们不需要传递`creator`到`notify`，除非是在测试中。

在 Kotlin 中，我们可以通过提供默认参数来避免这种情况。

所以现在，我们现有的界面保持不变，我们有自己的方式！

## 来嘲讽一下吧！

我在这里使用了 [Spek](https://spekframework.org/) 和 [mockito-kotlin](https://github.com/nhaarman/mockito-kotlin) 。

所以我们定义了一个返回模拟的`MessageCreator`的`creator`，这个 stub `create`方法返回我们想要的`Message`。

你可能会问，在第 22 行:

*   为什么不直接用`Message`构造函数呢？= >是私人的。
*   为什么我们不嘲笑 getters？= >就这么定了。

所以我们最终使用`fromJson`来代替。

***编辑 2018 年 7 月 14 日:*** [*大卫基什内尔*](https://medium.com/u/417d954d6bf5?source=post_page-----614df89482ae--------------------------------) *指出 mockito 2 已经可以嘲讽* `*final*` *类和方法了。*见全反应[见*此处*见](/@dpkirchner/mockito-now-supports-mocking-final-classes-natively-although-you-have-to-opt-in-and-be-aware-of-37fa0545d20e)

而我用`::creator`引用`creator`函数，它会当作一个值。另一种方法，我们可以使用 lambda 并将其分配给`val`而不是`fun`。

通过使用 lambda，我们可以忽略使用`_`的参数，因为我们不在这个上下文中使用它。如果我们用`fun`来定义它，这是不可能的。

关于这些函数和 lambdas 如何工作的更多信息，你可以在 Kotlin 文档中找到。

现在，我们可能想要验证是否用正确的参数调用了`creator`。问题是 mockito 的`verify`只支持方法调用验证。我们如何验证`creator`没有关联到任何对象？

但结果是，function 实际上只是一个带有`invoke`方法的类，所以我们可以模仿 function 类型，而不是自己创建 function。这样，mockito 可以验证该函数的`invoke`方法是否被正确的参数调用。这是函数在标准库中的定义方式。

现在我们知道，我们可以模拟我们的函数来验证调用，我们也可以存根返回值。让我们双管齐下！

正如您所看到的，我们可以验证我们的函数调用，并在不破坏语言的情况下清除静态方法的返回值。

## 摘要

我们正在利用语言的高阶函数和默认参数特性，允许我们将静态方法注入到我们想要测试的函数中，这样我们就可以模仿/存根它，同时保留旧的接口。任何具有这两种特性的语言也可以使用相同的技巧。

我希望你已经明白了。

感谢阅读！享受测试:D

在 twitter 上关注我 [@ibossptk](https://twitter.com/ibossptk)