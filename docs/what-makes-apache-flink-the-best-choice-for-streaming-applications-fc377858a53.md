# 是什么让 Apache Flink 成为流媒体应用的最佳选择？

> 原文：<https://medium.com/hackernoon/what-makes-apache-flink-the-best-choice-for-streaming-applications-fc377858a53>

![](img/01a71840ce3b9ff597e7e24cf29def83.png)

“Long lines of tiny speckles on light in a high-ceiling interior” by [Joshua Sortino](https://unsplash.com/@sortino?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

[Apache Flink](https://flink.apache.org/) 是一个[开源](https://hackernoon.com/tagged/open-source)流媒体平台，它为您提供了以容错方式运行实时数据处理管道的巨大能力，其规模为每秒数百万个事件。

关键在于，它使用尽可能少的资源，以单毫秒的延迟完成所有这些工作。

那么，它是如何做到这一点的呢？是什么让它优于同一领域的其他解决方案呢？

# 在最少的资源上实现低延迟

Flink 基于[数据流模型](https://research.google.com/pubs/pub43864.html)，即在元素到来时对其进行处理，而不是以小批量方式进行处理(这由 [Spark streaming](https://spark.apache.org/docs/2.2.0/streaming-programming-guide.html) 完成)。

微批次可以包含大量的元素，并且一次处理这些元素所需的资源可能是大量的。在稀疏数据流的情况下(在这种情况下，您只能以不规则的间隔获得突发数据)，这成为一个主要的棘手问题。

您也不需要通过反复试验来配置微批量大小，以便批量的处理时间不会超过它的累积时间。如果发生这种情况，那么批处理就会开始排队，最终所有的处理都会停止。

Dataflow 允许 flink 在单台机器上以毫秒级的延迟每分钟处理数百万条记录(这也是因为 Flink 的托管内存和自定义序列化，但在下一篇文章中会有更多介绍)。下面是一些[基准](https://data-artisans.com/blog/curious-case-broken-benchmark-revisiting-apache-flink-vs-databricks-runtime)。

# 各种源和汇

Flink 提供了到各种数据源和接收器的无缝连接。

其中包括:

*   [阿帕奇卡珊德拉](http://cassandra.apache.org/)
*   [弹性搜索](https://www.elastic.co/products/elasticsearch)
*   [卡夫卡](https://kafka.apache.org/)
*   [RabbitMQ](https://www.rabbitmq.com/)
*   [蜂巢](https://hive.apache.org/)

# 容错

Flink 使用检查点(定期将内部状态保存到外部源，如 HDFS)提供强大的容错能力。

然而，Flink 的检查点机制可以是增量的(只保存更改，而不是整个状态)，这确实减少了 HDFS 中的数据量和 I/O 持续时间。检查点开销几乎可以忽略不计，这使得用户可以在 Flink 应用程序中拥有大型状态。

Flink 还通过 zookeeper 提供了高可用性设置。这是为了在驱动程序(在 Flink 中称为 JobManager)由于某种错误而崩溃的情况下重新生成作业。

# 高级 API

与 [Apache](https://hackernoon.com/tagged/apache) Storm(也遵循数据流模型)不同，Flink 以 Map/Reduce、Filters、Window、GroupBy、Sort 和 Joins 的形式提供了一个极其简单的高级 api。

这为开发人员提供了很大的灵活性，并在编写新作业时加快了开发速度。

# 有状态处理

有时一个操作需要来自其他来源的一些配置或数据来执行操作。一个简单的例子是计算流 x 中 Y 类型记录的数量，这个计数器就是操作的状态。

Flink 提供了一个简单的 API 来与状态交互，就像与 java 对象交互一样。状态可以由内存、文件系统或 RocksDB 支持，它们具有检查点，因此是容错的。例如，关于上面的例子，在应用程序重启的情况下，您的计数器值仍将被保留。

# 一次加工

Apache Flink 像 Kafka 0.11 和更高版本一样，以最小的开销和零开发工作提供了一次处理。这在 Spark Streaming 和 Storm 等其他流媒体解决方案中是很重要的，并且在 Apache Samza 中不支持[。](https://cwiki.apache.org/confluence/display/SAMZA/SEP-10+Exactly-once+Processing+in+Samza)

[](https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it/) [## 恰好一次语义是可能的:下面是阿帕奇卡夫卡如何做到这一点

### 我很高兴我们达到了一个激动人心的里程碑，卡夫卡社区期待已久:我们已经…

www.confluent.io](https://www.confluent.io/blog/exactly-once-semantics-are-possible-heres-how-apache-kafka-does-it/) 

# SQL 支持

像 Spark streaming 一样，Flink 也提供了一个 [SQL API 接口](https://ci.apache.org/projects/flink/flink-docs-release-1.4/dev/table/sql.html)，这使得没有编程背景的人编写工作变得更加容易。Flink SQL 日益成熟，已经被优步和阿里巴巴等公司用于对实时数据进行分析。

Flink SQL & TableAPI in Large Scala Production at Alibaba

# 环境支持

Flink 作业可以在分布式系统或本地机器上运行。该程序可以在 mesos、yarn、kubernetes 以及独立模式下运行(例如在 docker 容器中)。从 Flink 1.4 开始，Hadoop 不再是先决条件，这为运行 Flink 作业提供了许多可能性。

# 令人敬畏的社区

Flink 有一个很棒的开发社区，允许频繁的新功能和错误修复，以及很棒的工具来进一步减轻开发人员的工作。这些工具包括:

*   [Flink Tensorflow](https://github.com/FlinkML/flink-tensorflow) —将 Tensorflow 图形作为 Flink 进程运行
*   [弗林克 HTM](https://github.com/htm-community/flink-htm)—弗林克一条溪流中的异常检测
*   [Tink](https://github.com/otherwise777/Temporal_Graph_library)——构建在 Flink 之上的时态图形库

Flink SQL 和复杂事件处理(CEP)最初也是阿里巴巴开发的，并回馈给了 Flink。

**注意** : Spark Streaming 2.3 已经开始支持连续处理，而不是微批处理。点击查看[。我将使用 yahoo-streaming-benchmarks 运行一些基准测试，并在下一篇文章中发布结果。](https://databricks.com/blog/2018/03/20/low-latency-continuous-processing-mode-in-structured-streaming-in-apache-spark-2-3-0.html)

***在***[***LinkedIn***](http://www.linkedin.com/in/kartik-khare)***或*** [***【脸书】***](https://www.facebook.com/KK.corps) ***上与我联系或发邮件至***[***【kharekartik@gmail.com***](mailto:kharekartik@gmail.com)***分享反馈。***