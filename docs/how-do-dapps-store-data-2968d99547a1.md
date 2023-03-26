# dApps 是如何存储数据的？

> 原文：<https://medium.com/hackernoon/how-do-dapps-store-data-2968d99547a1>

![](img/3d91de56fdb29617d9135d28e1d9ef50.png)

Photo by [NASA](https://unsplash.com/@nasa?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

我在尝试[区块链](https://hackernoon.com/tagged/blockchain)开发时遇到的最早的问题之一是，*“所有东西都存放在哪里？”*

我一直在关注[这个可爱的教程](/@mvmurthy/full-stack-hello-world-voting-ethereum-dapp-tutorial-part-1-40d2d0d807c2)，作者是[橙纸](https://medium.com/u/14d83a8fab8c#orange-papers)，

> Swarm 是一个由节点组成的对等网络，通过互相贡献资源(存储、消息转发、支付处理)来提供分布式数字服务。这些贡献在对等的基础上被精确地计算，允许节点以资源交换资源，但是向消耗少于它们服务的节点提供金钱补偿。

他们的目标是用一个去中心化的版本取代带有中心化服务器的万维网。与 DNS 用于查找网页的方式相同，Ethereum Swarm 使用一种称为 ENS 的智能合同，允许域名所有者注册对其内容的引用，即使所述内容不会存储在传统的集中式服务器中。

老实说，这看起来像是一种革新网络的迷人方式，但我跑题了。这里的文档[虽然读起来很有趣。](http://swarm-guide.readthedocs.io/en/latest/architecture.html)

## 但是，嘿，这两个听起来不是很相似吗？

他们当然知道。以至于 swarm 的主要作者写了一篇关于这个事实的文章。除了不同的对等管理协议和不同的底层哲学(IPFS 希望尝试和集成所有现有的协议，Swarm 希望与 Smart Conract 和 Whisper 一起使用，以提供一个真正分散的 web)之外，一个关键的区别似乎是你可以使用 Swarm 作为云托管存储。你可以启动一个节点，上传，下线(他们管这个叫*上传，消失。*)你的数据还是会留在 swarm 上。在 IPFS，你只能在你的硬盘上发布内容。

# 等等，像 MongoDB/RethinkDB 这样的分布式 NoSQL 数据库呢？

好问题。老实说，我不确定。在 MongoDB 这样的数据库中，所有的节点都是平等的，相互信任。问题是一个恶意节点会导致错误的信息。这与一个叫做拜占庭容错的概念有关。

拜占庭容错对区块链来说绝对至关重要。在典型的分布式网络中，即使几个恶意节点也会导致整个系统可靠性的破坏。拜占庭容错是指即使存在拜占庭故障，即恶意节点/传播虚假信息的节点，区块链也将继续运行。这是如何被反击的确实很有趣，但那是另外一天。

BigChainDB 和 **TiesDB** 为 dApps 提供了存储数据的替代方法，但我将在另一篇文章中更详细地介绍这些方法。