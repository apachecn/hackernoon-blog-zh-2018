# 上传交易记录

> 原文：<https://medium.com/hackernoon/uport-transactions-d5b171f7068f>

![](img/b3860e763cf4d87e0c8efa21aaed485e.png)

如果您使用 [uPort](https://hackernoon.com/tagged/uport) 为您的[智能合约](https://hackernoon.com/tagged/smart-contract)签署交易，您会注意到，如果您在 Etherscan.io 上检查，您找不到任何关于该合约交易的痕迹。这是因为用于 Rinkeby 的 Etherscan 不提供关于内部交易的信息。当我们在[排灯节](http://www.diwala.io)开发我们的分散式技能验证平台时，我们面临这个问题，并认为记录这个问题是有用的，因为这对其他开发人员也是有用的。在这篇文章中，我试图提供一些关于如果有人使用 uPort 签署交易的交易过程的细节，以及获取内部交易细节的可能方法。

需要记住的重要一点是，当您使用 uPort 进行身份管理时，您的私钥存储在您的移动设备中，绝不会暴露给外界。因此，区块链的任何交易都应该通过您的移动设备进行。这可能与本文没有直接关系，但是当您使用 uPort 设计系统时，它会非常有帮助。

## 与 uPort 的交易之旅

DApp: 通常一笔交易是在 DApp 内部形成的。然后，通过二维码或深层链接，交易将被传输到您设备中的 uPort 应用程序。

**uPort App:** 使用 uPort App 内部的私钥对交易进行签名，并发送给 TxRelay contract。

**TxRelay:** 从 uPort 应用程序接收交易，如果您使用 uPort 应用程序返回的交易哈希，您将看到该交易。顾名思义，一旦收到消息，TxRelay 会将消息中继到 MetaIdetityManager 智能合同。

**MetaIdentityManager:** 该契约提供了在发送方不拥有任何以太网的情况下发送事务的方法。这个契约最终将事务转发到它的最终目的地，也就是您想首先调用的自定义契约。

## 查看发送到智能合同的数据

一旦提交了一个事务(没有 uPort ),并且它成功地到达网络中的目的地，就可以通过调用 Web3.js 方法***web 3 . eth . get transaction***来提取与该事务相关的所有数据，并使用类似于*[**ABI-decoder**](https://github.com/ConsenSys/abi-decoder)***的库来解码其数据参数。****

*原因是，在幕后，uPort 应用程序不会将交易直接发送到您的合同，而是通过 ***TxRelay*** 和***meta identity manager***合同进行路由。*

*因此，要查看通过与 uPort 签约发送到您合同中的实际数据，需要遵循以下步骤。*

1.  *通过提供事务哈希，使用***web 3 . eth . get transaction***拉事务*
2.  *使用 [**abi-decoder**](https://github.com/ConsenSys/abi-decoder) 与 ***TxRelay*** 约定的 abi 解码数据*
3.  *从步骤 2 接收的数据属性的值，应该被输入到[**abi-decoder**](https://github.com/ConsenSys/abi-decoder)**但是这次用的是***meta identity manager***的 ABI***
4.  ***最后，从步骤 3 接收的数据属性的值应该被输入到[**abi-解码器**](https://github.com/ConsenSys/abi-decoder) 中，但是这次使用的是**目标契约**的 ABI***

## ***摘要***

***从 uPort 应用程序签署的交易在到达目标合约之前会遍历多个合约。因此，您使用事务哈希获得的数据应该使用相关的智能契约 ABI 进行递归解码，以将数据释放给目标契约。你可以在 [uPort 消息解码器](http://www.google.com)上尝试一下，或者使用[简单节点模块](https://github.com/Diwala/eth-internal-transaction-disecter/tree/master/examples)进行开发。您可以找到一个由 Snorre 完成的更通用的模块，您可以采用这个库来查看任何智能合同的内部交易。***

***[](/diwala/uport-internaltransactions-c8fc4e31d9) [## 上传内部交易

### 因此，我们一直在为我们的平台使用 uPort，并发现对内部事务的支持…

medium.com](/diwala/uport-internaltransactions-c8fc4e31d9) 

## 资源

1.  可重用库( [GitHub](https://github.com/Diwala/eth-internal-transaction-disecter) 、 [NPM](https://www.npmjs.com/package/eth-internal-transaction-disecter) 、[样本](https://github.com/Diwala/eth-internal-transaction-disecter/tree/master/examples))
2.  [在线工具](https://disect.diwala.io/)
3.  [***meta identity manager***契约和来自以太扫描](https://rinkeby.etherscan.io/address/0x87ea811785c4bd30fc104c2543cf8ed90f7eeec7#code) (Rinkeby)的 ABI
4.  [***TxRelay*** 契约](https://rinkeby.etherscan.io/address/0xda8c6dce9e9a85e6f9df7b09b2354da44cb48331#code) [和 ABI](https://rinkeby.etherscan.io/address/0x87ea811785c4bd30fc104c2543cf8ed90f7eeec7#code) [来自以太扫描](https://rinkeby.etherscan.io/address/0xda8c6dce9e9a85e6f9df7b09b2354da44cb48331#code) (Rinkeby)
5.  [ABI 解码器库](https://github.com/ConsenSys/abi-decoder)
6.  [uPort 项目](https://github.com/uport-project/uport-identity)
7.  [支持白皮书](https://github.com/uport-project/specs/)***