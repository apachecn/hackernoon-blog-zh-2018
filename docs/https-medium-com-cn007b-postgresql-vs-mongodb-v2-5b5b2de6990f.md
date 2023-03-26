# PostgreSQL vs MongoDB v2

> 原文：<https://medium.com/hackernoon/https-medium-com-cn007b-postgresql-vs-mongodb-v2-5b5b2de6990f>

![](img/de3bb6305b457db607d14c4dfa675f39.png)

## 在相同数据集上测试 PostgreSQL 和 MongoDB 的读取性能

## TL；博士:

在第一篇[文章](https://hackernoon.com/https-medium-com-cn007b-postgresql-vs-mongodb-6d8bdb7c1697)中，我已经概述了如何决定在一个特殊的情况下(也许是一种罕见的情况)使用特定的数据库。但是在收到信息、评论和反馈后，感觉这种情况被认为是普遍的。因此，我改进并扩展了我的基准，我将与您分享最新信息。

## 未更改

数据模式保持不变(两个实体:`storage`和`file,`，具有关系`1 to many`)。

`postgres`数据示例:

`mongo`数据示例:

## 改变的

导入数据库 **2500** 条目为`storage` 数据，导入 **24705** 条目为`file`数据(分别为 500 和 4924 **)。**

在这次基准测试中，添加了更多的查询。
不仅考虑了之前基准测试中的`query1`:

`query1` `postgres`:

`query1` `mongo`:

而且还添加了一堆 else 查询。

更新的数据库版本:`*PostgreSQL*` *10.0* → 10.5，`MongoDB` 3.4.9 → 4.0.1。

增加了衡量后续步骤花费时间的基准:

*   `v1` —连接数据库，从数据库获取数据，打印数据。
*   `v2` —从数据库获取数据，打印数据(不测量连接时间)。

## 基准测试:

关于`Docker`、`PostgreSQL`、`MongoDB`等的所有技术信息。你可以在 [github repo](https://github.com/cn007b/benchmark-postgres-mongo) 中找到。

## 结果:

对我来说，在我的电脑上结果看起来像:

```
+-------------+--------------------+--------------------+
| Benchmark # | PostgreSQL 10.5    | MongoDB 4.0.1      |
+-------------+--------------------+--------------------+
| v1.query1   | 34137 microseconds | 52020 microseconds |
| v1.query2   | 20691 microseconds | 14950 microseconds |
| v1.query3   | 15952 microseconds | 14410 microseconds |
| v1.query4   | 18025 microseconds | 15389 microseconds |
+-------------+--------------------+--------------------+
| v2.query1   | 33596 microseconds | 43352 microseconds |
| v2.query2   | 20072 microseconds |  1711 microseconds |
| v2.query3   | 14750 microseconds |   573 microseconds |
| v2.query4   | 17727 microseconds |  1678 microseconds |
+-------------+--------------------+--------------------+
```

我从这个表中省略了许多基准测试，因为在简单的查询中`mongo`比`postgres`要快。

## 结论:

只有在我的`query1`中，`postgres`比`mongo`更快(因为`$unwind`)对于所有其他查询来说`mongo`比`postgres`更快。

问题是:对于您的特定情况，哪个数据库更快？
答案:你要基准！)

PS:如果您对基准测试有什么有趣的疑问，请告诉我。