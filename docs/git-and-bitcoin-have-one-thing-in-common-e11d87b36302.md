# Git 和比特币有一个共同点🔊

> 原文：<https://medium.com/hackernoon/git-and-bitcoin-have-one-thing-in-common-e11d87b36302>

## 时间到了。

![](img/19c4f11f01c2a9863c3b97a1c69deaa6.png)

The picture of an eye with an ancient clock reflecting on it.

Listen to the audio version!

你有没有发现自己陷入一片混乱？

如果你在[编程](https://hackernoon.com/tagged/programming)方面有相当丰富的经验，并且已经使用 Git 有一段时间了，那么这个问题的答案最有可能是**是的**。

由于存在许多命令和复杂的规则，一个混乱的 Git 状态就像一个糟糕的应用程序一样难以理解。即使你对 Git 有更多的经验，事后再去恢复一个错误的 [rebase](https://git-scm.com/docs/git-rebase) 也不是一件容易的事，除非你知道最初造成混乱的所有步骤。

如果你不知道所有的步骤——比如说你在帮助别人——最简单的方法是回去重新开始。将状态重置为工作提交，并再次重新应用所有更改，最好是从`[git reflog](https://git-scm.com/docs/git-reflog)`中挑选每个提交。

> 为了事后修复一个 Git 烂摊子，您需要首先了解造成这种混乱状态的所有步骤。

Git 实现了事件源。

事件源与驱动[比特币](https://en.wikipedia.org/wiki/Bitcoin)的架构相同。你可以说 commit 在 Git 存储库上存储一个“已提交”事件，就像事务在[区块链](https://en.wikipedia.org/wiki/Blockchain)上存储一个“已确认的事务”一样。不同之处在于，没有人需要等待提交确认。

Git 就像时间。

每次你做出承诺，你都在分秒必争地向前迈进。

您可以通过[将](https://git-scm.com/docs/git-reset)状态重置为之前的时间点来回到过去。然而，如果你[改变过去并回到未来](https://git-scm.com/docs/git-rebase)，你就冒着改变未来的副作用的风险。

出于这个原因，如果您更改了过去，Git 将不得不创建一个新的**分叉**时间线，并修改自您更改以来所有提交的散列。Git 将要求您检查所有的向前更改，以确保它们仍然处于有效状态。这是一种避免“时间悖论”发生的方法。

Git 的事件源模型非常有用。它使系统**安全**并且**可调试**。

如果没有人能够不被注意地改变过去，这意味着你的代码将不会被恶意修改。如果你能回到过去，那意味着当问题出现时，你能第一时间调试代码。

> Git 就像时间。如果你改变了过去，未来会受到影响。

Git 允许高可用性。

它[为](https://hackernoon.com/affordance-in-software-design-12cc0d9d2721)提供了从多个`remote`存储库中[读取](https://git-scm.com/docs/git-fetch)的能力。这些存储库可以是项目的分布式副本，因此许多人可以通过网络进行协作。

*   `git fetch sam`。
*   `git fetch mary`。

如果一台机器与网络断开连接，不管是因为开发者[被总线](https://en.wikipedia.org/wiki/Bus_factor)撞了还是被 [410 撞了](https://www.kennethreitz.org/essays/the-reality-of-developer-burnout)，项目仍然可用，工作也将继续。

然而，大多数项目使用像 [Github](https://github.com/) 这样的集中式服务来存储代码。它们没有利用 Git 的高可用性功能。如果一个中央服务器拔掉插头，其他人都必须停止。

*   `git fetch origin`。

对于许多项目来说，默认的`origin`遥控器已经成为了 [Github](https://github.com/) 的同义词。

> Git 允许去中心化。然而，人们坚持集中使用它。

与[比特币](https://hackernoon.com/tagged/bitcoin)类似，Git 是一项精心制作的工程，旨在**去中心化**、**安全**和**可调试**。[事件源](https://martinfowler.com/eaaDev/EventSourcing.html)是核心架构，而 [Merkle 树](https://en.wikipedia.org/wiki/Merkle_tree)是数据结构。它使用了世界如何工作的许多基本原理，并将其转化为有效的[源代码控制系统](https://en.wikipedia.org/wiki/Source_Code_Control_System)。

过去，我写过关于[医生和手术刀](https://hackernoon.com/the-doctor-and-the-scalpel-78656f508c9a)的文章。这篇文章讲述了开发人员如何倾向于发现问题并责怪他们的工具。然而，大多数时候问题不在于工具，而在于缺乏对底层基础知识的了解。

Git 就是一个很好的例子。

如果你不了解基本原理，你将无法在**如何**使用其命令以及如何推理其复杂规则上做出好的决定。也许你可以记住什么是`git commit`，但是你将注定总是在第一次看到代码冲突时发现自己处于不可逆转的混乱中。

有时候，最好的长期策略不是学习如何解决问题和记住命令，而是有足够的知识**从一开始就正确地去做**，这样你就永远不会在一开始就陷入混乱。

如果你没有，至少互联网已经覆盖了你。

感谢阅读。如果您有一些反馈，请通过 [Twitter](https://twitter.com/FagnerBrack) 、[脸书](https://www.facebook.com/fagner.brack)或 [Github](http://github.com/FagnerMartinsBrack) 联系我。