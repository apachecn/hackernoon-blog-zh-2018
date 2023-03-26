# GraphQL 简介

> 原文：<https://medium.com/hackernoon/an-introduction-to-graphql-2c3f7d8fb4e0>

> 对学习 JavaScript 感兴趣？在 jshandbook.com 的[获得我的免费电子书](https://jshandbook.com/)

GraphQL 是 API 的新前沿(*应用编程接口*)。

它是一种用于 API 的查询语言，以及一组用于执行查询的服务器端运行时(用各种后端语言实现)。

它不依赖于特定的技术，但是你可以用任何语言来实现它。

这是一种**直接与 REST** ( *具象状态转移*)API 竞争的方法论，很像 REST 最初与 **SOAP** 竞争。

GraphQL 是在脸书开发的**，就像最近震撼世界的许多技术一样，比如 [React](https://flaviocopes.com/react-beginners-guide) 和 React Native，它是在 2015 年**公开发布的**——尽管脸书之前在内部使用了几年。**

除了脸书，许多大公司都在采用 GraphQL，包括 GitHub，Pinterest，Twitter，Sky，New York Times，Shopify，Yelp 和其他许多公司。

# GraphQL 原则

**GraphQL 公开了一个端点**。

您通过使用特殊查询语言语法向端点发送一个查询。那个查询是一个字符串。

服务器通过提供 JSON 对象来响应查询。

让我们看看这样一个查询的第一个例子。这个查询获得了一个名字为`id=1`的人的名字:

```
GET /graphql?query={ person(id: "1") { name } }
```

或者只是

```
{ person(id: "1") { name } }
```

我们将得到这个 JSON 响应:

```
{ "name": "Tony" }
```

让我们增加一点复杂性:通过从`address`对象中提取，我们得到了这个人的名字，以及这个人居住的城市。我们不关心地址的其他细节，服务器也不会将它们返回给我们。

```
GET /graphql?query={ person(id: "1") { name, address { city } } }
```

或者

```
{ 
  person(id: "1") { 
    name 
    address { 
      city 
    } 
  } 
}{ 
  "name": "Tony", 
  "address": { 
    "city": "York" 
  } 
}
```

如您所见，我们得到的数据基本上与我们发送的请求相同，充满了值。

# GraphQL 与 REST

由于 REST 是构建 API 的一种非常流行的方法，或者我可以说是通用的方法，所以可以假定您对它很熟悉，所以让我们来看看 GraphQL 和 REST 之间的区别。

# 休息是一个概念

REST 是一个事实上的架构标准，但是它实际上没有规范和大量的非官方定义。GraphQL 有一个[规范](http://facebook.github.io/graphql/)草案，它是一种[查询语言](http://graphql.org/learn/queries/)而不是一种架构，有一套围绕它构建的定义良好的工具(以及一个蓬勃发展的生态系统)。

REST 构建在现有架构之上，最常见的场景是 HTTP，而 GraphQL 正在构建自己的一套约定。这可能是一个优势，也可能不是，因为 REST 通过在 HTTP 层进行缓存而免费惠及了*。*

# *单一端点*

*GraphQL 只有一个端点，您可以在那里发送所有的查询。使用 REST 方法，您可以创建多个端点，并使用 HTTP **动词**来区分读操作(GET)和写操作(POST、PUT、DELETE)。GraphQL 不使用 HTTP 动词来确定请求类型。*

# *根据您的需求量身定制*

*使用 REST，您通常不能选择服务器返回给您什么，除非服务器使用[稀疏字段集](http://jsonapi.org/format/#fetching-sparse-fieldsets)实现部分响应，并且客户端使用该特性。API 维护者不能实施这样的过滤。*

*API 通常会返回比您所需要的更多的信息，除非您也控制 API 服务器，并且针对每个不同的请求定制您的响应。*

*使用 [GraphQL](https://hackernoon.com/tagged/graphql) 时，您显式地只请求您需要的信息，而不是从完整的默认响应中“选择退出”,而是强制选择您想要的字段。*

*这有助于节省服务器上的资源，因为您很可能需要更少的处理，也节省了网络，因为要传输的负载更小。*

# *GraphQL 使得监控字段使用变得容易*

*对于 REST，除非强制使用稀疏字段集，否则没有办法确定一个字段是否被客户端使用，所以当涉及到重构或弃用时，不可能确定实际的用法。*

*GraphQL 使得跟踪客户机使用哪些字段成为可能。*

# *访问嵌套的数据资源*

*GraphQL 允许生成少得多的网络调用。*

*让我们来做一个例子:你需要访问一个人的朋友的名字。如果您的 REST API 公开了一个`/person`端点，该端点返回一个包含朋友列表的 person 对象，那么您通常首先通过执行`GET /person/1`来获取 person 信息，该信息包含其朋友的 ID 列表。*

*除非一个人的朋友列表已经包含了朋友的名字，否则有 100 个朋友时，你需要向`/person`端点发出 101 个 HTTP 请求，这是一个巨大的时间开销，也是一个资源密集型操作。*

*使用 GraphQL，您只需要一个请求，即询问一个人的朋友的名字。*

# *类型*

*REST API 是基于 JSON 的，它不能提供类型控制。 **GraphQL 有一个类型系统**。*

# *哪个更好？*

*世界各地的组织都在质疑他们的 API 技术选择，他们试图找出从 REST 迁移到 GraphQL 是否最符合他们的需求。*

*当您需要公开复杂的数据表示时，或者当客户端可能只需要数据的一个子集时，或者当它们定期执行嵌套查询以获取所需数据时，GraphQL 是一个完美的选择。*

*就像编程语言一样，没有唯一的赢家，这完全取决于你的需求。*

# *GraphQL 查询*

*在本文中，您将了解 GraphQL 查询是如何组成的。*

*我将介绍的概念是*

*   *字段和参数*
*   *别名*
*   *碎片*

# *字段和参数*

*以这个简单的 GraphQL 查询为例:*

```
*{ 
  person(id: "1") { 
    name 
  } 
}*
```

*在这个查询中，您可以看到 2 个字段和 1 个参数。*

*字段`person`返回一个*对象*，其中有另一个字段，一个*字符串*。*

*参数允许我们指定我们想要引用哪个人。我们传递一个`id`，但是我们也可以传递一个`name`参数，如果我们与之对话的 API 可以选择通过名字找到一个人。*

*参数不限于任何特定的字段，我们可以在`person`中有一个`friends`字段，列出那个人的朋友，它可以有一个`limit`参数，指定我们希望 API 返回多少:*

```
*{ 
  person(id: "1") { 
    name
    friends(limit: 100) 
  } 
}*
```

# *别名*

*您可以要求 API 返回具有不同名称的字段，例如:*

```
*{ 
  owner: person(id: "1") { 
    fullname: name 
  } 
}*
```

*会回来的*

```
*{ 
  "data": {
    "owner": { 
      "fullname": "Tony" 
    } 
  } 
}*
```

*除了为您的客户机代码创建更多的特别命名之外，如果您需要在同一个查询中**引用同一个端点两次**，这个特性是唯一可以使查询工作的东西:*

```
*{ 
  owner: person(id: "1") { 
    fullname: name 
  } 
  first_employee: person(id: "2") { 
    fullname: name 
  } 
}*
```

# *碎片*

*在上面的查询中，我们复制了人员结构。片段允许我们指定一次结构(对许多字段非常有用):*

```
*{ 
  owner: person(id: "1") {
    ...personFields 
  } 
  first_employee: person(id: "2") {
    ...personFields
  }
} fragment personFields on person {
  fullname: name 
}*
```

# *GraphQL 变量*

*更复杂的 GraphQL 查询需要使用**变量**，这是一种动态指定查询中使用的值的方式。*

*在这种情况下，我们将人员 id 作为字符串添加到查询中:*

```
*{ 
  owner: person(id: "1") { 
    fullname: name
  } 
}*
```

*id 很可能会在我们的程序中动态改变，所以我们需要一种方法来传递它，并且**不使用字符串插值**。*

*对于变量，相同的查询可以写成*

```
*query GetOwner($id: String) { 
  owner: person(id: $id) { 
    fullname: name 
  } 
} { 
  "id": "1"
}*
```

*在这个代码片段中，我们为我们的查询指定了名称`GetOwner`。把它想象成命名函数，而之前你有一个匿名函数。当应用程序中有大量查询时，命名查询非常有用。*

*带有变量的查询定义看起来像一个函数定义，它以同样的方式工作。*

# *使变量成为必需*

*向类型追加一个`!`:*

```
*query GetOwner($id: String!)*
```

*而不是`$id: String`将使$id 变量成为必需的。*

# *为变量指定默认值*

*您可以使用以下语法指定默认值:*

```
*query GetOwner($id: String = "1")*
```

# *GraphQL 指令*

*指令允许您在变量为真或假时包含或排除字段。*

```
*query GetPerson($id: String, $getAddress: Boolean) { 
  person(id: $id) { 
    fullname: name, 
    address: @include(if: $getAddress) { 
      city 
      street 
      country 
    } 
  } 
}{ 
  "id": "1", 
  "getAddress": false
}*
```

*在这种情况下，如果我们传递的变量`getAddress`为真，我们也会得到地址字段，否则不会。*

*我们有两个可用的指令:`include`，我们刚刚看到的(如果为真，则包含在内)，和`skip`，相反(如果为真，则跳过)*

# *@include(if: Boolean)*

```
*query GetPerson($id: String, $getAddress: Boolean) { 
  person(id: $id) { 
    fullname: name, 
    address: @include(if: $getAddress) { 
      city 
      street 
      country 
    } 
  } 
} {
  "id": "1", 
  "getAddress": false 
}*
```

# *@skip(if: Boolean)*

```
*query GetPerson($id: String, $excludeAddress: Boolean) { 
  person(id: $id) { 
    fullname: name, 
    address: @skip(if: $excludeAddress) { 
      city 
      street 
      country 
    } 
  } 
}{ 
  "id": "1", 
  "excludeAddress": false 
}* 
```

> *对学习 JavaScript 感兴趣？在 jshandbook.com 的[获得我的免费电子书](https://jshandbook.com/)*