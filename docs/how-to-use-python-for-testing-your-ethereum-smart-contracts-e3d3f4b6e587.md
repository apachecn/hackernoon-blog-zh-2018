# 如何使用 python 测试你的以太坊智能合约

> 原文：<https://medium.com/hackernoon/how-to-use-python-for-testing-your-ethereum-smart-contracts-e3d3f4b6e587>

![](img/74323663c6f832f531f31405bae9710f.png)

最近开始做以太坊区块链开发。包括 solidity 语言在内的大多数工具都偏向于 javascript。

当我开始使用`truffle`进行测试时，我发现 javascript 测试(IMO)不必要的太长。

但这就是语言的本质。

比起 JS，我一直更喜欢 python。我喜欢并在我以前的项目中使用过`py.test`。所以当我有机会使用`py.test`测试我的智能合约时，我肯定会试一试。

就在那时我遇到了`populus`。原来它的目标是“给皮托尼斯塔的松露”(我的原话，不是“官方”标语，但他们可以使用它。)

最初它是一个个人项目——被以太坊组织(在 github 上)正式采用，为迄今为止所做的出色工作增加了可信度。

你可以在这里阅读官方文档

# 测试

正如我前面提到的，我来是为了有机会使用`py.test`来测试智能合约。

编写测试(以及`web3.py`)非常容易。

在 not `populus test`(像`truffle test`)中运行测试。我前面提到过，是通过`py.test`。所以是`py.test <folder_containing_tests>/`

..这就是我遇到“小故障”的地方。

原来有一个已知的[问题](https://github.com/ethereum/populus/issues/431)和一个[解决方案](https://github.com/ethereum/populus/issues/414#issuecomment-352628648)

我喜欢使用`py.test`的一点是，我不需要明确地**开始`testrpc`**

对于`truffle test` testrpc 或`geth`需要显式运行。否则，您会得到以下错误:

```
$ truffle test 
Could not connect to your Ethereum client. Please check that your Ethereum client: 
    - is running 
    - is accepting RPC connections (i.e., "--rpc" option is used in geth) 
    - is accessible over the network 
    - is properly configured in your Truffle configuration file (truffle.js)
```

我不是说这有什么不对，只是说省略一个步骤是件好事。

# 本地链

`populus`比`truffle`更好的一个特性是创建本地`geth`实例的能力。

这可以通过`populus chain new <chain_name>`轻松完成

这将完成以下几项工作:

*   创建一个`genesis.json`(通常需要手工创建，或者从“某处”复制一个现有的并修改)
*   创建一个帐户，有足够的余额。
*   创建两个脚本:一个创建创世块(`init_chain.sh`)，另一个启动“节点”(`run_chain.sh`)

这样做的好处是，新开发人员不需要了解`genesis.json`中各种选项的复杂性，也不需要了解`geth`的一长串命令行选项

它只是工作。

这对半经验丰富开发人员也很有用。她可以根据需要修改`genesis.json`和脚本。

由于没有“更改密码”的概念，如果不删除现有帐户并重新创建一个新帐户，就无法创建一个“更好”的密码。这意味着您需要修改`run_chain.sh`脚本，因为它提到了一个用于`--unlock`参数的帐户。还需要修改`genesis.json`，因为`alloc`中提到了“预填充”账户。

但这没关系，因为`populus`无论如何都是为了开发而设计的。

阅读关于使用 populus 创建本地链的详细教程[这里](http://populus.readthedocs.io/en/latest/tutorial.part-2.html)

*还有* `*populus chain reset <chain_name>*` *但是都不行。我已经提出了一个问题* [*这里*](https://github.com/ethereum/populus/issues/451)

# 部署合同

对于简单的一次性合同，有一个命令行版本:`populus deploy`

对于稍微复杂的部署，尤其是当需要向契约构造函数传递参数时，需要编写自己的 python 代码。

这与在`truffle`平台上编写迁移脚本没有什么不同，除了在`truffle init`平台上有一个“默认”脚本，在这里我们没有。

点击阅读详情

# 迁移

`populus`旧版本中有此功能，但已被移除。当我在 gitter 上被问到时，我被告知有一个“把他们带回来”的计划

# 我应该扔掉松露吗？

还没有。

虽然我想把`populus`作为唯一的选择，但我认为它比`truffle`更“成熟”

目前，我在与他人分享的项目中使用`truffle`(因为`truffle`似乎更“知名”)，但对于我的“个人”项目，我将继续使用`populus`(报告问题，在 gitter 上讨论，并在可能的情况下发送 PR)

**注**:你可以在这里查看我的(在制品)代码[。它既有`truffle`也有`populus`配置文件。测试只在 python 中进行。我有一个 python 的部署脚本。](https://github.com/mandarvaze/eth-playground)

*原载于 2018 年 3 月 20 日*[*mandarvaze . bit bucket . io*](https://mandarvaze.bitbucket.io/posts/populus-ethereum-dev-env/)*。*