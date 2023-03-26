# 为什么应该在浏览器中运行机器学习

> 原文：<https://medium.com/hackernoon/why-to-run-machine-learning-in-the-browser-67e0dc11a76b>

![](img/eaa9bb947f24f8a6c22f9313b3a8888e.png)

Photo by [Alex Toulemonde](http://'https://www.flickr.com/photos/alextoul/6412207917/in/photolist-aLCf1V-5LKqDi-5LTp6F-8B2wqT-wokRP-7DvxRd-bdd77c-7xV1w-PNY1sa-fasCLu-9NT1UA-ndnvww-6Pym4y-9NQ5HZ-9NQffM-9NQgCV-9NSZZy-9NSXa5-9NSW8b-4oSw2n-9NSV4u-76jcMx-9DK39M-VPmfLi-8B5E5W-dNHLzC-eZ9uMB-8mE46Q-5wwF6d-hoi196-5wWE7L-wEgXH-aH1nLH-5quA5v-nfuE8a-6RZYky-7xQty-9dWJYM-bX1whU-7xSx6-6S19pA-pcK6bA-76oxa7-ptXDd2-6S1bns-dNHL61-6RVKrR-76jYdc-6RZKB7-6RZQbQ) on [Flickr](https://www.flickr.com/photos/alextoul/)

假设你每天从布鲁克林通勤到曼哈顿，去你几个月前刚加入的新机器学习创业公司工作。你的火车停在两个车站之间，炎热使每个人都变得黏糊糊的，其他乘客像蜂窝一样嗡嗡作响。

别担心，你的耳塞已经戴上了，你可以开始新的堵塞了。你拿出手机，试图加载最新推荐的音乐，但是*废话*——因为你没有网络服务，所以无法加载推荐。你*知道*你今天早上下载了一些新音乐；事实上，它就在你的*新音乐*播放列表中，等着你。但是推荐？没有骰子。

[近日，谷歌向全球推出 tensor flow . js](/tensorflow/introducing-tensorflow-js-machine-learning-in-javascript-bf3eab376db)。TensorFlow.js 只是可以采用客户端机器学习的机器学习框架的一个例子。**这意味着机器学习算法可以直接在用户的设备上运行，而不需要与任何服务器对话。这意味着这些音乐推荐可以被推荐给你——不管你是在家、在办公室，还是在没有互联网、汗流浃背的火车上。**

这篇文章将探索客户端机器学习的独特例子，以了解像 TensorFlow.js 这样的 [JavaScript](https://hackernoon.com/tagged/javascript) [机器学习](https://hackernoon.com/tagged/machine-learning)框架如何为用户、企业和软件开发人员释放巨大的价值。

# 训练与推理

在开始之前，有必要定义机器学习的两个阶段:训练和推理。**训练**是机器从现有的一组数据中学习新能力的过程。就像去学校学习一个新的学校或行业一样，我们通过训练过程教会机器新的技能，给它们足够多的具体例子让它识别。这些示例称为训练数据集。

**推理**另一方面，一旦机器从训练会话中运行了足够多的例子，它就执行该技能。这就像计算机的钢琴独奏会或突击测验，计算机将它的技能应用于它从未见过的数据。

# 隐私

正如 Vinay Muttineni 在 Quora 上建议的那样，服务器端机器学习的核心好处之一是，用于训练模型的数据和模型的使用可以完全在用户的设备上完成。这意味着服务器上没有输入或存储任何数据。

![](img/106dede0427a27dbb70feb77d2e4ff54.png)

这方面的一个例子是[托马斯·雷默斯建议的](/@tomasreimers/compiling-tensorflow-for-the-browser-f3387b8e1e1c)像谷歌助手或 Alexa 这样的语音助手。这些系统使用一个 wake-word (OK，Google 或 Alexa ),使用客户端机器学习推理来知道监听以下命令。唤醒词是本地的，这确保发送到云的数据是用户同意发送的数据。只有当用户明确告诉系统开始监听时，数据才会被发送到服务器。你可以将客户端机器学习的这种用法几乎视为一种数字神经系统反射——在从用户向服务器发送更强大的请求(数据被发送到大脑)之前，系统开始使用来自唤醒词的客户端推理(反射)来集中注意力。

# 更广泛的接触和传播

不管你喜不喜欢，JavaScript 是所有语言和框架中拥有最广泛安装基础的语言之一。几乎所有现代个人计算设备都安装了网络浏览器，并且几乎所有现代网络浏览器都可以在其上运行 JavaScript。

这个简单的事实本身就意味着机器学习对大量新设备开放，这些设备现在可以利用机器学习算法。几年前可能已经达到鼎盛时期的智能手机突然可以直接在设备上利用机器学习框架。

![](img/8b8b532dcd658594c5c6c2069a6d0ba1.png)

例如，你最喜欢的街头服饰品牌可能想开发一个虚拟试衣间，这样你就可以在购买前看到自己穿着最新款的样子。据 Business Insider 报道， [23%的电子商务销售额是通过移动网络](http://www.businessinsider.com/mobile-apps-most-popular-e-commerce-channel-q4-2017-2018-2)实现的，因此这个街头服饰品牌希望确保他们能占领这一市场份额。在浏览器中使用客户端机器学习，该品牌可以创建一个虚拟试衣间，供客户在手机或电脑上使用。无需下载应用或安装任何东西，浏览器中的客户端机器学习有助于降低客户参与虚拟试衣间的门槛，从而提高转化率和客户满意度。

# 分布式计算

之前的许多例子都专注于推理——也就是说，利用预先训练的机器学习模型和数据集。此外，使用客户端机器学习的分布式计算有一个很好的用例。例如，每次用户参与系统时，他或她应该在自己的设备上运行机器学习算法，使用他们参与的数据在自己的计算机上训练模型，然后将新的数据点推送到服务器，以帮助改进模型。以这种方式，用户利用他或她自己的设备来运行算法，并且另外用结果来训练模型，这将帮助算法的未来用户。这可以降低计算能力持续训练模型的成本。

客户端机器学习的世界正在快速增长和发展，尽管 Javascript 的机器学习存在明显的局限性。然而，来自谷歌等公司的投资将 TensorFlow 移植到 JavaScript，带来了一个令人兴奋的新领域，即用户只需利用他们每天使用的网络浏览器，就可以访问机器学习。

最初发表于[thekevinscott.com](https://thekevinscott.com/reasons-for-machine-learning-in-the-browser)

特别感谢[阿里·齐尔尼克](https://medium.com/u/b1e6e94ff725?source=post_page-----67e0dc11a76b--------------------------------)