# Ethereum:使用 Python 创建原始 JSON-RPC 请求，以便使用智能合约进行部署和交易

> 原文：<https://medium.com/hackernoon/ethereum-create-raw-json-rpc-requests-with-python-for-deploying-and-transacting-with-a-smart-7ceafd6790d9>

![](img/4f02ecaeab670eccaaeaa5393541e565.png)

2018 年 2 月。

**目标**:这篇文章探索了如何向 [Geth](https://geth.ethereum.org/) 节点发送 [JSON-RPC](http://www.jsonrpc.org/specification) 请求，以创建原始事务。我们的目标是在使用高级库(如 [web3py](https://github.com/ethereum/web3.py) 或 [web3js](https://github.com/ethereum/web3.js/) )时，理解并查看后台发生了什么。

我对处理错误和异常也不感兴趣。如果出现任何问题，它将[大声失败](http://oncodingstyle.blogspot.ch/2008/10/fail-early-fail-loudly.html)。这篇文章只是为了教育目的。对于生产，考虑使用 [web3py](https://github.com/ethereum/web3.py) 。

**总结**:我们将仅使用 HTTP 请求在私有网络上部署智能合约并与之交互(调用函数和读取`public`变量)。事务被离线签名，然后才被发送到 geth 节点进行处理。

对于本指南，我使用的是一个私人的[权威证明](https://github.com/ethereum/EIPs/issues/225)网络。如果你想创建这样一个网络，我邀请你阅读我之前的文章。否则使用 [Ganache](http://truffleframework.com/ganache/) (以前叫 TestRPC)或者任何以太坊[网络](https://hackernoon.com/tagged/network)都完全没问题。因此，我不会在这里讨论任何关于建立网络的内容，因为重点是使用 [python](https://hackernoon.com/tagged/python) 向 Geth 节点发送 HTTP 请求。

如果你只是想看完整的代码，跳到最后一章。

# 先决条件

1.  通过 IPC 或 RPC 访问以太坊网络(可能是公共的、私有的或类似 Ganache 的模拟器)。
2.  安装了 python 3。就我个人而言，我喜欢《蟒蛇分布》。
3.  安装最新版本的 [web3py](https://github.com/ethereum/web3.py) 。

# 1.向 Geth 发送一个简单的请求

让我们通过向 Geth 发送一个非常简单的请求来热身。比如问网络 id。第一步是[读取 doc](https://github.com/ethereum/wiki/wiki/JSON-RPC) 。我们需要的方法叫做`net_version`，在这里描述为。

我的 Geth 节点 URL 和端口是:`http://localhost:8501`。如果你使用的是带默认值的 Ganache，URL 可能是`http://localhost:7545`。

我使用[请求 python 库](http://docs.python-requests.org/en/master/)来发出我的 HTTP 请求。说够了。

```
import requests# create persistent HTTP connection
session = requests.Session()# as defined in [https://github.com/ethereum/wiki/wiki/JSON-RPC#net_version](https://github.com/ethereum/wiki/wiki/JSON-RPC#net_version)
method = 'net_version'
params = []
payload= {"jsonrpc":"2.0",
           "method":method,
           "params":params,
           "id":1}headers = {'Content-type': 'application/json'}response = session.post('[http://localhost:8501'](http://127.0.0.1:7545'), json=payload, headers=headers)
print('raw json response: {}'.format(response.json()))
print('network id: {}'.format(response.json()['result']))
```

印刷品:

```
raw json response: {'id': 1, 'jsonrpc': '2.0', 'result': '1515'}
network id: 1515
```

干得好！从那里，我们准备好部署和处理合同。最好建立在良好的基础上。1515 是我在 genesis 文件中定义的私有区块链的网络 id。一切看起来都很棒。使用 Ganache，网络 id 应该是 5777。

但是在能够签署和发送交易之前，我们需要一个地址、一个私钥和一些以太网。

# 2.创建一个私有-公共密钥对并获得一些以太网

web3py (release 4)库将帮助我们创建一个密钥对。

```
import web3w3 = web3.Web3()myAccount = w3.eth.account.create('put some extra entropy here')
myAddress = myAccount.address
myPrivateKey = myAccount.privateKeyprint('my address is     : {}'.format(myAccount.address))
print('my private key is : {}'.format(myAccount.privateKey.hex()))
```

在我的情况下，我得到:

```
my address is    : 0xF464A67CA59606f0fFE159092FF2F474d69FD675
my private key is: 0x94cb9f766ef067eb229da85213439cf4cbbcd0dc97ede9479be5ee4b7a93b96f
```

请**永远不要分享你的私钥**！我在这里这么做是因为这是一个本地私有开发网络，我每天都要破坏和重启几次。我没有在任何公共网络上使用这个密钥对。

现在要得到一些以太这个地址有多种方法:

1.一个非常简单的方法是将这个地址添加到您的`genesis.json`文件中，并启动一个新的网络。从我的[以前的指南](https://hackernoon.com/setup-your-own-private-proof-of-authority-ethereum-network-with-geth-9a0a3750cda8)，这里是我的创世纪文件，包括我们刚刚创建的地址(删除`0x`)。

```
{
    "config": {
        "chainId": 1515,
        "homesteadBlock": 1,
        "eip150Block": 2,
        "eip150Hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
        "eip155Block": 3,
        "eip158Block": 3,
        "byzantiumBlock": 4,
        "clique": {
            "period": 5,
            "epoch": 30000
        }
    },
    "nonce": "0x0",
    "timestamp": "0x5a722c92",
    "extraData": "0x000000000000000000000000000000000000000000000000000000000000000008a58f09194e403d02a1928a7bf78646cfc260b087366ef81db496edd0ea2055ca605e8686eec1e60000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
    "gasLimit": "0x8000000",
    "difficulty": "0x1",
    "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
    "coinbase": "0x0000000000000000000000000000000000000000",
    "alloc": {
        "08a58f09194e403d02a1928a7bf78646cfc260b0": {
            "balance": "0x200000000000000000000000000000000000000000000000000000000000000"
        },
        "87366ef81db496edd0ea2055ca605e8686eec1e6": {
            "balance": "0x200000000000000000000000000000000000000000000000000000000000000"
        },
        "F464A67CA59606f0fFE159092FF2F474d69FD675": {
            "balance": "0x200000000000000000000000000000000000000000000000000000000000000"
        }
    },
    "number": "0x0",
    "gasUsed": "0x0",
    "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}
```

2.如果您有一个挖掘节点或 ganache，打开一个 [Geth Javascript 控制台](https://github.com/ethereum/go-ethereum/wiki/JavaScript-Console)并手工创建一个事务

```
$ geth attach ipc:'http://localhost:8501' // 7545 for ganache
Welcome to the Geth JavaScript console!instance: Geth/v1.7.3-stable-4bb3c89d/linux-amd64/go1.9
coinbase: 0x87366ef81db496edd0ea2055ca605e8686eec1e6
at block: 1585 (Wed, 14 Feb 2018 11:46:04 CET)
 modules: eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0> eth.sendTransaction({'from':eth.coinbase, 'to':'0xF464A67CA59606f0fFE159092FF2F474d69FD675', 'value':1000000000000000000000})
"0xdbc86acbe3644ac2cdb68132bbeecda40733c10f07ca16d87a2e5001e50eec4c"
> exit
```

在这里，我将从`0x87366...`向我的地址`0xF464A...`发送 1000 个以太网。 [1 以太是 1e18 卫](http://ethdocs.org/en/latest/ether.html#denominations) (1 后面跟着 18 个零)。、[字段的](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_sendtransaction)[单位为魏](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_sendtransaction)。

3.在公共测试网上，使用水龙头。

# 3 使用智能合同进行部署和交易

很好，现在我们有了一个带乙醚的地址(用来支付汽油费用),我们可以离线创建我们的事务，签署它，然后用一个原始的 JSON-RPC HTTP 请求将它发送到一个节点。

我们将使用`send_rawTransaction` [方法](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_sendrawtransaction)，该方法将[事务](https://github.com/ethereum/wiki/wiki/JSON-RPC#eth_sendtransaction)的带符号的[参数](https://github.com/ethereum/wiki/wiki/JSON-RPC#parameters-22)作为输入。

python 代码正在寻找 json 文件，该文件包含 truffle 在编译智能契约时创建的契约 abi 和字节码。在测试 python 代码之前，创建一个 truffle 工作空间并编译虚拟契约`AdditionContract.sol`。

```
$ truffle init
// add the smart contract in contracts/
$ truffle compile
```

然后用您的 geth 节点的 URL、truffle 工作区的路径和您的 genesis 文件更新 python 代码(不要忘记在路径中用您的用户名替换我的用户名)。

其他的都在代码中，应该是不言自明的。

我保持一切都非常简单，以方便修改和实验。玩得开心:)