# 使用阿波罗-预言+阿波罗处理和记录 GraphQL 错误

> 原文：<https://medium.com/hackernoon/handling-and-documenting-graphql-errors-using-apollo-prophecy-apollo-7a32de3547a1>

![](img/0d105ddafc6f353bf126197c08437ae0.png)

You shall fail… successfully

所有计算机程序都有错误；它们可能很难维护，但是正确处理它们无疑是构建应用程序最关键的部分。

在客户端/服务器架构的环境中，我们需要服务器输出**格式良好的**和**易于识别的错误**，客户端可以**无缝读取、处理**和**处理**，以便**成功失败**。

![](img/a21fd669ed50fe4896914cfeceafb124.png)

**E.g. a successful failure**

GraphQL 驱动的 API 没有例外😏)到这个规律。以下是 GraphQL 规范的最新草案*(孙，2018 . 6 . 10)*关于错误输出应该如何格式化的内容。

> 如果操作遇到任何错误，响应映射必须包含一个带有关键字`*errors*`的条目。
> 
> 每个错误都必须包含一个带有关键字`*message*`的条目，该条目带有错误的字符串描述，旨在为开发人员理解和纠正错误提供指导。
> 
> GraphQL 服务可能会通过关键字`*extensions*`提供额外的错误条目。如果设置了这个条目，它的值必须是 map。这个条目是为实现者保留的，他们可以以他们认为合适的方式向错误添加附加信息，并且对其内容没有附加限制。

考虑到这一点，典型的错误对象应该如下所示:

```
...
"errors": [
    {
      "message": "Only Prophets can do this",
      "locations": [ ... ],
      "path": [ ... ],
      "extensions": {
        "code": "NOT_A_PROPHET",
        "timestamp": "Thu Jun 21 17:03:00 UTC 2018"
      }
    }
  ]
...
```

请记住，我们希望错误输出是“**格式良好的**和**易于识别的**”，这意味着它应该至少包含一个可以被计算机无缝处理的字段。

要考虑的第一个候选项是`message`，一个*“针对开发人员的错误的字符串描述[…]”。*由于它被格式化为人类可读的格式，它可能是一个包含不需要的字符( *%，à，$，€，@，空格，*等)的有表现力的长字符串，因此并不理想。

根据规范，`extensions`应为`errors` *的任何附加条目的专用空间。*在这里，它赋予我们附加一个`code`键的能力，提供一个**机器可读的**数据，可以被****无缝读取，处理*******处理*** *。***

```
**if (error.extensions.code === "NOT_A_PROPHET") {
  // Do Something
}**
```

# **走向🏇**

**我们刚刚看到了关于如何在 GraphQL API 的上下文中输出错误的指南。有了它，我们应该能够:**

*   **在我们的解析器中抛出并输出符合**规范的**和**可识别的**错误——多亏了`*extensions*`。**
*   **识别和处理错误客户端到**失败成功**。**

**然而，该规范并没有为 API 错误文档、重试或故障处理等问题指定指导原则，这意味着有无数种方法可以为此目的适当地安排我们的代码库。**

**没有明确的约定导致我构建了 [**阿波罗-预言**](https://github.com/theGlenn/apollo-prophecy) 。**

# **异教徒的方式**

**首先，让我们举例说明没有 [**阿波罗预言**](https://github.com/theGlenn/apollo-prophecy) 维护错误会是什么样子。为此，我们将使用 **Apollo Server** ，这是一个优秀的、符合规范的、功能齐全的、维护良好的 nodeJS graph QL 服务器实现。**

**因为我们使用的是 [Apollo 服务器](https://github.com/apollographql/apollo-server)，所以我们可以使用构造函数`ApolloError(message, code)`:使用这个构造函数抛出的错误会产生一个符合规范的 JSON 输出，如上图所示。**

```
**throw new ApolloError("Only Prophets can do this", "NOT_A_PROPHET");**
```

**为了让我们更容易存储错误，我们可以按照以下方式组织我们的服务器端代码:**

**server**

**并正确处理这样的错误:**

**client**

**完成了，对吗？**

**不，我们可以做得更好。有了这个配置，我们最终做了同样的工作两次:因为**对于服务器上每个现有的错误条目**，我们必须在客户端编写一个**对应的键**。**

**我不知道你怎么想，但我更喜欢说干。**

# **利用 API 文档📑**

**GraphQL 最有趣的命题之一是 ***API 应该是自文档化的。*** 虽然这通常是通过一种称为“自省查询”的机制来完成的，这种机制为我们提供了模式中字段和类型的详细信息，但这并不意味着我们不能向模式本身添加文档资料:**

> **如果错误是模式的一部分呢？**

**下面是我们如何利用这一点:**

****1。**我们将错误包含在模式中:**

```
**type ErrorExtensions {
  code: String!
}type Error {
  name: String!
  message: String
  extensions: ErrorExtensions
}type Query {
  ...
  errors: [Error!]!
  ...
}**
```

****2。**我们在查询字段上创建相应的解析器:**

```
**...
const resolvers = {
  Query: {
    ...
    errors: { ... }
  }
}
...**
```

## **那很酷，但是客户怎么办？🤷**

**假设关于错误的信息可以通过我们的 API 访问，我们需要找到一种从客户端访问它们的方法，记住我们希望避免两次做同样的工作。**

**从这里我们可以讨论两种不同的实现:**

1.  **每次我们的应用程序启动时，客户端可以**执行一个查询来获取所有的错误代码，并将它们存储在本地**。**😒** *咩…***
2.  **作为构建过程的一部分，通过获取和**在代码库**中静态存储错误，在开发端处理它。**💁** *有何不可？***

**由于正确的错误处理对应用程序的良好运行至关重要，使用**选项 1** 会使获取所有错误的定义成为应用程序启动过程的一个强制步骤——因此增加了加载时间。**

**这就是为什么为了清洁度和整体性能，我更喜欢**第二选项**。**

# **先知之道？🧙🏼‍**

**我已经开始从事[阿波罗预言](https://github.com/theGlenn/apollo-prophecy)的工作:一个代码生成命令行界面，做我们需要的事情(还有一点点！).它将:**

*   **生成我们可以在解析器中抛出的错误，并通过模式作为文档公开— `apollo-prophecy generate`**
*   **查询服务器模式并生成带有方法和帮助器的文件，以优雅地处理错误— `apollo-prophecy ask`**

**目标是让你的服务器和客户端错误库保持同步。**

**首先，通过你喜欢的软件包管理器安装它。**

```
**[npm | yarn] install -g apollo-prophecy**
```

## **像希腊神一样制造错误🔮**

**`generate`命令将创建一个包含可抛出错误类的文件。它接受如下格式的 JSON 文件作为输入:**

**Map of objects whose keys are error names, each of those object has a key “`message"` (optional) and "`code"`**

**它可以像下面这样运行(如果没有指定，它将在运行文件夹中查找一个 **errors.json** 文件):**

```
**apollo-prophecy generate errorsDef.json**
```

**使用上面的 **errosDef.json** ，CLI 将生成以下文件。**

**以下是生成的文件关键组件:**

*   **`errorsList` —用于文档输出的普通 JSON 数组。它包含所有错误表示及其静态数据:`name`、`message`、`extensions -> code`。*如果没有要生成的错误，总是生成但为空。***
*   **`errorType` —可以包含在**模式定义**中的 GraphQL 对象类型。对于文档，应与`errorsList` 一起使用。*总是按原样生成*。**
*   **`PropheticError` —类扩展 ApolloError 本应由该文件中的其他错误**继承**。*总是按原样生成*。**
*   **`NotAProphetError ProphetNotFoundWithId` —这是用 JSON 文件输入的信息生成的两个自定义错误类。**

**我们可以在我们的服务器中使用所有这些元素。假设我们需要错误成为我们模式的一部分，我们可以做以下事情:**

```
**import { errorsList, NotAProphetError } from './gen/GeneratedErrors'Query: {
  errors: () => errorsList
  getAllUsers: () => {...throw new NotAProphetError()},
}**
```

## **嗯，好吧…那我们现在是先知了吗？🤔**

**还没有；先知需要和神交流才能预见未来，不是吗？使用[阿波罗预言](https://github.com/theGlenn/apollo-prophecy)，我们可以用命令`ask`做类似的事情:**

```
**apollo-prophecy ask [http://localhost:3000/graphql](http://localhost:3000/graphql) [--field]**
```

**这将向指定的端点发送一个请求，并尝试对`--field`选项执行 GraphQL 查询，以尝试获取错误信息(如果未指定任何内容，默认情况下将查询一个*“错误”字段*)。**

**下面是生成文件的一个极其简化的版本。如果你想知道它到底是什么样子，那就自己去试试吧！**

*   **`PropheticErrorCode`—一个枚举，包含模式中暴露的所有错误的代码。**
*   **`errorHere`和`isThis`是真正的两个助手方法，它们使我们能够以干净和可重用的方式处理客户端错误。**

## **`- errorHere(error)`**

**当被调用时，它返回一个对象，该对象有一个以在服务器上发现的每个错误命名的**属性。根据所提供的参数，被调用的属性返回**真或假**:****

```
**import { errorHere } from `./_generated/Errors.ts`;...(error) => {
  if(errorHere(error).isNotAProphetError){
    // Do something
  } else if(errorHere(error).isProphetNotFoundWithId){
    // Do something else
  }
}**
```

## **`- isThis(error)`**

**当被调用时，它返回一个对象，该对象有一个以在服务器**上发现的每个错误**命名的**处理函数。****

```
**import { isThis } from `./_generated/Errors.ts`;...(error) => {
  isThis(error)
  .UserNotFoundError(() => ...)
  .NotAProphetError(() => ...)
  .handle()
}**
```

**处理程序返回与`isThis`相同的实例对象，这样每个函数调用都可以被链接起来。一旦调用了`handle`方法，它就开始检查，如果匹配就调用相应的处理程序。**

**然后…瞧！多亏了`ask`命令，我们可以通过模式保持客户端错误存储库与 API 同步。通过使用`errorHere`和`isThis`,我们现在有了一个干净的、富有表现力的处理错误的方法——看，代码也很漂亮！**

# **结论**

**就像任何年轻的技术一样， [GraphQL](https://graphql.org/) 仍然有空白需要填补。[Apollo-prophet](https://github.com/theGlenn/apollo-prophecy)就是为了填补其中一个空白而构建的:*我们如何实现错误处理和文档*。但这并不是谈话的结束；阿波罗预言是开源的，我相信我们可以一起想出更好的方法来改进它。**

**关于阿波罗预言[](https://github.com/theGlenn/apollo-prophecy)**已经有很多工作和修正要做；贡献和建议都是受欢迎和需要的。请访问 [Github](https://github.com/theGlenn/apollo-prophecy/issues/new) 并查看现有的[问题](https://github.com/theGlenn/apollo-prophecy/issues)或者甚至[创建新的](https://github.com/theGlenn/apollo-prophecy/issues/new)问题。****

****如果你已经走了这么远，感谢你阅读❤️。我真的希望你喜欢这篇文章，我很乐意听到你的想法和反馈🙂。****

****[](https://github.com/theGlenn/apollo-prophecy) [## 葛兰/阿波罗预言

### 阿波罗-预言-🔮GraphQL 错误管理变得简单，为 Apollo 生成自定义的机器可读错误…

github.com](https://github.com/theGlenn/apollo-prophecy)****