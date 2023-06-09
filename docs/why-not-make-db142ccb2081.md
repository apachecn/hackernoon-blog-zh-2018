# 为什么不做？

> 原文：<https://medium.com/hackernoon/why-not-make-db142ccb2081>

![](img/5d83cb7c2961bbfd655fdb9430d2f991.png)

“blue building block lot” by [Iker Urteaga](https://unsplash.com/@iurte?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Make 是一个概念上简单的构建系统，它与主要的 Linux 发行版打包在一起。然而，在开始一个新的 C++项目时，Make 很少会成为您的构建系统的选择。

这是一个大胆的主张，所以这里是为什么…

## 编写一个大而正确的 Makefile 真的很难

C++库的依赖图实际上相当复杂。每次一个链接的目标文件改变时，它必须被重建…但是每次一个目标文件的翻译单元改变，或者编译标志改变，或者它包含的任何头文件改变，或者编译器改变时，它都应该被重建…

您可以使用编译器查询这些信息，并将所有这些信息编码到 Makefile 中，但是这会变得非常冗长。

很有可能你会错过一个依赖。如果发生这种情况，那么在一个改变之后，你可能会有陈旧的工件。提示一个干净的构建！

## 没有沙盒

缺少沙盒是根本问题。Make 有一个依赖图，但它实际上并不强制执行它。这意味着一个构建规则可以读取一个文件，即使它在 Make 中没有被声明为依赖项！

## 缓存是不可移植的

Make 不允许你跨网络共享构建工件，所以你的开发团队中的每个人都将一遍又一遍地执行相同的构建步骤！

是的，您可以使用 CCache，但这并不普遍(只有编译器调用被缓存)，而且它不是现成的。

## 没有提供语言抽象

Make 的简洁是一大优点，但也是一大弱点。Make 不提供任何特定于语言的抽象，所以必须自己编写。这可能会变得非常乏味，而且出错的几率很高。

这也适用于测试、部署等等。你需要自己创造一切。

## 时间戳，而不是哈希

在确定何时重建时，Make 使用构建输入的时间戳，而不是它们的散列。当文件已被触摸，但实际上并未更改时，这会导致许多不必要的重建。如果这发生在依赖关系树的深处，那么重建将花费很长时间！

# 那么什么时候应该使用 Make 呢？

也不全是坏事；Make 仍然是一个引导工具。如果您有极端的依赖性需求(例如，您正在从源代码构建 Linux)，那么您需要一些简单且自包含的东西。但是对于大多数开发者来说，情况并非如此。我们已经获得了 Clang、GCC 和 Visual Studio 作为预构建的二进制文件，那么为什么不下载一个预构建的构建系统呢？

# 既然你在这里…

我们最近发布了 [BuildInfer](https://buildinfer.loopperfect.com/) ，这是一款优化 C/C++构建脚本的新工具。[看一看](https://buildinfer.loopperfect.com/)！

![](img/5b29e7d89e98091a3ce03d7088438ce9.png)