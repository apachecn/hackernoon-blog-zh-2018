# 灵动网络入门

> 原文：<https://medium.com/hackernoon/getting-started-with-the-liquid-network-c87e2cb5996b>

开始运行流动节点，转移到侧链，要求 L-BTC，并发行资产！

![](img/32fb7ab47d05efd178511ca092b36167.png)

## 简介:

流动网络是一个比特币侧链，有助于快速和私人交易。侧链是一个独立的区块链，其特征与基本链不同，但为了利用主链的本币，侧链与基本链挂钩。液体中的双向挂钩与比特币的比例是 1:1，所以你每挂钩一个比特币到网络中，你就解锁或创造了 1 个液体比特币(简称 L-BTC)。Liquid 通过被称为机密交易的技术，提供 1 分钟的短时间内快速结算和隐私。外挂基本上只需要你把 BTC 发送到一个多重签名地址，这个地址是由所谓的强大联盟的成员控制的。挂钩(转移回比特币链)要求你通过联盟成员交易所。

## 跳进来:

Liquid 源代码和 wallet / full node 二进制文件托管在 Github [这里是](https://github.com/Blockstream/liquid/releases)。目前，有适用于 Windows、MacOS 和 Linux 的二进制文件。现在甚至有一个图形用户界面！在这篇文章中，我不会深入探讨建筑和其他实用工具。您可以使用这个[文档](https://github.com/Blockstream/liquid/blob/liquid.3.14.1/doc/build-unix.md)从源代码构建 Liquid。和`bitcoind`号建筑差不多。

`liquidd`需要*一个运行中的`bitcoind`来服务 RPC 调用。就像`bitcoind`用`bitcoin-cli`操作一样，`liquidd`用`liquid-cli`操作。因此，在`liquidd`运行时，所有命令都需要用`liquid-cli`运行。您的流动节点需要能够与比特币节点通信，以验证插件并申请流动比特币。

**如果不想验证插件，* `*bitcoind*` *就不需要。* `*liquidd*` *然后可以用* `*validatepegin=0*` *自行运行。*

这意味着在`bitcoin.conf`中，需要以下内容:

```
server=1
rpcuser=
rpcpassword=
```

液体也需要一个`liquid.conf`(在`~/.liquid/`中)与这些条目:

```
mainchainrpcuser=  <-- rpcuser in bitcoin.conf
mainchainrpcpassword=  <-- rpcpassword in bitcoin.conf
mainchainrpchost=  <-- usually 127.0.0.1
mainchainrpcport=  <-- usually 8332
```

就是这样！你现在可以启动`liquidd`并验证侧链的模块。就像用`bitcoind`一样，你可以用:

`tail -f ~/.liquid/liquidv1/debug.log`

## 追溯:

启动一个插件非常简单。这只是告诉`liquidd`生成一个`peginaddress`让你发送比特币。

`$ liquid-cli getpeginaddress`

这将输出一个插件地址和一个索赔脚本。如果`peginaddress`不在您的钱包中，则需要索赔脚本——例如，如果您已经在离线计算机上生成了`peginadddress`,并且正在单独的计算机上提交索赔。

一旦你生成了一个地址，你就可以向它发送比特币。出于安全目的，需要 102 个比特币块来完成锁定过程。在 2 个流动街区，挂牌要快得多，但是需要你通过流动会员交换来完成。

## 声称 L-BTC:

一旦 102 个比特币区块被开采出来，你就可以在液态侧链上获得你的 L-BTC。要做到这一点，需要两件事情，如果您使用的钱包**中没有* `*peginaddress*` *** ，还可以选择索赔脚本。

您需要:

*   固定交易的原始交易(十六进制)
*   由`bitcoind`产生的一个`txoutproof`
*   (可选)使用`getpeginaddress`生成的索赔脚本

一旦你有了这些，你就可以使用`liquid-cli`来认领你的 L-BTC:

(示例来自`liquid-cli`帮助)

```
> liquid-cli claimpegin "0200000002b80a99d63ca943d72141750d983a3eeda3a5c5a92aa962884ffb141eb49ffb4f000000006a473044022031ffe1d76decdfbbdb7e2ee6010e865a5134137c261e1921da0348b95a207f9e02203596b065c197e31bcc2f80575154774ac4e80acd7d812c91d93c4ca6a3636f27012102d2130dfbbae9bd27eee126182a39878ac4e117d0850f04db0326981f43447f9efeffffffb80a99d63ca943d72141750d983a3eeda3a5c5a92aa962884ffb141eb49ffb4f010000006b483045022100cf041ce0eb249ae5a6bc33c71c156549c7e5ad877ae39e2e3b9c8f1d81ed35060220472d4e4bcc3b7c8d1b34e467f46d80480959183d743dad73b1ed0e93ec9fd14f012103e73e8b55478ab9c5de22e2a9e73c3e6aca2c2e93cd2bad5dc4436a9a455a5c44feffffff0200e1f5050000000017a914da1745e9b549bd0bfa1a569971c77eba30cd5a4b87e86cbe00000000001976a914a25fe72e7139fd3f61936b228d657b2548b3936a88acc0020000", "00000020976e918ed537b0f99028648f2a25c0bd4513644fb84d9cbe1108b4df6b8edf6ba715c424110f0934265bf8c5763d9cc9f1675a0f728b35b9bc5875f6806be3d19cd5b159ffff7f2000000000020000000224eab3da09d99407cb79f0089e3257414c4121cb85a320e1fd0f88678b6b798e0713a8d66544b6f631f9b6d281c71633fb91a67619b189a06bab09794d5554a60105" "0014058c769ffc7d12c35cddec87384506f536383f9c"
```

## 发放资产:

与其他平台相比，发行资产简单得可笑。

`$ liquid-cli issueasset 1000 1 true`

这将创建一个具有 1000 个单位和 1 个重新发放令牌的资产。它也是秘密发行的，所以你可以完全秘密地发行和分配资产——就像它们只是普通的流动交易一样。或者，您可以取消隐藏发行，使其成为非私有发行。

## 转让 BTC 或资产:

传输 L-BTC 与使用`bitcoin-cli`相同:

有人生成了一个机密地址:

`$ liquid-cli getnewaddress`

他们给了你地址。然后，你发送 L-BTC:

```
$ liquid-cli sendtoaddress  VTq74LvRzjwxqNg47UaJgCqm8go8diH6XtBZdgHhNhPGVRC8xVj8adnMLQ9NfwV87iUnJ5jni2wAQoYT 0.01
```

这将 0.01 升 BTC 发送到我生成的演示地址。

或者资产:

```
$ liquid-cli sendtoaddress VTq5YvHXHq4rsSVQNuGVC4ahrd25c1XrPqv59DBD73u9ZKbk2g6794Ge9zEKcR3zVaTR3diahW6S3vyx 0.01 "giveaway" "[@RobEdb](http://twitter.com/RobEdb)" false e33b0c87afdef7e0d942daf1d9735b9a79ddefc10a3a631783f397f86f418278
```

这会向@RobEdb 发送 0.01 的资产(在本例中是 Grubles Tickets)。

## 结论:

现在，您应该准备好交易和发行流动侧链上的资产了！相当简单！此外，不要忘记加密你的液体`wallet.dat`,就像加密你的比特币钱包一样。如果您想发送提示，可以使用我之前使用的演示地址:

```
VTptRF5zL4kzEYwCrRjAbSPGAGUmujuxVHY7z6w5dypXm2hzGB5YqbybPKFSJih7onnX3WBRnnHQqQsf
```

感谢阅读！