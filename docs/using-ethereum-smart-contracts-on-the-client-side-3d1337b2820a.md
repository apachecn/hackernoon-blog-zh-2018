# 在客户端使用以太坊智能合约

> 原文：<https://medium.com/hackernoon/using-ethereum-smart-contracts-on-the-client-side-3d1337b2820a>

我最近在做一个项目，试图构建一个从客户端部署的智能契约。这个想法是允许人们上传乘车请求(以及任何种类的购买请求)作为以太坊网络上的智能合同。任何人都可以对它出价，然后所有者可以选择他想从谁那里购买。交易在以太网和 weis 中进行，每个人都需要存款作为参与交易的担保。

有很多方法可以做到这一点。你可以信任每一个使用你服务的人，并把你的信用借给他们。你可以代表参与者自己持有担保。你可以让银行参与进来。所有这些之间的区别在于谁在充当托管人。

我想在以太坊上使用可靠性合同。我有三种方法可以做到这一点。我可以运行一个与我维护的后端通信的前端。后端将与区块链通信，并将交易信息反馈给客户端。这包括建立一个用户账户管理系统——我最终也完成了——和一个与区块链对话的节点服务器。这也会使用户的 UX 更加容易。

我可以尝试整合第三方钱包提供商，如 coinbase 或其他。这也会保持 UX 的简单，也允许其他货币的交易。在这两种情况下，我都必须运行后端。我很好奇要让它完全去中心化。

为此，我想在应用程序的前端部署区块链并与之交互。我可以通过编写和测试智能合同来做到这一点。一旦我确定了它的正确性，我就会编译它并把它放到网上。然后，应用程序可以获取此合同，并部署/使用它。理想情况下，我也希望在前端进行编译，但这可能会给前端带来不必要的开销。目前的方法是好的。

我不会进入 UI 和其他服务，这是应用程序的一部分。无论应用程序的其他部分如何，与区块链的通信都是一样的。

[](https://github.com/O-LAP/contracts/blob/master/contracts/DeliveryRequest.sol) [## o-LAP/合同

### 合同-O-LAP 的合同

github.com](https://github.com/O-LAP/contracts/blob/master/contracts/DeliveryRequest.sol) 

这是用白纸黑字写的合同。这是一个非常简单的投标合同。我做了一些非常基本的测试。

 [## 混合可靠性集成开发环境

### 编辑描述

remix.ethereum.org](https://remix.ethereum.org) 

然后我使用 [remix](https://remix.ethereum.org) 来编译合同，以获得合同 ABI 和字节码。我把这两个硬编码到我的应用程序中，但理想情况下，我想把它们放在网上，只在应用程序中存储链接。

[](https://ethereum.stackexchange.com/questions/27536/where-to-find-contract-abi-in-new-version-of-online-remix-solidity-compiler) [## 在哪里可以找到新版本的在线混音可靠性编译器的合同 ABI？

### 在线 remix solidity 编译器最近更改设计后，我想不出在哪里可以找到合同的…

ethereum.stackexchange.com](https://ethereum.stackexchange.com/questions/27536/where-to-find-contract-abi-in-new-version-of-online-remix-solidity-compiler) 

这个链接对于提取合同的 ABI 和字节码可能很有用。

当有人创建新的购买请求并从已部署的合同中读取值以显示信息时，应用程序部署新的合同，并调用不同的方法从合同中投标、奖励和要求奖励。我假设客户会安装[元掩码](https://metamask.io/)来管理他们的钱包。

这个要点显示了我是如何根据用户请求部署合同的。我遇到的一种情况是，我试图部署一个契约，并立即调用它的一个方法。因此，用户将单击按钮，这将从 MetaMask 打开部署契约请求，几秒钟后(mainnet 上为几分钟)调用契约上的一个方法，用相关信息初始化它。理想情况下，用户只需一次点击就能完成。

一旦我有了部署的契约地址，这就是我调用契约上的方法的方式。

一旦我有了地址，我就是这样从已部署的契约中读取值的。

我仍然遇到一个 CORS 的问题，我需要解决。但我对此有一些想法。

优点:

*   分散的
*   我不运行任何后端。*
*   应用程序可以始终保持最新。

骗局

*   UX 受到限制。
*   期望用户有元掩码。
*   部署合同中的错误是不可修复的。
*   我确实需要为应用程序的其他部分运行后端服务器。

这看起来是一个值得尝试的方法。缺点中列出的问题是可以解决的，或者是我们已经存在的问题。

*Amit Nambiar 为 O-lap 撰写的文章*