# 金融时间序列数据库 MarketStore 现在是开源的

> 原文：<https://medium.com/hackernoon/marketstore-the-financial-time-series-database-is-now-open-source-fd04343f439>

![](img/ae2a07d4aa4deadb0ce1f1640580cf15.png)

我们很高兴地宣布 [MarketStore](https://github.com/alpacahq/marketstore) 现已开源！ [MarketStore](https://hackernoon.com/tagged/marketstore) 是针对纯 [Go](https://golang.org/) 编写的金融时间序列数据优化的数据库服务器，由[羊驼](https://alpaca.markets/)设计开发。您可以将它视为一个可扩展的 [DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) 服务，可以从系统中的任何地方访问，具有更高的可伸缩性。

它从头开始设计，旨在解决处理大量金融市场数据的可扩展性问题，这些数据用于算法交易回溯测试、绘制图表和分析跨越多年的价格历史，包括所有美国股票或爆炸式加密货币空间的分笔成交点水平。如果您正在努力管理大量的 [HDF5](https://support.hdfgroup.org/HDF5/) 文件，这是您问题的完美解决方案。

# 问题是

几年前，羊驼开始开发[羊驼算法](http://algo.alpaca.ai/)，它可以帮助散户将自己的想法转化为交易算法，而无需编写任何代码。该平台训练了一个深度学习模型，以利用每个模型的历史价格数据捕捉交易想法，并快速进行回溯测试。然后，您可以打开 algo to live 模式，这将执行与回溯测试相同的计算。

然后是 [AlpacaScan](https://scan.alpaca.ai/) ，它为所有美国股票提供预建的算法结果，并提供快速回溯测试结果。以时间序列的方式对 8000 个符号运行复杂的逻辑不是一件容易的事情。

在这样做的过程中，我们发现访问十多年来具有数千个符号的分笔成交点级别的历史价格数据是系统的性能瓶颈，也是用户体验的痛点。我们的大部分应用层都是使用 PyData 库(包括 Pandas [DataFrame](https://hackernoon.com/tagged/dataframe) )用 python 编写的，但这些库更侧重于计算和分析，很明显，有必要改进对不同部分的大量数据的快速访问。

# 解决方案

当时，处理金融市场时间序列数据的最简单方法是将 DataFrame 存储在 HDF5 中。对于小型用例来说，这是事实上的存储格式，但是由于可伸缩性的原因，它不适合我们的情况。

然后我们坐在一起，探讨这种可能性。这个想法很快出现，我们需要某种数据库来按需提供数据框架。它应该是一个基于 HTTP 的 API 服务，易于访问，在磁盘上存储万亿字节的价格数据，在文件系统上提供简单的数据管理，能够每秒更新 10k+符号价格，并以亚秒延迟响应 10k+客户端。

在 github 中寻找可能的解决方案，有许多时间序列数据库，但主要用于通用时间序列数据，针对物联网传感器数据或系统监控指标，并为 JSON 设计。特别是金融时间序列数据有不同的需求，其中数据非常密集，更加结构化，并且需要长历史。我们找不到这种特定使用情形的最佳解决方案。很明显，不仅仅是我们，任何处理金融时间序列数据的人都需要这个解决方案。在越来越多的人编写自动化交易系统或分析这种数据的未来时代，非常高效地处理这些数据是不可避免的。数据库服务器应该写得干净利落，并且可以重复使用，以便有朝一日开放源代码，供所有人使用。

这就是我们如何开始开发 MarketStore 的，这是一个专为金融时间序列数据设计的纯 Go 数据库。我们设计了一个真正的数据库架构。Luke 是成功的 MPP 数据库 [Greenplum](https://greenplum.org/) 的联合创始人之一兼首席技术官，他加入了对话并设计了存储层。Hitoshi 曾经为 Greenplum 和 [Pivotal](https://pivotal.io/) 工作，作为 Greenplum 的架构师和 [PostgreSQL](https://www.postgresql.org/) 的主要贡献者，他开发了围绕查询和插件架构的代码库。

# 特征

*   **基于 HTTP 的 API，带有 MessagePack 二进制序列化**
    许多传统数据库都有自己专有的有线协议。然而，在这个云时代，遵循 MarketStore 的基于 HTTP 的标准 API 更有意义。HTTP 主要是基于文本的，但为了提高效率，MarketStore 使用 MessagePack 作为数据序列化格式，这允许服务器和客户端之间的最佳数据交换。
*   **用于优化读写的时间索引行定向存储格式**
    存储在 MarketStore 中的所有内容都被假定为时间序列。可以通过与行相关联的时间戳在精确的字节偏移量处对行进行寻址。在许多分析数据库中，数据是面向列的，以优化读取；MarketStore 的要求是同时连续写入数千个符号，并且更新量很小，但是时间索引数据结构允许优化读取。
*   **利用文件系统漏洞在考虑市场开放时间的情况下使文件大小最小**
    现代文件系统在文件中提供了漏洞，这意味着您可以提前分配文件字节，而不用实际占用底层磁盘。换句话说，就是虚拟分配。因为 MarketStore 在许多情况下都知道每个文件有多大，所以它首先分配文件并填充漏洞以优化存储使用。
*   **不同时间分辨率的时间聚合，具有单一真实来源**
    金融时间序列数据库中最常见的操作是时间聚合。有了 1 分钟棒线数据，您可能想要下采样到 1 小时或 1 天棒线。这是通过[触发插件系统](https://github.com/alpacahq/marketstore/tree/master/contrib/ondiskagg)完成的，在写入底层时，您将获得预聚合的下采样数据。预聚合数据为许多用例提供了更高的查询性能。
*   **支持不同资产类别和市场的定制插件**
    不同的上游数据源，以及不同类型的资产类别，在数据集成方面有不同的需求。MarketStore 的核心引擎在金融市场数据的常见工作方面做得最好，它提供定制插件支持来解决每个不同的用例。
*   **时区支持**
    时区是面向时间的应用程序中最令人头疼的问题之一。MarketStore 支持系统范围的时区，以适应不同的市场。例如，在 Alpaca，美国股票市场数据使用东部时间，并在东部时间边界汇总 1 日棒线，而加密和外汇数据在 UTC 汇总。

# 客户支持

有原生的 [Go](https://github.com/alpacahq/marketstore/blob/master/frontend/client/client.go) 和 [Python](https://github.com/alpacahq/pymarketstore) 客户端，它们都表现得非常好。Python 客户端可以轻松地将服务器响应转换成数据帧，您会发现这与从本地磁盘读取数据几乎没有什么不同，而且具有更高的可伸缩性。

# 数据摄取

数据库软件中的一个常见挑战是数据导入层。我们的财务数据系统也不例外。不同的资产类别具有不同的特征，每个上游数据提供商提供不同的数据格式。为了解决这个问题，MarketStore 为数据摄取层提供了插件系统。它带有默认插件，用于从 [GDAX API](https://docs.gdax.com/#get-historic-rates) 和 [Slait](https://github.com/alpacahq/slait) 获取数据，这是我们的另一个开源产品。我们将在另一篇文章中讨论 Slait。使用 [GDAX 插件](https://github.com/alpacahq/marketstore/tree/master/contrib/gdaxfeeder)，你可以从启动 MarketStore 的那一刻起，立即开始消费和存储比特币、以太坊、比特币现金和莱特币数据。

由于它是一个插件架构，您可以根据自己的需要编写自己的数据摄取。此外，Go 和 Python 客户端支持从远程写入数据。

# 有效性

MarketStore 现在是 GitHub 上的一个开源项目，已经可以生产了。鉴于 Go 中构建系统的简易性，自行构建它是相当简单的。为了方便访问，每个版本都有一个 Docker 容器。

在羊驼内部，从我们的交易管理系统到深度学习建模再到制图，几乎所有的应用程序在开发和生产中都使用 MarketStore 作为后端。

我们希望开源 MarketStore 可以帮助更多在类似领域工作的人，并为社区做出贡献。羊驼的使命始终是帮助个人在金融市场中拥有更多技术力量，这不仅仅意味着我们的最终产品。通过以这种方式提供技术，我们希望帮助每个人。

# 现在就试试吧！

今天就试试吧，让我们知道什么适合你，什么不适合。我们也非常乐意在特性开发和文档方面得到帮助。这里会有更多关于 MarketStore 使用细节的帖子。

[![](img/4d7d212ef68dd071daeaae0916d72f65.png)](https://medium.com/automation-generation)

## 请关注[羊驼](/@alpacahq)和[自动化生成](https://medium.com/automation-generation)关于金融市场、算法交易、技术的新帖子。

## 你可以找到我们[@羊驼 HQ](https://twitter.com/AlpacaHQ) ，如果你用 twitter 的话。

[![](img/cdfe4e1b3df7ff7b28e2474ff8dfef1d.png)](https://medium.com/automation-generation)

如果你是一名黑客，并且可以创建一些在金融市场中工作的很酷的东西，请查看我们的项目"[Commission Free Stock Trading API](https://alpaca.markets/?utm_source=medium&utm_medium=blog&utm_campaign=strategy_list&utm_content=part1)",在那里我们免费提供简单的 REST 交易 API 和实时市场数据。

经纪服务由 FINRA/SIPC 成员羊驼证券有限责任公司([羊驼市场](https://alpaca.markets/?utm_source=medium&utm_medium=blog&utm_campaign=strategy_list&utm_content=part1))提供。羊驼证券有限责任公司是 AlpacaDB，Inc .的全资子公司。