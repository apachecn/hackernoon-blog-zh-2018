# 使用 PySpark 和 Spark-Submit 快速入门

> 原文：<https://medium.com/hackernoon/get-a-quick-start-with-pyspark-and-spark-submit-da16bd7e75c>

![](img/0d680494a509d6be8866168ada1e3838.png)

我们刚刚发布了一个新的[开源样板模板](https://github.com/Soluto/spark-submit-with-pyspark-template)来帮助你(任何 Spark 用户)顺利运行 [spark-submit](https://hackernoon.com/tagged/spark-submit) 命令——比如插入依赖项、项目源代码等等。

**TLDR:这里有一个** [**开源模板**](https://github.com/Soluto/spark-submit-with-pyspark-template) **帮你入门**

在 Soluto，作为数据科学家日常工作的一部分，我们创建 [ETL](https://en.wikipedia.org/wiki/Extract,_transform,_load) (提取、转换、加载)作业。我们的主要工具是 [Spark](https://spark.apache.org/) ，具体来说就是 [PySpark](https://hackernoon.com/tagged/pyspark) ，带有 [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html) 。

[Spark](https://spark.apache.org/) 用于大规模数据集上的分布式计算。 [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html) 帮助您在集群上启动代码应用程序。

以下是我们在 Soluto 日常工作的一些例子:

*   为用户创建离线内容推荐
*   将单个事件聚合到更具逻辑性的表格中——作为我们服务的一部分，我们通过聊天信息提供技术支持。我们没有为单个支持会话创建多个消息事件，而是使用一个会话实体创建 SessionsTable，该实体保存单个聊天会话的所有聚合信息

使用 Spark 进行 ETL 作业时的一些基本需求:

*   传递参数
*   创建 Spark 上下文和 sql 上下文
*   加载项目源代码(src 目录)
*   加载 pip 模块(带有简单的需求文件)

我们创建了一个 [简单模板](https://github.com/Soluto/spark-submit-with-pyspark-template)，它可以帮助您开始使用 PySpark 运行 ETL 作业(使用 Spark-submit 和交互式 shell)，创建 spark 上下文和 sql 上下文，使用简单的命令行参数并加载您的所有依赖项(您的项目源代码和第三方需求)。

因此，如果你正在开始一个新的 Spark 项目，那么就把它“叉”到 [GitHub](https://github.com/Soluto/spark-submit-with-pyspark-template) 上，享受点燃它的乐趣吧！

请随时分享任何想法，开放的问题和贡献代码！