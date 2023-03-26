# Javascript 闭包

> 原文：<https://medium.com/hackernoon/picklejs-closures-in-javascript-1cd35576f060>

## PickleJS 系列的一部分

最初发布于 1.26.18，作为 JavaScript 月的一部分。原帖可以在[这里](https://www.javascriptjanuary.com/blog/picklejs-closures)找到

![](img/4800c773b0dc6eb14adf6377c9e90d63.png)

# **简介:**

欢迎来到(希望)将会成为围绕 [JavaScript](https://hackernoon.com/tagged/javascript) 中的一些基本概念的系列博文。这个系列的灵感来自于我在 T21 图灵软件设计学院上学的时候。当我第一次开始[编程](https://hackernoon.com/tagged/programming)时，我发现使用像`Foo, Bar, Baz, Bash`这样的变量和函数名的代码示例非常令人困惑。

例如，来自凯尔·辛普森关于范围的一次演讲:

```
var foo = “bar”;function bar() {
 var foo = “baz”;
}function baz(foo) {
 foo = “bam”;
 bam = “yay”;
}
```

对于许多可能已经从事编程工作一段时间的人来说，这是简单易懂的。对于初学者来说，这可能非常令人困惑。这是一种经常使用的模式，当我第一次学习 JavaScript 时，我以为`foo, bar, bam, baz, bash`是像`return`和`function`一样的特殊关键字。

当查看代码示例时，读者应该能够确定示例的哪些部分是必要的，哪些是不必要的，如变量名、函数名、参数等。在这篇文章中，我们将使用[三明治](https://en.wikipedia.org/wiki/Sandwich)和制作它们的人，作为谈论 JavaScript 的`closure`的例子。长得离谱的变量和函数名应该很容易区分一个例子的哪些部分是 JavaScript 语言和语法的一部分；以及哪些部分由您作为开发人员来命名。

## **在我们开始之前**

我们假设你已经写了一些 JavaScript，你理解了[函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions)和[函数声明](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function)。为了理解闭包的威力，熟悉[作用域](https://en.wikipedia.org/wiki/Scope_(computer_science))和词法作用域是必要的，所以如果你不熟悉这些概念，花一点时间阅读它们并回来。

## **什么是闭包？**

这是一个在技术面试中经常被问到的问题。闭包并不是 JavaScript 独有的，但它是 JavaScript 如此强大的原因之一。**闭包**是一个计算机科学术语，定义了一个函数如何维护它被调用的环境的记录。这意味着一个函数可以跟踪它最初被调用时的参数和变量，即使它是在那个范围之外被调用的。换句话说， [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)将闭包定义为“函数和声明该函数的词法环境的组合。”如果这不太有意义，不要担心，我们会在这篇文章结束时解决所有问题！

## **你可能已经在哪里使用闭包了**

所有的 JavaScript 函数都是闭包。函数可以访问函数中定义的变量，以及任何封闭作用域(如全局作用域)中定义的变量。出现闭包的其他常见地方是`setTimeout`、事件/点击处理程序和对象。

## 为什么要利用闭包？

如上所述，在定义点击处理程序、编写回调函数或者使用`setTimeout`或`setInterval`时，你会用到闭包。

一个`setTimeout`函数可能看起来像这样:

在我们声明了我们的`greetTheCustomer`函数的地方，我们创建了一个新的范围。如果没有闭包，函数就不能访问`customerName`或`deliEmployeeName`变量，我们必须显式地将这些变量传递给`greetTheCustomer`。在这个例子中，我们已经在[全局作用域](https://developer.mozilla.org/en-US/docs/Glossary/Global_scope)上声明了我们的变量，但是如果这些变量都是在一个函数中定义的，或者是作为一个对象上的一个方法定义的，那么这个概念是相同的。

在对象内部定义私有变量时，闭包非常有用:

在本例中，我们通过将`bread`和`topping`定义为`Sandwich`对象的属性，公开了它们。`typicalNumberOfPickles`变量在三明治对象之外不可用，为了在`Sandwich`对象的范围之外访问它，您可以使用该变量编写一个方法，该方法将作为一个闭包。

在这里，我们展示了一种公共方法`howManyPicklesAreOnMySandwich`来为客户提供关键的一点信息。即使我们不能直接访问全局范围内的`typicalNumberOfPickles`变量，由于闭包的存在，我们可以通过我们的新方法获得这些信息。

用一个方法代替一个对象的常用模式是通过创建一个返回内部函数的函数来利用闭包。该内部函数将可以访问外部函数中定义的任何变量，而不会将这些变量暴露在更大的范围内。

在这里，我们的`morePicklesPlease`函数维护对原始`typicalNumberOfPickles`变量的访问，并将继续更新它。需要注意的是，每次您存储返回的函数供以后使用时，它都会形成自己的闭包。原始变量不是在每个函数之间共享的，每个函数都有自己的原始环境副本。

在下面的例子中，我们将使用相同的`askForMorePickles`功能制作 2 个不同的三明治，每个三明治都有自己的泡菜数量。

这让消费者可以根据完美的泡菜数量定制三明治体验！

## 使用闭包的缺点

因为闭包的每个版本都保留了原始环境的副本，所以直到使用它的所有闭包都被[垃圾收集](https://developer.mozilla.org/en-US/docs/Glossary/Garbage_collection)后，该环境才会从[调用栈](https://developer.mozilla.org/en-US/docs/Glossary/Call_stack)中移除。因此，举例来说，如果你有 100 个不同的函数引用这个原始变量，其中 99 个被垃圾回收，这个变量仍然存在，直到第 100 个函数被回收。这会耗尽本地内存，最终导致[内存泄漏](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management)。

## 总结，或者，你如何在采访中谈论结束

闭包是许多编程语言的基础部分，不仅仅是 JavaScript。闭包指的是一个函数，它保持对调用该函数时定义的自由变量的访问。闭包的一些用例是事件处理程序、超时、间隔、回调和在函数中保持变量私有。使用闭包的一个缺点是，它们会导致内存的过度消耗，如果处理不当，还可能导致内存泄漏。

希望这有助于你解开闭包的谜团。如果你想了解更多，我强烈推荐这本 [**你不知道的 Javascript:Scope&Closure**](https://github.com/getify/You-Dont-Know-JS/tree/master/scope%20%26%20closures)书。

如果你喜欢这篇文章，并希望看到未来的帖子，请在这里或 Twitter 上告诉我:[https://twitter.com/Jeff_Duke_io](https://twitter.com/Jeff_Duke_io)