# Python 库在现场运行 Quantopian 算法

> 原文：<https://medium.com/hackernoon/python-library-to-run-quantopian-algorithm-in-live-5a2d6025dd45>

![](img/fa8d3558d03fc4cdec481f8ad821ad48.png)

Photo by [Rodion Kutsaev](https://unsplash.com/photos/xNdPWGJ6UCQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/oil-mining-pipe?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# quantopian——在线算法交易平台

Quantopian 是当今最受欢迎的在线算法交易平台和社区之一。它提供了一个很好的回溯测试环境，在这里你可以试验你的想法，构建[算法](https://hackernoon.com/tagged/algorithms)，甚至参加竞赛，还可以分享想法，并与那里的聪明人讨论。

在 beta 计划期间，许多人问 Alpaca 的一个问题是，如何运行他们在 Quantopian 平台中构建的算法，以实现他们自己的目的，而不仅仅是为了比赛。虽然 Quantopian 在平台中构建了如此多的东西，但他们能够像开源 zipline 一样共享内部框架实在是太棒了。

# 面向 Quantopian 用户的最新开源库

今天，我想为 Quantopian 用户分享我们最新的开源库；[皮里维特](https://github.com/alpacahq/pylivetrader/)和[管道直播](https://github.com/alpacahq/pipeline-live/)。

[](https://github.com/alpacahq/pylivetrader/) [## 羊驼总部/皮里韦德勒

### 带 zipline 接口的 Python 实时交易执行库。-羊驼总部/皮里韦德勒

github.com](https://github.com/alpacahq/pylivetrader/) [](https://github.com/alpacahq/pipeline-live/) [## 羊驼总部/管道-直播

### 实时交易的管道扩展。通过在 GitHub 上创建帐户，为 alpachq/pipeline-live 开发做出贡献。

github.com](https://github.com/alpacahq/pipeline-live/) 

pylivetrader 是一个兼容 zipline API 的交易框架，使用的是 python[和](https://hackernoon.com/tagged/python)语言，它同样专注于实时交易，开销和依赖性问题要少得多。它是为实时交易用例从头开始编写的，因此它消除了许多 zipline 必须做的繁重工作，如价格调整等。

这意味着，你不需要构建你的数据捆绑包来启动你的实时算法，相反，你可以从 Quantopian 算法源代码立即开始你的实时交易。

目前，支持的后端只有[羊驼](https://alpaca.markets/)，但我们很乐意连接到 IB 等。如果有人贡献了代码。

# 管道 API——quanto pian 框架的核心部分

Pipeline API 是 Quantopian 算法框架的核心部分，它允许基于不同的指标轻松选择股票，这在很大程度上是以 pythonic 的方式进行的，这将该平台与其他平台区分开来。我发现管道在交易大范围的宇宙时提供了巨大的价值。不幸的是，对于大多数人来说，在 Quantopian 平台之外使用这个伟大的功能并不容易。

[pipeline-live](https://github.com/alpacahq/pipeline-live/) 是一个 python 工具，它允许你在任何地方做类似的事情，这样你就可以在其他地方做你的研究，并与现有的 python 交易框架一起使用，如 zipline-live 或 backtrader，包括我下面介绍的 pylivetrader。pipeline-live 主要使用 IEX 公共 API 来获取定价和基本的基本信息。

如您所知，IEX 为每日 OHLCV 提供市场范围的流量数据，这使其成为管道应用的完美选择。由于 pipeline-live 专注于实时交易用例，它不像内部 Quantopian 那样提供历史视图，但好处是它相当独立且易于使用。它也是非常可扩展的，所以如果你觉得有用的话，你可以连接其他付费数据源。

# 如何将你的量子算法转换成实时交易

我们也把一些实践放在一起，告诉你如何把你的量子算法转换到实时交易中。如果你有兴趣，你可以看看这些文件。

[https://github . com/alpacahq/pipeline-live/blob/master/migration . MD](https://github.com/alpacahq/pipeline-live/blob/master/migration.md)
[https://github . com/alpacahq/pylivetrader/blob/master/migration . MD](https://github.com/alpacahq/pylivetrader/blob/master/migration.md)

我还在 Quantopian 论坛上贴出了真实的例子，你也可以看看。

[](https://www.quantopian.com/posts/long-only-non-day-trading-algorithm-for-live) [## 只做多的非交易日实时交易算法

### 这是在…中提出的算法的修改版本

www.quantopian.com](https://www.quantopian.com/posts/long-only-non-day-trading-algorithm-for-live) 

请随时给我任何反馈/问题/批评。很高兴帮助你开始使用这些工具进行实时交易。

下面是从上面的文章中移植过来的示例代码。

[![](img/4d7d212ef68dd071daeaae0916d72f65.png)](https://medium.com/automation-generation)

## 金融市场、算法交易、技术方面的新鲜帖请关注[羊驼](/@alpacahq)和[自动化生成](https://medium.com/automation-generation)。

## 你可以找到我们[@羊驼 HQ](https://twitter.com/AlpacaHQ) ，如果你用 twitter 的话。

[![](img/cdfe4e1b3df7ff7b28e2474ff8dfef1d.png)](https://medium.com/automation-generation)

如果你是一个黑客，并且可以创造一些在金融市场上工作的很酷的东西，**请查看我们的项目“** [**无佣金股票交易 API**](https://alpaca.markets/?utm_source=medium&utm_medium=blog&utm_campaign=strategy_list&utm_content=part1) **”,在那里我们免费提供简单的 REST 交易 API 和实时市场数据。**

经纪服务由 FINRA/SIPC 成员 Alpaca Securities LLC([Alpaca . markets](https://alpaca.markets/?utm_source=medium&utm_medium=blog&utm_campaign=strategy_list&utm_content=part1))提供。羊驼证券有限责任公司是 AlpacaDB，Inc .的全资子公司。