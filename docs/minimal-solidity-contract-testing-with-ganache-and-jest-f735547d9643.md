# 基于 Ganache 和 Jest 的最小可靠性契约测试

> 原文：<https://medium.com/hackernoon/minimal-solidity-contract-testing-with-ganache-and-jest-f735547d9643>

最近，在为 dapps 编写一些前端工具时，我遇到了快速测试我的库是否能与真正的 Solidity 契约一起工作的需求。

![](img/b24c5db198f3f035d3ad475f43f3180b.png)

虽然我总是建议 dapp 制造商从块菌项目开始，但有时你需要更轻量级的东西。例如，与其必须运行迁移，**也许您只想编译一个智能契约并立即进行测试。**

不用担心，松露工具套件已经覆盖了你！

在本教程中，我将向您展示如何用 Ganache 和 Jest 设置最小智能契约测试。

***如果不需要循序渐进的解释，回购的例子就在这里:***

[](https://github.com/adrianmcli/ganache-jest-example) [## adrianm CLI/gan ache-jest-example

### 🚀使用 Ganache 和 Jest 进行最小可靠性契约测试-adrianm CLI/gan ache-Jest-example

github.com](https://github.com/adrianmcli/ganache-jest-example) 

# 仅仅三步

本质上，你只需要做三件事:

1.  编写可靠性合同
2.  催生一个测试“区块链”
3.  部署合同。

听起来很吓人，对吧？但是感谢松露和整个社区，这实际上比你想象的要简单得多。

# 我们的合同

在我们开始之前，让我们创建我们的合同，`SimpleStorage.sol`:

这个契约很简单，它允许你获取并设置一个整数，就是这样。

# 编制合同

我们将使用 [solc-js](https://github.com/ethereum/solc-js) 来编译我们的可靠性合同。这意味着我们将不得不遵循他们的做事方式，这可能会有点奇怪，所以让我们为此创建一个新文件。

启动一个名为`compile.js`的新文件，并粘贴以下内容:

我们正在导出一个函数`compile`，它将接受一个文件名，并在同一个文件夹中查找它。我们以它想要的方式传递`solc`options 对象(即`input`)，它抛出一个 JSON，我们可以从中提取工件。

请注意，我也有一个版本的回购使用`truffle-compile`而不是`solc`。这两种方式都有利弊，你可以随意做出自己的判断。

# 用加纳切繁殖一只试验区块链

对于这一部分和下一部分，我将首先解释这些概念，然后我将向您展示整个文件，所有这些都在这里。所以请注意，我保证会有回报的！

繁殖区块链测试比我想象的要简单得多。实际上你唯一需要做的就是:

```
// import Ganache
const Ganache = require("ganache-core");// spawn the test "blockchain" provider
const provider = Ganache.provider();// use it like how you would normally use a provider
const web3 = new Web3(provider);
const accounts = await web3.eth.getAccounts();
```

这个一行程序本质上为你做了所有繁重的工作。它基本上会在内存中生成一个区块链，并给你一个`provider`对象来与之交互。

从提供者那里，我们可以很容易地获得`web3`实例，以及下一步需要的`accounts`数组。

# 部署您的合同

现在我们有了 web3 和契约工件，我们可以将它部署到我们的测试区块链上。

```
const instance = new web3.eth.Contract(SimpleStorage.abi);const deployedInstance = await instance.deploy({
  data: SimpleStorage.evm.bytecode.object
}).send({
  from: accounts[0],
  gas: 150000
});
```

这是 web 3 . js API(1.0 版)的标准用法。我们首先通过从工件`SimpleStorage.abi`传入 ABI 来创建我们的契约实例。然后，我们通过调用合同工件中的字节码`.deploy()`来部署它，最后从我们的帐户中调用指定 gas 量的`send()`来确保它通过。

# 把所有的放在一起

既然我们知道了我们需要做什么，我们可以创建一个名为`test.js`的新文件，并开始编写我们的测试。我们谈论的大部分是为我们的测试设置环境，所以它将从 Jest 的`beforeAll()`钩子下。

现在，我将向您展示整个文件:

请注意，`provider`、`web3`、`accounts`和`contractInstance`变量是在`beforeAll()`钩子之外声明的，因此我们可以在测试中使用它们。

我们还有一个`afterAll()`钩子，在这里我们调用提供者上的`stop()`来防止内存泄漏。

最后，实际的示例测试演示了如何设置和获取值。

# 一些想法

当然，如果你有许多不同的契约，并且它们是相互继承的，这可能会有点难以操作。对于那些用例，我会强烈推荐一个传统的块菌项目。

然而，如果您有一些简单的东西要测试，或者如果您想从您的 Truffle 项目中单独测试您的前端，这是一个允许您这样做的很好的选择。

请记住，生成一个 Ganache 提供者需要几秒钟的时间，所以不要到处都这样做。如果你开始不得不这样做，这可能是另一个迹象，表明你应该考虑将你的项目转换为全面的松露项目。

这种格式可能不适合所有人，所以请务必查看您可以自己克隆和运行的示例回购！

[](https://github.com/adrianmcli/ganache-jest-example) [## adrianm CLI/gan ache-jest-example

### 🚀使用 Ganache 和 Jest 进行最小可靠性契约测试-adrianm CLI/gan ache-Jest-example

github.com](https://github.com/adrianmcli/ganache-jest-example) 

如果你喜欢这篇文章，请给我几个掌声！