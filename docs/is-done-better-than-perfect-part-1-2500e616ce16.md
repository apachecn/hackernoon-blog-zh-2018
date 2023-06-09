# 做得比完美更好吗？—第一部分

> 原文：<https://medium.com/hackernoon/is-done-better-than-perfect-part-1-2500e616ce16>

![](img/6c10d3cbfa99828aac707cf4a6aae75d.png)

[Source](https://lifehacker.com/5870379/done-is-better-than-perfect)

## 在实现复杂的给定算法时，维护干净的代码和良好的架构是一项挑战

# 背景

在我理学学士学位的最后一个学期。在电气工程中，我们被分配了实施 [WFQ-GPS](https://en.wikipedia.org/wiki/Weighted_fair_queueing) 路由算法的项目，作为计算机网络课程的一部分。除了将它实现为一种在线调度方法(这对输入包的处理有影响)之外，我们没有任何其他限制。效率是加分项。

我认为这个项目是一个很好的机会，可以让我的软件工程技能运转起来，并以一种简洁高效的方式编写这个算法。不幸的是，我的愿望并不符合现实，由于截止日期，我和我的合作伙伴妥协了，最终交出了一个“完成”而不是完美的项目。

当学期结束时，我决定实现我的目标，以正确的方式完成这个项目。

# 决定

*   我需要学习 C++。最初的项目是用 C 编写的，但是我知道 C++包含了更多有用的数据结构(例如 STL 库)，并且在内存分配和实现设计模式方面更加灵活。
*   我是一名 C 开发人员，我已经知道 C++的主要概念。那么，成为 C++-er 的最好方法是什么呢？有些指南概述了这两种语言之间的区别。然而，我发现最有用的是咨询我的一位同事，他是一名高级 C++开发人员。他为我提供了关于这种语言及其最佳实践的更广阔的视角。他还同意在这个项目中全程指导我，这是无价的。

# 入门指南

## 提高我的绘画技巧

我从绘制代码的架构开始。我从之前的几个项目中学到的最好的经验之一是，这一步是至关重要且非常有帮助的。它有很多优点，值得强调一下:

*   它帮助你更好地理解你想要实现的功能，因为设计首先应该服务于算法的正确性。
*   它是你的代码的地图——这是你决定你的对象、它们之间的相关性、共享资源和数据结构的地方。**写作**(不是在你脑子里想象的文件里)这一切都提前记下来，迫使你在实际写作之前思考以上所有的问题。我通常使用 draw.io 或简单的纸笔(希望获得更多好工具的推荐)。
*   当事情变得不顺利时(当您开始编写和/或有到期日时，事情确实变得不顺利),或者在调试过程中，这是一个安全的区域。它确切地提醒你你打算做什么。当然，在这个过程中它会经历修改，但是它就像你的代码的指南针。
*   对于与同行的合作来说，这是非常有价值的。

## 测试。一切。

根据我过去的经验，再加上我是 C++的新手，我几乎测试了所有的东西。这是我第一次以这种方式工作，它证明了自己。

一个很好的例子是，我知道当在函数内部创建对象时，我必须使用“*new”*(这使得它们被分配在堆中，而不是函数的堆栈中)，以防我希望在函数外部使用它们(当然，之后会释放这些内存)。那是在我将对象的创建从 main 导出到一个单独的函数之后，尽管它“只是”取出相同的行并从中创建一个函数——我测试了它并发现了错误。多亏了我的导师，我才能够学会这种做法。

> (在我的下一篇文章中，我将详细说明在这样的项目中调试的时间、方式和内容)

## 一步一步来

一旦我有了在效率、架构和对 C++特性的充分利用方面令我满意的坚实的代码框架，我就从一个基本的例子开始。这类似于算法的工作方式。然后，我开始了我的第一次“真正的”投入。

基本示例在第一次尝试时几乎完美无缺。我的想法是:

> 我做的一切都是正确的，我的代码很棒，现在一切都应该工作了。

不是。我的意思是，它确实给了我一个很好的观点，但第一次真正的输入只起了部分作用。我非常希望它能成功。这几乎让我完全放弃了我画的架构，并在没有新功能和特性文档的情况下对代码进行微调试以了解它的错误。**不好。**

感谢 Git，以及我提醒自己这个项目的目标，没过多久，我就回到了我的架构“指南针”上，试图找出缺少的东西。

目前，3 个输入中有 2 个工作正常，这非常令人满意。这些天来，我一直在努力理解为什么第三个不是。

得回去画我的画了。第二部再见。

顺便说一句，你可以在这里找到我的代码，所以请随意提出你的建议，帮助我成为一名更好的软件工程师。