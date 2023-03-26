# GX 先锋指南——IPFS 的分散依赖管理

> 原文：<https://medium.com/hackernoon/the-pioneers-guide-to-gx-decentralized-dependency-management-on-ipfs-90064858f4c2>

![](img/0cd6267ff52d9f9104c29cdf549b3e39.png)

对于今天的开发人员来说，分散式包管理是一个令人兴奋的工具开发领域。分散的包管理器有可能从现代软件库的互联网络中消除集中的故障点。它们为开发团队提供了一种直接的方法来确保他们的内部代码网络始终可用，同时确保整个互联网上的冗余。分散的包管理器可以使我们的代码——现代社会最有价值的产出之一——更能抵抗篡改、审查和操纵。

也就是说，分散的包管理仍然是不完整的。当将我们的代码发布到围绕数字信息的不变性而构建的网络中时，有许多问题需要解决，在这些网络中，信息的无限拷贝可能随时存在。如果没有中央机构，他们需要新的解决方案来发现、删除信息(例如，内容不归发布者所有)、解决争议和防范潜在的不良行为者。这些问题不仅仅是分散的包管理器所共有的，也是分散的网络所共有的，因此已经提出了一些解决方案，许多其他方案正在积极开发中。也就是说，在使用任何分散的包管理器之前，理解您的风险和责任是很重要的。

我在文章中更完整地讨论了我们在分散源代码和网络依赖性时所面临的前景(和一些陷阱)。如果您不熟悉这个主题，我强烈建议您在阅读本教程之前阅读它。在这里，你可以发现[去中心化的代码分发对于开源的未来。](/textileio/decentralized-code-distribution-for-the-future-of-open-source-2dc58f1153b2)

至此，这里简单介绍一下如何使用分散式包管理器 [GX](https://github.com/whyrusleeping/gx) (特别是 [Golang](https://hackernoon.com/tagged/golang) 实现， [gx-go](https://github.com/whyrusleeping/gx-go) )来分发你的代码并管理你在 IPFS 上的代码依赖。在我们开始之前的最后一件事，这里是 GX 开发者在他们自己的自述中所说的话，

> gx 是**阿尔法质量**。它还不完美，但是已经被证明足够可靠来管理 go-ipfs 中的依赖关系，并且已经为先锋开发者和早期用户做好了尝试和探索的准备。

# GX 和 gx-go 简介

gx-go 和 [gx-js](https://github.com/sterpe/gx-js) 包管理器使用的 GX 库核心功能。它们共有的基本原则是，

1.  您发布的代码版本由包含的代码的不可变哈希引用。
2.  您链接到代码中的依赖项链接到那些不可变的散列，以便查找和检索代码，并确保您使用的是您期望的版本。
3.  不可变散列是发布的代码应该在 [IPFS](https://ipfs.io/) 上可用的地址。

这一切意味着什么？首先，它使用代码本身的指纹(散列),而不是依赖语义版本化之类的东西作为对特定代码版本的全局引用。GX 还支持散列到语义版本的二次映射，这主要是为了改善人类与代码的交互(例如可读性)。这些散列成为 IPFS 使用的内容寻址系统的[的一部分。](/textileio/enabling-the-distributed-web-abf7ab33b638)

如果你不熟悉 IPFS，这里有一些很好的概述来解释什么是[系统](/@ConsenSys/an-introduction-to-ipfs-9bba4860abd0)、[它如何工作](/@jasonrigden/a-very-brief-introduction-to-ipfs-b2356e023b98)，以及[在 GX 之外](https://ipfs.io/docs/getting-started/)使用它。需要理解的是，它是一组技术和协议，使任何用户都能够通过分散的计算机网络分发媒体或数据。在 GX 用户的情况下，他们的代码。只要有人在分发您正在寻找的媒体，您就可以使用 IPFS 通过网络检索这些数据。

对 GX 来说，这种分布模型意味着如果你想在系统上发布你的代码，你需要从让它在 IPFS 网络上可用开始。虽然您可能是第一个托管这些数据的人，但是一旦其他人开始使用这些数据，其他节点很快就会提供这些数据。纺织品和其他 GX 用户经常镜像他们使用的代码，增加了网络的冗余和速度。例如，在[纺织品照片 Go 库](https://github.com/textileio/textile-go/)中，我们有一个团队 IPFS 节点，在那里我们锁定了我们从 GX 依赖的所有依赖项。

好了，这是基本原理。让我们试着使用它。

# 安装 GX 和 gx-go

接下来的两个部分(分发和依赖项管理)都需要这个简单的步骤。让 GX 和 gx-go 在您的系统上运行，只需运行以下两个命令。

```
go get -u github.com/whyrusleeping/gx
go get -u github.com/whyrusleeping/gx-go
```

只要你把你的根目录和`/bin`正确地添加到你的`path`中，上面应该可以使用 GX 命令行工具。如果上一行对你来说没有意义，在这里继续之前，这里有一些关于开始使用 Go 的教程。

# 分享 GX 的代码

我们或多或少偶然发现了 GX，因为它是 IPFS 相关项目中使用的包管理器。但是使用它让我们真正思考了我们上面提到的所有好处。GX 最初是为了支持 [gx-go](https://github.com/whyrusleeping/gx-go) 而编写的，但也有 [gx-js](https://github.com/sterpe/gx-js) 的，而且最初的 [GX](https://github.com/whyrusleeping/gx) 代码已经准备好了，可以为任何语言的包管理器分叉。它具有足够的可扩展性，这让我们开始思考 IPFS 的移动应用商店的未来会是什么样子……不过我们还是留到以后再说吧。在接下来的博客文章中，让我们浏览一下在您自己的项目中使用 gx-go 的基础知识。

## 在您的 Go 项目中设置 GX

从 Go 项目的文件夹中，您只需在终端中运行以下命令，

```
gx init --lang=go
```

运行上面的代码将会在你的文件夹中添加一个`package.json`文件，其中包含一些与 GX 一起工作所必需的信息。如果你的项目因为其他原因已经有了一个`package.json`文件:重要的部分将被添加。以下是 GX 在你的`package.json`文件中储存了什么信息的概述。

您的`package.json`将列出您在该项目中使用的 GX 版本:

```
"gxVersion": "0.12.1",
```

同样从`package.json`开始，GX 添加了导入重写路径。这将指定如何在用户代码中重写 import 语句(而不是一直向他们显示非人类可读的散列)。这里是我的样子，从我的围棋路径自动检测。

```
"gx": {
  "dvcsimport": "github.com/textileio/gx-demo"
}
```

接下来，`package.json`包含 helper 命令，稍后它将告诉 GX 如何为您处理新的发布。

```
"releaseCmd": "git commit -a -m \"gx publish $VERSION\""
```

就是这样。现在，您的代码可以开始在 GX 上发布了。让我们试着在 GX 上发布你的代码版本。为此，我们可以使用 GX 提交我们的代码，它将运行上面的`releaseCmd`脚本。

```
gx release 0.0.1-devError: ipfs daemon isn't running
```

现在…你可能已经抓住了第一个问题。正如我在引言中所说，GX 使用 IPFS 来分发你的代码，这意味着你需要在你的系统上运行一个 IPFS 守护进程。我不打算在这里介绍[安装步骤](https://ipfs.io/docs/install/)或[一行程序，您需要](https://ipfs.io/docs/getting-started/#going-online)来启动和运行守护程序。但是一旦你这样做了，你应该能够再次运行`gx release 0.0.1-dev`,并得到一个输出，

```
gx release 0.0.1-dev package gx-demo published with hash: QmZFb51F1rJcHy9UUWWgPvwJAMorMgdzw2a52y2xgjVZJu
```

现在，任何人都应该能够通过 GX 或者直接通过 IPFS(甚至是一个网关)访问您的代码。

您可能已经注意到，当您运行`gx release`时，会创建一个新文件`.gx/lastpubver`。该文件包含语义版本号到 IPFS 散列的映射。您应该将这个文件夹包含在您的 git 历史中，因为它可以在将来的任何时候将代码链接到发行版。与哈希不同，语义版本不能保证是不可变的。即使在 GX，你也可以用一套全新的代码重新发布相同的语义版本，因此也有一个新的散列。

## 出版关于 GX 的新版本

好了，你已经检查并修改了你的代码，现在你准备发布一个新的版本。你现在有几个选择。GX 没有太多的规则，所以技术上你可以通过运行`gx release 0.0.1-dev`再次发布相同的版本号。但是更有可能的是，你想要发布下一个版本，所以只要运行，

```
gx release 0.0.1package gx-demo published with hash: QmcUaXVES69qZEhV8tdUbhYtVBkbGU7b9h7bpvAo6xnvC9
[master 6f1a98f] gx publish 0.0.1
 2 files changed, 2 insertions(+), 2 deletions(-)
```

你会注意到你的`.gx/lastpubver`已经更新了，现在你可以在网上分享你的新散列了。

# 从 GX 安装依赖项

如果你想编写源代码，防止集中式服务离线、改变路线图或被阻塞，那么你可能想使用 GX 来管理你的依赖关系。你不一定要在 GX 上发布你的项目才能以这种方式使用 GX，也不是每个图书馆都能在 IPFS 上找到，但希望在未来的几个月里会有越来越多的图书馆能找到。

**那么我们来看看。**

您可以做的第一件事是导入一个新的依赖项。你需要有一个目标哈希，GX 才能做任何事情。好的，这是一个我们可以使用[https://github.com/mr-tron/base58](https://github.com/mr-tron/base58)的[开源](https://hackernoon.com/tagged/open-source)项目。它是一个简单的库，可以在 Go 中把`strings`转换成`base58`的形式。因此，如果你知道你在找什么，你会注意到回购不包含任何`.gx/pubver`文件。所以我们有几个选择:

*   我们可以使用上一节中的步骤将 GX 添加到项目中，并以这种方式获得散列。之后，我们可以使用和共享这种依赖性，并通过将源代码锁定在我们的 IPFS 节点上来帮助解决网络问题。
*   我们可以说服业主自己做。

对我们来说很容易，有人已经采取了这个项目的第一条路线。分叉原来的 base58 项目，在这里发布分叉 GX 版本，[https://github.com/gxed/base58](https://github.com/gxed/base58)。所以现在，我们可以使用发布的散列来尝试和安装导入。

```
gx import QmWFAMPqsEyUX7gDUsRVmMWz59FxSpJ1b2v6bJ1yYzo7jYupdate imports of github.com/mr-tron/base58 to the newly imported package? [y/N]
```

**通过对提示**回答 `**yes**` **，您将提示 GX 重写库的导入以使用 GX 提供的库。所以在这种情况下`import github.com/mr-tron/base58`看起来更像`import gx/ipfs/QmWFAMPqsEyUX7gDUsRVmMWz59FxSpJ1b2v6bJ1yYzo7jY/go-base58-fast/base58`。建议你**而不是** **将你的项目发布**到 GX，并重写它们的导入。相反，让下游的 GX 来处理这个问题。**

GX 将使用您的`package.json`文件来跟踪这些依赖关系，并处理未来的重写/取消重写。您可以在`gxDependencies`部分找到 GX 托管依赖项的列表，看起来像这样，

```
"gxDependencies": [
    {
      "author": "mr-tron",
      "hash": "QmWFAMPqsEyUX7gDUsRVmMWz59FxSpJ1b2v6bJ1yYzo7jY",
      "name": "go-base58-fast",
      "version": "0.1.1"
    }
  ]
```

# 今天就开始使用 GX！

就这样，只需几个命令，你就可以开始运行了…但是…哦，等等。你可能会遇到一些关于项目可用性的障碍。在 [Textile](https://textile.photos/) ，我们在代码上合作，每个人都可以更新依赖关系。这意味着，如果我添加了一个依赖项，并且是第一个固定源代码的 IPFS 同事，当我的任何合作者试图安装它并且代码不可用时，他们可能会遇到困难。GX 尽了最大努力，但有时仍有问题。为了在我们的团队中解决这个问题，我们已经将所有的 GX 依赖项都绑定到了我们共享的 IPFS 服务器上。

通过锁定我们工作所依赖的代码，我们还可以提高网络的冗余性和可用性。你应该加入进来:)

如果您对我们更多的开发工作感兴趣，请查看我们在 GitHub 上的 [textile-go](https://github.com/textileio/textile-go/) 库或 [textile-mobile](https://github.com/textileio/textile-mobile/) 库。如果您对 IPFS 网络的其他想法感兴趣，请查看我们的[计划，将下一批 1，000，000 名同行加入该网络](/textileio/adding-the-next-million-peers-to-ipfs-76d356352d14)。当然，如果你只是想让 IPFS 在你的手机上运行，存储和保存你的照片，或者实现点对点共享，[请加入我们的手机应用程序](https://textile.photos/join)试用列表。