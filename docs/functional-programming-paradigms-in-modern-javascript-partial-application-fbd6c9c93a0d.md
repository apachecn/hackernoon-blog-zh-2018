# 现代 JavaScript 中的函数式编程范式:部分应用

> 原文：<https://medium.com/hackernoon/functional-programming-paradigms-in-modern-javascript-partial-application-fbd6c9c93a0d>

![](img/e9dae9409032e4fe9465e46f38328ab3.png)

如果你从一开始就一直在关注这个系列，那么我有信心说，通过读完这一章，我们将对函数式编程的**基础**有一个坚实的理解。

到目前为止，我们已经讨论了纯粹性和不变性的概念，并通过探索组合和 curry 深入到了函数的世界。在本文中，我们将研究**局部应用**，它是什么以及如何使用它。

# 什么是局部应用？

为了产生更多可重用的函数和创建更好的抽象，我们需要一种机制来预置函数的一些参数。

**部分应用**是一种用于将更高 **arity** 的函数(采用多个参数的函数)转换为采用更少参数的多个函数的技术。

部分应用的函数是不使用所有参数调用的函数。因为它还不能执行任何东西，它将返回给我们另一个函数，这个函数期待其余的。当我们用剩下的参数调用返回的函数时，它将运行。

通过这种技术，我们试图实现更好的可重用性和抽象性。通常，使用重复参数调用的函数可以部分应用，因此隐藏了函数的一些实现细节。

# curry vs 部分应用

currying 和 partial application 都是旨在实现相同目标的技术——将带有许多参数的函数转换成带有较少参数的函数。

尽管它们在这一点上很相似，但它们实现的方式是不同的。

一个 curried 函数将被分割成许多链式函数，所有这些函数都只接受**一个参数。这使得 curried 函数更容易预测——你总是知道会发生什么。**

另一方面，部分应用的功能没有这种限制。您可以使用任意数量的参数来应用它，这取决于返回的函数将具有不同的 **arity** 。我们只知道返回的函数将比原始函数接受更少的参数。

这两种技术代表了相同的一般概念，但我们应用它们的方式不同。这就是他们经常被混淆的原因。

# 部分适用于。`bind()`

进行局部应用的最简单方法是使用内置方法`Function.prototype.bind`。

`bind`函数最常见的用例是用预置的`this`值创建一个新函数。我们还可以传递任意数量的参数，这些参数将被预置在返回的函数中。

在这种情况下，我们没有为`this`对象传递任何值，但是我们传递了两个额外的参数，它们将被用作`x`和`y`。然后我们用最后一个参数`z`调用部分应用的函数。

公平地说，这不是我在与团队合作时用过的方法。不是每个人都意识到这种行为，使用一个叫做`partial`的函数或者类似的东西至少会更有描述性一点。

# **应用默认参数**

我发现部分应用特别有用的一种情况是当您想要应用默认参数时。

有时候，我们会一次又一次地用同一个参数调用一个函数。这可能是某个 API 或配置对象的 url。

通过使用部分应用程序，我们可以预先为函数提供重复的参数，并且只使用它来获取我们正在发送的有效载荷。

# **拆分功能**

有时我们需要编写带有许多参数的复杂函数。一般来说，我尽量将传递的参数数量保持在最多三到四个，但是在某些情况下，事情会逐步升级，最终你会得到一个包含七个参数的巨大函数。

像这样的函数很容易失控，每次你需要调用它的时候，它都会迫使你去查找它的定义。

像这样的问题的第一个解决方案是分解逻辑，这样你就可以使用更小的函数。然而，我们通常没有时间进行重构，或者很大一部分代码依赖于函数的 API，因为它是如此的变化，这是一个禁忌。

一个简单的解决方法是使用一个效用函数或`Function.prototype.bind`来部分应用这个函数，并在必要时使用返回的函数。

# **适应功能**

当我们需要在特定条件下“适应”给定的函数时，可以选择部分应用给定的函数并减少它所需要的参数数量也是很有用的。

一些函数接受带有预定义数量参数的回调。但是你拥有的函数需要两个或者三个，所以你不能在回调中直接传递。相反，你需要使用一个匿名函数。

这并不是说语法有问题，但是正如我们前面提到的，简化代码是我们的首要任务之一。

这种情况很适合部分应用的功能。换句话说，我们可以预先设置一些值，得到一个只需要最后一个参数的函数。然后我们可以将这个函数直接传递给回调函数。

# 参数顺序

我们不能部分地应用一个函数，除非我们首先正确地构造它。这里需要记住的是，我们将要使用的论点的顺序。

换句话说，如果参数在函数的末尾，我们就不能预定义它们。所以我们在构造函数时应该牢记这一点。

一些像 Ramda 这样的库遵循一个特定的惯例，其中每个函数都接收它最后操作的数据，所以它可以很容易地部分应用。

但是，有一些方法可以灵活调整参数的顺序。有时候你继承了一个代码库，不能随意修改现有的 API。

或者，您可能遇到了需要以不同顺序提供它们的边缘情况。

Ramda 有一个名为`partialRight`的实用函数，用于这种情况。

# **何时使用局部应用**

谈到函数式编程，我不是专家。写下这些文章对我的帮助不亚于对阅读它们的人的帮助。

然而，如果说我学到了什么的话，那就是实现这些技术不应该是强迫的。

对于根据具体情况使用什么，没有最终的清单。尤其是涉及到学习曲线如此陡峭的题目。

我真的不能说什么时候是使用部分应用的好时机，或者你是否应该首先使用它。这取决于你的团队、你正在做的项目以及最重要的代码可读性。

如果使用 currying 或部分应用程序会降低代码的可读性，请使用命令式方法。

我使用过这些技术，并且看到了它们在代码重用方面的益处。但我也见过当我决定做一些花哨的事情时，同事们困惑的表情。

这里的正确答案问题是你是否应该**练习**局部应用和奉承。而我在这里的回答是**是的**。

# 结论

函数式编程在很多方面更多的是忘记旧的概念，而不是学习新的概念。每次你尝试这些技巧时，你的大脑都会训练自己以不同的方式思考。

从许多方面来看，这将会令人困惑。你会不确定为什么有些东西在工作，或者为什么没有。但这是一件好事——困惑意味着你正在做一些新的、有挑战性的事情。

学习函数式编程会让你成为更好的 JavaScript 开发者吗？**也许是**。你会在日常生活中使用这些技巧吗？我们大多数人都不会。这会让你成为更好的软件开发人员吗？**绝对**！

如果你觉得这篇文章很有帮助，那么就把它分享给你的朋友或同事。另外，我有一份时事通讯，如果你想在我发表这些文章时得到通知，你可以 [**订阅**](https://buttondown.email/kondov) 。掌声和反馈非常感谢！

[](https://hackernoon.com/functional-programming-paradigms-in-modern-javascript-function-composition-109670038859) [## 现代 JavaScript 中的函数式编程范式:函数组合

### 在前两章中，我们介绍了函数式编程的一些基本范例——纯函数和…

hackernoon.com](https://hackernoon.com/functional-programming-paradigms-in-modern-javascript-function-composition-109670038859) [](https://hackernoon.com/functional-programming-paradigms-in-modern-javascript-immutability-4e9751ca005c) [## 现代 JavaScript 中的函数式编程范式:不变性

### 这是实用函数式编程范例系列文章的第二章。如果你没有读过…

hackernoon.com](https://hackernoon.com/functional-programming-paradigms-in-modern-javascript-immutability-4e9751ca005c) [](https://hackernoon.com/functional-programming-paradigms-in-modern-javascript-currying-5652e489cce8) [## 现代 JavaScript 中的函数式编程范例:Currying

### 我记得我决定扩展函数式编程技能的那一刻。这是一个寒冷的十月夜晚…

hackernoon.com](https://hackernoon.com/functional-programming-paradigms-in-modern-javascript-currying-5652e489cce8) [](https://hackernoon.com/functional-programming-paradigms-in-modern-javascript-pure-functions-797d9abbee1) [## 现代 JavaScript 中的函数式编程范例:纯函数

### JavaScript 是最流行的编程语言之一。它可以在浏览器、桌面、手机上运行…

hackernoon.com](https://hackernoon.com/functional-programming-paradigms-in-modern-javascript-pure-functions-797d9abbee1)