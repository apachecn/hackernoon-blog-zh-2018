# 对 ERC 725 和 ERC 735 的第一印象——身份和主张

> 原文：<https://medium.com/hackernoon/first-impressions-with-erc-725-and-erc-735-identity-and-claims-4a87ff2509c9>

## 为众筹销售实现简单的 KYC 索赔验证

**ℹ️这是 2018 年初出版的。从此，** [**LSP-6**](https://github.com/lukso-network/LIPs/blob/main/LSPs/LSP-6-KeyManager.md) **取代了 ERC-735。一定要** [**查出来**](https://github.com/lukso-network/LIPs/blob/main/LSPs/LSP-6-KeyManager.md) **！**

在 [Fractal](https://www.trustfractal.com/business/) ，我们专注于从 UX 和法规遵从性的角度改善 ICO 体验。成功的 ICO 的关键之一是适当的参与资格验证(KYC、反洗钱、CFT、资本市场法规等)。因此，我们正在密切关注身份空间，关注诸如 [Sovrin](https://sovrin.org/) 、 [Taqanu](https://www.taqanu.com/) 、 [uPort](https://www.uport.me/) 和 [Civic](https://www.civic.com/) 等项目，并尝试潜在的集成。

[ERC 725 是在位于 EVM 的区块链(如以太坊)上通过智能合约发布和管理身份的标准。这个身份由它的链地址来表示。](https://medium.com/u/4b59b3ef14a2#ERC735 </a>)，以及直接作用于区块链的代理功能。</p></blockquote><p id=)

[我们可以将几把钥匙与这个身份联系起来。这些键可以有不同的用途:标准目前提出了`MANAGEMENT`、`ACTION`、`CLAIM`和`ENCRYPTION`。密钥是公共地址的`keccak256`，因此映射到外部拥有的账户(钱包)或智能合同。](https://medium.com/u/4b59b3ef14a2#ERC735 </a>)，以及直接作用于区块链的代理功能。</p></blockquote><p id=)

[另一个重要的概念是，ERC 725 合约可以通过其`execute` / `approve`方法用作代理。如果第三方承认给定身份契约的合法性，它的所有者可以调用它的`execute`与所述第三方进行交易，正如我们将在下面的 KYC 例子中看到的。](https://medium.com/u/4b59b3ef14a2#ERC735 </a>)，以及直接作用于区块链的代理功能。</p></blockquote><p id=)

[多个密钥在特权访问的上下文中特别有用。比如，假设这个身份不是针对个人的，而是针对一个组织的。持有较低特权密钥的成员可以向`execute`提交事务，但是只有那些能够访问较高特权密钥的成员才能`approve`执行那些事务。](https://medium.com/u/4b59b3ef14a2#ERC735 </a>)，以及直接作用于区块链的代理功能。</p></blockquote><p id=)

[我们可以在这个兔子洞里走得更远。由于密钥也可以映射到智能合同而不是钱包，因此可能会实现进一步的访问要求。例如，拥有一个映射到一个`AccessControl`契约的`ACTION`键允许我们在这个契约中构建任意复杂的访问控制级别。这很重要，因为它保持了标准本身的简单性，同时支持所有可以想象的用例。](https://medium.com/u/4b59b3ef14a2#ERC735 </a>)，以及直接作用于区块链的代理功能。</p></blockquote><p id=)

[另一个例子与撤销索赔有关。如果我们使用自己的身份对另一个身份进行声明，拥有几个`CLAIM`密钥会很有用——删除其中任何一个都会导致相关声明失去有效性。](https://medium.com/u/4b59b3ef14a2#ERC735 </a>)，以及直接作用于区块链的代理功能。</p></blockquote><p id=)

[举一个真实世界的例子，假设 Mozilla 有一个身份契约。除了 Mozilla 选择对该身份做的其他事情，Firefox 软件团队可以持有属于该身份的一个密钥，在发布时用它来签署软件校验和。](https://medium.com/u/4b59b3ef14a2#ERC735 </a>)，以及直接作用于区块链的代理功能。</p></blockquote><p id=)

[对于我们的 KYC 用例，如下所述，我们](https://medium.com/u/4b59b3ef14a2#ERC735 </a>)，以及直接作用于区块链的代理功能。</p></blockquote><p id=)[提出了一个对标准的变更](https://github.com/ethereum/EIPs/issues/725#issuecomment-398750048)，这将使`execute`成为一个`payable`方法，允许我们转发以太事务来执行。另一种方法是使用两个独立的事务:首先将 Ether 发送到契约，然后用`execute`移动它。

## ERC 735:索赔持有人

> 下面描述了添加、删除和保留索赔的标准功能。这些声明可以由第三方(发行人)证明，也可以自我证明。
> 
> 这种标准化的索赔持有人接口将允许 Dapps 和智能合同检查索赔持有人的索赔。在这里，信任被转移给债权的发行人。

ERC 735 处理关于 ERC 725 身份的声明的管理。它通过依赖可信第三方对给定身份的声明，促进了信任网络的出现。

这些索赔可能涉及多个主题，从生物特征数据到电子邮件帐户所有权。这对于我们的 KYC 用例特别有用，如下所述。作为 ICO 发行者，我们可以与受信任的 KYC 提供商合作来决定投资者是否有资格购买代币。

为了让某人向其身份添加声明，他们必须首先向相关的可信第三方请求。这个第三方(声明发布者)将对包含三项内容的消息进行签名:身份的地址、声明主题以及可选的一些数据(例如，KYC 数据的散列)。然后，身份所有者将该声明存储在他们的身份契约中(或者，声明发布者也可以自己添加声明，这必须得到身份所有者的批准)。

请注意，声明也可以自我证明，这对于许多其他用例来说已经足够了(对于没有严格 KYC 要求的简单应用程序，比如常规新闻网站，可以想到电子邮件和名称)。

# KYC 为众筹销售索赔

## 实体

该演示考虑了以下实体。

*   [**非常好**](http://swansonquotes.com/quotes/season07/ep-01-2017-good-building-development-co/#.Wyt2hhIzZ24) ，一家希望颠覆房地产开发市场的建筑公司。
*   **Fractal ID** ，执行 KYC/反洗钱检查的身份提供者。
*   **投资人**，谁需要清除 KYC 参与一个**非常好的**的 ICO。

## 流程概述

在编译完契约之后，我们的演示采取了以下步骤。

1.  **分形 ID** 部署自己的身份契约。
2.  **Fractal ID** 在其身份契约中添加一个`CLAIM`键。
3.  **投资者**部署其身份合约。
4.  **投资人**成功 KYC 后，**分形 ID** 为**投资人**签署 KYC 债权。
5.  **投资者**将**分形 ID** 签署的 KYC 声明添加到他们的身份合同中。
6.  **非常好**部署他们的代币和众筹合同。
7.  **投资者**参与**非常好**的 ICO，通过他们的身份合约转让给**非常好**的众筹合约。
8.  **非常好的**众筹合同确认**投资人**的身份合同包含**分形 ID** 在接受投资前的 KYC 债权。

## 智能合同

我将 [Origin 对标准的执行](https://github.com/OriginProtocol/identity-playground/tree/master/contracts)作为这项工作的起点。我不得不[稍微改变](https://github.com/OriginProtocol/identity-playground/pull/11/files)的工作方式:由于上面描述的`payable`问题，这个实现没有准备好将值传递给执行目标。为了清楚起见，这里有一个简单的契约继承图。

```
+---------------+     +---------------+
|               |     |               |
|    ERC 725    |     |    ERC 735    |
|               |     |               |
+-------+-------+     +-------+-------+
        ^                     ^
        |                     |
+-------+-------+             |
|               |             |
|   KeyHolder   |             |
|               |             |
+-------+-------+             |
        ^                     |
        |                     |
+-------+-------+             |
|               |             |
|  ClaimHolder  +-------------+
|               |
+---------------+
```

代币和众筹合约被解除 [OpenZeppelin Solidity](https://github.com/OpenZeppelin/openzeppelin-solidity/) 。

为了部署这些契约并与之交互，我使用 web3.js 来构建一个简单的 js 脚本。(如果你好奇的话，这里有完整的回购协议

## 步骤 1/4 —部署身份契约

**分形 ID** 和**投资者**都需要部署一份`ClaimHolder`合约，以便持有他们所有的钥匙和 claims⁴.该合同同时执行 ERC 725 和 ERC 735。

部署后，**分形 ID** 将创建一个`CLAIM`目的密钥。这是与 KYC 声明的签名相关的关键。

```
var fractalIdClaimsKey = web3.utils.keccak256(
  fractalIdClaimsAccount
);fractalIdClaimHolder.methods.addKey(
  fractalIdClaimsKey,
  KEY_PURPOSES.CLAIM,
  KEY_TYPES.ECDSA,
).send({
  from: fractalIdAccount,
  gas: 4612388,
});
```

如上所述，密钥是其控制账户地址的`keccak256`散列。 **Fractal ID** 调用`ClaimHolder`契约(ERC 725)上的`addKey`方法，传递给它这个键、键的`purpose`和它的`keyType`。

最后，**非常好的**需要部署他们的代币和众筹合同，并将`VeryGoodCoin`的所有权转移到`VeryGoodCrowdsale`合同，因此它可以根据需要铸造代币来奖励贡献。

```
veryGoodCoin.methods.transferOwnership(
  veryGoodCrowdsale.options.address,
).send({
  from: veryGoodAccount,
});
```

## 第 2/4 步—签署 KYC 索赔

在**投资人**通过**分形 ID** 的[在线 KYC 流程](https://www.trustfractal.com/business/)后，**分形 ID** 签署 KYC 债权。

```
var hexedData = web3.utils.asciiToHex("Investor is VBR V0 legit.");
var hashedDataToSign = web3.utils.soliditySha3(
  investorClaimHolder.options.address,
  CLAIM_TYPES.KYC,
  hexedData,
);
var signature = await web3.eth.sign(
  hashedDataToSign,
  fractalClaimsAccount,
);
```

## 第 3/4 步—将 KYC 债权添加到投资者合同中

**Fractal ID** 然后向**投资者**提供这份签名声明，投资者将其添加到他们的身份合同中。

```
var claimIssuer = fractalIdClaimHolder.options.address;
var addClaimABI = await investorClaimHolder.methods
  .addClaim(
    CLAIM_TYPES.KYC,
    CLAIM_SCHEMES.ECDSA,
    claimIssuer,
    signature,
    hexedData,
    "[https://www.trustfractal.com/business/](https://www.trustfractal.com/business/)",
  ).encodeABI();investorClaimHolder.methods.execute(
  investorClaimHolder.options.address,
  0,
  addClaimABI,
).send({
  gas: 4612388,
  from: investorAccount,
});
```

这是通过`ClaimHolder`契约上的`addClaim`方法(ERC 735)完成的，我们为其提供一个`topic`、一个`scheme`、`issuer`、`signature`、`data`和一个`uri` ⁵.

`execute`的工作方式如下。我们向它传递一个目的地址、一个以太值和一个序列化的方法调用。如果我们用`MANAGEMENT`或`ACTION`键调用它，请求就会被批准并执行。如果我们用一个其他用途的键调用它，一个更高特权的键必须随后批准执行。

## 步骤 4/4 —参与 ICO

为了让**投资者**参与 ICO，我们也将使用`execute`方式。这里的情况更容易理解:由于 **Fractal ID** 的身份声明出现在**投资者**的身份契约上，并且**非常好的**众卖契约将检查消息发送者的 KYC 声明，该契约必须是调用者。

```
var investABI = veryGoodCrowdsale.methods.buyTokens(
  investorClaimHolder.options.address
).encodeABI();var investmentAmount = web3.utils.toWei("1", "ether");
investorClaimHolder.methods.execute(
  veryGoodCrowdsale.options.address,
  investmentAmount,
  investABI,
).send({
  gas: 4612388,
  from: investorAccount,
  value: investmentAmount,
});
```

现在所有的检查都应该通过了，**非常好的**众筹合同将铸造新的代币，并将它们的所有权分配给**投资者**的身份合同！🎉

***感谢*** *至* [*法比安·沃格斯泰勒*](https://medium.com/u/4b59b3ef14a2?source=post_page-----4a87ff2509c9--------------------------------) *、雨果·佩肖托* [*若昂·格拉迪姆*](https://medium.com/u/22c33f1d4c05?source=post_page-----4a87ff2509c9--------------------------------) *阅读本帖草稿。*

*更准确地说，* `*keccak256*` *只是标准推荐的非十六进制和长度超过 32 字节的密钥。*

***分形 ID*** *也可以代表* ***投资者这样做。***

*还有一个 ERC 725 建议的流程，是让* ***分形 ID*** *调用* `*addClaim*` *上的* ***投资方*** *合同。该索赔可以通过* ***投资人*** *的身份合同立即受理，或者先经过审批程序。*

**⁴** *从技术上来说，一个* `*KeyHolder*` *契约对于* ***分形 ID*** *就足够了。*

**⁵** *注意，我们使用* `*encodeABI*` *来序列化* `*addClaim*` *方法调用，并通过* `*execute*` *进行管道化。* ***投资者*** *可以改为直接调用* `*addClaim*` *，使用账户映射到相应的权限。*

**策划一个 ICO？**在 [Fractal](https://www.trustfractal.com/business/) 大会上，我们提供了一个简单易用的 launchpad 解决方案，结合全面的客户身份识别服务(KYC/AML ),轻松成功地部署您的代币发行。