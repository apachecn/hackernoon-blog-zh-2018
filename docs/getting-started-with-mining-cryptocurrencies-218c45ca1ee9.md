# 挖掘加密货币入门

> 原文：<https://medium.com/hackernoon/getting-started-with-mining-cryptocurrencies-218c45ca1ee9>

随着[加密货币](https://hackernoon.com/tagged/cryptocurrency)空间的白热化，我一直非常好奇，并尽我所能地学习。要理解核心概念，还有什么比从加密货币产生的根源(即采矿)开始更好的方式呢？所以这里是我在做这件事的过程中学到的一些东西，希望你会觉得有用！

# 什么是采矿？

最简单地说，采矿行为有两个目的。第一，这家矿商本质上是在为这家加密公司的公共账本(又名[区块链](https://hackernoon.com/tagged/blockchain))提供记账服务，每隔几分钟记录一次最近的交易，并将它们写入账本中被称为“块”的一页。

虽然矿工必须解决一个难题/密码功能，才能将他们的区块添加到区块链中，但谁能首先做到这一点，谁就可以获得一些密码作为他们服务的回报。

这让我想到了第二个目的，被奖励的密码被认为是“被挖掘的”。这基本上是因为它是新的货币被释放到网络上。所有加密货币都有一个固定的数字，通过这个挖掘过程设置为向网络发布。

随着这种未发行的货币逐渐减少，矿商就会受到每笔交易的奖励。因此，包含此奖励(即交易费)的交易可能会处理得更快。

# 选择一种货币

现在我们已经理解了挖掘背后的高级概念，让我们弄清楚要挖掘什么。自然，BTC(比特币)或 ETH(以太坊)将是我们显而易见的候选货币，因为它们是目前最知名和最受欢迎的货币。尽管这两者的问题在于，使用基本的 CPU 或 GPU 进行开采并不十分有利可图。如果你刚开始，我想这差不多就是你能接触到的。

在大多数情况下，一种货币的受欢迎程度与其挖掘难度直接相关。加入资金池的矿工越多，新的一页就写得越快。为了保持创建的页数稳定，谜题的难度会定期增加。

因此，在 coinmarketcap.com[的前 10 种货币中，CPU 或 GPU 挖掘的最佳候选货币将是 XMR (Monero)，因为它基于一种名为 CryptoNight 的工作验证算法，该算法旨在适用于普通 CPU 和 GPU，并能抵抗特殊硬件(如 ASIC)的挖掘。所以说了这么多，还是挑 XMR 吧。如果你需要一些更有说服力的东西，这里有一个很好的列表](https://coinmarketcap.com/)来说明为什么 XMR 是一个很好的选择。

# 设置钱包

所以接下来我们要解决的是把采矿的收益存放在哪里。对于 XMR，选择是有限的，因为它仍然是一种发展中的货币。最好的办法是下载并安装官方钱包。

或者，你可以在网上交易所比如 [Bittrex](https://bittrex.com/) 或[北海巨妖](https://www.kraken.com/)设立一个账户，并使用他们内置的钱包，尽管不建议将它用作大量货币的长期存储。

一旦你有了这个设置，记下你钱包的地址。在接下来的步骤中，我们将需要它。

# 加入游泳池

因此，虽然 XMR 对单独采矿很友好，但获得奖励的机会很低。因此，为了增加我们的机会，我们将希望在其他矿工的网络中分享我们的处理能力。然后，我们可以根据我们为创建一个块所贡献的散列能力来获得奖励。

有几个 XMR 矿池你可以从这个列表中选择。如果你在美国，我建议你坚持去 xmrpool.net，如果你在欧洲，我建议你去 moneropool.com。

两者都很好地平衡了挖掘者数量和散列率。你不希望加入一个有很多矿工的池，因为你的收入可能会被稀释，你会希望在散列能力和池中成员数量之间保持良好的平衡。

# 开始采矿

所以现在我们终于准备好开始采矿了。我们有两个选择，我们可以使用 CPU 或 GPU 来进行挖掘。让我们看看这两个选项以及它们是如何工作的。

**CPU**

为了让这变得简单，serve home 收集了一些很棒的 Docker 图片。如果您还没有为您的平台安装 Docker ,您可以[安装 Docker](https://docs.docker.com/engine/installation/),并运行以下命令:

对于 xmrpool.net:

```
sudo docker run -itd -e username**=[**YOUR_WALLET_ADDRESS] servethehome/monero_cpu_xmrpooldotnet
```

对于 moneropool.com:

```
sudo docker run -itd -e username**=[**YOUR_WALLET_ADDRESS] servethehome/monero_cpu_moneropool
```

**GPU**

再次感谢[serve home](https://www.servethehome.com/)为 Nvidia GPUs 提供图像，这使得 GPU 挖掘超级简单，无需在你的机器上安装 CUDA 依赖项。

虽然你需要安装的是 nvidia-docker ，这样 docker 容器就可以使用你的 GPU。

在 Ubuntu 上安装应该非常简单:

```
*# Install nvidia-docker and nvidia-docker-plugin*
wget -P /tmp https://github.com/NVIDIA/nvidia-docker/releases/download/v1.0.1/nvidia-docker_1.0.1-1_amd64.deb
sudo dpkg -i /tmp/nvidia-docker*****.deb **&&** rm /tmp/nvidia-docker*****.deb*# Test nvidia-smi*
nvidia-docker run --rm nvidia/cuda nvidia-smi
```

在他们的报告上检查其他发行版的安装步骤[。一旦完成了这项工作，您就可以启动 Docker 实例了:](https://github.com/NVIDIA/nvidia-docker#quick-start)

对于 xmrpool.net:

```
NV_GPU**=**0 nvidia-docker run -itd -e username**=[**YOUR_WALLET_ADDRESS] --name GPU0_monero servethehome/monero_gpu_nv_xmrpooldotnet
```

对于 moneropool.com:

```
NV_GPU**=**0 nvidia-docker run -itd -e username**=[**YOUR_WALLET_ADDRESS] --name GPU0_monero servethehome/monero_gpu_nv_moneropool
```

如果您有多个 GPU，您将需要添加如上所示的`NV_GPU=0`前缀，并为您想要定位的每个 GPU 旋转额外的容器。如果只有一个，可以跳过前缀和显式名称。

# 结论

根据您的硬件和池，您可能需要等待一天左右才能看到一些奖励。你可以检查这个计算器来计算出基于不同的变量你可以期望什么样的回报。

你最好的办法是在一台备用机器上运行它，并让它运行几天。你可能不会成为下一个密码百万富翁，但这是一个学习更多关于密码空间和 Docker 容器的绝佳机会。

![](img/741489b1a9fc01c5fc3d34aff66d8e36.png)

The modern day crypto gold rush reminds me a little of Charles Nahl’s “Miners in the Sierras”

愿好运与你同在！