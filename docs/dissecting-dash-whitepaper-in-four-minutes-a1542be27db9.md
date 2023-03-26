# 四分钟剖析 Dash 白皮书

> 原文：<https://medium.com/hackernoon/dissecting-dash-whitepaper-in-four-minutes-a1542be27db9>

*本文是名为*[*info urminds*](http://infourminutes.co/)*的每周系列的一部分。他们每周都会发布一份密码摘要或区块链白皮书。请在此* *订阅* [*。*](https://infourminutes.us14.list-manage.com/subscribe/post?u=94a41e60921ac2e7b1bd1945e&id=c8ed5bd2de)

![](img/dca81693e9e2ebda3c91694010cf0452.png)

Photo by [Niels Steeman](https://unsplash.com/photos/9oHlADjtBTQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/digital-currency?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 简介:

在比特币网络中，确认支付的等待时间很长。交易“得到确认”通常需要几分钟甚至几个小时。这阻碍了主流的采用，尤其是商家使用和柜台结账。

但是，这是分散网络的副作用之一。参与节点必须同步最新的块，并等待固定的时间来确认区块链中的事务。

Dash 的目标是成为日常活动的支付系统。因此，它侧重于即时确认付款的能力。它也主要集中于在网络中安全地发送交易。

# Dash 如何解决隐私和即时支付？

比特币中的完整节点允许网络上的参与者接收关于事件的更新。一个完整的节点对于网络的健康非常重要。

随着流量的增加，运行一个完整节点的成本会大幅增加。由于没有动力运行一个专用的完整节点，人们转而使用轻量级客户端。较轻的客户端对网络没有任何帮助。

# Dash Masternode 网络简介:

Dash 引入了主节点网络的概念。这是一个二级网络，提供一定水平的服务，作为回报，他们赚取额外的红利。

# 什么是主节点？

主节点是完整的节点，但可以向网络提供任意数量的额外服务，并有担保债券(1，000DASH)参与。Masternodes 网络保证了安全性，并允许参与者赚取利息，同时降低货币的波动性。

# Masternode 网络有什么特点？

一个帐户包括—

*   网络上的节点以不可信的方式执行敏感任务。
*   一个节点必须存储 1,000DASH 才能成为主节点。
*   主节点获得大约总奖励的 45%。(45%的奖励给矿工，45%给主节点，10%给自治社区)
*   网络上活动节点的数量有一个硬限制和一个软限制。硬性限制是流通中的破折号总数除以 1000——不可能有这么多破折号。另一方面，软限制是由收购一个节点的成本和交易所有限的流动性所决定的。

# 主节点是一个不可信的网络:

在主节点网络中，几个伪随机节点执行工作，而不需要整个网络执行相同的任务。没有一个实体能够控制主节点网络中执行的任务的结果。

# 主节点网络中的制衡:

网络中的节点必须 ping 网络的其余部分，以确保它们是活动的。网络伪随机地为每个块选择两个节点，以确保它们是活动的。每个街区检查大约 1%的网络，整个网络每天检查大约六次。

攻击者必须连续六次被选中来欺骗这个系统。当法定系统选择其他节点时，系统会取消违规。

# 私有事务:PrivateSend

隐私是 Dash 的一个重要特征。Dash 团队的目标是拥有一个标准的无信任实现，以提高其用户的隐私性，以及其客户的高度隐私性。

PrivateSend 是 CoinJoin 的改进和扩展版本。CoinJoin 只是合并事务以混淆原始事务。CoinJoin 的漏洞在于，原始交易可以简单地通过各种回溯方法进行回溯。

除了 CoinJoin 的核心概念之外，Dash 还实现了一系列改进，如去中心化、使用链接方法的强匿名性、命名和被动提前混合。

PrivateSend 接受几个输入，并将它们合并，这样它们就不能在那之后被解耦。合并需要至少三个参与者。操作中的面额必须是 0.1DASH、1DASH、10DASH 和 100DASH。

# 即时交易:即时发送

通过利用 Masternode 网络，用户可以发送和接收即时不可逆交易。

网络设置事务锁，该事务锁强制事务的输入只能在那些特定的事务中使用。形成仲裁并设置事务锁大约需要四秒钟。

InstantSend 为供应商打开了使用移动设备代替传统 POS 系统的大门。

# 结论:

Dash 建立在比特币的基础上，提供即时和私人交易。Dash 通过利用一种被称为 Masternode 网络的激励性双层模型来实现这些目标，而不是比特币等其他加密货币中现有的单层模型。

# 感谢阅读！如果您相信区块链可能带来的技术影响，并且喜欢您刚刚阅读的内容，请点击并按住👏，或者在下面留言评论。这将有助于我们接触到更多的观众。

*本故事由* [*普拉希什·拉杰班达里*](/@prashishh/) *撰写，最初发表于*[*info urminds*](http://infourminutes.co/whitepaper/dash)*。*

*阅读白皮书每周摘要，请订阅* [*邮件列表*](http://eepurl.com/dkTrvz) *。*

你在推特上吗？请 [*关注我们*](https://twitter.com/infourminutesco) *保持更新。*