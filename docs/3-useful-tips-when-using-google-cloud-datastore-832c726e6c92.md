# 使用谷歌云数据存储的 3 个有用技巧。

> 原文：<https://medium.com/hackernoon/3-useful-tips-when-using-google-cloud-datastore-832c726e6c92>

![](img/5b74f294fb926bb3e775c0bf1ccb8ccd.png)

[Google](https://hackernoon.com/tagged/google) Cloud Datastore 是一个托管的、无模式且高度可扩展的 [NoSql](https://hackernoon.com/tagged/nosql) 数据库，对于那些不太担心随着使用量的增长而扩展数据库的开发人员来说尤其有用。它是建立在 Google 的 Bigtable 数据库之上的，它有很多强大的功能，你可能想在你的下一个项目中考虑它，但在你这样做之前，这里有一些你应该知道的快速提示，以避免使用这个数据库时的一些陷阱。

1.  **谷歌云数据存储不是 SQL，它甚至都不接近。**

Google Cloud Datastore 是一个 NOSQL 数据库，但当人们和文档试图解释这个概念时，他们倾向于将其与 SQL 进行比较，例如，实体可以比作表，其属性可以比作列，因为许多开发人员已经熟悉 SQL。此外，Datastore 还有一个强大的查询引擎，允许您查询数据，甚至相应地对数据进行排序，这可能会使开发人员认为他们可以像在 SQL 中一样对数据进行建模。但事实是，数据存储的底层实现与 SQL 有很大不同，以下是一些关键差异。

I. *无自动递增主键*:与 SQL 不同，在 SQL 中，您通常可以拥有一个随着每次写入而递增的自动递增主键，在 Google Cloud Datastore 中，实体由一个 [**键**](https://cloud.google.com/datastore/docs/reference/data/rest/v1/Key) 标识，该键通过其[种类及其标识符](https://cloud.google.com/datastore/docs/concepts/entities#kinds_and_identifiers)的组合而变得唯一。该标识符要么是 Datastore 自动为您生成的 ID，要么是您可以作为标识符提供给 Datastore 的[键名属性](https://cloud.google.com/datastore/docs/concepts/entities#kinds_and_identifiers)，该属性是一个您确信对其来说是唯一的字符串

> 注意:标识符在一个种类中是唯一的，并且在实体创建后不能改变。

二。*无外键约束*:在数据存储中，您可以使用其他实体的 [**键**](https://cloud.google.com/datastore/docs/reference/data/rest/v1/Key) 来引用它们，但是在写入或删除这些实体时不会进行数据完整性检查，因此您无法确保您所引用的键的实体确实存在。这里您必须小心，因为这是数据完整性问题可能性极大的地方。

三。 *Datastore 不强制唯一性*:除了上面(I)中提到的标识符，Google Cloud Datastore 不允许您为实体中的任何其他字段指定唯一性，如果您希望确保唯一性，您必须通过在编写实体时执行读取来自己实现。

2.**很早就知道索引什么:**

Google Cloud datastore 使用[索引](https://cloud.google.com/datastore/docs/concepts/indexes)来帮助查询，索引实体中的字段对于 Datastore 中的查询非常重要，事实上，如果您没有索引字段并试图使用该字段过滤查询，即使查询匹配结果，您也不会得到任何结果，这是因为 Datastore 在查询时只查找索引，它不允许您像许多其他数据库一样进行全表扫描来获得查询结果。

数据存储中有两种类型的索引，基本索引和复合索引，基本索引用于单个属性字段，而复合索引用于按多个字段进行筛选的情况。

> 了解您希望尽早索引哪些字段是很重要的，因为索引可能需要很长时间来构建，尤其是当您的数据库中有很多实体时。

事实上，对于单属性索引，它们不是在索引后自动构建的，索引是在每次写入时构建的，因此，如果您有 100 万个这种类型的实体，并且您打算索引一个字段，您将必须读取 100 万个实体中的每一个，并将它们写回以记录索引，这可能非常昂贵。此外，当您尝试查询尚未编制索引的复合字段时，即使您已经编制了这些字段的索引，并且还没有完成索引的构建，也会出现错误。

因此，为了避免这些问题，请尝试考虑您希望在查询中使用的字段，并尽早对它们进行索引。

3.**如何对实体进行计数(分片计数器):**与 SQL 不同，在 SQL 中您可以轻松地使用查询

```
select count(*)
```

要获得一个表中的条目数，Datastore 没有任何这样的语法，你必须自己使用其他实体对你的实体进行计数，所以如果你有一个名为 **User** 的实体，并且你希望知道你的数据库中有多少用户，那么你需要有另一个实体让我们称它为 **UserCount。**User count 实体将有一个数字字段，该字段将在用户被写入数据库时增加，在用户被从数据库中删除时减少。这相当简单，但是当计数需要频繁更新时，比如说每秒 20 次，可能会出现问题。这可能导致[数据存储争用错误](https://cloud.google.com/appengine/articles/scaling/contention)，这意味着每个更新项目的并发请求在其请求超时之前无法完成。使用 [**分片计数器**](https://cloud.google.com/appengine/articles/sharding_counters) 解决了这个问题，它只是意味着创建该实体的片段(分片),并在您需要更新计数时随机选取这些实体中的每一个进行更新，这将降低出现数据存储争用错误的可能性。

> 注意:分片数量越多，出现数据争用错误的可能性就越小，G [oogle Cloud Samples](https://github.com/GoogleCloudPlatform/appengine-sharded-counters-java) 有一个关于如何实现分片计数器的很好的例子。

因此，虽然数据存储保证了可扩展性，但它也有一些缺点，特别是在数据完整性和查询方面，在将它用于项目之前，您应该考虑这些因素。

**如果你有其他建议想分享，可以在下面的评论里添加，谢谢**。