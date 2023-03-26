# 在 Stellar 上为您的 ICO 创建智能合同

> 原文：<https://medium.com/hackernoon/creating-smart-contracts-for-your-ico-on-stellar-b77fa2490ff0>

![](img/2b1332f18e464cd16e892d0fafd515fc.png)

Background image by [**Nathan Anderson**](https://unsplash.com/photos/eS7HrvG0mcA)

B lockchain，不管你喜不喜欢，每天都在改变技术的面貌，而且看起来不会很快停止。我们越依赖这种分布式网络架构，我们实际上就越依赖它以最透明、最不可信的方式进行交易的能力。

为了实现这一点，今天的区块链技术为我们提供了一个非常强大的工具— **智能合约**。

根据你选择的区块链，智能合约有不同的风格，目前最著名的是以太坊智能合约，主要是因为它的多功能性(因为它是图灵完备的)。

但是还有其他的区块链技术可以让你建立同样多的东西…只是方式不同。

其中一项技术是 **Stellar** ，我们选择它作为我们产品的基础工具集 [TillBilly](https://tillbilly.com/) ，并运行其 [ICO](https://ico.tillbilly.com/) 。

我们选择 Stellar 的原因有很多，您可以在我们的[白皮书](http://bit.ly/tillbilly_whitepaper)中找到更多信息，但一个主要原因是其简化的智能合同，这是我们从一开始就一致同意的，是运营 ICO 的必备条件。

> **“Stellar smart contracts 对大多数人来说仍然是一种黑魔法”**这是我们的顾问 Johan Stén 在我与他讨论 Stellar smart contracts 时所说的话。

因此，希望这篇文章将指导并可能帮助那些希望利用 SSC (Stellar 智能合约)和 SDEX (Stellar 分布式交易所)来运行其 ico 的人。

> 让我们跳进来吧！

顾名思义，这个模型处理为每个希望为 ICO 做贡献的用户创建一个中间帐户。

这里的主要亮点是，在贡献者和公司之间存在一个帐户，该帐户通过 Stellar 多重签名功能由双方部分控制。

为了使事情更简单，让我们设置几个缩写:

```
**IM:** Intermediary Account **CU:** Contributing User **CO:** COmpany conducting the ICO **SDEX:** Stellar Distributed Exchange **SSC:** Stellar Smart Contract (or a fully formed unsubmitted transaction)
**GA:** Genesis Account (token issuer) **HA:** ICO funds Holding Account **TP:** Trusted Party
```

**主要亮点:**

*   IM 是 **CU** 将资金转入的账户
*   IM 的密钥完全由 **CU** 控制，在 Stellar remains 上资助(和激活)这个账户的责任由 **CO** 负责
*   添加 **CO** 作为 **IM** 的签署人，进行部分控制
*   **IM** 参与 **SDEX** 购买代币并在 ICO 期间持有代币
*   ICO 成功后， **CO** 不再作为签署人。这通过一个 **SSC** 处理，IM 对**的完全控制转到 **CU****
*   在 ICO 失败时， **CO** 仍然作为签名人被删除，但是退款机制由 **HA** 上的另一个 **SSC** 处理

## **发卡账户(起源账户)设置:**

这里需要注意的一点是——**GA**需要对自身的操作有一些控制，即**低阈值**操作。这是因为我们希望控制谁可以用我们的令牌建立信任线，并且允许信任是一个低门槛的操作。

1.  **GA** 向 **HA** 发送设定数量的令牌
2.  **HA** 设置自己的**SSC**(稍后解释)
3.  **GA** 执行以下操作:

```
- Add **TP** as a signer with weight 1
- Set its own weight to 1
- Set authorization required flag
- Set low threshold to 1
- Set medium and high threshold to 2
```

这将允许 **GA** 允许信用额度，但不允许其进行任何更多的支付(令牌生成)交易。

*注意:由于我们必须在 ICO 期间允许信用额度，因此我们不能在 ICO 后使用预签名的时间锁定交易来清除授权要求标志。*

ICO 完成后，将提交以下交易(由 **GA** 和 **TP** 双方签字):

```
- Clear authorization required flag
- Reset thresholds
- Remove **TP** as a signer
- Set own weight to zero
```

将自己的权重设置为零将有效地使帐户变得一文不值，并且不能生成更多的令牌。然而，用户可以自由地建立信用额度来交易这些代币。

## **关于 ICO 成败的 SSC(关键要点):**

*   **HA** 将在 Stellar 上实现带有 **TP** 的多重签名(可以是个人、组织等。).
*   HA 持有所有在 ICO 期间要出售的代币。
*   **HA** 在 **SDEX** 上创建向 XLM 出售代币的报价
*   带有设定日期的预签署交易决定了 ICO 的成败
*   成功交易将被时间锁定，并具有以下操作(序列号:M)

```
- Send SOFT_CAP_AMOUNT to **CO**
- Remove **TP** as a signer
- Reset thresholds
- Clear the offer on **SDEX***(If the ICO is not successful in raising the soft cap, this transaction will fail)*
```

*   失败交易，时间锁定(序号:M + 1)

```
- Create an offer on **SDEX** to buy back tokens for XLM
```

这里的挑战是，在这种情况下，我们必须从 **HA** 释放所有 xlm。不能预先写好的东西。因此，在这种情况下，我们将再次依赖 **TP** 来创建一个交易，或者用适当数量的 XLMs 更新 **SDEX** 上的报价。

我们将假设满足所有其他先决条件，包括网站帐户创建、电子邮件验证、KYC 和反洗钱检查等。

***1。*** 用户来到仪表板，看到一个创建钱包的按钮( **IM** )。

***2。*** 点击后，为用户生成一个离线的恒星密钥对(仅限第一次)。该秘密被临时保存在浏览器中，用于即将到来的一组事务。

***3。*** 提示用户安全保存该密钥对(可以给出一个选项，我们生成一个用密码锁定的密钥库文件，并将其保存在服务器上)。

***4。*** 然后提示用户再次输入密钥，以便进行下一步。这是为了确保用户保存了私钥。

***5。*** 在后台(通过 xhr)，我们用 3 个 XLM 资助 **IM** ，从而激活它(稍后计算)。服务器响应一个成功状态以及 **CO** 的公钥。

***6。*** 激活成功后，我们在 **IM** 上用以下操作创建一个事务(这个事务信封(XDR)被发送到服务器而不是 Stellar)

```
- Request trustline for token
- Add **CO** as a signer with weight 1
- Set all thresholds to 2
- Set own weight to 1
```

***7。*** 服务器在 Stellar 上提交该交易，然后从代币发行账户批准信用额度。钱包现在设置为从外部来源接收资金(XLM)并准备从 **SDEX** 购买代币。

**8。** 用户现在被呈现一个购买代币的按钮。点击时，我们要求用户从他们想去的任何地方把 XLM 存入 **IM** 。这可能是他们自己的钱包，也可能是一种交换。

**9*。*** 浏览器在等待输入交易时显示等待状态。我们通过使用服务器发送的事件(流)模式来检查这一点。

**10*。*** 收到资金后，我们创建以下交易(使用保存在浏览器中的密钥部分签名):

交易#1: (N)

```
- Send 3 XLM to **CO** *(recovering the account activation funds — one time only)*
- Create an offer on **SDEX** to buy tokens for all but 3 XLM in account
```

交易#2: (N + 1) *在 ICO* 期间时间锁定

```
- Remove **CO** as a signer from this account
- Reset thresholds
```

**11*。*** 两笔交易都发送到服务器。交易#1 由**公司**签署并直接提交给 Stellar network。事务#2 被签名，其散列和事务信封被保存在服务器上。如果用户想要验证并保存这些文件，服务器也会将它们返回给用户。

***12。*** 每当用户想要转移资金时，重复步骤 8 至 11。

*无论 ICO 成功与否，在 ICO 完成时，服务器将在步骤 10 中发布该交易#2。*

在 ICO 成功的情况下，令牌已经在用户的帐户中，并且完全控制该帐户。仪表板可以提供快速和简单的选项，将这些资金转移到不同的帐户或将其与另一个帐户合并等。

万一它失败了，我们会被另一套**SSC**锁定，保护我们和我们的用户。我们将再次在仪表板上提供简单的选项来收回资金。

## **计算 IM 的最小账户余额:**

最低余额要求:

```
*0.5 x (2 + # of entries)*
```

ICO 的每个 **IM** 都应该有:

*   1 具有发行者令牌的信用额度
*   1 名签名人( **CO** )
*   1 个数据条目(如果需要，用于处理奖励代币)

将这些值代入等式:

```
*0.5 x (2 + 3) = 2.5 XLM*
```

**IM** 将要经历的潜在操作:

*   建立信任线
*   设置选项(添加签名者、设置阈值、设置自身权重)
*   在 **SDEX** 上创建报价
*   ICO 成功后将资金转移到用户的主账户
*   将帐户与用户的主帐户合并

由于这些操作的成本不太高，0.5 XLM 的费用是足够安全的。所以总的来说，每个 **IM** 最少应该有 3 个由 **CO** 存放的 XLM，以后可以收回。真实世界(菲亚特)的价值可能会根据当时 XLM 的成本而变化。

这就结束了！

现在可以有更多的方法来增强这个模型。例如:

*   这也是 Johan 的建议，在 ICO 销售流程的第 2 步到第 4 步中，你可以使用 BIP-39 助记码来确保贡献用户确实记得他们的密钥。
*   另一个 **SSC** 用于当用户根本没有贡献并且 **CO** 需要收回最初传输的 3 个 xlm 以激活 **IM** 。

但这是我留给你们的事！

*最初由*[*Aditya Mog he*](https://medium.com/u/c6ac78c612f2?source=post_page-----b77fa2490ff0--------------------------------)*发布，工程总监 at**[***till Billy***](http://tillbilly.com)**——一家前瞻性的初创公司，致力于将基于区块链的销售点支付解决方案引入主流零售业。***