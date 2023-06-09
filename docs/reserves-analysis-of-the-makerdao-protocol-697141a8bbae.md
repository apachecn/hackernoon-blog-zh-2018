# 储备银行对马克道协议的分析

> 原文：<https://medium.com/hackernoon/reserves-analysis-of-the-makerdao-protocol-697141a8bbae>

感谢 MakerDAO 团队对这篇文章的反馈，以及他们对建设性批评的开放态度！

![](img/98240449d9d0075863c42c6c16191e69.png)

最近几个月，MakerDAO 的戴获得了 stable coin(T3)报道的最大份额，只有 Basis 紧随其后。([在这里阅读我们对基础协议的分析](/reserve-currency/our-analysis-of-the-basis-protocol-cf1e0713b849))。)有很多理由对戴持积极态度:稳定算法是经过深思熟虑的，他们背后有一个规模庞大、资金雄厚的团队。此外，它最大限度地降低了日常用户的风险。但是，我们相信戴最终是不行的。即使它的稳定算法能够维持联系汇率——我们对此表示怀疑——它也存在无法扩大规模的严重风险。

戴很难分析。[迷惑人](https://web.archive.org/web/20171118094841/http://www.getbasecoin.com:80/basecoin_whitepaper_0_99.pdf)。这部分是因为它*很复杂，包含几个相互作用的机制；部分原因是 Dai 协议有不同的版本，有时会混淆，部分原因是没有规范的文档。*

因为戴的稳定算法实际上是复杂的，为了解释为什么我们担心它不会成功，我们必须详细检查该协议。这使得这个帖子特别密集——绝对不是轻松阅读！

如果你觉得你对戴的工作原理有足够好的理解，你可以跳到第二部分，详细讨论戴的稳定机制，或者跳到第三部分，在第三部分，我们对戴进行全面的评估。

# 摘要

*   戴缺乏强有力的价格稳定机制，在中期内将很难保持价格稳定。尽管如此，**它可能会保持相对稳定**。
*   戴的供应量可能有限，无法满足潜在的大量稳定玉米需求。**我们认为戴将无法扩大规模成为全球稳定的货币**。
*   然而，**戴很可能将稳定币用户置于小风险之中**。戴对加密崩溃的适应能力比看起来更强。
*   最后，Maker 之前计划在 Dai 的成熟版本中包含一个额外的稳定机制，即目标速率反馈机制，他们最近决定取消该机制。从我们的角度来看，这是幸运的，因为我们认为从过程控制的角度来看，它的设计很差。尽管之前的包含显示了他们部分的一些错误判断，但是移除举例说明了**纠正他们的错误和迭代改进的能力。**

## 目录

[1。戴解释了](/p/4a9872c1a824#fb7a)
- [分析戴](/p/4a9872c1a824#bd5f)-
-[戴](/p/4a9872c1a824#c4d4)-
-[单质押戴](/p/4a9872c1a824#01ea)-
-[发行戴](/p/4a9872c1a824#068b)-
-[自动清算](/p/4a9872c1a824#da0e)-
-[多质押戴【】
-](/p/4a9872c1a824#7953)[全球结算](/p/4a9872c1a824#034e)
-[代币和](/p/4a9872c1a824#128c)

[2。戴的平准机制](/p/4a9872c1a824#dbac)
- [戴不可赎回](/p/4a9872c1a824#c1ea)
- [全球结算平准](/p/4a9872c1a824#37e3)
- [平准投机](/p/4a9872c1a824#b826)
-[CDP 创造者操纵价格](/p/4a9872c1a824#6ac4)-
——[戴下跌时](/p/4a9872c1a824#fb9f)
——[戴上涨时](/p/4a9872c1a824#ee4f)
- [断链平准](/p/4a9872c1a824#15e3)-

[3。戴评估](/p/4a9872c1a824#fd42)
- [可扩展性:戴的可扩展性不好](/p/4a9872c1a824#0f4c)
-[CDP 需求有限](/p/4a9872c1a824#640b)
-[多元化进一步限制戴的供给](/p/4a9872c1a824#c44a)
- [稳定性:做市商系统如何处理连带崩溃](/p/4a9872c1a824#555f)

[关于制造商、目标比率反馈机制和责任的最终想法](/p/4a9872c1a824#ba31)

# 1.戴解释道

如前所述，戴很难分析。在这个由两部分组成的部分中，我们首先回顾*为什么*Dai 很难精确分析，然后将 Dai 分解成各个组成部分并解释它们。

# 分析戴的困难

创客团队已经在戴上工作了三年，添加，提炼和删除部分。这个协议已经有了很多不同的版本，目前还没有完成。

他们已经发布了 alpha 版(“Sai”)和 beta 版(“Dai 1.0”)。今年夏天创客团队计划推出完整版:多抵押品戴(“戴 2.0”)？).目前运行的版本是 Dai 1.0。这是一个测试版，与即将发布的完整版有很大不同。完整版的行为决定了戴的成功，也是本帖关注的焦点。除非另有说明，否则我们指的是完整版本。

一个人怎么知道完整版会是什么样子？Maker 没有提供一个规范的最新文档来详尽地描述它。

然而，他们用*的[的](https://hackernoon.com/tagged/documentation)*文档来弥补规范规范的缺失。这些文献来自不同时期，描述了戴的不同版本。[白皮书](https://makerdao.com/whitepaper/DaiDec17WP.pdf)是最新的文档，对 Dai 1.0 和多抵押品 Dai 进行了高度概述。[紫纸](https://makerdao.com/purple/)填了一些关于多旁系戴的细节。[市场动态模拟纸](https://makerdao.com/assets/documents/Dai-Stability-Engine-Simulation.pdf)给出背景，模拟老版戴。[聊天记录](https://chat.makerdao.com/home)和 [Reddit 帖子](https://www.reddit.com/r/MakerDAO/)给出了该系统未来[版本可能的样子。](https://hackernoon.com/tagged/future)

人们可以部分理解这种缺乏规范说明的情况。成熟系统的某些部分可能还没有决定，即使是已经决定的部分，在决定之后立即发布也没有意义。为一个复杂且不断发展的系统保持最新的文档是很费力的。

戴不断变化的本性和令人困惑的沟通的一个说明性例子是目标速率反馈机制(TRFM)。TRFM 是一个稳定机制，在紧急情况下，Maker token 持有者可以通过治理系统激活该机制。它将自动调整抵押债务头寸(CDP)创建和 Dai 控股的激励，通过改变目标价使 Dai 市场价格更接近目标价。如果人民币汇率被激活，戴将暂时与人民币脱钩。

最初，这个系统围绕着一个类似 TRFM 的稳定机制[，正如我们在旧的模拟论文](https://makerdao.com/assets/documents/Dai-Stability-Engine-Simulation.pdf)中所看到的。后来，在戴的白皮书中，TRFM 被描述为戴稳定机制的几个部分之一。然而最近，创客道创始人 Rune Christensen **、**、声明 TRFM 将不会包含在完整版中，并将从白皮书中删减。这是因为它是一个“非常复杂的机制设计，很难理解，只在非常特殊的情况下有用。”

TRFM 的例子突出了制造商如何不断开发和改进他们的系统，以及他们缺乏清晰的沟通:甚至他们的白皮书都没有准确地描述预期的成熟系统，他们系统的重大变化只能在 reddit 线程中找到。

积极的一面是，Maker 团队不断改进 Dai，而不是拘泥于不成熟的协议设计。然而，这些持续的变化使得理解和评估协议变得更加困难。我们认为，除了该协议的复杂性之外，这也是人们难以对其进行评估的原因。

同样，为了避免这种混淆，我们将总是谈论完整版本，除非我们明确声明，并且当我们有分歧时，将试图明确区分我们正在谈论的协议的不同版本。

在下一节中，我们将把 Dai 分解成不同的组件，并解释它们是如何协同工作的。

# 戴的不同成分

其核心是，Dai 系统的工作方式是通过激励个人用户锁定链上的抵押品，以便向他们自己发行 Dai。

该系统由两个令牌组成:稳定币“戴”和股权和治理令牌“MKR”。

这两个令牌用于生成制造者协议的各种功能。该系统有四个组成部分:

1.  抵押债务头寸。CDP 就像智能合约机，人们可以通过锁定抵押品来发行 Dai。如果抵押品价值下降导致抵押品债务比率过低，该协议会自动清算 CDP 中的抵押品。这是戴发行、撑腰、烧戴的核心机制。
2.  支持多种类型的抵押品。目前，CDP 只能接受 ETH 作为抵押品。Dai 的完整版本将通过不同的 CDP 类型支持许多不同类型的链上担保品。
3.  全球结算。全球结算是一个重置按钮，由 MKR 代币持有者间接控制。它冻结了协议，并给予 Dai 持有人和 CDP 持有人他们在系统抵押品中的份额。
4.  协议治理。MKR 持有者通过链上投票来管理协议。

在接下来的内容中，我们将首先了解 Dai 协议如何适用于单一抵押品类型——Dai 是如何发行、支持和销毁的。这包括解释用户如何锁定抵押品以发行 Dai，以及他们为什么要这样做。我们还将检查协议的机制，以确保戴有足够的支持。然后，我们将看到系统如何处理多种类型的抵押品。接下来，我们将介绍全球结算机制，这是一种稳定机制，可以防范攻击。这说明了 Dai 发行和燃烧 Dai 的核心机制，以及确保 Dai 由分散抵押品支持的核心机制。最后，我们将解释二级股票持有者是如何有效治理戴的。

# 单络戴

让我们来看看戴的核心机制:一种由锁定在智能合约中的加密资产支持的稳定货币，受到自动抵押品清算的保护。每一次通过分散抵押品支持稳定货币的尝试都必须克服波动性问题:每个 Dai 代币必须由至少 1 美元的价值支持(Dai 与美元挂钩)，以保持良好的抵押。然而，加密资产是不稳定的，经常会失去价值。支持 1 美元 Dai 代币的初始 1 美元 ETH 可能会迅速贬值。由于每只 Dai 的支持价值不到 1 美元，Dai 变得抵押不足，其盯住美元的机制可能会崩溃。

解决这个问题的自然方法是过度抵押。假设加密资产的价值从未低于其最大价值的 50%。如果是这种情况，那么制造商可以确保他们从 2 美元的抵押品开始，总是为每个 Dai 提供 1 美元的支持。然而，过度抵押的方法遇到了一个问题，即预测加密资产未来的最大价值下降非常困难。假设你押了 50%。嗯，运气不好:ETH 最近下跌了近 70%。因此，我们不能排除未来 ETH 或其他加密资产会出现同样大、甚至更大的下跌。

Maker 的解决方案是在需要时自动出售(“自动清算”)抵押品，使用智能合同来确保每个 Dai 由超过 1 美元的加密资产支持。要发行 Dai，你需要在一份名为 CDP(抵押债务头寸)的智能合约中锁定价值超过已发行 Dai 150%的抵押品。CDP 允许你锁定抵押品，并以抵押品为抵押自主地获得贷款。贷款是以 Dai 代币的形式，这种代币是在飞行中铸造的，并且你根据 Dai 承担相应的债务，即你需要在以后偿还 Dai。CDP 记录债务，你支付利息(“稳定费”)。目前的利率是每年 0.5%，但 MKR 持有者可以通过投票改变利率。按照目前的比率，为了创建 CDP 并向自己发行 Dai，发行者必须锁定价值至少 1.5 美元的加密抵押品。如果 CDP 中的抵押品价值低于未偿 Dai 的 150%,合约会自动出售足够的抵押品，以回购您发行的 Dai。发行的代币因此停止流通。

自动清算强制执行的 150%抵押要求使得整个 Dai 系统的抵押不足(少于 100%支持)变得不太可能。只有当 Dai 的总抵押品价值从流通 Dai 供应价值的 150%下降到低于智能合约回购已发行 Dai 作为抵押品的速度的 100%时，Dai 才会成为抵押不足。

请注意，Dai 的发布不是由协议或制造商集中发起的。相反，每一个存在的 Dai 都是因为一些用户开立了 CDP，锁定了抵押品，并决定从该抵押品中发行 Dai，在此过程中承担了债务。这指出了一个事实，这是理解戴的关键:stablecoin 系统有两个不同的用户群。Stablecoin 用户买卖戴。但也有 CDP 消费者开 CDP，发行 Dai。稳定币用户不必为 CDP、利率和 Dai 发行的复杂性而烦恼，因为 Dai 一旦发行，就可以在正常的交易所买卖。

在下一节中，我们将了解 CDP 创建者发行 Dai 的动机。稍后，在第 3 部分中，这些动机被证明对理解 Dai 至关重要，并且是我们评估 Dai 的可伸缩性的关键。

## 发行 Dai 的动机

Dai 用户依赖 CDP 消费者发行 Dai。但是为什么会有人发行戴呢？Dai 发行人不得不锁定比他们从 Dai 中获得的更多的抵押品价值。这看起来像是教科书上的糟糕交易。然而，在某些情况下，这是值得的。发放抵押贷款相当于获得一笔抵押贷款，就像用你的房子做第二次抵押来为改造提供资金。

抵押贷款的主要用途是投资杠杆。以下是你如何使用 CDP 来利用 ETH 投资的方法:想象你拥有 1500 美元的 ETH，并相信 ETH 的价值会翻倍。你现在没有流动性去买更多，但是想从你的知识中获利。首先，你把你在联邦储蓄银行的 1500 美元锁定为 CDP 的抵押品。然后你发行 1，000 Dai 作为抵押品，获得 1，000 Dai 的债务。接下来，你在瑞士联邦理工学院以 1000 美元的价格出售 1000 戴。通过 CDP，你现在拥有价值 2500 美元的 ETH，包括作为抵押品锁定的 1500 美元。你的初始投资杠杆是 1.66 倍。当 ETH 翻倍时，你把它卖给戴，然后你用它来偿还债务(连本带利)，你就可以带着可观的利润回家了。您还可以递归地重复这一利用过程，让您获得高达 3 倍的利用。

然而，在实践中，你不会得到最大的杠杆。那是因为你想要比 150%最低额度更多的抵押品。在 150%抵押品的情况下，抵押品价值的任何下降都会自动平仓。由于自动清算伴随着罚款，用户将希望通过在 CDP 的抵押比率中保留缓冲来降低风险。例如，不是从 1500 美元的抵押品中发行 1,000 Dai，而是只发行 750 Dai，并保持 200%的抵押率和 1.5 倍的非递归杠杆。

CDP 的第二个用例是增加短期流动性:你现在需要钱，并拥有一项资产作为抵押品来换取资金。但是为什么不直接卖掉资产呢？有不同的原因。也许你认为资产会升值，所以你想继续持有，以便将来出售时获利。或者可能是资产缺乏流动性:如果你不等待合适的买家，而是马上卖掉它，你预计你的资产会得到更低的价格。

## 自动清算

Dai 发行、支持和燃烧的核心机制的最后一部分是控制当 CDP 没有足够的抵押品来维持 150%的抵押品债务比时会发生什么。

发生这种情况的过程有些复杂。它涉及两种不同的连锁拍卖，试图筹集足够的资本，使 CDP 债务免费。要完全理解这个过程，你可能需要在阅读完之后花一些时间去思考。如果你没有完全理解，不要担心:完全理解对于后面的分析是不必要的。

事情是这样的:首先，“债务拍卖”试图通过 MKR 稀释来偿还 CDP 的债务。债务拍卖买戴，用新造的支付。戴被烧了，要取消 CDP 的未偿戴债务。债务拍卖的目的是确保债务得到偿还，即使 CDP 中没有足够的抵押品来偿还债务。

同时，“抵押品拍卖”用 CDP 的抵押品购买 MKR。抵押品拍卖出售足够的抵押品来支付债务、累计利息(称为“稳定费”)和清算费。在单抵押品 Dai 中，清算费是 CDP 中抵押品的 13%，也就是说，当用户的 CDP 自动清算时，他们会收取用户锁定抵押品资本的 13%。智能合约最终将剩余抵押品返还给 CDP 持有者，并烧掉所有购买的 MKR。

抵押品拍卖抵消了债务拍卖带来的 MKR 稀释。只要清算的 CDP 没有抵押不足，并且智能合约设法以市场价格出售抵押品，抵押品拍卖就能完全抵消稀释。通过焚烧 MKR 代币，MKR 持有者实际上也获得了累计利息和清算费，这进一步减少了 MKR 的流通供应量。当协议购买并烧掉 MKR 令牌时，每个剩余的 MKR 令牌的值(大概)会增加。MKR 烧伤(可以说)相当于把烧伤的价值按比例给 MKR 持有者。

# 多络戴

目前，在单一抵押贷款中，你只能用一种抵押贷款，即 ETH。但成熟的版本，多抵押品戴，旨在有许多不同类型的抵押品。Maker 的计划是让用户不仅使用 ETH，甚至其他纯加密资产，而且使用各种令牌化的现实世界资产作为抵押品来发行 Dai。

添加不同类型的抵押品有两个目的。首先，通过增加人们有兴趣购买的 CDP 债券数量，它可以增加 Dai 的供应量，因为有更多的保证金选择。请记住，Dai 的金额始终等于 CDP 的未偿债务。对 CDP 债务的需求总是对特定资产的债务需求。一些人希望 CDP 与 ETH 合作，另一些人希望 CDP 与 BTC、黄金或苹果股票合作。Dai 系统包含的 CDP 类型越多，Dai 的潜在供应量就越大。因此，获得多种允许的抵押品对戴的扩张能力至关重要。(在下面的评估部分，我们将在对 Dai 的可伸缩性的评估中再次讨论这一点。)

第二，增加更多的抵押品类型可以通过多元化戴的抵押品支持来帮助保护戴免受崩溃。由于只有一种抵押品，戴对这种抵押品的价值暴跌非常敏感。当抵押品资产价值崩溃时，带有该抵押品的 CDP 开始自动清算。由清算的 CDP 发行的 Dai 被停止流通。供应量的减少暂时限制了戴作为稳定货币的作用。如果崩盘很快，CDP 在清算时抵押不足，MKR 稀释被迫进行。如果崩溃的抵押资产是 Dai 的唯一抵押资产，就像在单一抵押 Dai 中 ETH 是唯一可接受的抵押资产，崩溃对 Dai 来说可能是灾难性的。有了支持 Dai 的多样化抵押品组合，其中一种抵押品类型崩溃的负面影响将会减轻，这取决于该抵押品类型的 CDP 在 Dai 总供应量中所占的比例。从循环中取出的 Dai 量和发生的任何标记物稀释都将成比例地减少。

多抵押品 Dai 协议将如何整合不同类型的 CDP 抵押品？每种资产都有独特的风险和波动情况，为了应对这种变化，不同类型的 CDP 中允许有不同的资产。这些类型的不同之处在于它们锁定的抵押品或它们的其他属性——创客团队将这些称为“风险参数”,它们是:

1.  债务上限——对可从 CDP 类型中集体发行多少 Dai 的限制，以确保 Dai 的抵押支持多样化
2.  清偿率——如上所述
3.  稳定费——即利率
4.  惩罚比率—对自动清算的惩罚

风险参数通过治理设定，MKR 持有者可以投票决定增加或修改 CDP 类型。

鉴于 CDP 类型的资产属性，Maker 计划开发通用风险框架，系统地确定 CDP 类型的风险参数，并使用这些框架作为设定新 CDP 类型风险参数的指南。新的 CDP 类型可以包含新的担保品类型，也可以包含现有担保品类型的一组新的风险参数。

# 全球结算

全球结算是一种帮助稳定 Dai 的紧急关闭功能。它通过冻结创客平台和停止交易来做到这一点。

当全球结算发生时，用户可以要求他们的 CDP 抵押品份额。Dai 持有人享有优先权——他们可以为他们拥有的每一个 Dai 要求价值 1 美元的抵押品——而剩余的抵押品(如果有的话)将返还给 CDP 持有人。用户对其抵押品的要求没有时间限制，但他们获得的金额在全球结算中是固定的；该系统使用结算时抵押品的市场价格来计算每个用户获得多少抵押品。

在 Dai 1.0 中，属于创客基金会的 multisig 钱包拥有触发全球结算的能力。在成熟的系统中，触发是分散的，由选举产生的委员会(“全球定居者”)控制。MKR 持有者投票决定谁成为全球定居者，以及触发定居所需的全球定居者人数。

全球结算有几个目的。升级系统时，结算关闭旧版本。例如，当多抵押品 Dai 启动时，Dai 1.0 将触发全球结算。结算还可以防止或限制紧急漏洞和攻击造成的损害，如漏洞、黑客攻击、安全漏洞、治理攻击、价格馈送攻击和“非理性”市场行为。在第二部分中，我们将深入探讨全球结算如何作为戴的稳定机制。

# 代币与戴之治

是戴的股权和治理令牌；它的功能类似于戴体系中的股权。像股票持有者一样，MKR 持有者因持有 MKR 而获得回报。他们从 CDP 债务和自动清算费用中获得利息。(“付费”在这里被广泛使用——利息和费用被用来购买和燃烧 MKR 代币，这是为了提高 MKR 的单位价格，假设稳定的市值。)然而，它们也承担了下行风险。

正如我们在上面的自动清算部分看到的，如果 CDP 自动清算，系统无法筹集足够的 Dai 来支付 CDP 的债务，那么 MKR 持有者必须通过 MKR 稀释来支付:系统自动铸造新的 MKR 代币并出售它们，以回收足够的 Dai 来支付全部未偿债务。

与股东一样，股东对戴拥有决策权。在成熟的系统中，他们可以投票支持改变协议中某些变量的提案。投票可以添加或修改 CDP 类型，并选举全球定居者。在戴的甲骨文系统中，持有人的治理尤为重要。

对于某些操作，如自动清算和全球结算，Dai 协议需要知道 Dai 和附属资产的市场价格。它通过一个价格 feed 获得市场价格，这个价格 feed 是由一组可信的以太坊账户提供的，这些账户就像神谕一样。MKR 持有者投票决定信托账户。他们还投票设置一个参数，该参数确定 oracles 在给定时间内可以改变协议对 Dai 和抵押品市场价格的内部估计的最大数量(“价格反馈敏感度参数”)。这个参数是一个安全预防措施，意在给全球定居者时间来冻结系统，以防甲骨文的攻击，以免造成太大的损害。

MKR 持有者通过定期选举执政。任何以太坊地址都可以提出一个提案进行投票，其中一个提案是一个智能合同，旨在改变系统的可管理变量。当选举发生时，拥有大多数 MKR 投票的提议被赋予根访问权，并被允许对系统执行其提议的更改。这意味着每次定期选举只能发生一次变化。

# 2.戴的稳定机制

尽管有抵押品支持，但 Dai 并不因为可直接*赎回该抵押品而稳定。相反，Maker 打算通过其他稳定机制来稳定 Dai。我们将在这里检查和回顾这些机制。令人惊讶的是，我们将证明戴缺乏强有力的稳定机制。*

# 戴是不可救赎的

令人惊讶的是，你无法用支持它的抵押品赎回戴。你不能从 Dai 系统用 1 美元抵押品赎回 1 Dai，也不能从系统用 1 美元买入 1 Dai。

这是令人惊讶的，因为人们会认为戴的价格会稳定在与挂钩的水平，因为你可以随时用它来换取支持它的抵押品。这是一个合理的假设。通常，抵押品支持的硬币是可赎回的抵押品。你可以去发行者那里，以挂钩价值兑换硬币作为抵押品。也可以买硬币做抵押。例如，有链外货币抵押品支持的 stablecoin [TrueUSD](https://www.trusttoken.com/trueusd/) 是可赎回的；您可以随时从平台兑换 1 美元的 TrueUSD。您也可以向 TrueUSD 平台发送 1 美元，并收到 1 个 TrueUSD 作为回报。

可赎回性是保持抵押品支持的货币稳定的因素。这是一种强有力的稳定机制，因为每当一种可兑换货币的市场价格远远偏离联系汇率时，套利者就从以“折扣”购买该货币并以抵押品的全部价值进行交易中获利，从而使市场价格回到联系汇率。如果市场价格高于挂钩价格，它们可以以挂钩价格从发行者手中买入，并在市场上卖出获利，从而使市场价格回落。只要交易费用足够低，市场流动性足够高，套利者就会将可赎回货币与美元挂钩。(点击阅读更多关于货币挂钩如何运作的信息[。)](/reserve-currency/why-another-stablecoin-866f774afede)

然而戴是不可挽回的。由于不可挽回，戴需要另一种方式来保持稳定。四种不同的机制旨在促进戴的稳定:

1.  *全球结算预期:*当全球结算发生时，Dai 持有人有权获得以抵押品形式支付的 Dai 目标价值。对未来可能的全球结算的预期将当前价格推向盯住汇率。
2.  *稳定投机:*当投机者预期价格最终会回到联系汇率时，如果他们不采取行动，他们会重新与戴挂钩。
3.  *CDP 创建者的价格操纵:*在钉住汇率制度下发行 Dai 的人有动机在价格低于他们开立 CDP 时回购 Dai，从而帮助抬高价格。当戴的交易价格高于挂钩汇率时，CDP 的创建者可能希望创建更多的 CDP。此外，CDP 可以自动清算，这将给 Dai 的价格带来上行压力。
4.  【链外稳定:】创客可能利用销售的链外资金，通过各种手段稳定价格，有效维持戴的买卖壁。

在对戴的稳定机制进行评估之前，让我们先深入了解一下。

# 全球定居稳定

全球结算通过市场的预期稳定戴。其实现方式有点复杂，因此我们将分两个阶段来介绍。首先，当全球结算迫在眉睫时，Dai 是如何稳定的，然后，当结算发生在未知的未来时间时，Dai 是如何稳定的。

迫在眉睫的全球结算有效稳定戴。在结算时，每个 Dai 持有人都有权获得其 Dai 的挂钩价值(1 美元)作为抵押品。如果你以低于挂钩价的价格买入戴，只要有足够的剩余抵押品，你就能从结算中获利。另一方面，如果你在挂钩价以上买入，你就亏损了。因此，当全球结算迫在眉睫时，就有一种强烈的动机来尽可能接近盯住价值地交易戴。

如果有些人，不管出于什么原因，决定放弃戴，他们将无法改变价格。知道即将结算的投机者将注入资金，从差价中获利，使市场价格与汇率挂钩。

在未来未知时间点发生的全球结算以同样的方式稳定戴，但提供的稳定性不如即将发生的结算。由于货币的时间贴现，稳定效应降低了:拥有同样数量的货币越早越好。因此，结算的距离越远，你在交易中的损失就越少。未来结算的稳定能力与预期的未来时间成比例减少。

一般来说，全球结算何时或是否会发生是无法确定的。尽管如此，市场将对全球结算在未来不同时间发生的概率有所预期。这些预期将通过与即将结算和已知的未来结算相同的机制稳定戴。如果市场预期未来有公平的结算机会，并且相信在结算发生之前担保品的价值不会下跌太多，人们将避免进行太远离挂钩的交易，并且投机者将在价格远离挂钩时低买高卖，预期从未来结算中获利。这两种交易行为都会把戴引向钉住，当它脱离钉住的时候。

这让我们想到了市场会有什么样的预期。由于定居是由 MKR 持有者选举的全球定居者引发的，因此对定居概率的预期将反过来由市场对全球定居者行为和 MKR 选举的预期决定。这种设置导致了很多不确定性。选举以及当选代表的行为是不完全可预测的。尽管如此，对全球定居者的行为还是会有一些理性的预期。定居者可能会根据他们的代表，MKR 持有者的激励行动，MKR 的很大一部分将由创造者的基金会以及他们网络中的人和他们可以影响的人持有。Maker 的白皮书说，结算是为了保证戴的钉住价格的最后手段。这大概意味着，如果戴汇率在太长的时间里脱离联系汇率制度太远，他们将会调整汇率制度。他们还举例说明了应该发生结算的其他情况:“[长期市场非理性、黑客攻击或安全漏洞，以及系统升级。](https://makerdao.com/whitepaper/DaiDec17WP.pdf)“这些承诺可能会部分决定市场对定居者行为的预期，从而决定全球定居点稳定的力度。

总之，当市场预期全球结算是一个更罕见、更不紧迫的事件时，它的稳定作用就更弱。相反，当预计定居将更加频繁和紧迫时，稳定效果更强。

这种稳定机制是有问题的，因为它既相当间接又相当有限。由于和解不受智能合同中实施的明确规则的约束，而是取决于选举和人类行为，因此除了特殊情况外，和解何时以及是否会发生存在很大的不确定性。这一机制或许能够防止戴汇率过于偏离联系汇率，但不清楚它能在多大程度上降低更接近联系汇率的波动性。

# 稳定投机

稳定投机并不是戴独有的——每一种功能性稳定币在某种程度上都有。为了完整起见，我们在这里简单地介绍一下，因为理解稳定投机可以更容易地通过 CDP 用户和全球结算的预期来理解戴的稳定。

稳定投机有两种形式:向下稳定和向上稳定。向下稳定是通过钉住汇率投机者在当前市场价格高于钉住汇率时卖出他们先前购买的戴，或通过借入戴来卖空它来实现的。

向上稳定是通过投机者在戴低于联系汇率时买入，在价格回到联系汇率时卖出获利来实现的。

请注意，稳定投机需要投机者对市场价格回归联系汇率有信心，而不受投机者自身行为的影响。因此，这是一个二阶稳定机制:要让它发挥作用，必须有一些其他机制确保(或使其有可能)价格在未来回到盯住汇率制。稳定投机不可能是戴的主要稳定方法。因此，这一机制能否发挥作用取决于戴的其他稳定机制。

# CDP 创建者的价格操纵

# 当戴倒下的时候

当债务凭证价格低于挂钩价格时，持有未偿债务凭证的 CDP 用户可以通过廉价偿还债务凭证债务而获利。任何在联系汇率制下发行 Dai 的人(然后在其他资产中以≈1 美元的价格出售他们的 Dai ),将能够以市场价购买 Dai，并以低于他们最初出售贷款 Dai 所获得的价格偿还债务。由于市场价格低于挂钩价格，他们得到折扣，从而获利，只要折扣大于交易费和他们的戴债务的累计利息。

具体来说:Alice 打开一个 CDP，向自己发行 1 个 Dai，然后以 1 美元的价格出售它。Dai 后来以 0.90 美元交易，因此 Alice 以 0.90 美元购买 1 个 Dai，偿还她的债务，取回她的抵押品，并获得 0.10 美元的自由利润。(这忽略了交易费用和利息，但是你明白了。)

当用户回购戴以偿还债务时，他们抬高了市场价格。从表面上看，这似乎是一个很有前途的直接挂钩机制！然而，随着我们深入思考，我们发现了两个问题:

**首先:**如果 CDP 持有人之前希望锁定抵押品并从戴获得贷款，然后因为他们能够以折扣价购买戴而关闭了他们的 CDP，那么一旦戴回到联系汇率制，他们的下一个行动似乎很可能是重新开放另一个 CDP——想必无论他们要求头寸保持不变的理由是什么。如果这种情况同时发生在许多 CDP 持有者身上，那么可能是大约相同数量的 Dai 从流通中取出，然后稍晚一点又回到流通中。这将对 Dai 价格产生相同但相反的影响。可能会出现某种均衡，当作为稳定货币的需求呈下降趋势时，戴的交易价格略低于挂钩汇率，当挂钩汇率呈上升趋势时，戴的交易价格略高于挂钩汇率。很难对如此具体的事情做出预测，所以我们认为只有时间才能告诉我们这种特殊的激励模式会如何发展。

**第二:**注意，这种交易并不完全取决于戴市价是否低于钉住汇率。这取决于市场价格低于您最初发行 Dai 时的 Dai 价格。所以只要戴下跌，你就可以做这笔交易并获利。

这意味着这种机制不仅仅激励 CDP 用户在 Dai 低于钉住汇率*时回购 Dai；每当 Dai 价格比你发行 Dai 时的价格低时，它就鼓励回购 Dai。*

*这种机制不是在联系汇率上创造对 Dai *的需求(这将有助于其稳定于联系汇率),而是在围绕*联系汇率的不同价值*上创造需求，在这些价值上，用户打开他们的 CDP 并发行 Dai。**

*如果 Dai 面临波动，其价格开始波动，而人们继续使用 CDP 发行 Dai，这一机制不仅没有多大帮助，而且在最糟糕的情况下，实际上可能会导致波动加剧。*

*这里的另一个细节是，在某种意义上，这种稳定机制是次要的，就像做市一样:如果 CDP 持有人认为 Dai 的价格将继续下跌，他们可能希望等待更长时间来回购 Dai 并偿还贷款，以获得更多利润。因此，如果市场出于某种原因认为戴的价格将会暴跌，这种机制可能没有任何帮助。*

# *戴崛起的时候*

*假设戴的股价大大高于挂钩汇率，比如说 1.20 美元。对于任何认为金价将回到 1.00 美元的人来说，都有理由开设 CDP 来做空戴。Bob 锁定一些抵押品，借 1 Dai，以 1.20 美元卖出。如果戴跌到 1.00 美元，他回购戴，偿还他的贷款，取回他的抵押品，并免费获得 0.20 美元。*

*当我们意识到这一点时，我们很兴奋，并认为我们可能已经驳斥了对 Dai 的需求和对 CDP 的需求最终是脱钩的说法(这就是我们担心 Dai 将无法扩展的原因，对 Dai 的更多需求不会导致更多 Dai 流通)。我们认为“啊，任何时候戴高于挂钩，实际上都有额外的激励来创建 CDP 和发行戴，因此戴可能会根据需求进行扩展！”*

*但经过进一步研究，我们认为事情不会这样发展。假设一群人在这种情况下创建 CDP，计划做空戴。他们在二级市场发行和出售 10 万戴，价格开始下跌。一旦 Dai 的价格跌至他们认为的最低点(可能是 1 美元，或其他更高或更低的价格)，为了赚取利润，他们将不得不*回购*这 100，000 个 Dai，使 Dai 的供应量回到原来的位置，并可能使价格回到原来的位置。因此，我们再次看到当价格下降时发生的同样类型的振荡激励——任何时候对 CDP 的人为需求是由戴价格上升产生的，我们预计它是*一种暂时的需求，而不是持久的*。*

*与当 Dai 价格下跌时平仓 CDP 的动机类似，1.00 美元的目标价不一定具有任何特殊意义——重要的是投机者认为价格在不久的将来会走向哪里。如果 Dai 的价格是 1.20 美元，他们预计它很快会涨到 1.30 美元，那么现在就没有动力为此目的开设 CDP，如果他们已经开设了 CDP，而价格正在跌破 1.00 美元，如果他们认为价格将继续下跌，他们可能会继续开设 CDP。这就是为什么我们担心这些激励措施实际上会导致*增加波动性*，尤其是在动荡的市场条件下。*

# *链外稳定*

*早期，创客团队可能会通过链外资金稳定戴。由于代币目前的[市值](https://coinmarketcap.com/currencies/maker/)比[戴](https://coinmarketcap.com/currencies/dai/) **，**大得多，他们可以抛售代币，产生大于戴总市值的链外储备。然后，该团队可以使用储备以各种方式稳定戴。*

*在交易所里，他们可以在挂钩的正下方(买入墙)设置大额买入订单，在挂钩的正上方(卖出墙)设置大额卖出订单。或者，他们可以向以更动态的方式捍卫盯住政策的机器人提供大量资金。他们还可以利用这些资金进行动态做市，以确保 Dai 市场在任何交易价格下都有足够的流动性。*

*然而，离链稳定不能作为戴的主要稳定手段。首先，依赖链外资金作为主要的稳定机制使得戴集中化，就像信誉扫地的 stablecoin [Tether](https://tether.to/) 一样。它将面临与其他集中式数字货币相同的问题，包括交易对手风险，以及被政府冻结或查封账户的风险。第二，使用链外资金进行稳定并不具有规模效应。如果戴是成功的，并增长到一个非常大的市值，该团队可能最终没有足够的剩余 MKR 代币来提供足够的链外储备。*

*戴目前的情况可能是链外稳定。戴的创始人 Rune】指出一月份的短暂崩溃是由他们的一个做市商机器人故障引起的。目前还不清楚这个机器人使用的是什么资金来源。*

*Maker 潜在进行链外稳定的一个非常重要的含义是*peg 早期的行为并不能很好地预测 peg 随后的行为*。这种评估上的困难适用于任何一开始就有能力筹集和花费链外资金的 stablecoin 项目。*

# *戴稳定机制的综合作用*

*Dai 的稳定机制提供了一些稳定性，但是没有一个机制同时是强大的和可扩展的:如上所述，全球沉降预期仅提供有限的稳定性。CDP 用户可以在一定程度上稳定戴的价格上下波动，但该机制取决于价格朝着已经需要稳定的方向移动的假设，并且似乎产生振荡力。链外稳定是一个强有力的选择，但只能在早期进行，因为它不可扩展。最后，稳定投机只有在存在另一种提供长期稳定的机制的情况下才能发挥作用，因此，除非已经存在某种其他强有力的机制，否则这不会是一种强有力的稳定机制。*

*我们可以得出结论，尽管戴有分散抵押品的支持，但一旦戴不再得到链外资金的支持，它就缺乏强大的稳定机制。一旦它变得足够大，使得链外稳定不起作用，该方案可能能够使 Dai 不至于太远离挂钩，但是，在接近挂钩时，我们预计 Dai 将变得不稳定。当然，真正的黑天鹅事件总是存在风险，导致基础抵押品非常快速地贬值，在全球结算可以进行之前，支持低于 1:1，戴持有人没有得到完整。*

# *3.戴评估道*

*既然我们已经了解了 Dai 的组成部分，也看到了 Dai 系统的稳定机制，我们就可以对 Dai 作为稳定硬币的前景进行总体评估了。对稳定的加密货币的需求可能会非常大。只有具备足够的可扩展性和足够的稳定性，才能满足如此大的需求。戴是否充分满足这些要求？*

# *可伸缩性:Dai 的可伸缩性不好*

*对稳定的硬币有很大的潜在需求。Dai 的供应量不大可能增长到足以满足稳定货币需求的一小部分以上，因为它可能永远不会有足够的 CDP 支持抵押品来满足稳定货币的需求。*

*Dai 由通过 CDP 承担债务的用户发行，因此 Dai 的供应量仅限于 CDP 用户拥有的债务总额。因此，如果 CDP 中锁定的抵押品不足，就没有足够的 Dai 来流通。*

*两个因素限制了道在 CDP 中获得足够抵押品的能力。首先，对 CDP 债务的需求有限。此外，为了保持良好的抵押品多样化，制造商将不得不设定 CDP 债务使用者可以接受的限度，这可能比自然需求还要低。让我们依次看看这两个因素。*

# *CDP 需求有限*

*Maker 计划通过提供广泛抵押品类型的 CDP 来获得足够的 CDP 使用量。目前，在单抵押品 Dai 中，仅接受 ETH 作为抵押品，但在成熟版本中，也将接受许多其他易变的加密货币。例如，Maker 已经决定[在成熟版本中包含 OMG CDPs](/makerdao/makerdao-and-omisego-announcing-dai-and-omg-collaborations-23600a080046) 。*

*除了易变令牌，Maker 还计划为代表资产的令牌提供 CDP，即所谓的“令牌化资产”。例如，创客将接受[新推出的 Digix 代币](https://digix.global/)作为抵押品。每个 Digix 代币代表一定数量的黄金，可以从代币发行公司兑换实物黄金。除了黄金之外，Maker 还希望提供各种其他令牌化资产的 CDP，如其他贵金属、股票和债券。这个想法是，令牌化资产中的抵押债务可以像资产本身中的抵押债务一样发挥作用。*

*如果其他方将资产令牌化，然后制造商为这些资产提供 CDP，那么制造商或许能够进入规模大得多的传统贷款市场。*

*我们感觉这不太可能行得通。*

*为什么？抵押债务有两个主要的使用案例:杠杆和用你的投资作为抵押品借钱来获得短期流动性。在这两种情况中，[更常见的用例是杠杆](https://www.reddit.com/r/MakerDAO/comments/8ckvib/who_here_has_created_a_cdp_why_did_you_do_it_and/)。我们担心，无论是加密的 CDP 还是令牌化传统资产的 CDP，制造商都可能无法抓住足够的杠杆需求。*

*制造商在传统贷款市场接触消费者的能力受到三大挑战的困扰。*

*第一个挑战是没有人设法将相关传统资产令牌化的风险。Maker 只能在广泛的链外资产中提供 CDP，前提是将这些资产令牌化的努力成功。尽管有成熟的黄金资产令牌化项目，如 Digix 和 Royal Mint Gold，但何时会有股票、债券或其他商品的可靠令牌化尚不清楚。虽然存在非零风险，即我们需要数年时间才能对传统资产进行可信的令牌化，但对这些资产类别进行令牌化没有不可逾越的障碍，许多人正在为此努力。因此，Maker 在这里获得了一个通行证。*

*第二个挑战是，传统资产类型需要足够的杠杆需求。大多数传统的贷款市场都是难以标记的资产，比如房地产。对更容易标记的资产，如贵金属以及股票和债券等证券的杠杆需求较低。尽管如此，可能有足够的需求让制造商能够扩大规模，所以我们也会让他们通过这个挑战。*

*最后是竞争。这是创客的关键问题。即使令牌化努力取得成功*和*，对可令牌化传统资产中的抵押债务有大量需求，制造商也只能通过克服来自其他抵押债务发行者的竞争来抓住这一巨大需求。*

*传统资产的债务市场已经是成熟的市场。这些市场中现有的杠杆需求，尤其是来自机构投资者的最大需求，已经被与这些大客户有着稳固关系的大型金融公司所捕获。因此，制造商在进入这个市场时面临着非常艰巨的销售挑战。我们认为，如果创客占领了这些市场，这将意味着其他人无法提供这么低的利率，借款人愿意采取非常规的措施来省钱，包括足够详细地了解创客平台，以信任这些机制。也许他们中的一些人会在这样的旅程中遇到这个帖子！如果是您，请联系我们，我们会告诉您:)*

*也许 Maker 已经退出了传统借贷市场的竞争，但是加密市场呢？*

*Maker 最初的 CDP 用例，在加密领域，看起来很有前途。在拥有加密资产的人中，相当一部分人可能希望利用他们的资产。不仅如此，没有令牌化的挑战，也没有大的根深蒂固的竞争对手提供加密杠杆。*

*不幸的是，加密世界提出了一个不同的挑战——规模。今天的密码市场太小了。截至今天，coinmarketcap 上市的所有加密资产的总市值不到 3000 亿美元。让我们假设 Maker 的成熟版本将能够包括覆盖 50%加密市值的加密资产的 CDP 类型，并且人们希望将所有令牌的 10%放在 CDP 中。然后，以 300%的平均抵押率，他们将拥有 33 亿美元的最大 Dai 市值。这几乎不足以满足 Tether 目前在加密市场的使用情况，也远远不能提供一种可以用作全球货币的稳定货币。*

*未来，加密资产市值可能会增长得更大。我们认为，只有当它们成为广泛使用的价值储存手段或交换手段时，这种情况才有可能发生。我们相信这些用途，而不是 dApps，是未来潜在加密资产价值的主要来源。现在我们可以看到这种情况发生的两种方式:*

1.  *BTC 和类似的加密资产成为一种更加重要的价值储存手段*
2.  *一枚(或几枚)稳定的硬币成为一种实质性的交换手段(或许它们相关的共享代币成为价值储存手段)*

*在我们看来，戴本身无法扮演主要稳定核心的角色，除非#1 先实现*。*换句话说，戴的崛起看起来只能是巧合，或者是在其他不稳定的加密资产作为价值储存手段崛起之后。然后，最流行的对这些资产进行保证金交易的方法就是制造商 CDP。*

*让我们通过观察戴目前的 ETH CDPs 在竞争中的表现，来看看戴能从加密领域的 CDP 用户中获得多大的份额。ETH 杠杆的竞争环境应该与其他易变加密资产中杠杆的竞争环境足够相似，以便让我们了解制造商如何在加密领域捕捉抵押债务需求。除了让我们对戴的未来有所了解之外，这也更具体地展示了竞争问题是如何发生的。*

*加密贷款市场不如传统的链外资产贷款市场成熟。尽管如此，该公司仍面临一些竞争对手。主要的密码交易所提供杠杆交易。加密期货合约越来越受欢迎。还有像[盐贷](https://www.saltlending.com/)这样的点对点借贷服务。*

*与现有的加密杠杆竞争相比，Maker 有两个主要优势。首先，连锁贷款的交易对手风险低。这让 Maker 相对于一些存在重大交易对手风险的交易所而言具有优势。低交易对手风险确保了制造商不会偷窃、误用、错误分配或只是简单地损失资产(假设没有错误)。第二，戴的利率很低。目前，戴借款人每年只需支付 0.5%的利息，这使得借款成本很低。*

*另一方面，与竞争对手相比，Maker 也有一些劣势:较低的最大杠杆、缺乏标准的保证金要求以及自动平仓处罚。*

*制造商提供的杠杆比竞争对手低。您可以通过制造商的 ETH CDPs 获得高达 1.67 倍的杠杆，但例如， [Bitmex](https://www.bitmex.com/) **，**提供高达 100 倍的杠杆。不仅如此，Maker 对借款人的惩罚要比普通机构严重得多。在传统机构中，借款人通常会被要求追加保证金。当用户的抵押品与债务比率下降到某个限额以下，但在机构清算用户的抵押品之前，机构会要求用户提供更多资本。相比之下，Maker 不提供任何此类追加保证金通知。相反，当抵押品价值低于最低抵押比率时，Maker 会自动清算用户的 CDP。Maker 团队告诉我们，他们打算在自动清算之前增加一个短暂的延迟，这将允许用户建立自己的保证金追缴系统。这似乎是一个很好的妥协。但该平台仍有前述 13%的自动清算罚款，这使得 CDP 比其他一些抵押债务提供商更具风险。*

*考虑到这一点，戴可以在加密领域占领哪些债券市场？短期交易者更关心他们能获得多高的杠杆，而不是他们支付的年利率，所以他们不会使用 Maker。在其他市场，如股票市场，短线交易者[可能是杠杆使用者的最大类别](https://www.bostonfed.org/-/media/Documents/neer/neer401a.pdf)，所以这对 Maker 来说是一个很大的损失。*

*另一个重要的细分市场是机构投资者。他们经常可以在著名的密码交易所免费获得杠杆作用。鉴于这种选择的简单性，以及这些机构习惯于信任其他市场中的交易对手这一事实，在我们看来，做市商较低的交易对手风险不太可能成为机构选择做市商而非这些交易所的令人信服的理由。*

*这就是为什么我们认为戴很难从交易商或大投资者那里获得大量加密 CDP 需求。在我们看来，Maker 面临的是不想要高杠杆的长期散户投资者的潜在抵押债务需求。但即使在这些群体中，创客也将面临来自链上点对点贷款和其他基于抵押品的稳定信贷的竞争，例如像 [Sweetbridge](https://sweetbridge.com/) 这样的供应链稳定信贷。为了赢得稳定的货币，制造商需要从上述来源建立一个非常大的 CDP 市场，同时与其他债务提供商竞争。这看起来并非不可能，但也不乐观。*

*在这一论点的另一面，对 Dai 的需求与对 CDP 的需求脱钩这一事实可能有一个好处。假设需要一段时间来让市场了解稳定硬币的存在，并让所有市场都开始使用它们。当他们决定这样做时，他们将如何选择从众多稳定的硬币中挑选呢？他们可能使用的一个简单的衡量标准是:哪个供应商最多(因此显然已经得到了许多其他供应商的信任)？如果 CDP 的需求超过有机稳定币的需求，戴可能会在这个关键时刻超过其他稳定币的市值。不过目前，true USD T3 在市值和交易量上都领先戴 T5。*

*制造商可以通过向 CDP 持有者提供负利率来增加 CDP 需求，从而避免这个问题。换句话说，以某种方式付钱给 CDP 持有者，让他们开更多的 CDP。当我们向团队展示了我们的草案后，我们与他们讨论了这部分评论，这是他们关于如何处理问题的建议。*

*为了支付 CDP 持有者，资金必须来自某个地方。据我们所知，可能有两个来源:MKR 持有者或戴持有者。以下是我们可以想象这种情况发生的方式:*

1.  *在 MKR，CDP 持有者不需要为 CDP 支付大量利息，而在新成立的 MKR，他们可以获得稳定的利率。这将激励 CDP 的创建和持有。但这将稳步稀释和降低 MKR 代币的价值，因此可能不是一个可行的选择。*
2.  *当 CDP 关闭时，CDP 持有人可以被收取低于 100%的 Dai，这样即使 CDP 中的基础抵押品的价值没有变化，他们也可以获得利润。然而，这将使创客生态系统中的一些 Dai 得不到支持，因为当 CDP 关闭时，Dai 中没有被烧掉的部分将只是留在流通中，没有抵押品被锁定来支持它。这可能最终导致系统被欠抵押，我们不希望制造商团队会容忍这种情况。*
3.  *Dai 持有人可能会因持有 Dai 而被征收直接税。与您的钥匙相关的 Dai 数量会随着时间的推移而下降，而支付给 CDP 持有人以开立更多 CDP 的合约余额会相应增加。这也可以通过交易费来实现。这似乎是我们能想出的三条路线中最有希望的一条。问题是:这些税应该是多少？它们最终会在相对较低的水平上刺激足够多的 CDP 创造吗？还是它们必须足够高，才能让 Dai 需求大幅下降？如果是后者，那么这仍然可以解决 CDP 和 Dai 需求匹配的问题，但主要是通过减少 Dai 需求而不是增加 CDP 需求，这并不是我们真正想要的结果。*

*我们认为，最终结果与没有负利率的情况相同:戴的市值和扩张潜力最终受到对 CDP 的自然需求的限制。*

# *多元化进一步限制了戴的供应*

*除了 CDP 需求有限，还有第二个因素限制了戴的供应:戴将不得不放弃 CDP 的需求，以保持其抵押品的多样化。*

*戴需要多样化的抵押品。这就是从单络戴向多络戴转变的原因。没有多元化，戴对黑天鹅事件更加敏感。*

*适当的多元化不仅要求戴拥有多种 CDP 类型，还要求每种 CDP 类型都有适当比例的总抵押品锁定在其中。例如，假设戴担保资产的理想投资组合多样化是 25%的 ETH、10%的 OMG 代币、15%的其他波动性代币、25%的代币化黄金和 25%的代币化白银。为了实现良好的多元化，戴需要用户在每种资产类型中锁定适当数量的抵押品。*

*戴面临的问题是，用户对 CDP 类型债券的需求不太可能反映出戴在多元化投资组合中应该拥有的资产比例。戴平衡其多元化的唯一方法是限制每种 CDP 类型允许的总抵押品比例。他们[明确地谈到为这个确切的目的设定相对的“债务上限”](https://www.reddit.com/r/MakerDAO/comments/8biua7/some_criticisms_of_makerdaos_multicollateral/dx8uyvl/)。*

*例如，假设 45%的 CDP 债务需求用于 ETH，15%用于 OMG，30%用于其他易变代币，只有 5%的需求用于代币化黄金，5%的需求用于代币化白银。那么，制造商只需将 ETH 限制在 25%，OMG 限制在 15%，其他易变令牌限制在总抵押品的 25%，就可以实现理想的多元化。这确保了 25%的资产是令牌化的黄金，25%的令牌是令牌化的白银。限制 CDP 类型使戴的抵押品组合非常多样化。然而，这是有代价的。在我们的示例中，戴的 CDP 抵押品总额减少了 80%，戴的最大供应量也减少了 80%。*

*因此，Maker 面临一个困境:**他们必须要么牺牲戴的弹性，要么牺牲它的可伸缩性**。由于 Maker 通常会做出最负责任的设计选择，因此 Maker 很可能会选择牺牲可伸缩性，以尽量降低用户损失金钱的风险。*

*如果大量 CDP 需求是针对传统加密资产中的 CDP，而不是令牌化资产中的 CDP，这种困境会变得更糟。这是因为不稳定的加密资产的价格[与](https://www.sifrdata.com/cryptocurrency-correlation-matrix/)高度相关。出于分散投资的目的，所有的加密资产都应被视为相同的资产。因此，制造商需要平衡所有不稳定的加密资产抵押品与不相关资产中其他抵押品的可比金额。为此，他们需要对 CDP 债务用户可以在不稳定的加密资产中持有多少进行限制性限制。*

*多元化需求在多大程度上降低了戴的最大供给，取决于制造商的选择，以及对 CDP 类型的实际需求与期望的多元化投资组合的匹配程度。*

*这给戴带来了什么？我们认为，该制造商在克服竞争以产生足够大的 CDP 需求方面的问题，以及在获取大量需求的同时仍使其抵押品支持多样化的困难，使得戴不太可能扩大规模，足以成为一种全球货币。*

*当然，也有一些机会让创客走运，戴通过某种方式抓住了传统债券市场的很大一部分，从而扩大了规模。如果是的话，那么对戴的评估将取决于它的稳定性。接下来我们来看看戴在这个领域的表现。*

# *稳定性:做市商系统如何处理连带崩溃*

*Maker DAO 系统的一个常见问题是，它是否能够处理大型附带崩溃。毕竟，加密货币非常不稳定，有时会严重崩溃。一场足够严重的崩盘可能会导致戴的抵押不足，跌破钉住汇率，并让戴的持有者损失资本。然而，戴比看上去更能抵御危机。首先，我们将说明原因——有三个原因——然后我们将评估剩余的危险。*

*由于中国金融体系目前的抵押品债务比率为 150%，乍一看，如果崩盘导致抵押品价值缩水三分之一以上，戴可能会成为抵押不足。然而，大多数关于创客的讨论都忽略了三个因素，这三个因素让戴比乍看上去更有弹性。*

*首先，对 CDP 持有者来说，自动清算真的很糟糕。除了他们的抵押品损失大量价值，他们还必须支付 13%的罚款。这一处罚强烈激励 CDP 持有者尽其所能避免自动清算。(我们只是在做了一些，并从内心感受到激励之后，才真正开始做这个的！)如前所述，CDP 用户将希望在其 CDP 的抵押比率和清算比率之间有一个像样的缓冲。此外，用户被激励跟踪他们的抵押品价值何时下降，并在抵押价值下降时尽其所能提高他们的抵押率。他们可以通过购买和增加更多抵押品，或者通过收购戴来偿还债务。*

*第二，在 150%的清偿比率和戴在低于 100%的总支持下成为欠抵押的点之间有一个相当不错的缓冲。当单个 CDP 在崩盘中跌破 150%时，系统将试图立即自动清算它们，并让它们发行的所有 Dai 退出流通。正因为如此，即使在大崩盘时，戴也能保持 100%以上的抵押支持。只有当危机速度足够快，以至于系统无法在抵押资产崩溃之前关闭足够多的 CDP 并回购其 Dai，以至于整个系统的抵押率下降到 100%以下时，Dai 才会被抵押不足。*

*第三，创客希望成熟版的戴有多元化的抵押品支持。如果 Maker 的计划成功，并且 Dai 得到了几种不相关的加密资产的支持，那么 Dai 应该能够抵御崩溃。例如，说戴的抵押物有三个大小大致相等的不相关资产:加密货币、令牌化金属、令牌化股票。那么，即使其中一个抵押品组出现大规模、快速的抵押品崩溃，戴的抵押品支持也只会减少三分之一。在这种平均抵押率超过 150%的情况下，戴不应成为抵押不足，除非一个以上不相关的抵押品类型同时崩溃。*

*因此，我们应该期待 Dai 系统比看起来更能抵御崩溃。但并不完全如此。尽管创客打算让戴多元化经营，但尚不清楚他们是否会成功。与加密货币一样，许多其他资产类别在某种程度上也是相关的——例如，众所周知，经济衰退会同时冲击许多资产类别。*

*如果 Maker 不能成功实现戴抵押品支持的多元化，它就不会那么有弹性。即使是正常的连带崩溃也会给戴带来问题。如果没有多元化，崩盘将立刻冲击戴的大部分支持资产。这将迫使 CDP 自动清算，并可能使 Dai 供应的很大一部分退出流通，因为协议试图回购并烧掉清算的 CDP 的未偿 Dai。在加密资产崩盘期间，由于投机者急于将投资从不稳定资产转移到稳定资产，对稳定资产的需求将会增加。(这发生在[努比茨](/reserve-currency/the-end-of-a-stablecoin-the-case-of-nubits-dd1f0fb427a9)的案例中。)供给的减少加上需求的增加将把戴的价格推高到挂钩之上，并使其不适合稳定的货币用户，直到戴的供给恢复——如果你试图避免在崩盘中失去价值，你就不会想买一些肯定会下跌的东西。*

*历史上，其他加密支持的自动清算的 stablecoins 也有这个问题。例如，看看 [BitUSD](https://coinmarketcap.com/currencies/bitusd) 和 [BitCNY](https://coinmarketcap.com/currencies/bitusd) 的市场历史。两者都是 BitShares“BitAsset”**理念的实现，这是戴的灵感来源。正如我们在它们的市场历史中看到的那样，当它们的支持资产——比特股票——价值暴跌时，它们的价格就会升至挂钩价以上，供应量就会减少。***

***事情弄得*真的不好*对戴来说如果撞车是既大又迅速的。这就是为什么人们认为制造者对黑天鹅很敏感。在这样的崩溃中，无论是用户还是 Dai 协议都无法以足够快的速度回购足够多的未偿 Dai，以防止 Dai 的总体抵押率降至 100%以下，从而导致 Dai 抵押不足。***

***在 Dai 成为抵押不足之前，全球定居者可能会也可能不会触发全球结算。然而，即使他们设法在抵押不足发生之前解决，这也可能对戴用户没有太大帮助。结算处理后，用户有权获得 1 美元的担保品。但即使戴被和解，抵押品的价值仍将迅速下跌。Dai 用户可能很难从协议中提取他们的抵押品，并在抵押品价值下跌到他们无法为每个 Dai 获得价值 1 美元的抵押品之前在市场上出售。***

***总的来说，戴应该对正常的连带崩溃有相当的弹性。然而，如果戴没有很好地多元化，它可能不会，在某些情况下仍将是脆弱的。正常的崩溃会暂时限制戴作为稳定币的效用，但在真正糟糕的情况下，比如黑天鹅，戴持有者可能会损失相当一部分钱。***

# ***关于制造商、目标利率反馈机制和责任的最后思考***

***制造者是马厩里的老人。多年来，他们一直在应对稳定的挑战。***

***他们的系统随着他们解决问题和迭代改进他们的协议而不断发展。然而，他们并不总是表现出完美的判断力，而且要花很长时间来纠正造成的错误。***

***这种错误的一个例子是我们在第一节中提到的目标速率反馈机制(TRFM ),它是 Maker 以前计划包含在 Dai 的成熟版本中的，只是最近才撤回。***

***TRFM 在他们当前的白皮书中有突出的描述，但是现在它不再是 Dai 协议的一部分。然而，该代码将被保留，以使 MKR 持有者能够在未来将其作为治理决策的一部分进行投票。***

***这是不幸的，因为从过程控制理论的角度来看，TRFM 设计得很差。过程控制理论是控制过程的算法设计，如船舶的转向、汽车的巡航控制和化工厂部件的温度。在这种情况下，要控制的变量是戴的价格。TRFM 的设计很差，因为它的设置方式容易导致 Dai 价格波动。由于显而易见的原因，振荡对稳定的货币非常不利。***

***Maker 在削减 TRFM 上绝对做出了正确的决定。这很好，是朝着正确方向迈出的一步。削减 TRFM 不仅消除了一个潜在的有问题的机制，而且使 Dai 协议更容易理解。这种改进只是许多例子中的一个，在总的趋势中，创客团队缓慢、仔细、稳步地不断改进戴系统，不怕改变或承认错误。***

***这是值得称赞的。***

***然而，Maker 计划这么长时间将 TRFM 包含在 Dai 中是有问题的。它举例说明了不完美的判断，并证明了这样一个事实，即它需要制造商大量的时间来发展和改善他们的系统。这两种倾向都是 Maker 的特征，也是我们未来应该从他们身上期待的。***

***尽管 Maker 到目前为止是成功的，但这种消除错误的缓慢特征使我们认为 Maker 将无法跟上竞争日益激烈的稳定竞争。***

***不管未来会怎样，戴可能是目前最好的算法稳定币。此外，他们在设计中一直很负责，确保任何采用 Dai 的行为都不会产生重大的负面后果。为此，我们非常尊重创客团队。***

*************************************************************************

***如果你喜欢这篇文章，一定要鼓掌 50 次。更多内容，关注我们 [*中*](https://medium.com/reserve-currency) *，关注我们* [*推特*](https://twitter.com/reserveprotocol) *，关注我们* [*电报*](https://t.me/joinchat/FVW41g_ZPj0Hhg7db4M6BA) *，关注我们* [*脸书*](https://www.facebook.com/reserveprotocol/.) *。****