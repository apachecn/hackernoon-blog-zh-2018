# 科特林的清洁建筑范例

> 原文：<https://medium.com/hackernoon/clean-architecture-example-in-kotlin-9f23169219be>

![](img/520d33bdcc71e44285507e571e8e9442.png)

在过去的几年里，已经有许多博客文章和文章介绍了*干净的架构*，正如*罗伯特·c·马丁(鲍勃叔叔)*在他的[博客文章](https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html)和(更详细的)他的奇妙的书 [*“干净的架构:软件结构和设计的工匠指南”中所介绍的那样*](https://www.amazon.com/Clean-Architecture-Craftsmans-Software-Structure/dp/0134494164)

在这篇文章中，我们展示了一个 REST 服务的例子，它使用了干净的架构，并且是用 *Kotlin* 编写的。

源代码可以在以下报告中找到:

[](https://github.com/thecodegang/clean-architecture-example) [## 烟囱/清洁建筑-示例

### 一个在科特林和 Spring Boot 2.0 中的简单的干净架构的例子

github.com](https://github.com/thecodegang/clean-architecture-example) 

# 干净架构示例模块的简短描述

该项目由四个模块`core`、`usecases`、`dataproviders`和`delivery`组成。

# `core`模块

该模块包含域实体。不依赖于框架和/或库。

# `usecases`模块

该模块包含我们的应用程序所必需的业务规则。这个模块唯一的依赖是对`core`的依赖。在本模块中，将定义存储库的网关。每个用例定义了遵循 [ISP](https://en.wikipedia.org/wiki/Interface_segregation_principle) 所需的网关接口。这些网关在`core`中定义的域实体上运行。

在该模块中，还定义了`[UseCase](https://github.com/thecodegang/clean-architecture-example/blob/master/usecases/src/main/kotlin/com/github/aantoniadis/delivery/usecases/core/UseCase.kt)`和`[UseCaseExecutor](https://github.com/thecodegang/clean-architecture-example/blob/master/usecases/src/main/kotlin/com/github/aantoniadis/delivery/usecases/core/UseCase.kt)`。

`UseCase`是一个类似于`java.util.Function`的接口。它只是得到一个请求，并将其转换成一个响应。

`UseCaseExecutor`处理`UseCase`的执行。为此，它有一个接受以下参数的`invoke`方法:

1.  将要执行的`UseCase`
2.  一个`RequestDto`
3.  一个*映射函数*，它将`RequestDto`转换成一个`Request`对象(用例的输入)
4.  一个*映射函数*，它将`UseCase`执行的`Response`对象(用例的输出)转换为`ResponseDto`

还有三个重载版本的`invoke`方法，它们省略了`UseCaseExecutor`的输入和/或输出。

目前，`UseCaseExecutor`实现(`[UseCaseExecutorImp](https://github.com/thecodegang/clean-architecture-example/blob/master/usecases/src/main/kotlin/com/github/aantoniadis/delivery/usecases/core/UseCase.kt)`)正在使用`java.util.concurrent.CompletableFuture`和`java.util.concurrent.CompletionStage`进行执行抽象。这些抽象很方便，因为它们可以执行异步执行，并且与大多数框架都有现成的兼容性。

# `dataproviders`模块

该模块包含`usecases`模块中定义的网关的实现。该模块依赖于便于数据访问的框架。在我们的例子中，我们使用 JPA 和 Spring 数据。`Jpa*Repository`类是在`usecases`模块中定义的网关的实际实现。

这些存储库利用了 Spring 数据`JpaRepository`。这里有一个`[JpaProductRepository.kt](https://github.com/thecodegang/clean-architecture-example/blob/master/dataproviders/src/main/kotlin/com/github/aantoniadis/dataproviders/db/jpa/repositories/JpaProductRepository.kt)`的例子:

`DBProductRepository`是 a Spring 数据`JpaRepository`的子类。

这个模块中的实体是 JPA 实体，所以需要映射器函数在这些实体和域实体之间进行转换。在前面的代码片段中，我们演示了如何在`JpaProductRepository`中使用这些映射器函数。一个实体的例子是`[ProductEntity.kt](https://github.com/thecodegang/clean-architecture-example/blob/master/dataproviders/src/main/kotlin/com/github/aantoniadis/dataproviders/db/jpa/entities/ProductEntity.kt)`:

# `delivery`模块

该模块包含我们使用的交付机制的所有细节，以及应用程序的连接和配置。在我们的例子中，我们使用 Spring Boot 构建的 rest 服务。类似地，对于`dataproviders`模块的 JPA 实体，d to 有映射器来转换域实体。

rest 控制器获取`RequestDto`，并通过`UseCaseExecutor`将其转发给相关用例。用例的响应(是一个`ResponseDto`)是实现端点的控制器方法的响应。这种用法的一个例子是`[ProductResourceImp.kt](https://github.com/thecodegang/clean-architecture-example/blob/master/delivery/src/main/kotlin/com/github/aantoniadis/delivery/rest/imp/ProductResourceImp.kt)`。

异常由[global exception handler . kt](https://github.com/thecodegang/clean-architecture-example/blob/master/delivery/src/main/kotlin/com/github/aantoniadis/delivery/rest/imp/GlobalExceptionHandler.kt)处理，并转换为`[ErrorDto](https://github.com/thecodegang/clean-architecture-example/blob/master/delivery/src/main/kotlin/com/github/aantoniadis/delivery/rest/api/ErrorDto.kt)`。

# 干净的架构将低层细节与高层策略分开

在这个[拉取请求](https://github.com/thecodegang/clean-architecture-example/pull/1)中，我们展示了在不触及业务逻辑(`core`和`usecases`模块)的情况下更改数据层是多么容易。每层之间清晰的边界提供了灵活性。此外，由于外层依赖于内层的依赖规则，`core`和`usecases`模块不知道我们在外层模块中所做的任何改变。因此，我们甚至不需要重新编译这些模块来部署带有新数据层的应用程序。

此外，由于`core`和`usecases`模块只包含业务对象和它们交互的方式，对于参与项目的人来说，理解应用程序的领域要简单得多。有人可能会说，对于一个新的团队成员来说，在没有充分熟悉所使用的框架和/或库的情况下就开始编写新的功能是可能的(当然也有一些例外)。我们应用程序的核心不包含任何可能使学习曲线变得更陡的*魔法*框架。

# 干净的架构是可测试的

由于上一节讨论的分离，测试应用程序的业务逻辑很简单，不需要设置任何框架或库。我们可以编写简单的单元测试来测试应用程序中复杂的业务逻辑，而不必处理框架。同样，当我们在一个框架中改变一些东西时，我们不必改变这些测试。当然，我们应该有集成测试来确保一切都正确连接，但是这些昂贵的测试将只是少数，并且不会随着我们业务逻辑复杂性的增加而增加。

# 干净的架构不是银弹

很容易注意到，我们为应用程序提供的简单功能写了太多东西，即使是用像 Kotlin 这样简洁的语言。如果我们构建的应用程序相对简单，干净的架构需要一些可能被认为是过度工程化的抽象。开始时间接调用数据和交付层可能感觉不自然，但是当我们向系统添加新功能时，我们的速度会增加，因为我们将纯领域代码与框架和配置分开。

干净的架构，像所有的软件架构一样，都是有成本的，所以由开发人员/架构师来决定他们是否能从中受益。

# 进一步阅读

1.  [“干净的架构:软件结构和设计的工匠指南(罗伯特·马丁系列)](https://www.amazon.com/Clean-Architecture-Craftsmans-Software-Structure/dp/0134494164)作者*罗伯特·马丁(鲍勃叔叔)*
2.  [洁净的建筑](https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html)
3.  [使用 Golang 清洁建筑](/@eminetto/clean-architecture-using-golang-b63587aa5e3f)