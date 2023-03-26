# 如何推出自己的生产就绪型加密货币

> 原文：<https://medium.com/hackernoon/how-to-launch-your-own-production-ready-cryptocurrency-ab97cb773371>

![](img/b5aca4365fe71bcf925deca00f580ea7.png)

*本文涵盖* [*【区块链】*](https://hackernoon.com/tagged/blockchain) *为* [*加密货币*](https://hackernoon.com/tagged/cryptocurrency) *编程，测试代码，并部署到区块链。了解如何为智能合同执行创建钱包和简单的用户界面。*

我们赚钱的方式正在改变。你能做些什么来跟上？区块链和加密货币已经存在多年了，但只是在过去几个月，这一话题才远远超出了神秘的互联网论坛和科技公司休息室。

也许是时候让你自己制造加密货币了，让你自己成为商品和服务的交易媒介。这听起来可能很复杂！但是这比你想象的要简单——这要归功于去中心化社区已经取得的巨大进步和持续的创新。

# 未来的银行

一种加密货币取代了今天的银行。银行会记录你账户里的钱数。你可以信任一个由任何人和每个人组成的大规模计算机网络来公开跟踪，而不是信任一个单独的机构来为你跟踪。这个网络中的集体计算机确认每一笔已经发生和将要发生的货币交易。这种公众共识是人们在使用加密货币进行支付时所依赖的保证。

你自己的加密货币可以是你接受的商业代币——有点像街机里的代币。这种货币可以在今天产生。第一步是选择一个大型的、分散的计算机网络，该网络可以不断地确认区块链新增内容的合法性。我们选以太坊吧。

# 智能合约编程

以太坊是一个用于执行智能合约的去中心化计算平台。这些程序在透明的环境中运行代码，没有第三方篡改的可能性。用户的私钥用于为执行智能合约的每个请求创建一个基本唯一的签名(如果您不熟悉非对称加密，请参见*[*本维基*](https://theethereum.wiki/w/index.php/Accounts,_Addresses,_Public_And_Private_Keys,_And_Tokens) *)。**

*以太坊社区拥有相当数量的开源软件工程师，他们为了人类的更大利益不懈地开发惊人的工具。要在以太坊网络上制作自己的加密货币，你需要这四个工具:*

*   *[Solidity](https://solidity.readthedocs.io/) —以太坊智能合约编程语言。*
*   *[松露框架](http://truffleframework.com/docs/)——以太坊开发套件。*
*   *[Web3.js](https://github.com/ethereum/wiki/wiki/JavaScript-API) —一个 JavaScript 包，用于通过互联网浏览器或 Node.js 与以太坊网络进行交互*
*   *以太(ETH)—[以太坊网络](https://ethereum.org/)的货币。*

*可选工具:*

*   *[meta mask](https://metamask.io/)——用于加密支付的 Chrome 扩展。*

*加密货币仅仅是区块链的无限用例之一。团结在 Solidity 周围的优秀社区吸引人们投入精力用以太坊构建去中心化的应用程序。首先，让我们构建自己的加密货币，开始学习区块链开发。*

# *开发 ERC-20 令牌*

*以太坊社区已经建立了一些关于智能合约功能的标准，包括令牌。本教程中的加密货币令牌基于 [ERC-20 令牌标准](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md)。*

*如果您没有 node.js，请现在安装它(*下面的测试不适用于早于****node . js 8***)的版本)。然后打开终端窗口，执行以下操作:*

```
*npm install -g truffle
mkdir MyToken && cd MyToken
truffle init
npm init -y
npm install -E zeppelin-solidity*
```

*这将安装 Truffle CLI，为您的令牌创建一个项目目录，并安装一个名为 [OpenZeppelin](https://github.com/OpenZeppelin/zeppelin-solidity) 的开源 Solidity 库。*

*接下来，我们可以开始写我们的可靠性合同。花几分钟时间熟悉一下[的坚固性](https://en.wikipedia.org/wiki/Solidity)以及令牌标准中注明的**危险** [。在您的项目目录中，在自动生成的`contracts/`文件夹中创建一个文件，并将其命名为 **Token.sol** 。添加以下遵循 ERC-20 令牌标准规范的代码。](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20.md#approve)*

*这种代币是不可开采的，而且有固定的供应量。默认情况下，有**10 亿个代币**，每个代币可以分成小数 18 位以内的分数。代币的最小分数在以太坊被称为**威**或者在比特币被称为**智**。您可以将这些数字、令牌名称和令牌符号更改为您想要的任何值(*参见上面的构造函数*)。*

*一旦这个代码被部署到主以太坊网络，任何想要发送或接收你的令牌的人都将执行这个合同中的代码。`balances`映射是保存所有关于谁拥有令牌的信息的数据结构。**地址**是钱包主人的公钥，**无符号 256 位整数**是钱包中令牌 Wei 的编号。*

# *读取与写入*

*区块链的读写操作有两个简单的规则:读是自由的，写不是。由于我们使用的是以太网，所以 **ETH 在做区块链加法时需要调用方花费**。加法是通过改变状态的函数来实现的，比如将令牌从一个钱包转移到另一个钱包。这与读取功能形成对比，例如查看钱包的余额。读取功能不会向区块链添加新数据，它们总是**自由执行**。*

# *测试您的代码*

*在我们部署我们的合同之前，我们应该彻底地测试它。Truffle 在其入门指南中包含了[可靠性测试](http://truffleframework.com/docs/getting_started/solidity-tests)和 [JavaScript 测试](http://truffleframework.com/docs/getting_started/javascript-tests)文档。我已经用 JavaScript 包含了示例集成测试(truffle-keys [这里是](https://github.com/ajb413/erc20-ethereum-token/blob/master/test/truffle-keys.js))。*

***test/integration . test . js—node . js 8 或更高版本***

*这里的测试并不详尽，但是对于一个正在学习可靠性的工程师来说，它们是一个很好的起点。这个测试文件只能在`truffle develop`环境中运行。*

*为了使用 truffle 将合同迁移到以太坊客户端，将这个文件 **2_deploy_contracts.js** 添加到`migrations/`中。运行测试需要这个迁移步骤。*

*为了运行集成测试，我们将使用 Truffle CLI:*

```
*npm i ethereumjs-txtruffle develop
truffle(develop)> test*
```

*块菌开发命令在你的本地机器上引导一个测试区块链。测试网络有 10 个默认以太坊密钥对，每次服务器启动时，每个密钥对都有 **100 ETH** 。这些键将调用 Solidity 契约中的函数来测试它们的功能。*

*`test`命令将执行`test/`文件夹中的所有测试。探索`integration.test.js`文件，以准确了解测试对你的测试钱包和测试区块链做了什么。*

# *部署*

*在你适应了网络油价和编写自己的测试之后，你可以把你的令牌放到一个公共测试网络上。Ropsten 网络是用于以太坊开发的几个公共测试网络之一。网络连接可以在`truffle.js`文件中[配置](http://truffleframework.com/docs/advanced/configuration)。*

*如果你没有自己的以太坊钱包，我建议你现在就做一个。如果你没有助记符和钱包，使用[工具](https://iancoleman.io/bip39/)生成一个。在硬币下拉菜单中选择 **ETH** ，点击英文按钮。将助记符保存在安全可靠的地方！*

*为测试网络和主网络制作单独的钱包是个好主意，这样就不太可能发生浪费 ETH 的事故。将你的以太坊密钥集助记符添加到你的环境变量中，就像上面的 truffle 配置文件中提到的`ethereum_mnemonic`。*

```
*## bash
export ethereum_mnemonic="candy maple cake...."*
```

*如果您的钱包中没有 Ropsten 网络上的 test ETH，请安装 MetaMask Chrome 扩展，以便从[水龙头](https://faucet.metamask.io/)中免费获得一些。为了在令牌合同中执行交易，你需要花费一些费用——在网络中代表你完成的工作。*

```
*truffle migrate --network ropsten*
```

*该命令将记录**合同地址**，务必保存！Migrate 使用`--network`标志，并引用`truffle.js`中网络对象中的`ropsten`键。可以像 Ropsten 对象一样包含主以太网的对象。出于安全原因，我把它从我的松露盒代码中排除了。**主以太坊网络见本[松露教程](http://truffleframework.com/tutorials/deploying-to-the-live-network)中的“活”连接对象。***

*部署令牌合同后，您可以使用 MetaMask Chrome 扩展查看您的令牌余额。在 MetaMask UI 中，从左上角的下拉选择器中选择 Ropsten 测试网络，然后单击“令牌”选项卡。点击**添加令牌**按钮，输入从`truffle migrate`命令记录的您的合同地址。*

*MetaMask 可以传输 token，或者您可以使用 Web3.js 创建自己的调用 UI 的契约。上面的 token 契约对于 ICO 开发人员来说是令人兴奋的，但它不能开箱即用地进行有新闻价值的**众筹**。让我们实现它吧。*

# *众筹销售*

*现在您已经安装了 Truffle，使用了 CLI，探索了 Solidity，并编写了一些测试代码，我们可以拆箱一个现有的 Truffle 项目了—**Crowdsalable ether eum Token**。让我们创建一个新目录，并使用 Truffle CLI 直接从 [my Github repo](https://github.com/ajb413/crowdsalable-eth-token) 中拉出这个项目。*

```
*mkdir crowdsalable-eth-token && cd crowdsalable-eth-token
truffle unbox git@github.com:ajb413/crowdsalable-eth-token.git*
```

*这个 Truffle 项目有一个更健壮的以太坊令牌实现。在已经部署协定之后，所有者可以更改令牌名称和符号。车主也可以**随时配置和开通新的众筹销售。***

*truffle box 附带了一个开发模式 UI，用于在本地以太坊客户端上执行合同。用户界面使用 10 个静态钱包，这些钱包是由`truffle develop`命令在你的机器上初始化的——来执行转账，查看钱包余额，以及启动令人兴奋的众筹销售。*

# *广播众筹广告*

*`app/`文件夹包含网络用户界面和一点额外的 PubNub 魔法。当众卖开始时，所有者可以选择给他们所有的关注者发短信，告诉他们众卖的详细信息，这样他们就可以开始购买你的代币。*

*该功能由 ClickSend API 和 PubNub 函数提供支持。为了启用这种实时更新功能，您必须注册 [PubNub](https://admin.pubnub.com/#/register?utm_source=Syndication&utm_medium=Medium&utm_campaign=SYN-CY18-Q1-Medium-February-13&utm_content=byo-crypto) 和 [ClickSend](https://www.clicksend.com/) 。您在 **app.js** 和 **sms-handler.js** 中注明的位置插入您的 **api 密钥**、**发布密钥**和**订阅密钥**。还可以编辑电话号码的 JavaScript 数组来选择接收短信的人。*

*摘自 **app/js/app.js***

*摘自**app/pubnub-Functions/SMS-handler . js—*将此部署到 pub nub 函数！****

*PubNub 函数事件处理程序必须部署在 [PubNub 管理仪表板](https://admin.pubnub.com/?utm_source=Syndication&utm_medium=Medium&utm_campaign=SYN-CY18-Q1-Medium-February-13&utm_content=byo-crypto)中。参见[本教程](https://www.pubnub.com/docs/blocks/tutorials/hello-world)在 2 分钟内部署函数事件处理程序代码。*

# *使用 Web3.js 的开发 UI*

*接下来，我们以与前面相同的方式运行本地以太坊客户端。*

```
*cd crowdsalable-eth-token
npm i
truffle develop## Truffle development blockchain and console are bootedtruffle(develop)> compile
truffle(develop)> migrate
truffle(develop)> test*
```

*   *让松露控制台保持运行*
*   *打开新的命令行窗口*
*   *导航到相同的项目目录*
*   *运行开发 UI，使用:*

```
*npm run dev> crowdsalable-eth-token dev /crowdsalable-eth-token
> webpack-dev-serverProject is running at [http://localhost:8080/](http://localhost:8080/)*
```

*接下来，在您的浏览器中打开 UI，探索关键特性。我们可以:*

*   *推出新的众筹销售*
*   *将令牌从钱包转移到钱包*
*   *使用 ETH 从大众商店购买令牌*
*   *检查任何钱包的余额*

*开发环境的本质允许任何人调用像`createCrowdsale`和`transfer`这样的函数，但实际上，这些方法不能被任何人调用。对于`onlyOwner`修饰的实性函数，调用者必须是契约的所有者，并且必须用他们的私钥签署他们的请求(*参见****test/integration . test . js***中的 `*rawTransaction*` *函数)。像`transfer`这样的功能也只能从私钥所属的钱包发送令牌。出于正确的原因，这将在主网络上受到更多的限制。**

```
*web3.eth.sendRawTransaction(...)*
```

*在你部署了你的 **PubNub 功能**之后，你就可以发起一个众卖活动，当合同开启时**会向你所有的关注者发送一条群发短信**。输入姓名，点击**发射**按钮，查看手机！*

*众卖的配置在`launchCrowdsale`事件处理程序的`app/js/app.js`脚本中指定。默认情况下，众筹永远开放，以 100，000 TOK**开始出售，买家每支付**可获得 **3 TOK。***

*使用 UI 底部列出的默认钱包公钥进行转账、购买和余额检查。记住，在这个网络上，每个钱包都有 100 个假 ETH 可以玩。*

*如果你已经做到了这一步，你现在已经对区块链和 PubNub 有了一些控制。概括来说，我们涵盖了:*

*   *什么是加密货币*
*   *如何构建一个符合标准的以太坊令牌*
*   *如何传递令牌和调用契约方法*
*   *如何测试所有的合同代码*
*   *如何创建众筹*
*   *如何用 PubNub 向你所有的追随者发布公告*

*区块链的真正力量还没有被认识到，因为这项技术只是最近才引起广泛的兴趣。PubNub 会继续参与。对于区块链编程的现场演示，请查看我们的[活动](https://www.pubnub.com/company/events/)和 [Meetup](https://www.meetup.com/MobileDeveloperGroup/) 页面。祝你在区块链的冒险之旅好运！*