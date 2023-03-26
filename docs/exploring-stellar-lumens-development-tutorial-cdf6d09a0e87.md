# 探索恒星流明-开发教程

> 原文：<https://medium.com/hackernoon/exploring-stellar-lumens-development-tutorial-cdf6d09a0e87>

![](img/539ae2106587c0cf1444e2153e77c13f.png)

今天，我有机会探索恒星流明的**技术面**，并对利弊进行**分析。我想我终于可以把它从我的遗愿清单上划掉了！本文的目标是将一个简单的事务推送到 Stellar 网络。由于我找不到太多关于 Stellar 的技术“如何做”的信息，我决定用老方法来做: [RTFM](https://en.wikipedia.org/wiki/RTFM) ！**

# 了解恒星

立即钻研代码是愚蠢的，因此我在 Stellar [网站](https://www.stellar.org/developers/guides/get-started/)上找到了这篇高层次的架构概述指南。

## API:地平线

所有与 Stellar 网络的交互都通过 Horizon(一个 RESTful HTTP API 服务器)进行。它允许开发者提交交易、检查账户余额和订阅事件。您可以通过 web 浏览器使用 Horizon，这是一个命令行工具，如 cURL，或者是最简单的解决方案 Stellar SDK。Stellar SDK 可用于[*JavaScript*](https://github.com/stellar/js-stellar-sdk)*，Java 和 Go* 。还有社区维护的针对 *Python、C#和 Ruby* 的 SDK。

## 恒星内核

恒星核心是恒星网络的主干，每台地平线服务器都与它相连。恒星核心负责验证交易和达成共识。核心是由一群个人创建一个分散的网络。网络上的每一笔交易都要支付一小笔费用:100 斯特鲁普(相当于 0.00001 XLM)。应该防止不良实体向网络发送垃圾邮件。

*Stellar Consensus Protocol(SCP)* SCP 优先考虑安全性，而不是活性——如果出现分区或行为不当的节点，它会停止网络的进程，直到达成共识。你可以在他们的[白皮书](https://www.stellar.org/papers/stellar-consensus-protocol.pdf)中找到更多关于该协议的信息。

*用 Docker* 运行 Stellar Core
通过运行 Stellar Core 实例有可能成为网络的一部分。使用[stellar/quick start docker image](https://hub.docker.com/r/stellar/quickstart/)可以轻松设置这样的实例。

幸运的是，Stellar 提供了一个用于开发目的的测试网。信息可以在[这里](https://www.stellar.org/developers/guides/concepts/test-net.html)找到。

# 让我们编码恒星

**先决条件**:JavaScript、Express 和 Async/Await 的基础知识。

**目标**:在 testnet 上创建两个账户，用 testnet 令牌填充账户，并将一些随机金额从一个账户转移到另一个账户。最后，我们希望能够用事务浏览器检索事务。总的来说，这个 Stellar 编码教程教你入门 Stellar 开发的基础知识。

**完整的工作代码:**可以在[这里](https://github.com/michielmulders/stellar-js-sdk)找到，这样更容易跟随教程。博客底部还添加了完整的代码片段。

## 创建帐户

Stellar JavaScript SDK 是所提供的 SDK 中最稳定的。你可以用 npm: `npm i -S stellar-sdk`轻松安装这个包。

我们告诉我们的 Stellar SDK 使用 testnet 服务器 URL:

```
const server = new Stellar.Server('https://horizon-testnet.stellar.org')
Stellar.Network.useTestNetwork()
```

我们准备创建两个随机帐户。Stellar SDK 提供了一个无种子随机创建密钥对的功能。

```
let pairA = Stellar.Keypair.random()
let pairB = Stellar.Keypair.random()
```

这个密钥对包含一个检索公钥`pair.publicKey()`和秘密`pair.secret()`的函数。

接下来，我定义了一个路由来初始化两个帐户。在这个函数中，我们将基于我们的公钥创建我们的钱包/账户，并请求`stellar friendbot`在两个账户上发送 10.000 testnet 流明给我们。我使用`request-promise` npm 包向 Horizon testnet API 发送请求，因为这与 async/await 配合得很好。我们通过一个查询字符串传递来自第一个密钥对的公钥。

```
await rp.get({
  uri: 'https://horizon-testnet.stellar.org/friendbot',
  qs: { addr: pairA.publicKey() },
  json: true
})
```

帐户现已创建，但我们的应用程序中没有帐户对象。那么，让我们来检索这个。

```
accountB = await server.loadAccount(pairB.publicKey())
```

现在可以检查我们的帐户上是否有一些恒星流明。

```
accountA.balances.forEach((balance) => {
  console.log('Type:', balance.asset_type, ', Balance:', balance.balance)
})
```

我们也为`pairB`重复这个代码，以便检索`accountB`。

## 传递恒星流明

一个事务可以由链接到`TransactionBuider`的多个操作组成，从而创建(多)事务。为了简单起见，我们将只转移少量的 testnet 流明。我们将`pairB`的公钥定义为目的地。转账金额等于`30.0000001`。StellarSDK 要求 amount 参数应精确到小数点后七位。

```
const transaction = new Stellar.TransactionBuilder(accountA)
  .addOperation(Stellar.Operation.payment({
    destination: pairB.publicKey(),
    asset: Stellar.Asset.native(),
    amount: '30.0000001'
  }))
  .build()
```

好了，交易已经准备好，可以由转账发起者— `transaction.sign(pairA)`签字了。好了，现在把这个发给地平线网:
`const transactionResult = await server.submitTransaction(transaction)`

## 检索历史

以下代码将为每个“历史页面”仅返回一个交易。您可以使用“历史页面”对象上的`next()`功能来检索账户历史中的下一笔交易。

```
let historyPage = await server.transactions()
  .forAccount(accountA.accountId())
  .call()
```

我们希望打印事务中操作的细节(XDR 编码)。让我们解码到 base64 并循环处理事务的操作，以打印所有发送的金额。

```
let txDetails = Stellar.xdr.TransactionEnvelope
  .fromXDR(historyPage.records[1].envelope_xdr, 'base64')txDetails._attributes.tx._attributes.operations
  .map(operation => console.log(
    `Transferred amount:         
    ${operation._attributes.body._value._attributes.amount.low} XLM`
  ))
```

最后，让我们检索下一个“历史页面”。

```
historyPage = await historyPage.next()
```

*就这样？是的，惊讶吗？*

## 你知道吗

*   你可以探索一下网络上的交易— [例子](https://horizon-testnet.stellar.org/transactions/8380cde0b2300c087427edf6cae08abb12cdc5eea30c3ec129f45f0db37c7c09)。链接在`transactionResult`参数:`transactionResult._links.transaction.href`中返回。
*   关于事务的元数据以标准数据序列化格式的 XDR 格式存储。你可以在这里反序列化它[。](https://www.stellar.org/laboratory/#xdr-viewer?input=AAAAABQfUJxjNiLOX3QqcxCIcpFemEm7uI6aeT5GGyBKRmhiAAAAZAB0LFwAAAADAAAAAAAAAAAAAAABAAAAAAAAAAEAAAAA0bR27%2ByXbgKzVd7tOZ2VreBy%2BijKqULK5aH%2F7R2EvfkAAAAAAAAAABHhowEAAAAAAAAAAUpGaGIAAABA1jaHLjUwWPe6rDpQEjv4mGMOrmZ00Ab%2BePjK1yrPZg7vpN1s%2FgsEULgbwyEXIFtteKN1imSO6k1mRFDKYpiPBQ%3D%3D&type=TransactionEnvelope&network=public)
*   您可以在这里查找类似[的公钥的所有交易。](https://horizon-testnet.stellar.org/accounts/GA47YQ3Y4UCUSOKGAR53L5WSM4AN34JQSGFQ2CMVELJVWN7IYOWI6PBB/operations?order=desc)

# 恒星分析

## 赞成的意见

*   Stellar 是一个支付引擎，专注于资产移动，而其他区块链可能专注于程序本身的逻辑。Stellar 和 Ripple 正在处理你的钱是如何交易和发送的，这是一个更特殊的目的。
*   Stellar 对于任何不太了解区块链的 web 开发人员来说都很容易集成。
*   非常适合支付/跨货币/跨境支付。
*   快速，便宜，容易建立自己的联合网络:这是由于共识算法不使用工作证明，而是他们的自定义[恒星共识协议](https://www.stellar.org/papers/stellar-consensus-protocol.pdf)。
*   非常便宜的费用:每次手术 0.00001 XLM。一笔交易可以包含多项操作，因此费用不取决于转账金额。

## 骗局

*   隐私——每一笔交易都可以被区块链浏览器发现和读取。
*   缺少重要的标准，如支付环节(仍在开发中),这降低了采用的速度。支付链接将处理链接上的点击，提示用户付款，这对电子商务至关重要。目前，你需要手动复制粘贴地址、交易备忘录和金额，比特币有一个一键批准支付选项。

很难确定这是否是一个*赞成/反对* : Stellar 并没有真正使用智能合同的概念(它不是图灵完成)。它适用于一些流行的用例，如托管、时间受限和原子事务。然而，标准可以扩展到更多的用例，但它永远不会达到“编写你自己的 DApp”的地步。这很好，因为它使 Stellar 为资产转移的特定目的而优化，而不是被人们的幻想所膨胀。

> Stellar 成功地消除了区块链的知识壁垒，尽管它是一家区块链支付提供商。米歇尔·穆尔德斯

# 成为一流专家的资源

*   这个例子的完整代码在 [Github](https://github.com/michielmulders/stellar-js-sdk) 上。
*   Horizon API [文档](http://stellar.github.io/js-stellar-sdk/TransactionBuilder.html)。
*   Testnet [explorer](https://horizon-testnet.stellar.org) 。
*   入门代码示例[这里](https://www.stellar.org/developers/guides/get-started/transactions.html)，这里[这里](https://www.stellar.org/developers/js-stellar-sdk/reference/examples.html)，这里[这里](https://github.com/rafaalb/React-StellarLumens/blob/5311e784e2a5ac85356a7fc97382a8537882d94c/src/actions/payments.js) (react)。
*   检查恒星网络的[状态](https://dashboard.stellar.org/)。

有兴趣开始自己的区块链项目，但不知道如何开始？您需要帮助开始代币销售或进行审计吗？与 [*取得联系*](https://theledger.be) *。*

完整的代码片段: