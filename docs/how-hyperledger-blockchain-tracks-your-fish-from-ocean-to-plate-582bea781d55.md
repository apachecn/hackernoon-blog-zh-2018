# Hyperledger 区块链如何跟踪你的鱼从海洋到盘子

> 原文：<https://medium.com/hackernoon/how-hyperledger-blockchain-tracks-your-fish-from-ocean-to-plate-582bea781d55>

听着，这是漫长的一天。7:15 起床，7:30 打电话，工作，健身，骑自行车上班，吃饭，骑自行车去聚会，做笔记，聊天，[网络](https://hackernoon.com/tagged/network)，回到家，看到桌子上有一块美味多汁的三文鱼。

![](img/73a3fc38e0b25c8f45a33cd3f65f8f42.png)

Nice slab of salmon sitting out.

什么？是的，你没听错。晚上 9 点过几分，我从 [Hyperledger Meetup](https://www.meetup.com/Hyperledger-SF/events/247293511/) 回来，走进我的公寓。我只看到一块生三文鱼放在外面。那是 Trader Joes 的“野生红鲑”。但是我不能停止思考，

> "我怎么知道这是野生的，而不是简单的市场宣传呢？"

这就是英特尔在 Linux 基金会项目 Hyperledger [锯齿](https://www.hyperledger.org/projects/sawtooth)的帮助下所做的事情。但是在我们开始之前，让我们先回顾一下。

![](img/9ff80db0f5c134072e2c8d6637792948.png)

The [JavaScript Foundation](https://js.foundation/), a project of the Linux Foundation, is responsible for creating [jQuery](http://jquery.com/), a popular JavaScript library.

# 什么是 Linux 基础？

1.  这是开源的。不仅如此，它还是世界上最大的开源非营利组织。
2.  **它是基于社区的。** 10/10 的大型云服务提供商为 Linux 基金会项目做出了贡献(编写或‘捐赠’代码)。
3.  这是最大的共享技术投资。$ 160 亿是 Linux 基金会项目的预计开发成本。
4.  **是关于部署代码。**该基金会的目的是加速技术发展，并帮助加快商业应用。

它的一些著名项目包括:Node.js、Kubernetes，当然还有 [Linux 本身](https://www.linuxfoundation.org/projects/linux/)。好吧，Linux 基金会有良好的记录，当然，但是 Hyperledger 和 Linux 基金会有什么关系呢？

![](img/c64812f42a76ffb3fe9b52b98fff8227.png)

How can you keep track of fish while possession moves from the ocean, to port, to factory, and ultimately to your plate?

# 野外的超级账本

1.  **一个保护伞项目。** Hyperledger 其实有五个‘框架’，俗称锯齿、Iroha、Fabric、Burrow、Indy。
2.  **海鲜溯源。回到那条美味的三文鱼。它就在那里，你付了两倍的钱，因为它的名字前面有“*野生*”。锯齿确保你得到你的钱的价值。但是怎么做呢？**
3.  **物联网传感器已启用。传感器附着在海鲜上，不断向区块链发送时间和位置数据。随着所有权的易手(从渔民到加工厂)，这些事件被记录在分类账中。**
4.  买方信任产品。由于买家可以查看鱼从渔民到零售商的全程记录，所以买家信任该产品。

![](img/0839391023ee15df7d72fbe1371c0ef8.png)

Plumbing is not fun.

# 为什么要开源？

虽然编写高级业务逻辑非常有趣，并且通常特定于某个公司的业务模型，但是您需要底层技术来做低级的事情，以便您可以在它的基础上构建业务逻辑。底层的东西就像水管。这是需要做的事情，但这并不美好。这就是开源社区的用武之地。由于人们对区块链技术如此感兴趣，开发人员愿意花时间做一些低级的“管道工作”,以便为所有区块链应用创建一个基线或“平台”。这个平台必须足够广阔，你可以为任何用例构建一个应用程序。除了这个底层平台，你还需要工具来构建你梦寐以求的下一个应用。

![](img/b5d1b7aeffd15b69e0d1be7cf1ef55ab.png)

Photo by [Barn Images](https://unsplash.com/photos/t5YUoHW6zRo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/tool-belt?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 超级分类帐工具

1.  **总账被套**。当你有多个账本，但是它们不能互相通信时会发生什么？Quilt 提供了一种使用 ILP 协议在分布式和非分布式分类帐系统之间转移价值的方法。
2.  **Hyperledger 大提琴。**该工具有助于减少创建、管理和终止区块链的工作量。
3.  **Hyperledger 浏览器。**就像它的名字一样，这个工具帮助可视化、查询或调用交易和相关数据以及存储在分类账中的其他信息。
4.  **Hyperledger 作曲家。**虽然该项目仍在酝酿中，但 Hyperledger 工具中最先进的是 Composer，它为构建区块链应用程序提供了更快的价值实现时间。这个工具在 Hyperledger Fabric 之上工作，并提供了一个简单的编辑器来开发智能合同和商业网络的细节。如果你想了解 Hyperledger Fabric，这个工具是一个很好的起点。

![](img/11a93000d0bce197262498e3e0bd9f2a.png)

Expect more (block)chain solutions to dominate headlines in 2018.

# 下一步是什么？

随着 Fabric 即将发布 1.1 版，预计 Hyperledger 项目的'*始祖*'将会开发出更多的概念验证。不仅如此，还期望围绕 Hyperledger 框架建立更多的工具。由于许多公司将其区块链解决方案保密，2018 年将是解决方案开始公开的一年。随着区块链和加密货币的流行，我们看到的另一个大问题是这些应用背后脆弱的基础设施。币安最近情绪低落。比特币基地在确认交易方面相当缓慢。这是**当你的系统受欢迎程度翻倍时出现的问题**。对于 web，在. com 时代，诸如 Cloudflare 之类的 cdn 被创建出来，以确保流量的增加将保持您的应用程序平稳运行。今天的区块链不存在这样的事情。

因为技术是如此的新，所以在这个领域有如此多的发展机会。这就是为什么我如此兴奋和幸运地在这个不可思议的新空间工作和学习。在外面保持饥饿！荷里活使命召唤:)