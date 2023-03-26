# 智能合同的演变

> 原文：<https://medium.com/hackernoon/are-smart-contracts-the-future-1d9028f49743>

![](img/026c64796f0aa6b40ca3b2689835e41e.png)

Replacing Paper Contracts? Photo by [Christa Dodoo](https://unsplash.com/photos/MldQeWmF2_g?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/paper?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

当我们谈论智能合约时，我们脑海中首先想到的通常是 [**以太坊**](https://www.ethereum.org/)(ETH)——坦率地说，这是所有智能合约之母。嗯，这种说法可能有点误导，但我认为以太坊是第一个引起公众关注智能合同实现的项目。这一切之所以成为可能，是因为区块链基础设施的设计。以防你可能不知道以太坊是什么，它是一个运行智能合约的去中心化平台。

![](img/dcc4b4fda2513a291daac0e21382ada3.png)

[https://www.ethereum.org/](https://www.ethereum.org/)

# 那么什么是智能合约呢？

智能合同是包含一组承诺的程序，包括合同参与者应如何履行这些承诺。它在多台计算机上执行，这样计算机就可以在执行契约有效性的计算上达成一致——并广泛用于以太坊。今天，主流智能合约采用包括 [ICOs](https://www.investopedia.com/terms/i/initial-coin-offering-ico.asp) 、 [Multisig transactions](/hellogold/ethereum-multi-signature-wallets-77ab926ab63b) 和 [Cryptokitties](https://www.cryptokitties.co/) 、*、*，而智能合约更广泛的应用仍处于早期发展阶段。

> 具体来说，在以太坊上，部署的每个智能合约都在以太坊区块链中的每个 [**完整节点**](https://blockgeeks.com/guides/what-are-ethereum-nodes-and-sharding/) 上运行。每当用户想要调用智能合约时，该智能合约就会在整个以太坊网络的每个完整节点上被激活。这导致了可伸缩性问题，这在某种程度上被认为是区块链应用程序实现大规模采用需要解决的关键技术挑战。以太坊社区已经投入到新的技术实现中，而其他企业/组织已经冒险创建他们自己的区块链，寻求解决可扩展性问题。

在我们深入探索其他具有智能合约功能的较新区块链之前，有趣的是注意到[智能合约并不是全新的](https://www.cryptoninjas.net/what-are-smart-contracts/)——这个术语是在比特币存在的 10 年前创造和探索的。然而，该术语及其应用直到最近才被进一步研究。以太坊的平台允许开发者在其区块链上构建 DApps(去中心化应用),这是由智能合约定义的。区块链的不变性和加密安全特性保证了这些合同的安全性。

> 有一个中间地带来存储智能契约消除了对中介方的需要，因为契约中的条件现在可以在平台本身上开发和执行。一个简单的例子是买卖协议——当买方向卖方付款时，合同强制要求卖方将物品交给买方。

# 智能合同的演变

自以太坊以来，实现智能合约的潜力一直在被探索。其他社区和企业也开始研究采用智能合同的更具体方式。

## 公共智能合同

到目前为止，公共智能合约的使用还很有限。如上所述，代币销售、multisig 交易和 cryptokitties 是我们在公共链上部署智能合同的主要领域。然而，关于这些实现的**问题**已经出现。[庞氏骗局 ICOs](https://www.wired.com/story/cryptocurrency-scams-ico-trolling/) 。 [Multisig 钱包被黑](https://cointelegraph.com/news/parity-multisig-wallet-hacked-or-how-come)。[去年 12 月，随着 Cryptokitties](https://www.coindesk.com/cat-fight-ethereum-users-clash-cryptokitties-congestion/) 的推出，以太坊区块链上出现了可扩展性问题。仅 Cryptokitties 一案就让整个以太坊区块链看起来不太可靠，可伸缩性问题已经成为普遍问题。

像 [Tendermint](https://tendermint.com/) 这样的(比以太坊)新创公司已经开始尝试提供一个“更好的”共识协议；它自诩每秒能够处理数千笔交易，并用更常用的编程语言如 Golang、Python 等编写智能合同。，相比以太坊的坚固性限制。

*(Tendermint 本身是另一个完整的虚拟服务领域&产品，我们可以在另一篇文章中讨论)*

## 企业智能合同

像 [***微软***](https://azure.microsoft.com/en-us/blog/introducing-enterprise-smart-contracts/)[***IBM***](https://console.bluemix.net/docs/services/IoT/blockchain/dev_blockchain.html)*[***摩根大通******SAP***](https://www.jpmorgan.com/global/Quorum)[***Consensys***](https://new.consensys.net/)**这样的大企业)来构建智能合约。***

***这是为了智能合同不一定需要部署在整个公共区块链中以供全世界看到。智能合同的参与方可以简单地剥离区块链财团内智能合同的全部节点，以执行这些智能合同，同时保持合同细节的私密性。其他方法还包括利用[c*ryplets*](https://github.com/Azure/azure-blockchain-projects/blob/master/bletchley/CryptletsDeepDive.md)*来保护智能合约的细节，并将其链接到以太坊(公共)区块链。****

## ******其他 DApps 链******

*****免责声明:下面的部分强调了一些 DApps 链，但是允许构建分散式应用程序的现有区块链的数量并不详尽。此外，我绝不是想在下面推广这些加密货币。*****

****以 DApps(去中心化应用)为特色的公共区块链的数量也激增。相对较新的平台*(或者不再那么新)*如 Enigma (ENG)、EOS (EOS)、ICON (ICX)和 NEO (NEO & GAS)，他们的目标是赋予世界不同的能力。****

****[**谜**](https://enigma.co/) **，**团队正在构建一个提供'[秘密合同](https://blog.enigma.co/defining-secret-contracts-f40ddee67ef2)'的区块链——由于公开区块链上的数据(包括智能合同)是公开的。在英格玛上部署的秘密合同将保持保密。****

****[**EOS**](https://eos.io) (又名。类固醇上的以太坊)，专注于以太坊的缺陷，以建立一个“更好的区块链”，因为他们声称提供比以太坊更好的技术能力。正在解决的主要技术能力之一是每秒交易数量(TPS)，这是一个众所周知的问题，比特币和以太坊在扩展时都面临着这个问题。****

****[**ICON**](https://icon.foundation/?lang=en) 或 ICON Foundation，旨在提供一个*(ICON Republic)*的网络，在这个网络中，多个独立的区块链可以用 DApps 建立在 ICON 区块链上，并且能够互相通信。****

****[**NEO**](https://neo.org) ，四人中的老大，在中国关系最深，创新技术的关键人物。它正在为[智能经济](/@MalcolmLerider/what-is-neo-smart-economy-381a4c6ee286)做准备，并非常重视保持监管合规性。这是目前可用的现有 [NEO + NEP-5 令牌](http://neonetwork.io/)的列表。****

# ****未来？****

****仅从这一点，我们就可以清楚地看到，许多方面都在寻找利用区块链基础设施来塑造下一阶段技术的最佳方式。很明显，智能合同的采用目前只会继续增长，因为越来越多的当事人正在寻求更好地利用智能合同的方法。****

****然而，我们应该对依赖智能合同的潜在危险保持警惕。 [**Quantstamp**](https://quantstamp.com/) 很可能是第一批在创建*智能合同安全审计协议*方面取得重大进展的少数人之一。尽管如此，重要的是要记住，智能合约的安全审计和漏洞发现仍处于初级阶段。****