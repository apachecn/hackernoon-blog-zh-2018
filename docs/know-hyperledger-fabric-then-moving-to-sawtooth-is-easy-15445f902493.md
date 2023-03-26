# 知道 Hyperledger 面料吗？那么转移到锯齿就容易了

> 原文：<https://medium.com/hackernoon/know-hyperledger-fabric-then-moving-to-sawtooth-is-easy-15445f902493>

[Hyperledger Fabric](https://hackernoon.com/tagged/hyperledger-fabric) 和锯齿是领先的区块链框架之一。但是你应该选择什么呢？在什么场景下？在此了解更多信息

![](img/a1ad882fdf5e81ca1e33d38d12ac9996.png)

我一直在使用 Hyperledger 面料和锯齿面料。虽然它们都是很好的框架，但是它们都有自己的缺陷和解决方法。在本文中，我的目标是找出两种框架的区别，这样你就可以选择最适合你的框架。

# 交易处理器和链码

锯齿的主要优势之一是“智能契约”或交易处理器(在锯齿世界中称为)可以用各种语言编写，如 Rust、Python 或 JavaScript。与 Fabric 相比，将事务处理器迁移到链中也有所不同。

# 验证者与背书同行

在 Fabric 中验证交易的过程涉及一组由认可策略定义的认可对等方。验证后，交易的分发由订购服务处理。

然而，在锯齿架构中，有验证器负责验证网络中的事务以及将事务分发到其他对等体。

# 国营商店

每个框架都以自己的方式处理维护[区块链](https://hackernoon.com/tagged/blockchain)网络的状态。

Hyperledger Fabric 根据设置将数据存储在 leveldb 或 couchdb 中，并管理每个渠道的分类帐。

在锯齿波的情况下，所有数据都存储在一个特定的地址中，这个地址是根据相应的事务处理器的前缀生成的。为了处理丰富的查询，设置了一个 rethinkdb 实例，并且复制了分类帐并与 rethinkdb 同步。

# 组件拓扑

Hyperledger Fabric 有许多组件，它们共同作用形成一个区块链网络。这包括订购者、同行、CAs、CouchDB 和工具。

但是在 Sawtooth 的情况下，只有三个主要组件，即 Sawtooth 验证器、事务处理器和用于事务处理器的 REST API。这降低了网络的复杂性，带来了更大的可扩展性。

# 语言支持

为了在 Hyperledger Fabric 上编写智能合同，必须依赖 GoLang 或 Javascript，因为不支持其他语言。

在 Sawtooth 中，支持多种语言，这有助于组织更快地采用它。支持语言包括 Go、JavaScript、Python、Java 和 Rust。Sawtooth 团队最喜欢的语言是 Rust，因为它提供了本机多线程，并具有 C 级性能。

[![](img/0dd6e0619b10d781363df64dd7ffbec0.png)](https://www.skcript.com/blockchain/?hackernoon)