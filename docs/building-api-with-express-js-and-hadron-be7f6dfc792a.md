# 用 Express.js 和 Hadron 构建 API

> 原文：<https://medium.com/hackernoon/building-api-with-express-js-and-hadron-be7f6dfc792a>

![](img/5e1a0cfc8192fa610479c592eb2a87e7.png)

# 背景

与 Brainhub 的人们一起，我们开发了一个工具，利用依赖注入提供的一些魔力，使 CRUD API 的实现变得更加愉快和容易。

[Hadron](https://hadron.pro/) 是一个轻量级的开源框架，可以与 Express.js(目前，Express 是唯一支持的 web 框架)和 TypeORM 等数据库管理工具一起使用。强子甚至由类似`hadron-serialization`的包组成，这使得简单地构造 API 输出的数据成为可能，即使选择的字段仅对一些“组”可见。

强子不会以任何负面方式影响后端性能，它的目标是在构建和维护 API 并提供高效率的同时改善体验。

# 强子+用例概述

用 Hadron 设置后端只需要几行代码。首先，我将向你展示如何使用*强子*和 *Express.js* 建立一个基本的 hello world 路线。

A hello-world example built with **Hadron**

这里最重要的是**配置**对象。我们在那里指定了 Hadron 包的配置(*路由`hadron-express`的*)。构造函数方法返回一个带有**容器**对象的承诺，用于依赖注入。我们将在本文的后面对此进行更深入的探讨。

正如你可能已经注意到的，当使用更多的强子包时，开发一个更大的应用程序可能是一场噩梦。config 对象将是巨大的——想象一下那里的所有路由。当然，我们可以简单地将路径划分到多个文件并导入它们。

这就是`[hadron-json-provider](https://github.com/brainhubeu/hadron/tree/master/packages/hadron-json-provider)`计划的用武之地。这个独立的软件包允许您为自动导入的文件指定路径和扩展名。

这是非常有用的分裂成不同的文件我们的路线。默认情况下，`hadron-express`包接受 Hadron 配置中的键 *routePaths* ，它接受一组路径和扩展名，然后使用`hadron-json-provider`包从这些文件中加载路由。

# 强子 vs 平原快递. js

请记住，强子**不会影响后端应用**的性能——使用 [api 基准](https://github.com/matteofigus/api-benchmark)和`10000 runs sampled with concurrency: 10000`进行测试。

Hadron 为您提供了空间和工具来提高您项目的生产率和可维护性，并在像 T1 这样的框架上创建了一个抽象层。

使用 Hadron 的主要优势在于，您可以快速而**轻松地选择各种工具**来管理数据库，**提供安全性**，并根据用户类型调整您的输出。一切都简单地为您完成，并由您轻松配置。

使用 Hadron 创建 API 是如此简单，以至于你可以将你的路径分成不同的文件，而不用关心依赖关系、实例等。这是因为依赖注入，它用在路由的回调函数中。

当您注册任何包时，它会使用提供的配置进行初始化，并放入容器中的特定键下，您可以稍后在路由的回调函数中使用这些键。

# 强子依赖注入

Hadron 提供了在我们称之为**容器**的东西中存储数据或对象实例的选项。它还保存由 Hadron 包初始化的数据，例如，`hadron-typeorm`包中的数据库存储库。

在容器中注册项目时，您可以选择一个生存期选项，即:

*   `value` —这是默认行为。当从容器中获取数据时，您获得的是注册的相同内容。
*   `singleton` —总是返回注册类/构造函数的同一个实例。
*   `transient` —总是返回注册类/构造函数的新实例。

依赖注入在路由的回调中使用，这允许您轻松地访问任何容器值。假设我们将把一个字符串`Hello World`存储在容器中的键`message`下，并且我们想在我们的路径上访问它。

向 [http://localhost:8080/](http://localhost:8080/) 发送请求后，您将收到:

```
"Message stored in container: Hello World"
```

这就是您访问容器项目的方式。大多数包都在那里存储项目，因此您可以轻松地执行一些操作或检索所需的数据。

您现在可能会考虑访问我们的路线中经常使用的*请求*变量，如**标题**、**参数**、**正文**、**查询**等..这些数据作为回调函数的第一个参数提供。

在这种情况下，路由的响应将返回请求的 header 对象。

# Hadron 软件包快速概述

到目前为止我们只发现了几个包，尤其是`hadron-express`。我们来讨论一些官方的强子包。

本文中的所有例子都可以在 GitHub 的[这里](https://github.com/damian66/hadron-examples)找到。随意和他们玩。

## [强子型态](https://github.com/brainhubeu/hadron/tree/master/packages/hadron-typeorm)

这个包将[类型的 ORM](https://github.com/typeorm/typeorm) 包与 Hadron 进行了封装和集成。它让我们能够以简单的方式注册和访问[库](http://typeorm.io/#/working-with-repository)或[迁移](http://typeorm.io/#/migrations)。

要连接您的数据库，您只需导入`hadron-typeorm`包，并向 Hadron 的配置提供连接对象。

假设我们有一个用户表。为了检索一个带有 ID 的记录，我们将使用一个端点，如`/user/1`。我们的回调可能看起来像这样:

看起来很简单，对吧？但是和我们数据库的连接呢？正如我之前提到的，在初始化强子时，我们需要为我们的配置提供一个连接对象。在下面的例子中，我们将使用 MySQL 数据库。

要在 TypeORM 中创建存储库，我们需要提供一个[实体](http://typeorm.io/#/entities)。然后，Hadron 将从其中注册一个存储库，并在包含实体名称和*存储库*后缀的关键字下注册—实体*用户*将在*用户存储库*关键字下的容器中注册。

## [强子-序列化](https://github.com/brainhubeu/hadron/tree/master/packages/hadron-serialization)

序列化程序允许您指定 JSON 输出的形状。

假设一个用户用一个 auth 令牌向 API 发送请求。使用一个`hadron-serialization`包，我们可以很容易地根据用户的权限来处理他将看到的字段。

在下面的例子中，只有一个端点
— `**/:group?**`(我们将关注两个组，mod 和 admin)。

在我们的例子中，将会有一个用户列表作为对该端点的响应返回。每个用户都应该看到用户的名字和姓氏，组 **mod** 中的用户也应该看到一封电子邮件，而 **admin** 将看到他们所有人的附加 ID 字段。

这是我们将使用的数据:

所以为了让`hadron-serialization`包工作，我们需要初始化它。但是在此之前，我们应该为我们的数据定义一个模式。我们可以在另一个文件中定义它，或者简单地在 Hadron 的配置中提供一个对象文本。我们将把它保存为外部 JSON 文件。

因此，明确地说，模式是一个对象，应该包含:

*   **名称** —简单的模式名称。在序列化数据时，我们将需要它来识别模式。
*   **属性** —这些字段包含一个对象数组。每个对象保存将被序列化的键的名称、它的类型和它应该可见的组(如果组是空的，那么它们对每个人都是可见的)。

```
Notice that if you don’t specify some key in a schema, it **won’t be visible**!
```

好了，现在我们已经定义了序列化包中所需的数据和模式，接下来让我们定义 Hadron 实例。我们的 **index.js** 文件应该是这样的:

上面的例子基本上是一个使用强子制作的整个 app 的例子。所以设置`hadron-serialization`包很简单——我们所需要做的就是声明模式对象，它将被用来形成我们的数据并在配置中提供它们。

接下来，当我们想要序列化数据时，只需从容器中取出 **serializer** 对象并执行 serialize 方法。

## [强子事件](https://github.com/brainhubeu/hadron/tree/master/packages/hadron-events)

`Hadron-events`包允许您使用内置事件，甚至声明您自己的定制事件，将监听器绑定到它们，并在应用程序生命周期的任何时候简单地发出它们。

目前，在强子中，包文档中列出的内置事件很少，你可以在这里找到[。](https://github.com/brainhubeu/hadron/tree/master/packages/hadron-events)

为了初始化我们的简单事件处理程序，我们需要提供事件监听器。在下面的例子中，我们将在`hadron-core`的引导函数中提供`hadron-events`包的配置。

所以我们将我们的侦听器包含在一个外部文件中。在我们的例子中，我们将尝试**handleRequestCallbackEvent**，它将在每个请求发生之前发出事件。

在`hadron-events`包中，我们还可以在应用程序生命周期的任何部分发出我们自己的事件。我们将监听两个具有简单名称的事件，如**成功事件**和**失败事件**。如果*键*值为 **foo** ，那么当用户向`*/:key*`发送请求时，我们将发出该事件。

我们例子的最后一步是实现监听器。如前所述，我们将创建两个侦听器，一个用于 successEvent，一个用于 failEvent。

这将允许我们从应用程序中的任何地方发出这些自定义事件。为了简单起见，在我们的例子中，这个地方就是请求的回调。

# 链接

*   [Brainhub 的 Github](https://github.com/brainhubeu) —如果你正在寻找更多的开源项目。
*   [Brainhub](https://brainhub.eu/)——如果你正在寻找专业人士来帮助你构建你的 JavaScript 应用程序。

# 关于

[强子](https://hadron.pro/)及其官方软件包由 Brainhub 开发团队维护。它由 Brainhub 资助，Brainhub 的名称和徽标是 Brainhub Sp 的商标。z o.o..你可以查看一下我们队友支持/开发的其他[开源项目](https://github.com/brainhubeu)。

![](img/de252fdb9335d3e2c80837e8f25da0dc.png)

我们热爱开源 JavaScript 软件！查看我们的其他项目或雇佣我们来构建您的下一个 web，[桌面和移动 JavaScript 应用](https://brainhub.eu/services)。

*帖子由* [*达米安*](/@damian23) *，* o *原创发布于*[*brainhub . eu*](https://brainhub.eu/blog/building-api-expressjs-and-hadron/)