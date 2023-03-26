# 你的资金在 IDEX 可能不安全

> 原文：<https://medium.com/hackernoon/your-funds-are-probably-not-safe-on-idex-d935415b1e20>

## 在最近[传出 SEC 收购以太三角洲的](https://www.coindesk.com/sec-charges-etherdelta-founder-with-running-unregistered-securities-exchange)消息后，人们可能会认为 [IDEX](https://idex.market/) 会是下一个目标。我们必须问自己——IDEX 到底有多分散和安全？

![](img/8a91751ca9f562f4361ff134ab8343e1.png)

Credits to Aleksey Shmatov

IDEX [智能合约](https://etherscan.io/address/0x2a0c0dbecc7e4d658f48e01e3fa353f44050c208#code)出现在以太扫描上。让我们深入研究一下。

**整个智能合约只有 184 行。为什么这么小的人会问？**

这背后的原因是 IDEX 区块链上的交易所没有全部容量。它不在区块链上存储其订单簿，也不使用智能合约进行订单匹配。这一切都委托给他们的私人服务器。

**那么他们的代码必须是开源的，以防他们出事，对吗？**

没有。他们的源代码是私有的。换句话说，如果他们的网站被黑客/监管者关闭，整个交易所肯定会停止运行，没有简单的方法让它运行。

至少我可以随时拿回我的乙醚/代币，对吗？

没有。他们对以太网/令牌撤销有锁定时间，称为`inactivityReleasePeriod`。此时，它被设置为 17 天，但管理员可以将其增加到大约 6 个月

Your funds are locked

你可能会想。在绝对最坏的情况下，我会在 6 个月内收回我的资金，对吗？

没有。在最坏的情况下，你几乎永远也得不到你的钱。在每个交易的`lastActiveTransaction[tradeAddresses[3]] = block.number;`行 182 更新不活跃期。这意味着，如果黑客得到了 IDEX，而你至少有一个未结订单，黑客只需一个令牌就可以满足你的订单，冻结你的资金 6 个月。从本质上说，这几乎可以持续到永远。

# 结论

我对 IDEX 在很多方面都感到失望。首先，IDEX 称自己是一个分散的交易所是一个巨大的延伸。他们可以声称是一个分散的托管人，但即使在这里，他们也未能正确实施锁定，因为它允许锁定用户资金 6 个月到多年之间。它们在区块链和私有服务器上都容易受到攻击。任何被黑的交易都会停止。更糟糕的是，如果智能合约遭到黑客攻击，用户的资金可能会被锁定。考虑到以太三角洲公司开发的惊人智能合同，这尤其令人不安。

# TL；速度三角形定位法(dead reckoning)

*   IDEX 根本不是分散式交易所。
*   IDEX 是以太网/代币的分散托管人，但由于智能合同实施不佳，资金可能会被锁定最少 6 个月，最长可达多年。

## 如果这篇文章有帮助，请点击拍手👏按钮下面几下，以示支持！⬇⬇

# 社会的

*   在 [LinkedIn](https://www.linkedin.com/in/ylv-io/) 上与我联系。
*   在[推特](https://twitter.com/ylv_io)上关注我。

# 阅读更多

[](https://hackernoon.com/how-to-create-and-deploy-your-own-eos-token-1f4c9cc0eca1) [## 如何创建和部署您自己的 EOS 令牌

### 我们将弄清楚什么是 EOS 令牌，以及您如何自己创建和部署它。

hackernoon.com](https://hackernoon.com/how-to-create-and-deploy-your-own-eos-token-1f4c9cc0eca1) [](https://hackernoon.com/how-much-does-it-costs-to-run-dapp-in-2018-87ee11fe1d5d) [## 2018 年办 DApp 要花多少钱

### 你认为你的 AWS 或网站的数字海洋账单正在杀死你吗？

hackernoon.com](https://hackernoon.com/how-much-does-it-costs-to-run-dapp-in-2018-87ee11fe1d5d) 

*原载于 2018 年 11 月 21 日* [*ylv.io*](https://ylv.io/your-funds-are-not-safe-on-idex/) *。*