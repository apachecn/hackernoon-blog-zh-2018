# Git 之后我们去哪里？

> 原文：<https://medium.com/hackernoon/where-do-we-go-after-git-d4d40d8303ce>

![](img/cdfb0304b3e6f885951a68577e011d12.png)

## 让我们开始这个派对吧

# 序文

Git 重新定义了我们编写软件的方式。这是一项巧妙的技术，它的社区和生态系统充满活力。它变得如此无处不在，以至于很难想象会有什么东西跟随它。但这种想法现在听起来应该很可疑:人们对马和马车、煤气灯和固定电话说了同样的话。从根本上说，人类是一个不停创新的物种，而技术不会坐以待毙。因此，尽管 Git 可能仍然让人感觉新鲜——也许是因为它周围的一切都欣欣向荣——但值得记住的是，第一个版本是在第一代 iPhone 之前两年*发布的。至少可以说，自那时以来，情况发生了很大变化。*

需要澄清的是，这不是一篇关于 Git 即将灭亡的文章。我喜欢 Git，我不认为它已经达到了极限，也不认为它会很快消失。在可预见的未来，它可能会继续增长，而且很可能永远不会完全消失。或许有一天，经过一段时间的使用，Git 甚至会复兴，让人想起黑胶唱片在[的回归。但是 Git 之后会出现一些东西，似乎是时候开始猜测那可能是什么了。](http://www.independent.co.uk/news/long_reads/vinyl-demand-lps-record-store-day-a7952911.html)

让我们先回到过去一会儿。Git 对分布式工作流的支持反映了正在进行的计算的分散化。权力不再仅仅掌握在中央服务器上，客户也不再扮演从属的角色。端点可以尽自己的力量，结果是一个新的移动，新的自由世界。这是真正的解放！

然而，新的问题出现了:最令人痛苦的是，不同设备之间的数据不同步，或者完全不可用。如果很难记起那是什么样子，那就想想 Git 发布的同一年[微软收购了 FolderShare](https://web.archive.org/web/20070705184347/https://www.foldershare.com/info/company/aboutUs.php) ，从而进入了*“文件同步和远程访问技术的新兴领域，帮助客户跨多种设备访问信息”*。这比谷歌文档早了一年，比 Dropbox 早了两年。这就是 Git 诞生的世界。

不用说，现在已经不是我们生活的世界了。技术领域不断变化，尤其是所有这些计算设备之间的连接方式发生了巨大变化。随着数字通信变得更快、更丰富、更可靠，不仅每个设备都可以处理自己的位处理，而且所有设备都可以实时有效地协同工作。一台计算机上的信息不同于另一台计算机上的信息的观念开始缓慢而不可避免地瓦解。这给了我们第一个提示，Git 之后的版本控制在哪里。

# 预言

由于 Git 的发展反映了十年前计算领域的发展，我们可能会怀疑它的继任者将反映我们今天正在经历的变革。因此，我们可能会看到其中一些:

## 默认情况下已连接

Git 使得将机器上的分支与远程设备上的分支关联起来变得很简单。但是默认情况下，这些关联只是指针，而不是连接。除非您手动获取更新，或者使用 Git 客户端定期为您获取更新，否则您机器上的数据会过时——即使所有设备都联网。对于任何现代技术用户来说，这应该感觉非常过时。如果有人和我做同样的事情，我希望不用问就能被告知。

## 总是在动

Git 的轻量级分支模型使得快速退出并尝试一些改变变得容易，而不用担心真的会把事情弄糟。您可以在您自己的分支中提交您的更改，确保它们不会丢失，并且只有当事情顺利时才将其合并回来。但是仍然有很多丢失工作的机会:如果停电而没有保存更改，或者如果您决定需要返回到提交之间的状态，您可能就不走运了。如今，我相信软件永远不会丢失我的工作，无论我是否或何时想保存。我的工作总是在进行中，我希望我的软件能跟上。

## 社交场合

Git 的提交模型让我知道谁做了什么，什么时候做的——但只是在回顾的时候。当我们在各自的机器上工作时，没有人知道对方在做什么，即使我们在同一个文件中工作。现在，如果我和其他人同时编辑一个文件，我希望我的编辑能告诉我这一点，并准确地告诉我其他人在文件中的位置。GitHub 的巨大成功证明了在源代码控制之上分层社交工作流的好处，但现在是我们开始烘焙社交的时候了。

# 聚会

这些方面描述了与 Git 相当不同的东西。不仅仅是一个源代码控制管理系统，一些连接的、运动的和社交的东西听起来更像是…一个聚会。所以让我们开始派对吧。下面是对软件源代码控制的未来的一个假设。

```
$ party start myhouse @friend1 @friend2
```

为了让事情进展顺利，我们邀请了一个叫做 T2 的人，并邀请了我的两个朋友。这在我的机器和默认的中央服务器上建立了一个 Git 存储库，并向`friend1`和`friend2`发送通知。

```
$ party talk app.js
```

我们开始`talk`互相合作，研究一些核心逻辑。当我们编辑时，我们都实时看到彼此的击键和插入点。这通常是我们在 Git 中保存、提交、拉取和推送的地方——但是这里什么都没有发生。大家一起狂欢。

```
$ party punchline "Basic logic works"
```

最终我们到达一个稳定点。尽管我们到目前为止采取的每一个行动都被自动记住了，但时间线中的一些点肯定比其他点更值得记住。为此，我们调用一个`punchline`，它表示感兴趣的时间点。这些是方便将来使用的引用，类似于 Git 中的标记。

```
$ party clique refactoring @friend1
```

最终，我和朋友 1 决定我们需要对逻辑做一些冒险的改变，我们不希望打断朋友 2 的工作。我创建了一个名为`refactoring`的`clique`，并邀请了 friend1。尽管 friend2 可以实时查看重构团体中发生的一切，但只有 friend1 会被邀请进行编辑。因为集团用于隔离工作集，所以它们类似于 Git 中的主题分支。

```
$ party listen center
```

Friend2 还在党的`center`工作，相当于 Git 中的 master 分支。默认情况下，当我创建重构团体时，我和朋友 1 不再看到朋友 2 正在做的任何工作，这让我们专注于自己的工作。但是在进行了最重要的更改之后，我们希望再次跟上 friend2 正在做的事情，这样我们就可以解决任何冲突性的更改。我们进入了聚会的中心，并且立刻被朋友 2 的工作吸引住了。这类似于获取远程提交并在 Git 中本地合并它们。

```
$ party join center
```

一旦我们解决了一些合并冲突，并使一切处于良好状态，我们再次成为聚会的中心，此时朋友 2 会看到我们的工作。这相当于将工作推回到 Git 中的远程主机，只不过 friend2 不会手动获取这些更改。

```
$ party rewind 2min
```

在这一点上，朋友 2 完成了一些事情，然后浏览了我们的工作。我们所做的大部分都很好，但结果是我们删除了他们计划使用的一些东西。因此，friend2 决定提前几分钟参加聚会，以找到我们删除的代码。

```
$ party bring app.js:112–114
```

他们确定他们想要第 112 行到第 114 行，然后他们将这些行返回到聚会中的当前点。

```
$ party punchline "Full logic implemented"
```

解决了这个问题后，我们接着标记另一个笑点，表明我们已经达到的状态。

```
$ party replay start
```

在结束之前，我们从头开始我们的工作——当然，速度加快了不少。因为聚会存储了我们采取的每一个行动，它能够回放我们开始以来的活动。

```
$ party end
```

一切看起来都很好，所以我们`end`的编辑会议到此为止。

# 附言

Git 的继任者可能会出现，也可能不会很快出现。它可能看起来像我这里描述的一样，也可能不一样。它最终可能会也可能不会像 Git 那样席卷全球。但是为什么要等到发现呢？必须有人让它发生——会是你吗？如果是的话，请务必邀请我参加聚会。