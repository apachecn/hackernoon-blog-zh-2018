# 利用加密库从私钥中导出以太坊地址

> 原文：<https://medium.com/hackernoon/utilizing-cryptography-libraries-to-derive-ethereum-addresses-from-private-keys-1bedd1a85bd>

![](img/fae0ceddfd2dda73198e540ed60bc869.png)

## 对于没有数学倾向的开发人员

[密码学](https://hackernoon.com/tagged/cryptocurrency)是[区块链](https://hackernoon.com/tagged/blockchain)和加密货币世界中一切的核心。它用于创建哈希指针，使区块链成为可能，并为钱包创建密钥对！

在本教程中，我们将使用'**椭圆**和' **js-sha3** ' javascript 库，以便利用椭圆曲线加密(ECC)的原理从私钥中导出公钥，然后将公钥转换为其对应的以太坊地址。

许多在线资源从数学角度介绍了椭圆曲线加密，但没有提供适用的代码示例。作为一名程序员，我发现看到方程被分解成易于理解的代码片段可以将这些原则牢牢记住。在深入研究代码之前，让我们先了解一下什么是椭圆曲线加密，为什么它在以太坊生态系统中被认为是安全的，以及如何使用它来生成密钥对。

# **什么是椭圆曲线密码？**

![](img/4ee4b3a8a4c8238fa0fa01ab1abd446b.png)

Elliptic Curve Example

椭圆曲线加密是一种非对称加密形式，它利用椭圆曲线的离散对数问题(ECDSA)来创建公钥和私钥对。本质上，在一个方向上执行操作很容易，但在另一个方向上执行操作的计算开销非常大。

**有可能从公钥找到私钥吗？**

公钥是作为使用椭圆曲线乘法将**私钥(k)** 乘以**生成点(G)** 以获得**公钥(K)的结果而生成的。**

```
**K = k * G**
```

在*标准整数运算*中，如果我们知道 **K** 和 **g，**我们可以简单地将 **K** 除以 **g** 以得到 **k** 。换句话说，将公钥除以生成点将很容易得到私钥。

```
**k = K / G**
```

椭圆曲线算术**没有** **有除法**，因此被认为是单向数学函数。通过将导致 **K、**的所有可能因子相乘，在技术上有可能找到私钥**T5，但是，涉及的数字越大，计算时间就越长。要了解更多信息，我推荐阅读安德烈亚斯·m·安东诺普洛斯和加文·伍德的书[掌握以太坊](https://github.com/ethereumbook/ethereumbook/blob/develop/keys-addresses.asciidoc#elliptic_curve.)中的这一部分。**

# **概述**

如前所述， **K = k * G** 实际上是椭圆曲线上的另一个点。因此， **K = (x，y)** 其中 x 和 y 是它的坐标**。**

要将生成的公钥( **K)** 转换成相应的以太坊地址，我们必须通过 **Keccak-256** 加密散列函数来运行它。产生的散列的最后 20 个字节组成了我们的以太坊地址。

# **让我们跳进代码里吧！**

首先，让我们设置我们的项目。(如果您已经习惯于设置节点项目，可以跳到下一部分。)

打开我们的终端，为我们的代码创建一个目录。

```
mkdir deriveEthAddress
```

然后，使用以下命令进入该目录:

```
cd deriveEthAddress
```

为了能够 npm 安装我们将使用的模块，运行

```
npm init
```

并根据需要填写提示。

接下来，让 npm 安装‘elliptic’和‘js-sha3’库。 **Elliptic** 是一个 javascript 库，具有各种椭圆曲线加密函数， **js-sha3** 为我们提供了一个我们将要使用的散列函数。

```
npm install elliptic --save && npm install js-sha3 --save
```

最后，我们创建项目文件。

```
touch deriveEthAddress.js
```

## 现在我们的项目已经设置好了，在文本编辑器中打开 deriveEthAddress.js。

在 deriveEthAddress.js 中，我们将需要我们安装的模块。下面，我们使用预设的 **secp256k1** (以太坊和比特币都使用的特定椭圆曲线)创建一个新的椭圆曲线实例。我们还需要来自 **js-sha3 的 **keccak256** 哈希函数。**

```
// Required Modules
**const** EC = require('elliptic').ec;
**const** ec = **new** EC('secp256k1');
**const** keccak256 = require('js-sha3').keccak256;
```

然后，我们将为我们的私钥和以太坊地址创建变量。(该密钥对仅用于测试，不应用于存储实际资金。这是从 Ganache CLI 中获取的-更多信息请参见 [github repo](https://github.com/trufflesuite/ganache-cli) 。)

```
// Private Key and Public Ethereum address (from Ganache)
**const** givenPrivateKey = 'c87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3';
**const** givenEthAddress = '0x627306090abab3a6e1400e9345bc60c78a8bef57';
```

接下来，我们将利用等式 **K = k * G.** 从 secp256k1 椭圆曲线实例中获得我们的生成点( **G)** 。`generatorPoint`再乘以`privateKey` ( **k** )。这导致了`pubKeyCoordinates` ( **K** )。正如我们之前讨论的， **K = (x，y)，**所以我们提取坐标 x 和 y，并将其转换为十六进制字符串进行连接。

```
// Get secp256k1 generator point
**const** generatorPoint = ec.g;

// Public Key Coordinates calculated via Elliptic Curve Multiplication
// PublicKeyCoordinates = privateKey * generatorPoint
**const** pubKeyCoordinates = generatorPoint.mul(givenPrivateKey);

**const** x = pubKeyCoordinates.getX().toString('hex');
**const** y = pubKeyCoordinates.getY().toString('hex');

// Public Key = X and Y concatenated
**const** publicKey = x + y;console.log(publicKey);
//af80b90d25145da28c583359beb47b21796b2fe1a23c1511e443e7a64dfdb27d7434c380f0aa4c500e220aa1a9d068514b1ff4d5019e624e7ba1efe82b340a59
```

然后，我们必须通过 **keccak256** 散列函数运行我们的公钥。

```
// Use Keccak-256 hash function to get public key hash
**const** hashOfPublicKey = keccak256(**new** Buffer(publicKey, 'hex'));console.log(hashOfPublicKey);
// 3f704e8b0ee605967f55081f627306090abab3a6e1400e9345bc60c78a8bef57
```

如果我们看看我们的散列，最后 20 个字节(也就是最后 40 位)组成了我们的以太坊地址！让我们把它转换成一个缓冲区，这样我们就可以截取我们想要使用的字节，并把它转换成一个十六进制字符串。然后我们可以添加十六进制前缀`0x`。

```
// Convert hash to buffer
**const** ethAddressBuffer = **new** Buffer(hashOfPublicKey, 'hex');

// Ethereum Address is '0x' concatenated with last 20 bytes
// of the public key hash
**const** ethAddress = ethAddressBuffer.slice(-20).toString('hex');
**const** ethAddressWithPrefix = `0x${ethAddress}`;
```

最后，让我们将我们的派生地址与我们被告知的私钥对应的地址进行比较。

```
// Ethereum Address derived from private key
// is equal to the given address
console.log(ethAddressWithPrefix === givenEthAddress);// true
```

匹配了！这是完整的例子-

# **关闭**

我们只讨论了椭圆曲线加密的基本原理。也就是说，应用于以太网的数学概念非常简单，并且有许多库可以帮助我们！

*感谢阅读！如果你觉得这个有用，给一个* ***下面拍拍*** *和* ***关注*** *我，获取更多 Javascript 和区块链相关文章。*