# 什么是安全令牌？关于它们如何工作及其影响的综合指南

> 原文：<https://medium.com/hackernoon/what-is-a-security-token-a-comprehensive-guide-to-how-they-work-and-their-impact-ef429a77a9c3>

![](img/3bf14f9a1aea29eb971546634ddcb980.png)

最近有很多关于安全令牌的讨论。但是什么是安全令牌呢？它们是如何工作的？它们会如何影响你的工作或业务？也就是说，*你*为什么要关心他们？

在深入这些问题的其余部分之前，首先从以下问题开始是有意义的:什么是安全性？

证券是一种持有一定货币价值的可替代(可互换)的金融工具。广义而言，证券可以分为权益类或债务类。

股权证券代表公司的所有权利益(如苹果股票)，债务证券代表借入的必须偿还的资金，因此持有人有权获得发行人对该债务的支付(如公司债券或美国国债)。

现在，什么是安全*令牌*？

# 什么是安全令牌？

*令牌化*证券是简单地用电子包装包裹起来的证券。

这种电子包装的价值在于，它使安全令牌更容易以符合适当法规的方式进行交易。像宝洁公司的股票这样的上市股票，没有太多的好处，因为它们已经具有很高的流动性:几乎任何人都可以在没有太大问题的情况下买卖它们。

你可能有一个很好的投资假设，认为街尾的公寓楼会升值，但你可能没有几百万美元来买它——如果你有，你最好真的有信心，因为它很难卖掉。因为它很难出售(也称为非流动性)，所以它的价值更低——也就是说，存在非流动性折扣(在学术文献中通常被引用为 20–30%)。

目前有大量资本愿意投资于流动性差的证券，如小型房地产投资信托基金(REITs ),它们可能拥有你或小企业所在街道上的公寓楼，但为了做到这一点，它们必须长期锁定资本。你可以投资一家披萨店，但如果你需要这笔钱做其他事情，就没办法拿出来了:你必须等到披萨店被出售或支付股息。

相比之下，如果你想买苹果公司的股票，你可以花几百美元买一股，如果你改变主意，可以在几分钟内卖掉。

令牌化证券允许企业锁定资金，而不会锁定投资者，因为令牌可以在二级市场上交易。企业仍然可以使用资本，但投资者可以相互交换股份。

以一家小型私人美国房地产投资信托基金为例:在基金层面和监管层面都有限制(例如，非美国人可以拥有不到 50%的股份，你必须拥有 100 名以上的投资者才能获得优惠的税收待遇，但少于 2000 人，否则你必须上市)。

实际上，这意味着如果任何投资者想与另一个投资者交易他们的房地产投资信托基金股票，他们必须打电话给基金经理以获得许可，他们必须找到一个想购买他们股票的人，他也已经被基金经理批准交易。

这比登录你的嘉信理财或 TD Ameritrade 账户要困难得多，所以今天像小型房地产投资信托基金和小企业股票这样的证券会遭遇流动性折扣。学术文献认为这种非流动性在 20-30%之间。因此，如果你拥有一家价值 100 万美元的小企业的 100%股权，将该公司的股权象征化会立即将价值提升至 120 万美元。[1]

非流动性折扣的主要原因，以及使用证券代币的好处，是这些证券可以在世界各地交易。投资者仍然很难购买其他国家的证券。

使用令牌，您可以将限制编码到令牌中，使买方和卖方能够在二级市场进行交易，只要交易不违反硬编码的限制。

至少在今天，创业公司的股权象征化可能没有那么有价值，因为大多数创业公司资金过剩，而不是资本紧张。

安全令牌化的一个可能的早期用例是本地企业或特许经营，它们有一个经过验证的概念和几个位置，但需要资本来扩展。目前，他们的筹资选择有限。拥有更具流动性的令牌化证券将让他们获得更好的融资条件，有可能消除 20-30%的非流动性折扣。

从广义上讲，您可以将安全令牌视为“合规性即服务”通过在令牌级别进行合规性编码，您可以实现更高效的市场，减少寻租中介。

![](img/e3a2474dbe9ccfe5b65b910bd1b72d51.png)

*如果你正在寻找更多关于区块链和商业加密货币的信息，* [***访问我的最佳加密货币资源列表***](http://bit.ly/2KqvNXr) ***。***

# 安全令牌到底是如何工作的？

在高层次上，几乎所有的安全令牌化公司都以相同的方式工作:发行者(如 REIT 基金经理或比萨饼店老板)发行代表公司所有权主张的安全令牌。然后，发行者创建一个允许购买公司股份的投资者钱包地址(通常是以太坊)的白名单。

白名单上的所有人都必须证明他们符合特定安全性的任何限制。至少，这可能涉及遵守 KYC(了解你的客户)和反洗钱(反洗钱)法律，以及证明他们是合格的投资者。

安全令牌协议不可能将数百个管辖区的法规融入其中，但通过限制哪些人可以拥有令牌，可以遵守绝大多数法规。这就是白名单的作用。

发行者也可以将白名单的监管外包给第三方提供商，包括集中交易所。然后，白名单充当“流动性池”，白名单上的每个人都可以相互交易，因为他们都经过了适当的审查。

发行人可能会允许第三方提供商对投资者进行 KYC/反洗钱和尽职调查，并将他们加入白名单，这样交易所就不必每次有人想交易他们的代币时都去找发行人。这应该会增加证券代币的流动性，使它们(以及它们所代表的基础证券)更有价值。

只要你的交易对手被列入白名单，你就可以在任何地方或以任何方式交易安全令牌。 [tZero](https://www.tzero.com/) 、 [Blocktrade](https://blocktrade.com/) 和 [Open Finance](https://www.openfinance.io/) 是个人和机构交易安全令牌的首批交易所。

会有公司冒出来帮助发行者将像 [Harbor](https://harbor.com/) 这样的证券令牌化。Harbor 作为一个 oracle 存在，它是一个验证“真实世界”数据并将其提交给区块链的代理。在这种情况下，数据是交易对手是否被列入白名单。

如果您试图与某个交易对手交易安全令牌，那么 pings 命令会确保他们被列入白名单。如果是的话，交易就成功了。如果没有，它会抛出一个错误信息，不允许你完成交易。

与大多数其他加密资产不同，安全令牌不是无记名工具。[比特币](https://taylorpearson.me/why-is-bitcoin-valuable/)是一种不记名工具，因为如果有人获得了你的比特币私钥，他们就可以花掉你的比特币。现金也是一种无记名工具——如果我掉了一张 10 美元的钞票，你捡起来，你可以花掉它，我没有任何办法阻止你。

你不能窃取安全令牌，因为令牌不是安全本身，它是一种电子表示。除非钱包被列入白名单，否则任何人都不能将代币转移到他们的钱包，在这种情况下，他们将通过 KYC/反洗钱系统，这样你就会知道他们是谁(然后，我想，你可以派人逮捕他们)。

证券代币的另一个好处是，它们将帮助私人公司在没有律师大军的情况下跟踪他们的限额表。在 2018 年，你可能会认为这已经电子化了，但它仍然主要是在纸上完成的，这令人惊讶地存在问题。

2013 年有一个[臭名昭著的混乱案例](https://www.bloomberg.com/view/articles/2017-02-17/dole-food-had-too-many-shares)，都乐食品认为他们有 36000 股流通股，但投资者认为他们拥有 49000 股。

![](img/28bbc8240b27d19316662d81c625c67c.png)

# 安全令牌的潜在影响是什么？

安全令牌的一个可能影响是，我们将需要更少的安全律师。起草“湿”纸合同的大量工作将被书写[“干”代码合同所取代。](http://unenumerated.blogspot.com/2006/11/wet-code-and-dry.html)

法律知识仍然是必要的，所以你可以想象，学会写智能合同的律师将是非常有价值的。

另一个可能受到影响的行业是资产管理。从历史上看，对资产管理公司来说，重要的是他们管理的资产(AUM)和他们产生的管理费。如果一个资产管理公司管理着 1 亿美元，他们收取其中的 1%作为管理费，那么不管他们投资得好不好，他们每年都能赚 100 万美元。

在一个由安全令牌主宰的世界里，他们没有什么可“管理”的。没有股票证书需要被后台发送出去。

相反，你可能会看到投资顾问提出买卖建议，而作为投资者，你可以注册“跟随”他们，和他们一起投资，他们会因为做出正确的选择而获得报酬。

我们也可能会看到一些从未见过的突发效应。想象一下，如果纽约市所有的房地产都被令牌化了。你可以创建交易所交易基金(ETF)，比如上西区 ETF、布鲁克林 ETF 和金融区 ETF。

这会让交易员做多布鲁克林，做空上东区。

安全代币不同于其他金融创新，比如导致 2008 年全球金融危机的信用违约互换和信用违约责任。2008 年崩盘的一大原因是风险的集中。

有了区块链上的安全令牌，监管者或任何能够进入区块链的人将总是能够知道风险有多集中，因为有了更多的透明度。

另一个突出的影响是，我们将看到今天没有证券化的新事物被证券化。从历史上看，很难将艺术品之类的东西证券化，因此流动性折扣很大。没有多少人能买得起价值 100 万美元的艺术品，但更多的人可能会买其中的 2%。

这些资产也可以打包，这样你就可以做多法国印象派，做空现代艺术。

金融监管者最终可能会要求证券的令牌化。因为安全令牌实际上是“合规性即服务”，它们使监管者的工作变得更容易。

*如果您正在寻找更多关于区块链和商业加密货币的信息，* [***点击此处访问我的个人资源列表***](http://bit.ly/2KqvNXr) *，在这里我可以找到最好、最可靠的信息。*

*这篇文章主要是我从* [*关于安全令牌*](https://blog.nomics.com/flippening/security-token-documentary/) *的精彩播客系列中的笔记。如果你有兴趣了解更多，不妨听一听。*

*[1]显然，每项投资都有许多潜在的复杂因素。*