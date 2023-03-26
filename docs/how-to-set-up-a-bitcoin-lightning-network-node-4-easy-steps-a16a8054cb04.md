# 如何设置比特币闪电网络节点(4 个简单步骤)

> 原文：<https://medium.com/hackernoon/how-to-set-up-a-bitcoin-lightning-network-node-4-easy-steps-a16a8054cb04>

本指南将带你设置一台运行 ubuntu/debian 的机器作为 lightning 网络节点。

# 1.安装 Bitcoind

```
$ sudo add-apt-repository -y ppa:bitcoin/bitcoin
$ sudo apt-get update
$ sudo apt-get install -y bitcoind
```

# 2.安装 C 型闪电

[c-lightning](https://github.com/ElementsProject/lightning) 是 blockstream 的闪电网络守护程序。

```
$ sudo apt-get install -y
      \ autoconf automake build-essential git libtool libgmp-dev
      \ libsqlite3-dev python python3 net-tools
$ cd /opt
$ git clone https://github.com/ElementsProject/lightning.git lightningd $ cd lightningd
$ make
```

# 3.配置 Bitcoind 和 C-Lightning

在`**~/.lightning/config**`创建一个文件，内容如下。

```
# network=testnet # uncomment to run on testnet instead of mainnet
```

在`**~/.bitcoin/bitcoin.conf**`创建一个文件，内容如下。

```
# testnet=1 # uncomment to run on testnet instead of mainnet printtoconsole=1
server=1
txindex=1
```

# 4.与区块链同步

根据您的互联网连接速度，此步骤需要 1-4 个小时。

运行[比特币](https://hackernoon.com/tagged/bitcoin)守护进程。

```
$ bitcoind -daemon
```

在另一个终端窗口中运行 lightning 守护程序。

```
$ lightningd
```

检查一切运行正常。

```
$ lightning-cli getinfo
{
 "id": "07798d536c58ede3d3a150ba89679e2dc78a3a0966074024ad4081367abda76719",
 "port": 9735,
 "address": [
  {
   "type": "ipv6",
   "address": "edc7:c9f:dc33:5922:21b2:f0d0:2a02:4f40",
   "port": 9735
  }
 ],
 "version": "v0.5.2-2016-11-21-2438-g1fe79df",
 "blockheight": 1290015,
 "network": "testnet"
}
```

使用[浏览器](https://blockchain.info/)检查您的目标[区块链](https://hackernoon.com/tagged/blockchain)的`**blockheight**`以发现您的同步过程进行到什么程度。

# 结论

您将希望在永久打开并连接到互联网的机器上运行您的节点。即使在下载了区块链之后重启守护进程也需要很长时间，因为它们必须在内存中重新索引事务。此外，您会希望您的节点始终可用，以便它可以接受和路由付款，为您赢得 BTC。

就我个人而言，我使用的是一台旧笔记本电脑，我让它 24/7 开机并连接到我的路由器。为了方便起见，我设置了 ssh 访问，这样我就可以使用它进行支付。查看我的指南[如何进行闪电支付](https://andrewgriffithsonline.com/blog/guide-to-make-lightning-network-payments)来学习如何做到这一点。

*最初发布于*[*andrewgriffithsonline*](https://andrewgriffithsonline.com/blog/180330-how-to-setup-a-lightning-node)*. com .*