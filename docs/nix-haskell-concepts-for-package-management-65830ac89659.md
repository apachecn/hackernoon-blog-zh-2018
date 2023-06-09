# Nix:包管理的 Haskell 概念

> 原文：<https://medium.com/hackernoon/nix-haskell-concepts-for-package-management-65830ac89659>

![](img/81ebd273a6d5fb6745f879c513edcdda.png)

回到我的 BayHac 文章中，我讨论了我和 T2 尼克斯和 T4 尼克斯的一些冒险经历。我没做多少事。但我仍然很好奇，想了解更多关于这些系统的知识。我在以前的工作中“使用”了一点 Nix。我说的“用过”是指我已经学会了足够的基本命令来编写代码，继续我的生活。但我从未对“为什么选择 Nix”或“Nix 有什么好处”有充分的理解。所以我要花几周时间对这个项目做一个高层次的概述，以及它为什么这么酷。

作为介绍，Nix 是一个纯粹的功能包管理器。它的目标是成为一个语言无关的系统，以实现确定性的构建。我们将在下面讨论成为一个“纯粹功能性的”包管理器意味着什么。但是使 Nix 成为现在这个样子的许多属性在 Haskell 中也存在。因此，虽然您可以将 Nix 用于任何语言，但是到目前为止，大多数开发工作都来自 Haskellers。同时， [NixOS](https://nixos.org/) 是一个 linux 发行版，它试图在操作系统级别应用 Nix 的主要原则。

第一篇文章将讨论 Nix 的基础知识，它的优点和缺点。下周，我们将看看 HNix 项目，它试图在 Haskell 中实现 Nix。尽管 Nix 绝对不是 Haskell 最容易使用的包管理器，但理解这一点很重要。目前，我仍然推荐从[栈](https://docs.haskellstack.org/en/stable/README/)开始。您可以阅读文档或查看我们的免费[堆栈迷你课程](http://academy.mondaymorninghaskell.com/p/your-first-haskell-project)以了解更多信息！如果你以前从未使用过 Haskell，下载我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)开始吧！

现在，为了激励 Nix 的使用，让我们考虑一些关于包管理的更广泛的问题。

# 包装问题

在最基本的层面上，一个包管理器应该能让你用少量的命令(~3)就能让一个程序启动并运行。大多数人都完成了这项任务，但总有一些复杂的问题。我们将关注两个主要问题。一个是版本控制。这包括版本化您自己的项目和版本化依赖项。另一个问题与应用程序的可移植性有关。

当 Cabal 还年轻的时候，版本问题就一直困扰着 Haskell 开发者。默认情况下，Cabal 会在系统范围内安装依赖项。但是假设您的机器上有许多项目。这些可能依赖于同一个库的不同版本。这可能会导致系统中的冲突，从而导致多个项目不可用。

Cabal 沙盒和 Stack 程序的加入缓解了这个问题。这两个系统都在项目特定的位置安装依赖项。但是仍然存在一个问题，即很难回滚到项目的前一个版本。卸载和降级软件包的命令并不直观。如果你不小心，他们很容易打碎东西。

同时，看不见的依赖威胁着我们的便携性。这在构建 C 或 C++程序时比 Haskell 程序更常见。c 库通常仍然安装在系统范围内。结果之一是，您的系统上可能有来自另一个项目的库。然后一个新的项目也依赖于它，但是你忘记列出这种依赖。它在您的本地机器上运行良好。但是当你把你的代码放到其他地方时，这种依赖就找不到了。这可能会很麻烦。

# Nix 功能方法

Nix(包管理器)试图通过使用功能性的包管理方法来避免这些问题。它将每个包视为一个由函数构造的值。任何包的函数的关键输入是它的依赖图。也就是说，一个包是最终的输出，而其他(版本化的)库是输入。您构建的包的每个**版本**都有一个唯一的标识符。该标识符是依赖图的加密散列。因此，如果程序的任何依赖关系发生变化，您将重新构建并创建一个全新版本的包。这意味着添加依赖项、删除依赖项或更改版本。

Nix 将其所有包存储在`/nix/store`目录中。因此，您可能会构建项目的一个版本，该版本位于以下目录中:

```
/nix/store/2gk7rk2sxx2dkmsjr59gignrfdmya8f6s-my-project-1.0.1
```

然后，您可能会更改依赖关系，并最终使用另一个目录。

```
/nix/store/lg5mkbclaphyayzsxnjlxcko3kll5nbaie-my-project-1.0.2
```

这样做的后果是什么？

请注意，对我们的项目进行版本控制非常容易！如果我们决定回滚到以前的依赖集，那个版本将仍然存在于我们的机器上！我们将更新依赖集。然后它计算依赖图的散列，这将匹配一个旧的配置。所以我们都准备好了！这也适用于我们的任何依赖关系。

事实上，存在与回滚相关的特定命令。这意味着你可以升级软件包，而不用担心任何困难。

Nix 也解决了我们上面提到的第二个问题。首先，我们显式地将所有依赖项声明为输入。第二，我们只使用从 Nix 商店获得的依赖项，而不是任何系统范围的位置。这意味着我们的推导是完整的。因此，其他人应该能够接受这个定义并自己构建它。

# Nix 操作系统

NixOS 试图从 Nix 包管理器中吸取许多经验教训，并将它们应用于操作系统级别。困扰软件包管理的许多问题也困扰着操作系统管理。例如，用`sudo apt-get install`升级软件包可能是一个有风险的操作。回滚可能很困难，而且在升级之前测试将要发生的事情几乎是不可能的。NixOS 修复了这些。它允许您拥有版本化的、可复制的系统配置。您可以轻松地回滚到某个配置。它还为您提供了关于系统修改的原子事务。这样，即使出现问题，您也可以完全恢复到原来的系统状态。

# Nix 的缺点

Nix 的一个潜在弱点是它默认从源代码构建。这意味着你经常会有**长的构建时间**，即使是对你的代码或者依赖关系的小的改变。如果幸运的话，您可以将 Nix 缓存用于您的特定库。它存储了您可以使用的预构建的二进制文件。但是根据我使用 Nix 的经验，构建时间的长度是阻碍它的最大因素之一。特别是**很难**将 Nix 整合到 CI 系统中，因为它容易导致超时。

# 结论

所以希望这能给你一些关于 Nix 的概念。下周，我们将研究 HNix。这个开源项目正在寻求在 Haskell 中重新实现 Nix。我们将在对项目的探索中了解原因。同时，查看我们关于 Haskell 入门的一些资源，这样你就可以学习如何开始了！如果您想体验一下 Haskell 中的包管理，一定要试试 Stack！查看我们免费的 [Stack 迷你课程](http://academy.mondaymorninghaskell.com/p/your-first-haskell-project)来了解如何操作！