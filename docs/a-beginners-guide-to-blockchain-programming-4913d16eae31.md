# 区块链编程初学者指南

> 原文：<https://medium.com/hackernoon/a-beginners-guide-to-blockchain-programming-4913d16eae31>

![](img/1d05613aeb1a8397f9518fb6ccfd838b.png)

在我寻求理解区块链如何工作的过程中，我有很多问题。最重要的是 ***“我如何在其上构建应用程序？”*** 。经过几周的挖掘、阅读和实验，终于得到了它。我找不到简短而全面的指南。现在，我有了一些像样的理解，我想写一个可以帮助别人。这是一个轻量级的速度指南，为了减少学习曲线，我只保留了重要的部分。

![](img/4620f371581c46a8a0c5472bbf861c29.png)

# 章

1.  区块链的目的
2.  区块链是怎么发明的？
3.  以太坊和智能合约简介
4.  以太坊上的智能合约编程
5.  前进的道路

# 区块链的目的

![](img/64564e3e952aef6a5cf2ac8aa346cb45.png)

*Roopa 住在德里的一个偏远地区。印度政府每月分配给她少量的食物资源。因为她属于 BPL(贫困线以下)类别。政府通过中间人来分配这些食物资源。只有三分之一的分配食物资源到达像 Roopa 这样的人手中，其余的由中间人出售牟利。*

莎拉写小说，并在亚马逊上发表。她很不高兴，因为亚马逊收取销售额的 50%作为佣金。这不公平，因为只有她一个人付出了写作和营销的努力。

![](img/aa43537d575c4391fd46e57d2496e15a.png)

问题是中间人渴望权力和金钱。他们的座右铭已经变成了“不惜任何代价获取利润”，为了支持生产者和赋予穷人权力，我们需要中间商行为合乎道德。那几乎是不可能实现的， ***但是如果我们可以用一个自治系统代替中间人呢？***

![](img/a50bb1f553f8fe3535b950936f7ab1da.png)

因为计算机没有偏见，它们既不需要钱也不需要能量。2008 年，当 Satoshi Namakato 利用区块链技术发明比特币时，这可能是他的想法。

# 区块链是怎么发明的？

![](img/815945d8878271455d54a3b1bf23850f.png)

随着时间的推移，货币发生了演变，每一次演变都降低了其生产成本，并使其交易更加方便。金币的生产成本很高。纸币的发明解决了这个问题。但是，在计算机和互联网发明之后，人们发现了一种更好的方式来使交易变得方便和快捷。

为了安全保存我们一生的收入，并促进数字交易，我们需要一个中间人(银行)。这使得银行变得强大，他们可以对我们的取款/交易收取高额费用，出售我们的私人信息，等等

![](img/1183a745f5eba298d524f86ff3962adf.png)

银行对资金的渴求导致了 2008 年的金融危机。银行不尊重客户的隐私。他们薄弱的安全系统导致了数字欺诈。

货币的下一次进化必须解决以下问题。

1.  它不应该存储在一个中央实体中。
2.  它需要高度安全。
3.  它应该确保隐私。

由于法定货币由政府控制，Satoshi 别无选择，只能发明一种新货币(比特币)。他借助对等网络和密码学解决了这些问题。

## 分散

![](img/f7ed0e2f211d5afb1e2405b5ad32dc61.png)

Credits : [xkcd](https://www.google.com/url?sa=i&source=images&cd=&cad=rja&uact=8&ved=2ahUKEwipraWlgZHbAhXFRo8KHY7GDpsQjxx6BAgBEAI&url=https%3A%2F%2Fxkcd.com%2F350%2F&psig=AOvVaw16mXSfPPAdqG8YZQXCdSYi&ust=1526792963456864)

Torrents 使用点对点技术来共享文件。torrent 应用程序不从中央服务器或单台计算机下载文件，而是连接到其网络中的人，找出谁拥有该文件，并从他们的计算机上下载。

你从世界各地不同的电脑上得到文件的碎片。如果网络中的一个人离开了，你的下载不会受到影响，因为还有其他人可以共享文件。

Satoshi 采用了这项技术，因为它以分散的方式存储资金。没有一个实体能够控制它。

## 密码系统

![](img/256626023f341c248b680da88ce4c1d8.png)

Credits : [xkcd](https://www.google.com/url?sa=i&source=images&cd=&cad=rja&uact=8&ved=2ahUKEwidmv_HgZHbAhXIrI8KHc_bCL0Qjxx6BAgBEAI&url=https%3A%2F%2Fxkcd.com%2F153%2F&psig=AOvVaw0T82cRL8QpgULqBn2-N57f&ust=1526793072257766)

在密码学中，人们可以对信息进行数字签名。为了做到这一点，我们需要三样东西:公钥、私钥和消息。

公钥和私钥是一组数学上相关的长字符。公钥像用户名一样是公开的，私钥像密码一样是秘密的。

消息是您想要授权的信息，例如:“*我授权您支付约翰 100 美元*”。

如果你输入带有公钥、私钥和消息的算法。加密算法将产生一个签名。这是该消息内容特有的另一组字符。

```
**Public Key** -----BEGIN EC PUBLIC KEY----- MFkwEwYHKoZIzj0CAQYIKoZIzj0DAQcDQgAE50uE+YSxqDgMkFByhpcgTVqXCqHO h68Ljt1z0jklDff/WV7xo+U6o3REBtK/C0/LM+Ef3FB3wR9aXMGNMLb9EA== -----END EC PUBLIC KEY-----**Private Key** -----BEGIN EC PRIVATE KEY----- MIGHAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBG0wawIBAQQgwqIsXl9FqsgrzMdx axI6flBwWIev0Z7i+WF4j8BGnrKhRANCAATnS4T5hLGoOAyQUHKGlyBNWpcKoc6H rwuO3XPSOSUN9/9ZXvGj5TqjdEQG0r8LT8sz4R/cUHfBH1pcwY0wtv0Q -----END EC PRIVATE KEY-----**Message** Hello World**Signature** B0A9A4F641D3A2E3A65576B7311DCD62ABE78BBF4D3F5FE856598508E24FCB2E6F0277C2F8D57E9E2E108B7C493986E783F5316B8046597019951669B4EE6922
```

为了验证消息，必须输入公钥、消息和签名。加密算法可以验证消息是否由公钥的所有者签名。

破解密码算法需要 1000 年。由于我们的计算能力有限，这不可能做得更快。未来的量子计算机可能会挑战这一点。但是，比特币系统可以升级以确保安全。

Satoshi 在他的系统中加入了加密技术，以帮助人们授权从钱包中进行比特币交易。

## 隐私

![](img/f95ebb6282ed4da7c88242a89caea927.png)

Credits : [xkcd](https://xkcd.com/1269/)

你通过生成钱包(公钥/私钥)注册到比特币。系统不会收集任何信息，如电子邮件 id、全名等。因此你是匿名的，除非你公布你的公钥。

## 把它放在一起

Satoshi 使用加密技术和点对点网络建立了一个共享账本。当某人向某人发送比特币时，消息会被加密签名并向网络中的所有人广播。他们更新他们的分类账，因此网络中的每个人都知道谁拥有什么。

## 区块链

![](img/83d1fa2d2c649ccc77c74377da5b40c1.png)

每隔十分钟，事务被分组到一个块中，并链接回以前的块。这个过程形成了连续的区块链。挖掘是一个确认区块的过程，这涉及到网络中的计算机来解决一个数学问题。第一个解决这个问题的计算机/矿工将获得凭空制造的比特币奖励。

一旦数据块被确认并添加到网络中，它就会在整个网络中复制。区块链是在制作自动比特币系统的过程中发明的，该系统在没有人工干预的情况下确认交易。如果你在寻找对区块链的简单解释，这里有一个故事。

[](https://hackernoon.com/a-beginners-guide-to-blockchain-d04266844e7) [## 区块链初学者指南

### 我不明白为什么人们觉得理解区块链很难。我想知道为什么我自己没有。那是在 2013 年我第一次…

hackernoon.com](https://hackernoon.com/a-beginners-guide-to-blockchain-d04266844e7) 

# 以太坊和智能合约简介

![](img/63ed39d542c518cada48d88a4ea70c08.png)

前面我们谈到了用自治系统代替中间人。这可以通过编程来实现。比特币的系统让人们很难编写自主系统的代码。

因此，Vitalik Buterin 建立了一种新的加密货币叫做以太坊。它不仅是一种去中心化的加密货币，也是一个计算机网络，可以以智能合约的形式托管代码。

![](img/0baa3b51aff05a7a55e62e8f1928d4f9.png)

在智能合约中，我们可以设定条件。如果你想建立一个分散的书店。你写一些说明来帮助作者添加新书，一旦顾客完成交易就发送电子书的下载链接等等。

智能合约不仅存储条件，还存储数据。分散书店的智能合同本身存储图书列表、购买等。

![](img/091904de1b58a6354ba7b899b16a154b.png)

然而，我们应该承认智能合同的局限性。有些系统需要人的支持，计算机处理不了。在现实世界中，实现智能合约并不容易。聪明的合同一旦公布就无法更改，愚蠢的错误可能代价高昂。

# 以太坊上的智能合约编程

![](img/b30dd3dd026eacff361df41d0838a9bf.png)

Credits : [xkcd](https://xkcd.com/501/)

我们将建立一个简单的智能合同，存储和检索学生的成绩。我们将对合同进行可靠的编码。这里是 [github 回购](https://github.com/jamesfebin/GradesDapp)。

```
pragma solidity ^0.4.18;contract Grades{}
```

第一行告诉编译器我们正在使用哪个版本的 solidity。然后我们定义合同等级。

![](img/e9ef9f8e5ffddf2adf3dde1dfcd1e160.png)

我们需要在合同中存储两件事，学生的名字和他们的成绩。因此，我们将创建一个数组来存储学生姓名，并创建一个关联数组来存储他们的分数。

```
pragma solidity ^0.4.18;contract Grades{mapping (bytes32 => string) public grades;
bytes32[] public studentList;}
```

现在，我们将创建一种方法，通过它我们可以发送合同，学生姓名的列表。我们将在构造函数中这样做。

```
pragma solidity ^0.4.18;contract Grades{mapping (bytes32 => string) public grades;
bytes32[] public studentList;function Grades(bytes32[] studentNames) public {
studentList = studentNames;
}}
```

在 Solidity 中，我们将只调用构造函数一次。我们将传递学生姓名作为参数，它将存储在我们前面声明的 studentList 数组中。

现在，我们需要编写一个函数来给学生打分。我们还需要另一个函数来检查学生是否有效。

```
pragma solidity ^0.4.18;contract Grades{mapping (bytes32 => string) public grades;
bytes32[] public studentList;function Grades(bytes32[] studentNames) public {
studentList = studentNames;
}function giveGradeToStudent(bytes32 student, string grade) public {
require(validStudent(student));
grades[student] = grade;
}function validStudent(bytes32 student) view public returns (bool) {
for(uint i = 0; i < studentList.length; i++) {
if (studentList[i] == student) {
return true;
}
}
return false;
}}
```

“ *giveGradeToStudent* ”函数有两个参数，学生姓名和年级。require 函数检查“ *validStudent* ”函数是否返回 true 或 false。如果返回 false，则取消执行。

最后，我们需要编写一个函数来获取学生的分数。函数 getGradeForStudent 将学生姓名作为参数，从关联数组中返回相应的分数。

```
pragma solidity ^0.4.18;contract Grades{mapping (bytes32 => string) public grades;
bytes32[] public studentList;function Grades(bytes32[] studentNames) public {
studentList = studentNames;
}function giveGradeToStudent(bytes32 student, string grade) public {
require(validStudent(student));
grades[student] = grade;
}function validStudent(bytes32 student) view public returns (bool) {
for(uint i = 0; i < studentList.length; i++) {
if (studentList[i] == student) {
return true;
}
}
return false;
}function getGradeForStudent(bytes32 student) view public returns (string) {
require(validStudent(student));
return grades[student];
}}
```

出于本教程的目的，您可以将它部署在个人区块链上。可以用 [Ganache](http://truffleframework.com/ganache/) 创作。下面是安装和运行 ganache 的命令。

```
npm install ganache-cli web3@0.20.3 solc
node_modules/.bin/ganache-cli
```

保持 ganache 运行，我们将在新的终端上部署我们的智能合约。请将智能合同保存为“Grades.sol”。

让我们编译代码。

```
**node** code = fs.readFileSync('Grades.sol').toString()
solc = require('solc')
compiledCode = solc.compile(code)
```

现在，让我们部署智能合约。在区块链上部署合同会耗费你的汽油，这是对那些将计算能力出租给你的人的奖励。所以我们必须指定你愿意分配的汽油数量。你可以用[气体计算器](https://ethgasstation.info)来估算。但是，您不必现在付费，因为您正在部署个人区块链，并且正在使用您的资源。当你在区块链公共以太坊上部署契约时，你必须付费。

```
Web3 = require('web3')web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));abiDefinition = JSON.parse(compiledCode.contracts[':Grades'].interface)GradesContract = web3.eth.contract(abiDefinition)byteCode = compiledCode.contracts[':Grades'].bytecodedeployedContract = GradesContract.new(['John','James'],{data: byteCode, from: web3.eth.accounts[0], gas: 4700000})
```

现在让我们调用我们的函数给学生约翰打 A+。稍后，我们将使用我们的' *getGradeForStudent* '函数检查它是否已被更新。

```
deployedContract.giveGradeToStudent('John', 'A+', {from: web3.eth.accounts[0]})deployedContract.getGradeForStudent.call('John')
**'A+'**
```

恭喜，您已经部署了您的智能合同。如果你有问题/疑问，请对这个故事发表评论，我会回复或更新这个故事。

# 前进的道路

关于写以太坊智能合约，可以在以下网站进行深入了解。

## 神秘僵尸

你不用下载任何东西，你可以在他们的交互式编译器上写代码。你将通过构建一个游戏来学习可靠性编程的概念。它是免费的。

[](http://cryptozombies.io/) [## 隐型僵尸-学习编码以太坊上的游戏。由织机网络供电

### CryptoZombies 是一个交互式代码学校，教你通过建立你的…

cryptozombies.io](http://cryptozombies.io/) 

## 扎斯特林

Zastrin 有免费和付费课程。[马赫什·穆尔蒂](https://medium.com/u/14d83a8fab8c?source=post_page-----4913d16eae31--------------------------------)涵盖了所有重要的方面。他们甚至教你建立一个类似易贝的分散市场。

[](https://www.zastrin.com/?zref=febin) [## Zastrin |通过做真实项目学习以太坊编程

### 通过构建真实世界的项目来学习以太坊和 IPFS 等去中心化技术。

www.zastrin.com](https://www.zastrin.com/?zref=febin) 

请鼓掌👏，谢谢你😊。关注我们[**hacker noon**](https://hackernoon.com)**和我(**[**Febin John James**](https://medium.com/u/75a616711f4e?source=post_page-----4913d16eae31--------------------------------)**)了解更多故事。**

我希望能写更多关于区块链建筑应用的系列文章。你可以通过 [**请我喝咖啡**](http://buymeacoff.ee/febin) **来支持我。**

[![](img/9bc9302e3ce8de5c03a94d1822dc813c.png)](http://buymeacoff.ee/febin)