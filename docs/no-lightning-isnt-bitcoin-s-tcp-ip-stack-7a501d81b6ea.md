# 不，闪电不是比特币的 TCP/IP 栈。

> 原文：<https://medium.com/hackernoon/no-lightning-isnt-bitcoin-s-tcp-ip-stack-7a501d81b6ea>

我昨天读了梅利克·曼努基扬的文章，觉得补充一些东西很重要。Melik 使用 TCP/IP 和以太网来类比闪电[网络](https://hackernoon.com/tagged/network)如何降低区块链的负载。直觉上很容易…这也是我最初的想法，但是在进一步的研究中出现了一些重要的细节。

[](/@melik_87377/lightning-network-enables-unicast-transactions-in-bitcoin-lightning-is-bitcoins-tcp-ip-stack-8ec1d42c14f5) [## 闪电网络实现比特币单播交易。闪电是比特币的 TCP/IP 栈。

### 最近我注意到，围绕闪电网络有很多困惑…

medium.com](/@melik_87377/lightning-network-enables-unicast-transactions-in-bitcoin-lightning-is-bitcoins-tcp-ip-stack-8ec1d42c14f5) 

*在我们深入讨论这个问题之前:我对闪电计划或 BTC 计划并不感兴趣。我根据阅读和与人交谈，整理出了我对事情真相的最佳猜测。我可能错过了一些事情。如果你认为我有，请告诉我！*

我还没有找到一个能够抓住闪电最重要的细节的比喻，所以我将尝试指出我认为 TCP/IP 比喻遗漏的细节。TCP/IP 的一个关键部分是路由是尽力而为的，它工作的唯一原因是节点没有作弊的动机，并且错误地引导了 10%(或 100%)的数据包。这样做赚不到钱。

有了[比特币](https://hackernoon.com/tagged/bitcoin)，作弊的动力十足。如果我发送 Alice 发送 Bob 10BTC 给 Charlie，Bob 可以轻松地过一个愉快的假期。如果鲍勃像比特币基地一样是一家知名的比特币清算所，他们将因此失去业务(但[可能没有你想象的那么多](https://www.reddit.com/r/CoinBase/comments/7nq31u/wire_transfer_funds_missing_stolen/))。为了消除等式中的“信任”, Lightning 要求连接双方“拿出抵押品”,确保他们不作弊。为了在“闪电网络”上打开连接，双方创建并广播一个链上交易，该交易建立了一个被称为双向通道的“托管账户”。假设他们都投入了 1BTC。双方现在可以更新“余额”，开始时是相等的。通过将 Alice 的余额更改为 0.9BTC，Bob 的余额更改为 1.1BTC，Alice 可以向 Bob 发送 0.1BTC。他们可以来回发送这些相互签名的“更新”(实际上只是不被广播到网络的事务)。当他们中的任何一方“想退出”时，他们将交易广播到网络，区块链关闭托管账户。

如果在 Alice 签署了“给 Bob 的 0.1BTC”之后，她广播了**旧的**更新，也就是她没有发送 0.1 BTC 的那个更新，会怎么样？鲍勃必须观看比特币网络，如果他看到那笔交易，他必须迅速发送交易的“最新”版本，这将使爱丽丝的恶意交易无效。如果 Bob 的节点出现故障，或者连接不良，Bob 就倒霉了。抱歉鲍勃。

让我们回到“集线器/交换机”的类比。在 Lightning 中，为了让 Bob 将 Alice 的 10BTC 路由到 Charlie，Bob 需要在 Alice-Bob 通道和 Bob-Charlie 通道中至少有*10 BTC。这意味着 Bob 需要锁定 20BTC，这是他试图发送的金额的两倍。原则上，Bob 可以在任何时候“结算”,但是为了促进 Alice 和 Charlie 之间的自由交易而浪费 20BTC 似乎是一个过高的价格。我当然不希望 20BTC 只是为了帮助人们汇款而被关起来。如果我是鲍勃，我可能会很乐意做交易的百分之几…但这不是重点！*

回过头来看，在这一点上应该很清楚，TCP/IP 可能是闪电网络可能形状的一个很好的类比，但它未能捕捉到现实世界用例的一些重要方面。我不清楚我们如何能够“从这里到达那里”。在 NOC 租用空间并不便宜，但是一旦你进去了，就网络容量而言，基本上是无所不包的。闪电就不是这样了，每次你想“提高你的极限”，你就必须把越来越多的比特币放在一边，以服务于开放的连接。