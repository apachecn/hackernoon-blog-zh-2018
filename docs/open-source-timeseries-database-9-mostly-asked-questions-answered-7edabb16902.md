# 开源时间系列数据库—回答了 9 个最常见的问题

> 原文：<https://medium.com/hackernoon/open-source-timeseries-database-9-mostly-asked-questions-answered-7edabb16902>

![](img/6d43e37693ebf58c20b891d477ad68ec.png)

Photo by [William Stitt](https://unsplash.com/photos/_w8m-1jbCVs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/ask?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 开源金融时间系列数据库至关重要

我发了三篇文章:

1) [*金融时间序列数据库 MarketStore 现已开源*](https://hackernoon.com/marketstore-the-financial-time-series-database-is-now-open-source-fd04343f439)

2) [*如何在五分钟内设置 Algo 交易的比特币历史价格数据*](https://blog.alpaca.markets/blog/2018/5/8/how-to-setup-bitcoin-historical-price-data-for-algo-trading-in-fiveminutes)

3) [*快 50 倍的比特币价格数据由 MarketStore 提供，用于人工智能交易*](https://blog.alpaca.markets/blog/2018/5/18/enjoy-50x-faster-bitcoin-price-data-powered-by-marketstore-for-ai-trading)

每一篇文章都试图解释我们与羊驼 algo 交易经纪公司一起开发的技术。这些文章在 Reddit 和 Medium 上引发了许多很棒的评论和问题，我们清楚地看到，社区对专门用于金融市场数据的 timeseries 数据库有很大的兴趣和相当大的需求。在过去的十年中，数据库世界和软件工程发生了巨大的变化，我们看到了开源编程和数据库的爆炸式发展。我们看到一些人现在积极使用开源软件，并在 GitHub 库贡献一些代码。

在社交媒体和线下，我们一直在回答问题和回应评论，但今天我们想借此机会将所有的疑问和回应放在一个帖子中，并与整个社区分享，这样每个人都可以在一个帖子上看到回应。

# 问:MarketStore 在内存中存储数据吗？

答:不需要。 [MarketStore](https://hackernoon.com/tagged/marketstore) 设计用于在合理大小的主机上运行，无需巨额硬件投资。如果你有很多现金，软件技术是无关紧要的，但软件工程可以带来的是，你可以用更便宜的硬件做得更好。MarketStore 的主要用例是能够以二级粒度存储和分发数万个系列的多年数据(跨交易所的美国股票和加密硬币可以轻松达到这一规模)。数据大小可以是几个太字节，在商用硬件中使用这么大的 RAM 还不是很常见。相反，MarketStore 将所有内容存储在磁盘中，但磁盘上的格式几乎与内存中的布局相同，由于 SSD 的发展，MarketStore 可以以与内存存储竞争的速度加载数据。

# 问:与 PostgreSQL 和 includes DataFrame loading 相比有什么意义？

答:即使您可以存储数据，从应用程序进程中卸载数据，如果您不能使用它，它也是无用的。MarketStore 主要用于人工智能机器学习和回溯测试的环境中，应用程序通常会将其加载到一些表格结构中，如 Pandas DataFrame。这就是为什么 MarketStore 的网络协议是 MessagePack 中的字节序列，因此可以避免低效的 JSON 反序列化。客户端可以将交付的字节数据作为 C 数组加载到内存中，这就是 DataFrame 后面使用的内容。

# 问:与 InfluxDB 相比，它有哪些优势？

答:我们没有与 InfluxDB 进行性能比较，但 InfluxDB 和其他通用时间系列数据库用例是作为系统指标或活动日志分析。这些需要更灵活的数据结构，不一定需要特定的函数，如时区感知聚合。灵活性总是伴随着必要的开销作为权衡，如果用例是金融市场数据，MarketStore 应该更快且更具成本效益。

# 问:时标应该更快，为什么要和 PostgreSQL 比较？

答:如果你有基准测试结果，可以发给我们，但在我们的内部实验中，与 MarketStore 相比，scale 甚至比 PostgreSQL 还慢。数据库服务器级别的时标加载时间比 PostgreSQL 慢 2-3 倍，因为时标使用表分区(也称为表约束排除),需要从磁盘打开大量文件。从大量数据中过滤出一小部分数据会有好处，但如果扫描大部分数据，效果不会更好。与关系数据库相比，MarketStore 以最佳方式将数据存储在磁盘上，并按顺序直接读取到内存中，因此速度更快。

# 问:MarketStore 只能用于历史数据，而不能用于实时数据，对吗？

答:MarketStore 即将推出一项新功能，它将允许在每次新数据写入时进行流式传输和实时推送。MarketStore 最初旨在帮助我们的 [algo trading](https://hackernoon.com/tagged/algo-trading) 平台，该平台使用深度学习构建交易算法，并在真实市场中运行它们，并拥有 JSON websocket 流。该特性目前已经存在，因此 Marketstore 可以找到一种方法来适应更大的用例。但谢天谢地，它现在又作为一个插件回来了。我们已经用每几秒钟数千次的更新对此进行了测试，到目前为止，它运行得非常完美。

# 问:为什么机器学习需要这个？我可以毫无问题地从磁盘加载数据

答:如果你的训练过程不使用很多数据(例如，只使用一只股票的日线)，那么你可能因为性能原因不需要 MarketStore。我们需要在羊驼交易平台上做的事情需要一个足够大的服务器来存储整个市场的大量日内数据(可以高达 TB 级)，并来回加载必要的系列数据。如果你熟悉典型的机器学习训练过程，你可以告诉训练迭代如何从池中加载随机数据。也就是说，MarketStore 不仅仅是为了性能，也是为了方便证明以相同的方式访问历史和实时时间序列数据的统一方法，而不用担心如何管理本地文件等。内置的数据导入器甚至不用写任何代码就可以加载数据。

# 问:安装程序在哪里？

答:抱歉，目前我们不提供一键式安装程序！相反，我们将服务器进程打包到 docker 容器映像中，因此如果您有 docker，您可以在一秒钟内启动它。

# 问:为什么它是开源的？

答:因为有一个问题要解决！MarketStore 是专为我们内部使用而实现的，并已用于我们的生产中，但我们也看到了影响该领域许多人的常见问题。我们在羊驼的使命是用技术帮助个人投资者，并改善 algo 交易环境，无论我们是向用户提供这些信息，还是以高级包的形式提供这些信息。这种产品只有拥有大量资金资源的金融机构才能获得。但是现在我们让任何渴望尝试的人都可以使用它！太棒了，不是吗！？

# 问:我发现了一个错误

答:请在 GitHub 那期报道！

[![](img/4d7d212ef68dd071daeaae0916d72f65.png)](https://medium.com/automation-generation)

## 请关注[羊驼](/@alpacahq)和[自动化世代](https://medium.com/automation-generation)关于金融市场、算法交易、技术的新帖子。

## 你可以找到我们[@羊驼 HQ](https://twitter.com/AlpacaHQ) ，如果你用 twitter 的话。

[![](img/cdfe4e1b3df7ff7b28e2474ff8dfef1d.png)](https://medium.com/automation-generation)

如果你是一名黑客，并且能够创造出在金融市场上运行的很酷的东西，**请查看我们的项目“** [**免佣金股票交易 API**](https://alpaca.markets/?utm_source=medium&utm_medium=blog&utm_campaign=strategy_list&utm_content=part1) **”，在这里我们免费提供简单的 REST 交易 API 和实时市场数据。**

经纪服务由 FINRA/SIPC 成员 Alpaca Securities LLC([Alpaca . markets](https://alpaca.markets/?utm_source=medium&utm_medium=blog&utm_campaign=strategy_list&utm_content=part1))提供。羊驼证券有限责任公司是 AlpacaDB，Inc .的全资子公司。