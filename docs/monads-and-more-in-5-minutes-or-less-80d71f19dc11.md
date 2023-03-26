# 单子和更多在 5 分钟或更少

> 原文：<https://medium.com/hackernoon/monads-and-more-in-5-minutes-or-less-80d71f19dc11>

## 简单的英语，而不是疯狂的行话

![](img/066916c2ebc00fc78f66eef6eb1be6bb.png)

Haskell 中单子的必要性维护了语言的纯粹功能，但也考验了程序员的耐心。如果你想使用 Haskell，理解单子是必不可少的。本教程简要介绍了`[Monad](http://hackage.haskell.org/package/base-4.11.1.0/docs/Control-Monad.html)` typeclass 的结构。虽然您需要熟悉 Haskell，特别是理解如何处理它的`Monad` typeclass，但是本教程中介绍的概念很容易转移到其他语言。我们还将涉及`[Applicative](http://hackage.haskell.org/package/base-4.11.1.0/docs/Control-Applicative.html)`和`[Functor](http://hackage.haskell.org/package/base-4.11.1.0/docs/Data-Functor.html)`类型的职业。让我们从枯燥的、技术性的定义开始。单子是一个特殊的类型类，主要由两个函数组成，`bind`和`return`。这很简单，对吧？现在，让我们分解一些术语，并找出为什么这两个函数如此强大，以及所有这些如何影响您的真实代码。

你可能有点困惑，什么是类型类？把 typeclass 想象成一个定义一组函数的接口。您可以为数据类型定义 typeclass 的实例。现在让我们看看这两个函数，`bind`和`return`。第一个函数`return`接受一个值，并对其应用一个数据构造函数。然后，第二个函数`bind`允许您在顺序计算中使用数据类型中的值。为什么要在代码中使用单子？简单的回答是这样你就可以做有用的工作，比如输入和输出的操作和管理以及异步编程。接下来让我们谈谈 Haskell 中另外两个重要的概念:`Applicative`和`Functor`。

作为`Functor` typeclass 实例的数据类型将实现`fmap`。此函数允许您对数据类型中的一个或多个值运行任意函数。`Applicative` typeclass 定义了一个中缀运算符`<*>`，它将一个数据类型中的值应用于另一个数据类型中的值。例如，对两个列表应用一个函数，因为每个列表都有一个`Functor`的实例。将这些类型类的实例添加到`Monad`的实例中，可以让您对数据类型做更多的事情。接下来让我们看看一些数据类型，它们是`Monad`的实例，以及它们在日常编程生活中的实际操作。

我们要研究的第一个数据类型是`[Maybe](http://hackage.haskell.org/package/base-4.11.1.0/docs/Data-Maybe.html)`。`Maybe`有一个`Monad`的实例。这种数据类型允许您表示一个值或缺少一个值。让我们用我们之前学过的两个函数来讨论这种类型。首先`return`在数据构造函数`Just`中包装一个值。`Just`的作用类似于一个函数，当我们试图`bind`将一个值赋给一个名字或以某种方式使用该值时，`Just`要么给出我们的值，要么`fail`返回`Nothing`。最后，让我们看看另一种数据类型，`[List](http://hackage.haskell.org/package/base-4.11.1.0/docs/Data-List.html)`。就像`Maybe`对于`List`也有一个`Monad`的实例。与上一个实现一样，这个`return`在数据构造函数`List`中包装了一个值。对列表进行操作的函数使用`bind`将列表中的所有值作为输入。这可以认为类似于`[]`的 for 循环。然后该函数将返回一个新的列表，它仍然是数据类型`List`的一个实例。

没有几个带有`Monad`实例的数据类型，你就写不出有效的 Haskell。最后，称某物为“单子”会让它听起来像一个特殊的实体。更确切地说，人们称之为单子的所有东西实际上只是一种数据类型，带有一些`Monad` typeclass 的实现。我希望这篇文章有助于加深您对这个过于复杂的主题的理解。

[**感谢阅读，我和我的同事做自由移动和全栈开发。为你的下一个项目记住我们！**](http://whiteboarddynamics.co/)