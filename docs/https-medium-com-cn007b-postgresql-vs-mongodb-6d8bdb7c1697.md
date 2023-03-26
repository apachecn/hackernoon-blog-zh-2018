# PostgreSQL vs MongoDB

> 原文：<https://medium.com/hackernoon/https-medium-com-cn007b-postgresql-vs-mongodb-6d8bdb7c1697>

![](img/58c9a3c410a4eb7f9b1dd69b1825f760.png)

## 在相同数据集上测试 PostgreSQL 和 MongoDB 的读取性能

## TL；博士:

我现在正忙于为`web`项目编写新的微服务，目标是创建尽可能快的微服务。因为将使用编程语言`GOlang`(真的相信它超级快，并且非常适合这样的任务)。在这篇文章中，我将解释为什么决定使用某个数据库。

这个话题并不新鲜，您可以找到许多与这些数据库性能相关的基准测试。但是我没有找到任何关于基准的技术信息(什么，哪里，如何，多少，用了什么等等。)也没有关于数据库版本的信息(并不是我找到的每篇文章都提供这种信息),但也许有些事情已经改变了，也许一些数据库现在工作得更好了，也许我的情况与其他文章中考虑的情况有些不同，所以该轮到我对这些数据库进行基准测试了！)

在我的微服务中，我需要存储两个实体(`storage`和`file`)和关系`1 to many`(超级简单的情况)。
在`postgres`会有 2 张桌子:

并且在`mongo`中会有嵌入数据的文档:

到目前为止没什么特别的，但有趣的是**如何接收**数据。假设我们需要获得这样的信息:

为了从两个数据库中接收准确的信息，我们必须在`postgres`中运行下一个 SQL 查询:

而`mongo`中的这个聚合查询:

对简单的`mongo`查找和`postgres`连接进行基准测试是不公平的，因为`mongo`已经有了所有信息(作为嵌套数据),但是`postgres`必须工作……但是我们的查询会更有趣！！！
每个数据库必须执行几乎相同的操作:通过非索引字段`count`查找数据，通过`id`和`name`排序，执行`offset`和`limit`，当 mongo 必须执行`unwind`时`postgres`必须执行 join。我认为这是理所当然的！

## 基准测试:

关于`Docker`、`PostgreSQL`、`MongoDB`、`GOlang`、数据转储、数据导入等等的所有技术信息，你都可以在 [github repo](https://github.com/cn007b/benchmark-postgres-mongo) 中找到。
请看一下，检查所有步骤，甚至在您的计算机上执行该基准测试，或者只是查看 [postgres](https://github.com/cn007b/benchmark-postgres-mongo/blob/master/src/benchmark/postgres/query1.go) 和 [mongo](https://github.com/cn007b/benchmark-postgres-mongo/blob/master/src/benchmark/mongo/query1.go) 的`GOlang`实现。

## 结果:

对我来说，在我的电脑上结果看起来像:

```
+-------------+-------------------+--------------------+
| Benchmark # | PostgreSQL        | MongoDB            |
+-------------+-------------------+--------------------+
| query 1     | 8349 microseconds | 21721 microseconds |
+-------------+-------------------+--------------------+
```

## 结论:

这只是我的特殊情况，只有我的查询，只有我的计算机上的读取操作和结果…对于你的情况，你必须检查你的查询，你的数据集等。

如果您发现任何差距或错误，或者您的结果不同，请提供任何反馈。如果你有其他有趣的测试案例，请告诉我！

PS:我真的很喜欢 mongo，我正在寻找如何加快它的速度——如果你有任何线索——请联系我！