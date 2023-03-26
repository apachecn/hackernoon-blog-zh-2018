# 以太坊的生产替代品(2018 年 5 月)

> 原文：<https://medium.com/hackernoon/alternatives-to-ethereum-in-production-may-2018-91d132beb62>

## NEO 和 Qtum。对，就是这样。

***TL；博士:*** *这篇文章涵盖了以太坊作为分布式应用平台仅有的两个可行且有效的替代品(NEO 和 Qtum)，并解释了我对以太坊统治地位的信心。*

![](img/99694026181864765e143f248e0fdbc1.png)

Photo by [Dominik Scythe](https://unsplash.com/photos/v8vz-Roj8zo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/tools?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

我刚刚看完了艾德·波斯纳克正在进行的系列文章，[关于智能合约平台的起源](https://medium.com/on-the-origin-of-smart-contract-platforms)。这是一个在几个备受瞩目的智能合约平台上发表的文章集，在撰写本文时，他已经讨论了 [Tezos](/on-the-origin-of-smart-contract-platforms/on-the-origin-of-smart-contract-platforms-tezos-5eee6255c791) 、 [DFINITY](/on-the-origin-of-smart-contract-platforms/on-the-origin-of-dfinity-526b4222eb4c) 、 [NEO](/on-the-origin-of-smart-contract-platforms/on-the-origin-of-neo-7f9817849af3) 、 [EOS](/on-the-origin-of-smart-contract-platforms/on-the-origin-of-eos-c896281498ee) 、 [Cardano](/on-the-origin-of-smart-contract-platforms/on-the-origin-of-cardano-a6ce4033985c) 、 [Lisk](/on-the-origin-of-smart-contract-platforms/on-the-origin-of-lisk-de1be29271be) 、 [Qtum](/on-the-origin-of-smart-contract-platforms/on-the-origin-of-qtum-5f2e6daf798a) 和 [Waves](/on-the-origin-of-smart-contract-platforms/on-the-origin-of-waves-7ede1725a732) 。

事实证明，在支持任意复杂的分布式应用程序方面，目前没有太多以太坊的替代品。这个领域有很大的前景，我很高兴看到像 EOS 和 Cardano 这样的项目进展如何，但大多数项目还没有接近生产。

在这篇短文中，我们将快速了解以太坊仅有的两种替代物:NEO 和 Qtum。

注:我主要关注公共事业网络。我并不是看不到许可网络的价值(我看到了)，但设计合理的公共区块链的吸引力是很难抗拒的。

# 新的

[Neo](https://neo.org/) 号称比以太坊快很多。来自[他们的网站](https://neo.org/):

> 共识节点使用拜占庭容错算法达成共识，保证事务的终结性。这也确保了只要拜占庭故障发生在不到 1/3 的节点上，系统就能保持其终结性和可用性。

这使得他们可以吹嘘一些相当健康的数字，比如每秒 1000 笔交易，在 20 秒内得到确认。来自[他们的白皮书](http://docs.neo.org/en-us/):

> 在 NEO dBFT 共识机制中，生成一个数据块大约需要 15 到 20 秒，事务吞吐量最高可达约 1，000TPS，这在公共链中是非常出色的性能。通过适当的优化，有潜力达到 10,000TPS，使其能够支持大规模的商业应用。

然而，没有免费的午餐。这是以验证器节点的集中化为代价的，因为 BFT 最多只能在不诚实行为者的⅓下工作，并且当你越接近这个数字时，它的性能就会严重下降。实际上，这意味着 NEO 会密切关注谁可以在他们的网络上运行验证器节点。来自[的白皮书](https://github.com/neo-project/docs/blob/master/en-us/index.md):

> 簿记员可以是个人或机构的真实姓名。因此，根据司法判决，可以冻结、撤销、继承、收回和转让所有权。[……]近地天体网络计划在必要时支持此类行动。

来自[近地天体委员会在 Reddit 上的一篇帖子](https://www.reddit.com/r/NEO/comments/6uuwyv/a_response_to_fake_matrix_article/):

> NEO 目前还不够分散，这完全是有意为之。[……]近地天体委员会仍持有 50%的令牌[……]近地天体委员会希望确保所有共识节点都具有诚实、非恶意的意图。

虽然这样做可能有很大的理由，但我认为这与分权原则背道而驰。

如果你认为 NEO 对你来说是一个很好的选择，你可能会很高兴知道智能合约开发目前可以方便地在 C#、VB.Net、F#、Java 和 Kotlin 中完成。从[他们的文档](http://docs.neo.org/en-us/sc/introduction.html):

> 从语言上看，NEO Smart Contract 2.0 与以太坊的区别更加直观:与以太坊原有的 Solidity 语言不同，NEO Smart Contract 几乎可以被任何高级编程语言直接使用。首先支持的语言是 C#、VB.Net、F#、Java 和 Kotlin。

然而，在过于兴奋之前，请注意部署智能合同的价格被设定为 [500 汽油](http://docs.neo.org/en-us/sc/introduction.html#charge-mode)(嗯，490，因为[前 10 个是免费的](http://docs.neo.org/en-us/sc/systemfees.html#smart-contract-fees))。按照目前的市场价格(撰写本文时为 19.15 美元)，这相当于 9383.5 美元。

# Qtum

根据《每日电讯报》对 Qtum 联合创始人乔丹·厄尔斯的采访，Qtum 承诺每秒 60-70 笔交易。这比以太坊目前的版本高出一个数量级，并且通过使用更快的*利害关系证明*机制来实现，该机制每 16 秒生成一个块。

[Qtum 声称](https://cointelegraph.com/news/scalability-privacy-and-governance-main-problems-for-dapps-says-qtum-co-founder)进一步的交易吞吐量将通过分层网络或链外渠道实现。由于 Qtum 基础设施非常接近比特币及其 UTXO 模型，这可能暗示他们打算利用[闪电网络](http://lightning.network/)来提高可扩展性。

一些知名的项目如 [DataWallet](https://datawallet.com/) 和 [Luna](https://www.meetluna.com/) 选择部署在 Qtum 上，这为网络增加了一些可信度。这一选择可能是受到 Qtum 的 SPV ( [简单支付验证](https://bitcoin.org/en/developer-guide#simplified-payment-verification-spv))支持的启发，这使得与网络的交互更轻松，因此更适合移动设备。虽然以太坊的[轻客户端协议](https://github.com/ethereum/wiki/wiki/Light-client-protocol)的开发正在进行中，但目前还不认为它可以投入生产。

有趣的是，Qtum 声称拥有 6000 多个节点，这已经是以太坊的一半了，尽管 Qtum 的 mainnet 还没有上线一年。

根据[他们的白皮书](https://qtum.org/uploads/files/a2772efe4dc8ed1100319c6480195fb1.pdf)，Qtum 为智能合同执行实现了以太坊虚拟机，这意味着 Solidity 是目前唯一可用于部署的语言。这应该会让以太坊开发者很容易给 Qtum 的网络一个旋转。

# 以太坊经典怎么样？

[以太坊经典](https://ethereumclassic.org/)源于[刀叉](https://www.cryptocompare.com/coins/guides/the-dao-the-hack-the-soft-fork-and-the-hard-fork/)，社区在恢复刀叉的决定上产生分歧。他们的团队仍然在[努力工作](/@ClassicIsComing/emerald-project-f6744472109a)实现新的设施，平台似乎仍然看到[重大的](https://themerkle.com/4-ethereum-classic-icos-you-didnt-know-existed/) [利益](https://www.reddit.com/r/EthereumClassic/comments/7y9iji/upcoming_etc_icos/)。

如果以太坊受到 DDoS 攻击的困扰，被淘汰，或者面临任何其他存在的风险，那么使用以太坊经典作为以太坊的备份是有争议的。

# 不可避免的以太广场至上主义

以太坊是目前最受欢迎的智能合约平台。竞争者的声明比比皆是，但大多数声称的替代品甚至没有接近现场。

此外，以太坊正在努力解决这些竞争者在当前版本中看到的一些问题。如果以太坊——资金充足，拥有蓬勃发展的开发者社区——在替代方案流行之前找到可扩展性和更好的智能合约安全性的方法，我相信它仍将是首选平台。

以太坊社区正在[探索几个解决方案](https://media.consensys.net/the-state-of-scaling-ethereum-b4d095dbafae)，这些方案将会给平台带来巨大的改进。

*   采用*利害关系证明* ( [Casper](https://github.com/ethereum/wiki/wiki/Proof-of-Stake-FAQ) )应能显著降低能耗，并通过减少阻塞时间实现[更高的吞吐量](https://ethereum.stackexchange.com/a/5714)。
*   [分片](https://github.com/ethereum/wiki/wiki/Sharding-FAQ)应该带来[初始 100 倍的改进](/@icebearhww/ethereum-sharding-and-finality-65248951f649)以过渡吞吐量。
*   [第二层解决方案](/l4-media/making-sense-of-ethereums-layer-2-scaling-solutions-state-channels-plasma-and-truebit-22cb40dcc2f4)(意味着它们不是以太坊的核心，而是在以太坊之上实现的)，如[等离子](https://www.plasma.io/)、[雷电](https://raiden.network/)、[流动性网络](https://liquidity.network/)和 [TrueBit](https://truebit.io/) 将通过安全地将大量繁重的工作转移到链外来复合核心可扩展性改进。
*   随着社区对更安全的语言(如 [Bamboo](https://github.com/pirapira/bamboo) 、 [Vyper](https://github.com/ethereum/vyper) 和 [AxLang](/axoni/axlang-formally-verifiable-smart-contracts-for-the-ethereum-ecosystem-6201203be4e8) )和正式的验证工具(如[蝎狮](https://github.com/trailofbits/manticore)、 [Mythril](https://github.com/ConsenSys/mythril) 、 [Oyente](https://github.com/melonproject/oyente) 、 [Solgraph](https://github.com/raineorshine/solgraph) 和 [SmartCheck](https://tool.smartdec.net/) )加倍投入，智能合约的安全性也在逐步提高。

此外，以太坊的社区非常活跃，我相信最终胜出的平台将会获得最多开发者的支持。这不仅是因为有很多贡献者的平台发展速度更快，还因为在其上构建的应用程序和用例。

![](img/4f98063a0ef708e38b2283cddae2a067.png)

鉴于以太坊的相对成熟性、其社区的力量以及致力于改进它的大量研究，我看不出有令人信服的理由选择任何其他平台。

**策划一个 ICO？**在 [Fractal](https://www.trustfractal.com/business/) 大会上，我们提供了一个简单易用的 launchpad 解决方案，结合全面的客户身份识别服务(KYC/AML ),轻松成功地部署您的 token launch。