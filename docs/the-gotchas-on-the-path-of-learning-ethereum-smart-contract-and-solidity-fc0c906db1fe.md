# 学习以太坊智能合约和可靠性的过程中遇到的问题

> 原文：<https://medium.com/hackernoon/the-gotchas-on-the-path-of-learning-ethereum-smart-contract-and-solidity-fc0c906db1fe>

*第一部分有很多背景和故事。如果您是一名开发人员，正在学习 Solidity，并且只是略读，那么您可以通过向下滚动到下面的 gotchas 部分来充分了解本文。*

![](img/597e00a8ed4fd11e40df5bdbf92bd244.png)

I found this while googling for “Solidity” and with usage rights.

Cryptos 和 Ethereum 是目前的热门话题，在科技圈内，ethereum dapps(去中心化应用)的开发才刚刚开始起步(部分原因是 2017 年 12 月 CryptoKitties 的流行/病毒式传播)。

学习以太坊区块链开发的资源仍然随处可见。到处都有大量关于某些特定主题的一次性文章(比如这篇)，但是很少有文章是关于入门的最初步骤的。

几周前我开始摆弄它。我唯一的遗憾是没有在旅途中实时记下我所有的学习内容，因为有太多的东西需要学习，一些现在对我来说已经像是第二天性的东西，在几周前对我来说还是非常陌生的。

我现在就开始，并且还会尽我所能回溯，记录下我一路上遇到的所有问题。

## 入门指南

像许多其他人一样，我从[松露框架](http://truffleframework.com)开始。是开发以太坊 dapps 的 Node.js 框架。另一个流行的框架是 [populus](https://github.com/ethereum/populus) ，用的是 Python。不太了解，这里就不说了。

出现的第一个问题是“框架”在 Eth dev 中扮演什么角色？我以为 Eth dapps 是用他们自己的语言写的，叫做 Solidity ( [docs 这里是](http://solidity.readthedocs.io))，为什么会牵扯到 [Javascript](https://hackernoon.com/tagged/javascript) ？

首先要理解的是，为了在以太坊区块链上执行，Solidity 代码被编译成二进制代码。编译器是`solc`。拥有 Truffle 框架主要有助于两件事:1)编译并最终将编译好的智能合约迁移(部署)到实时网络上(mainnet 或类似 Ropsten/Rinkeby 的 testnet)，2)编写并执行单元测试和集成测试(用 Javascript 编写，通过 web3 调用智能合约函数，就像真实世界的应用程序一样)。

## 第一个教程

松露配有[松露盒](http://truffleframework.com/boxes/)，这是一套模板，你可以从中开始新的 dapps。

我选择从[宠物店](http://truffleframework.com/boxes/pet-shop)开始，它还附带了一篇[全教程文章](http://truffleframework.com/tutorials/pet-shop)。我必须强调，这个教程是***优秀*** 。如果你完全不知道从哪里开始——不要去阅读其他人写的所有文章，比如如何编写自己的 CryptoKitties 等等。阅读并遵循这个宠物店教程。(如果你因为没有使用过 Node.js 而需要帮助，我推荐我的[node . js 入门教程](/@bigilui/setting-up-your-first-node-js-environment-c78510cfc61f)。)

这个教程的好处是它假设几乎没有知识。它并不假设 React 知识。只有 jQuery，目前几乎每个人都知道它。以及 Node.js 的一些基本知识。

把这篇文章的剩余部分看作是该教程结束的“后续”——当你在完成该教程后感到迷茫时，试图拯救你！

## 宠物店教程后的生活

到这个时候，你会有所收获。您已经安装并运行了 Ganache，这允许您进行本地测试，包括单元/集成测试以及从浏览器到智能合约的端到端测试。你有一个带 MetaMask 插件的浏览器，可以在真实网络(mainnet 或 testnet)上进行测试。您有自己的代码编辑器(而不是我自己的代码)，以及运行 truffle 命令的终端。一切似乎都很好。

现在，您开始探索并对智能合约可靠性代码进行第一次更改，看看它是如何工作的。您可能会立即意识到，尽管 Solidity 的语法看起来很简单，看起来像 Javascript，但由于内存和资源方面的考虑，它实际上非常严格。

你的经历可能很快就会变成恐怖的[宠物店](https://ljn.io/posts/solidity-and-the-petshop-of-horrors/)。就像那篇文章的作者一样，我通过实践而不是阅读文档来学习。和作者一样，我也遇到了那篇文章中提到的大部分内容，甚至更多。

# 问题#1:以用户身份向合同发送 ETH:Solidity 端代码

我尝试做的第一件事就是让我的 dapp 付款(当然是在 ETH)。毕竟，大多数 dapps 的目的是在功能的某个地方涉及 ETH 支付，对吗？(至少如果你想靠做这个赚钱的话。)

我在任何地方都找不到一篇文章或文档页面来解释如何编写智能合同代码和 Javascript 代码，以便从用户那里获得报酬。(宠物店教程不包括它，上面链接的 Solidity 文档也不包括它

最后，我发现了这一点，因为我查看了另一个智能合同的源代码，它足够简单(除了发送 ETH 付款之外几乎没有其他功能)可以阅读和理解。

下面是一个接受付款的 Solidity smart 合同示例:

```
pragma solidity ^0.4.19;contract MySmartContract {
  // If this smart contract's purpose were to keep track of how
  // much ETH a user (an address) has sent in
  mapping(address => uint) public balances; function MySmartContract() public payable {
    // constructor needs `payable` keyword.
  } function() public payable {
    // fallback `payable` function is needed for a contract to
    // accept ETH payments.
  } function sendEth(bytes32 message) payable public returns (bool) {
    // Here, the ETH value being sent is available as `msg.value`.
    balances[msg.sender] = balances[msg.sender] + msg.value;
    // This function also takes in a message that is 32 chars max.
    // Also returns a bool as an example.
    return true;
  }
}
```

这在某种程度上是一个可靠的最小工作智能合同，可以接受 ETH 付款(除了保留资金本身之外什么也不做！).

这段代码中已经有几个子陷阱:

## 接受付款是一种特殊的“元数据”类型的东西。

它不是函数调用的参数。在搜索或查阅文档时，您可能会很快找到`transfer`调用。尝试调用`transfer`会遇到错误，因为它所做的是将你自己(合同)的钱*发送给其他人。*

应该发生的是，您需要使您的函数(和合同)`payable`，然后在 Javascript 方面，我们将看看如何触发具有附加的 ETH 支付的调用。

在可靠性方面，用户发送的数量以`msg.value`变量的形式出现。(`msg.sender`变量包含用户的公共地址。)

## 智能合约本身在 ETH 网络上有一个公共地址，并像任何用户一样有一个钱包，并可以拥有 ETH。当您将 ETH 发送到智能合约中时，该合约现在拥有该资金。

这是你第一次开始时可能没有意识到的另一个微妙之处。如果您假设智能合约是一个计算机程序，并且您的用户是用户，您可能没有意识到智能合约也像以太坊网络上的用户，因为它可以拥有 ETH 余额(存储货币)。

当用户向您的智能合约函数发送 ETH 值时，您的智能合约现在拥有该 ETH。如果您希望 ETH 最终进入您的钱包，您将需要公开一个只能由某个人/地址(您自己)调用的函数，该函数将契约拥有的一切转移到您自己身上。

## 合同变量是自动的"`storage`"类型，并且是永久的数据存储。

你可能已经知道有`storage`变量和`memory`变量。在契约级初始化的变量自动成为`storage`类型。(默认情况下，在函数级初始化的变量是`memory`类型，但是如果需要，可以明确定义为`storage`类型。)

如果您正在通过示例学习，这有另一个含义，您现在可能已经猜到了:**这是您在智能合约上存储数据的方式。**

是的，背后没有什么花哨的魔法。没有数据库调用。如果你开发一个去中心化的应用程序，并且你需要在区块链上存储一些数据，那么就把它放到契约层的一个变量上。

最方便的数据类型可能是`uint`的`array`和`mapping`以及`bytes32`和`address`的。

## 映射是哈希映射。

如果您来自 Javascript 世界，并且习惯于使用文字对象作为哈希映射 Solidity 中的等效数据类型是`mapping` s。

类似于 C++和 Java 这样有泛型的语言，您必须为映射的键和值定义数据类型。

到目前为止，您将使用的最常见的数据类型是`uint`(用于存储 ETH 值或任何数值)、`bytes32`用于存储字符/字符串，以及`address`用于存储用户地址(就像用户 id 一样)。还有一个实际的`string`数据类型，它实际上是一个`bytes`的动态数组，您很快就会发现它的局限性。

# 问题 2:以用户身份向契约发送 ETH:Javascript 端代码

现在你已经有了代码的可靠性，让我们看看你如何从 Javascript 端，从你的 UI 调用它。

```
var message = 'Hello World!';
var amountInWei = 1000000000000000000; // This is 1 ETHmySmartContractInstance.sendEth(web3.fromAscii(message), {from: account, value: amountInWei, gas: 3000000});
```

现在，我们假设之前的代码都是用一个`web3.js`实例设置的，并且它与您部署的契约的 ABI 文件(一个在编译/迁移到活动网络时生成的 json 文件)挂钩。我将在[未来](https://hackernoon.com/tagged/future)部分中介绍更多细节。

让我们看看这段 Javascript 代码中的子问题。

## Javascript 函数调用接受 Solidity 函数的参数，加上一个元数据对象作为最后一个参数。

这是我希望有一篇文章告诉我的事情；我只是在查看了一个现有的简单开源项目后才明白的。

该函数的 Javascript 接口总是接受与您在 Solidity 端声明的参数相同的参数，但是在末尾添加了一个额外的元数据对象，其中包含“from”地址(据我所知不可能是伪造的)，以及您随调用一起发送的 ETH 值。

有了这一点，您就可以通过编程从应用程序的业务逻辑中获取值，并将其传递给这个调用；用户的元掩码实例将提示他们是否同意发送该数量的 ETH。

## 你必须为修改数据或接收付款的函数调用指定一个气体量。

这并不总是用户实际需要花费的汽油量。如果你的智能契约不做大量的循环和耗费 cpu 资源的事情，它很可能很小。但是您仍然必须在这里指定气体量，否则它将不起作用。用户可以选择在元掩码提示符下进行调整。

## ETH 的单位是卫。

你现在可能知道 ETH 有一个词/术语来表示不同面值的货币。最小的面额叫做一个卫。你可以在这里找到完整的名单。常用的命名法似乎是 wei(所有代码交互的缺省值)、Ether 和 Finney (0.001 Ether，或一毫 Ether)。我猜这个想法是当乙醚值 1000 美元时，芬尼大约值 1 美元。

## 字符串必须通过 fromAscii 函数才能转换为有效的 bytes32 数据。

琴弦不容易牢固。字符串本身是动态大小的数组，这使得它不容易在 JS 和 Solidity 之间来回传递，以及在 Solidity 契约本身之间来回传递。如果可能的话，使用`bytes32`更可靠，因为它适用于所有情况。但是这确实意味着你必须使用一个转换函数将一个常规的字符串转换成正确的格式，并且当你从 Solidity 中检索它时再转换回来。

# 这篇文章的内容已经足够了

到目前为止，您已经知道，即使只是一个智能合同，就像简单地接受 ETH 付款而不做任何事情一样，如果您来自其他语言，并且没有好的官方文档/教程/示例来源来学习做这些简单事情的惯例，已经有很多陷阱了。

我不得不通过反复试验来学习几乎所有的上述内容。我希望这篇文章可以帮助其他走在同一条路上的人，当他们到达这些部分的时候。

在建立一个简单的端到端工作以太坊 dapp 的过程中，肯定会遇到很多问题。在下一篇文章中，我将写关于客户端 JS 的`web3.js`设置/结构代码，以及 Javascript 和 Solidity 代码之间更多复杂的交互。