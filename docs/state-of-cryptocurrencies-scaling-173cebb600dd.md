# 加密货币的发展状况

> 原文：<https://medium.com/hackernoon/state-of-cryptocurrencies-scaling-173cebb600dd>

任何深入加密货币世界的人都明白，这里存在可扩展性危机。2017 年已经向平台用户证明了这一点

*   交易比特币时的高佣金
*   以太坊平台的高油价

可伸缩性问题，如果是平台和概念所固有的，对它的未来可能是致命的。

目前大多数吹嘘低佣金的替代币只是因为它们尚未形成规模，但面临比特币面临的同样的内在障碍，因此它们的吹嘘是可笑的。

我将研究区块链出现缩放问题的领域，以及解决这些问题的建议方案。

**吞吐量** —中本聪提出的区块链有两个限制，使其无法扩展——恒定的块大小和将到达有效块的平均难度设置为恒定时间。在当前的设置下，这两者的结合产生了大约每秒 3 笔交易(tps ),远低于 Paypal 或 Visa 等集中式替代方案所达到的水平。更糟糕的是，降低块计算的平均时间会导致许多分叉和网络分裂，因此这不是一个可行的解决方案。取消数据块大小限制会导致 DOS 攻击，因为非常大的无效数据块可能会被发送到节点，导致网络流量拥塞。因此，需要对协议进行某种修改来解决这些问题，因为原始设置中的限制似乎很重要。

tps 问题如此重要的原因，除了限制加密货币作为现有在线支付解决方案的全面替代方案的可行性之外，还在于它对佣金的影响——当许多人希望交易时，佣金会上升——高需求，但由于架构原因，供应有限。以太坊的存储价格也是如此，因为每秒钟只能写入区块链的数据量有限，所以成本非常高。

**验证延迟**——在比特币中，交易永远不会被完全验证。由于区块链被定义为最长的有效区块链，所以通过从先前的区块创建将变得比现有的区块更长的分叉，一个极其强大的挖掘器可以在任何点出现以遍历整个现有区块链的历史。不过这很难做到，因此在比特币中，合理的等待时间是 6 个街区，在此之后，人们相信没有人能够玩这样的把戏。由于块验证时间平均为 10 分钟，这意味着接收资金的商家必须等待大约 1 小时才能知道他收到的付款是否足够可信。集中支付的替代方案是几秒钟，人们可以想到该功能至关重要的一系列用例。

**安全**——为了防止[西比尔攻击](https://github.com/ethereum/wiki/wiki/Problems)和[双消费攻击](https://en.wikipedia.org/wiki/Double-spending)，区块链使用了工作证明(PoW)。更不用说它对环境的影响了，这对于我将要分析的硬币有着特殊的影响。当然，在某种程度上，能源矿商自己承担的总成本必须渗透到整个硬币使用者身上。实际情况是，当硬币价格因高需求而上涨时，有多少新矿商将利用这一点生产更多硬币的均衡就被扭曲了。因此，价格的上涨幅度超过了正常情况下的水平，在正常情况下，会有更多的矿商加入这场游戏，使得货币作为一种交换手段变得不稳定。换句话说，假设矿工是理性的，这个等式成立

碳+磷*硫> = M

与—

C —迄今支付的佣金总额

p——比特币的当前价格

s——比特币的当前供应量

m——迄今为止的总采矿成本

这意味着以下不良后果之一-

*   不可阻挡的硬币通货紧缩，导致它无法作为一个可靠的交换媒介。
*   佣金价格飙升

我计划在未来发表一篇关于硬币价格稳定的博文，在那里我会更多地讨论这一点。

我的观点是，在这三者中，吞吐量是最糟糕和最令人担忧的问题。验证时间对许多用例来说很重要，但不是所有用例，但目前比特币交易中的主要延迟是因为吞吐量问题，因此吞吐量问题会产生延迟问题，反之亦然。安全扩展问题，虽然是处理得最多的问题，也是出于某种原因被炒作得最多的问题，但却是最不受关注的问题。我不认为它会对这项技术的未来或其现状产生重大影响，而且它有许多建议的替代方案。这里就不讨论了。

我现在将介绍现有的建议解决方案。

**比特币-ng** -

哈希谜语不是与验证块一起被解决和提交，而是在一个特殊的块中被预先解决，然后允许解算器发出与“迷你块”一样多的事务，直到另一个解算器出现在链上。

因此，比特币带来了两个新的想法

1.  没有块大小限制。矿工可以添加尽可能多的事务处理。即使实施了一些限制，有效数据块大小也会比今天的区块链大得多。
2.  矿工可以利用他专门用于工作证明的时间来通过网络传播块。这可以节省很多时间和不必要的叉子。

分析这两个想法，我会说第一个似乎是一个很好的功能，但它依赖于第二个，否则如前所述，这将相当于分叉比特币没有块大小限制，这将导致这里描述的问题。

第二个想法无疑是有益的，但引入了比特币-Ng 白皮书中没有提到的新风险。基本上，当验证者仍然在主动验证事务时，谁验证事务的知识就传播到了网络上，而不是与他验证的最后一组事务一起进行。这是有价值的信息，可能会导致如下行为-

*DOS* —对手会向节点发送无效事务，数量足以阻止其处理有效事务。很难在所有节点上同时复制这种攻击，但当一个孤立的节点成为整个网络的单点故障时，就容易实现了。为了执行这种攻击，对手将需要穿过区块链地址到达挖掘节点，但是如果节点将发送大量“牵连”微块，以将他暴露给所有第一层节点的方式，这是可以实现的。

*贿赂*——实现下面的场景会容易得多——选择的验证者将发布一个未决交易的日志，并要求佣金将它们包含在他的微块中。这个场景是说，在黑色星期五，亚马逊希望对小商家造成 DOS，这样所有的交易都会通过亚马逊。然后，他们会向验证者提供佣金，让他们不要处理这些交易——以一种互动的竞价方式，这种方式不可能在一个拥有无数可能的未来验证者的网络中传播。

**Iota**

Iota 可能是最具创新性的协议，它打破了最初的区块链公约和思维方式。在几个句子中，有一个 DAG——有向无环事务图，任何人都可以使用 PoW 添加自己的事务，指向两个以前的事务。平台用户通过非常强大的从众心理倡议来解决重复支出问题。您希望您的交易链接到 DAG 的主要分支，因此，如果它在某个点上连接得非常好，导致它被商家批准，那么在将来很难使双重消费交易比它连接得更好。这是你可以在 [iota 白皮书](https://iota.org/IOTA_Whitepaper.pdf)和[纠结平衡论文](https://arxiv.org/pdf/1712.05385.pdf)中读到更多的基本直觉。

尽管如此，似乎:

*   使用 Aviv Zohar 的分裂攻击的变体，在比特币-ng 讨论中描述的一个实体阻止其他实体使用区块链的场景甚至更容易在 Iota 中实现。这不需要贿赂任何单点故障，而是使用少量的计算能力来防止交易获得足够的有效性。
*   总的来说，他们的白皮书对分裂攻击的回应是多重启发，并不等于任何可验证的东西，有些需要双重阴谋，如引用*“防御分裂攻击的另一种有效方法是让一个足够强大的实体在一个分支机构上即时发布大量交易，从而迅速改变权力平衡，使攻击者难以应对这种变化”*
*   这种非正式的态度对于 iota 来说是非常独特的，这非常令人不安，因为他们的协议与其他协议非常不同，并且其中可能存在许多其他缺陷。一切大概都是通过他们自己进行的未公开模拟来验证的，而不是通过证据或彻底的逻辑讨论。这足以让我目前不认为它们是真正的未来扩展解决方案，尽管我相信它们在加密货币的构建模块中引入了非常有趣的概率元素。

**厉鬼**

推荐先看 Aviv Zohar 讲解 SPECTRE 架构的视频讲座[这里](/@avivzohar/the-spectre-protocol-7dbbebb707b5)。

在几个句子中，SPECTRE 使用 DAG，其中每个用户使用 PoW 提交他的块，指向图中所有可见的尖端。通过在块之间具有成对的顺序来解决双重花费，因此，如果块 A >块 B，则节点将根据 A 接受矛盾的事务。如果块 A >块 B 在某个点很可能会保持这种方式，这是由于该顺序的计算方式——SPECTRE 投票机制。事实上，Spectre 投票机制和 Iota 的随机游走均衡之间有很大的相似性——它们甚至都遭受分裂攻击，尽管在 Spectre 中这种情况有所缓解。如果可以证明它们本质上是相同的，这对 Iota 和 Spectre 来说都是一件大事——处理 Spectre 的投票复杂性和 Iota 中缺乏正式证明。

在投票机制中，确定 A 块？块 B，DAG 中的所有块投票决定他们“更喜欢”谁。一个区块的链接越多，得到的票数就越多。这意味着，未来的区块只会放大关联良好的区块，因此一旦交易被接受，重复支出的可能性极小。

关于这个投票，需要对每一个小块，每两个小块进行投票。似乎随着 DAG 变得越来越大，事务变得越来越频繁，这可能是每秒数万亿次计算，甚至更多，只是为了验证一切——更不用说生成用于向 DAG 提交任何东西的 PoW 了。

假设您有每秒 100 个事务的速率，这些新的块将需要对 DAG 的~N 个块的排序进行投票，在这个事务速率下，一年可以达到~3B 个块。不涉及实现细节和优化，从表面上看，这是当今最强的超级计算机无法应对的挑战。

Aviv Zohar 声称这没什么大不了的，DagLabs 的工作人员显然认为这不是一个会阻碍他们的主要流程。我没有看到对这个问题的简明讨论。我要说的是，虽然似乎有很多可能的途径来优化投票的天真实现，所以它可能是可解决的，但对我来说，这是目前悬在 SPECTRE 头上的最大的云。

**比特币现金**

随着时间的推移逐渐提高区块大小，这似乎是比特币现金所倡导的解决方案，将会奏效，但这将在很大程度上取决于社区在未来几年的完整性。这需要在两个不同的因素之间取得平衡——对区块链交易的需求，以及现代计算机的计算和网络能力——所以 DOS 不会构成威胁。虽然这将奏效，但这不是规模上的飞跃，因此这将实现以太坊对 Dapps 的愿景，Iota 对 IOT 微交易的愿景，如果在某一点上需求的增长远远超过当前可行的块大小选择，它甚至可能面临未来提高原生比特币交易的块大小的限制。

**同样值得注意的是&总结**

Iota 和 SPECTRE 还解决了延迟问题，将其缩短到几秒钟。

至于实现，目前比特币-ng 正在 waves-ng 平台下进行测试运行。Iota 是占主导地位的替代方案之一，尽管尚不清楚该网络在多大程度上遵循白皮书，以及有多少黑客和中央控制的元素仍被纳入其中。你可以说，截至这篇博文发表时，它仍处于测试阶段，市值约为 48 亿美元。加密货币的疯狂世界。

SPECTRE 由总部位于耶路撒冷的 DagLabs 实施，预计将于 2018 年第四季度发布测试版。

比特币现金是比特币的第二大分支——第一大当然是比特币。

只有时间能告诉我们加密货币的规模会带来什么。在我看来，这不像是有人在火中取栗，加密货币的缩放现在只是等待一些协议实施的问题。我最喜欢的电影是《幽灵党》,如果投票真的没有问题的话。否则，这可能只是最简单的态度，由比特币现金的家伙提倡的“随着它的到来而扩大”可能会走向胜利。Iota 和 bitcoin-ng 非常具有创新性，但我不确定它们将如何经受住时间的考验。

**简单提一下**

*以太坊分片*——是个好主意，但主要帮助小规模应用。

《幽灵》( SPECTRE)的作者最近出版了《幽灵》( Phantom ),我还不能参考它，但我会添加链接供进一步阅读。

*Ripple* —它带来的主要优势是减少延迟。我还没有弄清楚这是一个危及安全性的启发式解决方案，还是一个可行的无错误解决方案。

*闪电网络*——尽管被宣传为比特币的一件大事，但它并不是问题的真正协议解决方案，而是一种缓解措施，这提出了一个问题，为什么不使用第三方服务并结束它。

我要感谢迈克尔·米尔金在一个伟大的过程中共同讨论这些问题，我相信这个过程将在未来产生许多结果。他不必为我在这里犯的任何错误或妄言负责。

延伸阅读:

*   [https://TechCrunch . com/2018/02/08/whats-need-to-unlock-the-real-power of-the-区块链和分布式应用/](https://techcrunch.com/2018/02/08/whats-needed-to-unlock-the-real-power-of-blockchain-and-distributed-apps/)
*   [https://hacker noon . com/区块链-don-scale-not-today-至少-但是-有希望-2cb43946551a](https://hackernoon.com/blockchains-dont-scale-not-today-at-least-but-there-s-hope-2cb43946551a)
*   [https://ether eum . stack exchange . com/questions/872/将价值 1kb-10kb-100kb 的数据存入以太坊的成本是多少/896](https://ethereum.stackexchange.com/questions/872/what-is-the-cost-to-store-1kb-10kb-100kb-worth-of-data-into-the-ethereum-block/896)
*   [https://medium . com/loom-network/scalability-trades-why-the-ether eum-killer-has-not-arrived-8f 60 a 88 e 46 c 0](/loom-network/scalability-tradeoffs-why-the-ethereum-killer-hasnt-arrived-yet-8f60a88e46c0)
*   比特币-ng 白皮书—[https://www . usenix . org/system/files/conference/nsdi 16/nsdi 16-paper-eyal . pdf](https://www.usenix.org/system/files/conference/nsdi16/nsdi16-paper-eyal.pdf)
*   幻影[https://eprint.iacr.org/2018/104.pdf](https://eprint.iacr.org/2018/104.pdf)