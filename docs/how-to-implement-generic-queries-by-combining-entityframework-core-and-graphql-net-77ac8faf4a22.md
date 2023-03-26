# 如何结合 EntityFramework Core 和 GraphQL 实现泛型查询？网？

> 原文：<https://medium.com/hackernoon/how-to-implement-generic-queries-by-combining-entityframework-core-and-graphql-net-77ac8faf4a22>

![](img/8c18f21c25bb083ed295e84ec517fd57.png)

[Source](https://pixabay.com/en/graph-success-cooperation-together-1019845/)

目前，我一直在努力解决这样的问题，比如我必须对我的数据模型中的任何实体执行多次查询。我认为，如果我们可以将所有实体暴露给 API，或者可以限制一些敏感的实体，其他方面或组件随后只需查询它(就像一个通用查询，以避免样板代码，当我们为系统中的所有实体反复实现查询功能时)。

我花了很长时间深入研究了 [OData](https://github.com/OData/WebApi) 项目(实际上是解析器组件)，但结果并不令人满意。可能是因为它太复杂了，很多模式需要预先定义。此外，它仅由使用。NET 生态系统(例如，[微软动态 CRM](https://msdn.microsoft.com/en-us/library/gg334767%28v=crm.8%29.aspx?f=255&MSPPError=-2147217396) ，[微软 Sharepoint](https://docs.microsoft.com/en-us/sharepoint/dev/sp-add-ins/use-odata-query-operations-in-sharepoint-rest-requests)……)。

我真的想要一个能够很好地工作、适应和使用其他生态系统或组件(如前端、移动或物联网设备)的解决方案。这就是我选择脸书的 GraphQL 的原因。我想我不需要解释为什么这些时候会这么酷。关于它的更多信息，我强烈推荐你阅读这篇文章[2017:graph QL](https://dev-blog.apollodata.com/2017-the-year-in-graphql-124a050d04c6)中的一年。

在本文中，我将向您展示我的 POC 项目，该项目试图将[实体框架核心](https://github.com/aspnet/EntityFrameworkCore)和[GraphQL.NET](https://github.com/graphql-dotnet/graphql-dotnet)结合起来，然后我们可以使用它在前端提交 [gql DSL 语言](http://graphql.org/learn/queries/)并通过后端查询您数据库中的每个实体。

谢谢你，[](https://medium.com/u/282dc6f79eac#lib-dotnet 号</li><li id=)[https://github . com/nreco/data/tree/master/examples/SqliteDemo。图表 Api](https://github.com/nreco/data/tree/master/examples/SqliteDemo.GraphQLApi)

*   [https://github.com/landmarkhw/Dapper.GraphQL](https://github.com/landmarkhw/Dapper.GraphQL)*   [https://github.com/JacekKosciesza/StarWars](https://github.com/JacekKosciesza/StarWars)*   [https://github.com/JuergenGutsch/GraphQlDemo](https://github.com/JuergenGutsch/GraphQlDemo)

**感谢阅读！**如果您喜欢这篇文章，请务必**点击**👏所以**下面的符号别人**会**看**吧。

*   2019 年 2 月 13 日:更正本文中的一些含义和错别字