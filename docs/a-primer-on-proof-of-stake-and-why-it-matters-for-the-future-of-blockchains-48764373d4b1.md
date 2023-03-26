# 关于利害关系证明及其对区块链未来重要性的初级读本

> 原文：<https://medium.com/hackernoon/a-primer-on-proof-of-stake-and-why-it-matters-for-the-future-of-blockchains-48764373d4b1>

![](img/6c368fe607b30b0ef7c750365d290f83.png)

在加密领域，现在正在发生一个重大的、多方面的转变。鉴于社区中信息流和主题的嘈杂、复杂、脱节和跨学科，很难看出这一点；但是，新的共识协议加上日益成熟的监管格局，为统一区块链项目中经常发生冲突的四个群体创造了条件:

1.  核心开发人员
2.  操作底层节点的人
3.  网络用户
4.  主要代币持有者

在一个理想的世界中，这些组应该有紧密一致的激励，但是由于各种各样的原因，大多数区块链都有两个或更多的这样的组处于僵局中，减缓项目进展到停止。*(2017 年发生的大规模投机提高了交易量，使这些群体之间的紧张关系更加普遍。)*

令人欣慰的是，有一些新兴的模型可以更好地协调项目中的激励机制，并为参与者提供新的机会。

下面，我将简要介绍当今市场上出现的利益证明(PoS)共识协议的变体，以及它们如何/为什么不仅解决了与工作证明(PoW)相关的许多潜在挑战，还为令牌经济学的未来铺平了道路，令牌经济学可以更好地协调分散网络中的政治力量。

*仅供参考，鉴于我们将在此深入探讨的相对复杂的主题，我假设您已经阅读了我的关于区块链、协议和令牌销售的* [*初级读本，并且/或者已经相当熟悉基本的行业主题和对话。*](https://hackernoon.com/a-primer-on-blockchains-protocols-and-token-sales-9ebe117b5759)

## 什么是工作证明，什么问题与之相关？

到目前为止，区块链的主要共识协议是工作证明(PoW)，它通过要求挖掘节点解决计算密集型难题来解决可靠性和安全性问题。这创造了一个“[本质上是一个 CPU 一票](https://bitcoin.org/bitcoin.pdf)”的环境来实现块共识，使得攻击者几乎不可能获得足够的计算能力来入侵网络。

然而，这种方法存在许多重要的问题:

1.  **巨大的能量消耗。【2017 年 12 月，仅比特币和比特币现金网络就消耗了全球总能耗的 0.14%，这个数字[比许多国家的总和](https://cointelegraph.com/news/bitcoin-mining-uses-more-power-than-most-african-countries)都要多得多。六个月后，这个数字[已经翻了一倍多](https://digiconomist.net/bitcoin-energy-consumption)达到 0.29%，使得单笔交易消耗的能量相当于 29 个美国家庭一整天所需的能量。事实上，即使假设平均能源成本为 0.1 美元/千瓦时(仅供参考，全球平均价格约为[0.2 美元/千瓦时](https://www.ovoenergy.com/guides/energy-guides/average-electricity-prices-kwh.html)的两倍)，仅运营这两个网络每天就要花费约 1800 万美元。**
2.  **浪费能源消耗。用于供电和保护战俘网络的能源被用于解决计算量大的难题。虽然有团队致力于建立可以[充分利用所有能源消耗的电力系统](https://twitter.com/jarredsumner/status/968653474039611392)(例如，机器学习有助于找到疾病的治疗方法)，但目前网络上没有“赢得”给定积木拼图的矿工只是将电能转化为热能。**
3.  **缺乏可访问性**。并不是世界上每个地方都有廉价的电力。这意味着最具竞争力的矿池聚集在相对较少的地理区域。此外，购买高质量处理器、冷却采矿设备、维护基础设施等的高昂成本将世界上大多数国家排除在外。事实上，举例来说，不到 15 个比特币矿池占据了该网络的绝大部分哈希表。
4.  **错位激励。在战俘网络中，矿工不需要持有任何原生硬币，而是被激励从费用和区块奖励中获取尽可能多的钱。这造成了政治上的分歧，例如[矿工投票反对 Segwit 和闪电网络](https://hackernoon.com/the-great-bitcoin-scaling-debate-a-timeline-6108081dbada)的实施，而这对网络用户是有利的。**
5.  与菲亚特的讽刺关系。这是一个题外话(和一个微妙的论点)，但因为 PoW 网络内的共识是通过处理器的分布实现的，并且这些处理器需要相当大的菲亚特交易来获得电力以进行操作，所以相当大的(即不是微不足道的)菲亚特交易基本上是确保 PoW 区块链的主干。鉴于区块链和加密货币承诺给我们带来一个新的经济体系，这很讽刺。

## 什么是利害关系证明，它会带来什么问题，在实践中使用它的哪些变体？

由 Sunny King 和 Scott Nadal 于 2012 年在[的一篇论文](https://peercoin.net/assets/paper/peercoin-paper.pdf)中首次提出，PoS 旨在解决区块链战俘日益增加的成本以及他们对能源密集型交易的依赖。PoS 区块链根据与硬币“赌注”相关的算法来委派节点，而不是在无用的谜题上磨磨蹭蹭。

为了更好地理解 PoS 区块链的工作方式，在我们深入研究它们之前，一定要记住它们需要解决的四个核心问题(除了上面列出的 PoW 问题之外):

1.  **初始分配**——一个团队如何决定谁先拿到硬币并下注？
2.  **51%攻击** —如果一个 PoS 节点(或节点卡特尔)拥有 51%的股份权重，这就是一个问题。
3.  垄断——如果你持有一吨硬币，你将主宰奖励。
4.  **无利害关系** (NoS) —在日常操作中，区块链中的新分支很常见。虽然 PoW 鼓励采矿者将其资源集中在更有价值的区块链上(因为参与的外部成本较高)，但 PoS 节点可以立即开始签署链叉，而不会产生不利因素，并且[可能会在没有任何风险的情况下重复花费](https://ethereum.stackexchange.com/questions/2402/what-exactly-is-the-nothing-at-stake-problem)以防止这种行为。

考虑到这一领域的活跃程度，现在在实践中(或建议中)有多种利益相关证明实施来解决这些问题，这并不奇怪:

*   **币龄选择。**区块链喜欢 [Peercoin](https://peercoin.net) (第一个 PoS 链)，从 PoW 开始分发硬币，使用[硬币年龄](https://en.wikipedia.org/wiki/Proof-of-stake#Coin_age-based_selection)帮助防止垄断和 51%攻击(通过设置一个被选为节点的概率最大的时间范围)，初步实现[检查点](http://bytemaster.github.io/article/2015/01/08/Nothing-at-Stake-Nothing-to-Fear/)防止 NoS 问题。
*   **随机区组选择**。像 [NXT](https://nxt.org/) 和 [Blackcoin](http://blackcoin.co/) 这样的连锁店也使用检查点，但他们认为硬币时代不鼓励赌博。在最初的分发阶段之后(通过 PoW 或[或](https://en.wikipedia.org/wiki/Nxt#History)，这些链使用算法随机选择可以创建块的节点。
*   **以太坊的卡斯帕协议**。由于已经广泛分发，以太坊在切换到 PoS 时不必担心初始分发问题。 [Casper](https://github.com/ethereum/casper) 采取了一种更为[拜占庭式容错](https://en.wikipedia.org/wiki/Byzantine_fault_tolerance) (BFT)的方法，如果节点做了不正当的事情，将会通过取消([砍掉](/@VitalikButerin/minimal-slashing-conditions-20f0b500fc6c))它们的股份来惩罚它们。此外，共识通过[多轮过程](https://github.com/ethereum/wiki/wiki/Proof-of-Stake-FAQ#what-is-proof-of-stake)形成，其中每个随机分配的节点在一轮中为特定块投票。
*   **委托利益证明(DPoS)** 。由 Dan Larimer 发明并首先在 Bitshares 中使用(然后在 Steem、EOS 和许多其他产品中使用)， [DPoS](https://hackernoon.com/explain-delegated-proof-of-stake-like-im-5-888b2a74897d) 通过让社区“选举”代表来运行节点以创建和验证块来解决潜在的 PoS 问题。然后，不良行为会受到社区的惩罚，社区只需在投票中击败被委派的节点即可。
*   **委托拜占庭容错(DBFT)** 。与 dpo 类似， [NEO](https://neo.org/) 社区为(代表)节点投票，但不是每个节点产生区块并就共识达成一致，[只需要三分之二的节点就每个区块中的内容达成一致](https://themerkle.com/what-is-delegated-byzantine-fault-tolerance/)(表现得更像簿记员而不是验证员)。
*   **嫩薄荷**。作为 DBFT 的更复杂形式和 Casper 的前身[，Jae Kwon](https://blog.cosmos.network/consensus-compare-casper-vs-tendermint-6df154ad56ae) [在 2014 年推出了](https://tendermint.com/static/docs/tendermint.pdf) tendermint，它利用了[动态验证器集](https://github.com/bigchaindb/bigchaindb/issues/1989)、[轮流领导人选举](https://atrium.lib.uoguelph.ca/xmlui/bitstream/handle/10214/9769/Buchman_Ethan_201606_MAsc.pdf)和与节点(即“验证器”)的自筹资金和社区令牌分配成比例的投票权(即权重)。
*   **主节点。**由 [DASH](https://www.dash.org/) 首先推出，masternode PoS 系统要求节点下注最低限额的硬币，以便有资格成为节点。这通常伴随着以治理、特殊支付协议等形式向网络提供“服务”的需求
*   **重要性证明**。 [NEM](https://nem.io/) 采取了一种稍微不同的方法，对主节点进行“重要性计算”，赌注至少为 10，000 XEM。这个兴趣点系统然后奖励随着时间的推移以积极方式影响社区的活跃节点。
*   **“X 的证明”**。最后，在 PoS 领域，不乏提出巧妙的下注方法和变体的活动(有些比其他的更复杂)。除了 BFT 协议如[蜜獾](https://eprint.iacr.org/2016/199.pdf)、[大毒蛇](https://eprint.iacr.org/2016/889.pdf)和[泰佐斯](http://doc.tzalpha.net/whitedoc/proof_of_stake.html)之外，为了进一步阅读，还可以查看“证明”:[赌注匿名](https://www.newsbtc.com/2018/05/08/pivx-becomes-worlds-first-anonymous-proof-stake-cryptocurrency/)、[存储](https://storj.io/storj.pdf)、[赌注时间](http://www.vericoin.info/downloads/VeriCoinPoSTWhitePaper10May2015.pdf)、[赌注速度](https://www.reddcoin.com/papers/PoSV.pdf)、[活动](https://eprint.iacr.org/2014/452.pdf)、[燃烧](https://en.bitcoin.it/wiki/Proof_of_burn)和[容量](https://www.burst-team.us/)。

## 对于区块链的未来，什么样的利益相关证明方法最有说服力？

人们常说，区块链不能同时解决可伸缩性、安全性和去中心化(也称为[可伸缩性三难困境](https://github.com/ethereum/wiki/wiki/Sharding-FAQ#this-sounds-like-theres-some-kind-of-scalability-trilemma-at-play-what-is-this-trilemma-and-can-we-break-through-it))。在最好的情况下，一个连锁店可以在这两个方面做得非常好。

在确定哪种 PoS 方法最有说服力时，答案显然是“这取决于”要解决什么问题。就个人而言，我认为我们已经了解了足够多的可扩展性问题(特别是在 2017 年，包括著名的 [Cryptokitties 减慢 ETH 网络](http://www.bbc.com/news/technology-42237162)的例子)，强烈认为 DPoS、DBFT、Tendermint 和 Casper 是主流区块链进行的最谨慎的方式(如果他们能够在技术上和政治上实现这一点，这对新的连锁店来说更容易)。

为什么这些口味最有前景？简而言之，大多数链的交易速度太慢，无法在其上构建主要应用程序，需要主要应用程序将加密生态系统带到下一个级别，以便广泛采用。

因此，在链的短列表上，我认为我们(即对下一代赌注有强烈兴趣的密码社区的子集)应该紧随其后，包括[以太坊](https://www.ethereum.org/)、 [DASH](https://www.dash.org/) 、 [EOS](https://eos.io/) 、 [NEM](https://nem.io) 、 [NEO](https://neo.org) 、 [STEEM](https://steem.io/) 、 [ZenCash](https://zencash.com/) 、 [ZeroCoin](http://zerocoin.org/) 和[宇宙网络](https://cosmos.network/)

*事实上，如果你有兴趣加入一个专门研究和讨论连锁使用 DPoS 的小组，* [*让我知道*](https://docs.google.com/forms/d/e/1FAIpQLSfCKoozGnO_A_RXwlYGCCnmpk_xg4zz0AMlppoDa8AP25jAzQ/viewform?usp=sf_link)*；我和一些同事正在邀请志同道合的人，我们希望听到你的意见。*

## 利害关系证明的令人信服的变体如何为统一的代币经济学铺平道路？

为了实现核心开发者、节点运营商、主要令牌持有者和网络的日常用户聚在一起推进区块链进程的未来，重要的第一步是上面讨论的 PoS 方法。这些新的共识算法要求节点操作者(又名验证者、主节点、铸币者、伪造者、代理人和/或见证人，取决于链)拥有和/或被代理大量的令牌。这意味着操作节点的人将受到经济激励，以确保网络的长期增长，而不是受到激励，以快速将 crytpo 转换为 fiat 和/或为了短期利润在电力链之间切换。

此外，开发商、代币持有者之间的平衡。dpo/tender mint 链中的验证器由一个持续的“投票”环境支持，在这个环境中，staking 组件不是唯一的因素；政党必须以有利于网络的方式行动，否则他们将被投票淘汰。

然后，这种一致激励的基础为网络铺平了道路，这些网络可以依赖更多元级令牌经济学来确保网络中涉及的所有各方“做工作”(治理、计算、存储等)来支持社区的活动，而不是简单地猜测预产品 ICO 和/或为了法定收益的唯一目的而挖掘硬币。重要的是，这种未来与大多数国家不断变化的监管格局相一致，这些国家认识到大多数(如果不是全部的话)产品前公开 ico 都是证券发行[，并将按此执行](https://www.sec.gov/news/public-statement/statement-clayton-2017-12-11)。

关于这个话题，我强烈推荐听听[克里斯·伯恩斯克上个月对劳拉·申](https://unconfirmed.libsyn.com/chris-burniske-of-placeholder-on-the-downsides-of-icos)的采访:

> “Placeholder 的合伙人、《Cryptoassets》的作者克里斯·伯恩斯克(Chris Burniske)讨论了为什么他认为最初的硬币发行可能不总是最好的代币发行模式，为什么他对使用户能够通过对网络做出贡献来赚取代币的项目更感兴趣，以及代币发行模式如何能够被设计成利用区块链的民主化潜力。”

结合新兴的 PoS 模型，更紧密地将激励与所有相关方结合起来，对于聪明地思考如何相应地分发令牌以扩大其网络的团队来说，未来是光明的。

作者注:提前感谢对我所写内容的任何更正/更新。我非常欢迎反馈。 [*订阅我的时事通讯*](http://wclittle.us5.list-manage.com/subscribe?u=e0dcd463a855cae724998d8f5&id=030549b371) *，当我写更多关于加密和分布式账本技术的主题时，我会让你知道(仅供参考，我还写关于* [*创业*](https://www.startuprocket.com/articles/how-to-create-grow-and-fund-a-tech-startup-an-operational-framework) *，* [*健康科学*](https://ketoschool.com/) *和其他技术主题)。作为对上述提示的提醒，* [*如果您有兴趣加入一个专门研究和讨论使用 dpo、DBFT、Tendermint、Casper 和/或 masternodes 的链的小组，请告诉我。特别感谢*](https://docs.google.com/forms/d/e/1FAIpQLSfCKoozGnO_A_RXwlYGCCnmpk_xg4zz0AMlppoDa8AP25jAzQ/viewform?usp=sf_link) [*托尼*](https://www.linkedin.com/in/tony-little-nd/) *、* [*安迪*](https://www.linkedin.com/in/andrewcronk/) *、* [*马特*](https://www.linkedin.com/in/matt-harrop-141b2/) *，以及* [*洛瑞恩*](https://www.linkedin.com/in/loriengabel/) *围绕本文主题进行点评、反馈、见解深刻的对话。最后，如果你觉得这篇文章有帮助的话，可以在下面(或者在上面)随意评论，点击拍手按钮，或者和朋友分享这篇文章。谢谢！*