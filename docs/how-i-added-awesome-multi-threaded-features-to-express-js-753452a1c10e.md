# 我是如何添加令人敬畏的多线程特性来表达 JS 的

> 原文：<https://medium.com/hackernoon/how-i-added-awesome-multi-threaded-features-to-express-js-753452a1c10e>

![](img/3a8cce1f525cb51c8d6c349547141959.png)

Thread, yarn, and npm (not to scale): Photo by [Kelly Sikkema](https://unsplash.com/photos/8A7qKkvm_ew?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/thread?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# TL；速度三角形定位法(dead reckoning)

我写了`[express-http-context](https://www.npmjs.com/package/express-http-context)`,这是一个非常简单的 npm 包，它提供了对请求范围的上下文的访问，可以在代码库中的任何地方使用。它有助于使令人敬畏的事情变得简单，比如将关联 id 添加到日志中。

# 事件循环的快速复习

请允许我简单回顾一下 Node 的事件循环和“真正的”多线程语言之间的区别。

事件循环是 Node 及其底层 V8 引擎最酷的特性之一(我猜你可以说它*是*节点……但我跑题了)。它允许 Node 在单线程上下文中运行应用程序，还可以处理多个并发的异步操作，而不会阻塞应用程序流。漂亮。

当异步操作开始时(例如调用 API 或写入数据库)，Node 保持回调，然后继续运行当前的调用堆栈。当栈被清空时，当前的“帧”完成，节点或者开始另一个帧，或者如果没有其他事情要做就退出。(我很快就会回到这个话题。)

当异步操作完成时，节点将结果放在内部消息队列中。当帧结束时，节点检查队列以查看是否有任何已完成的操作。如果有，则使用下一个消息开始新的帧；回调中的代码成为下一个调用堆栈，执行继续。

通过这种方式，Node 可以轻松地完成所有的同步工作，直到完成为止。然后，它检查在它忙碌的时候是否有新的工作进来。如果什么都没有，应用程序就退出。

在真正的多线程环境中，每个线程都有自己的调用堆栈，并且在异步调用期间线程会被挂起。当异步调用完成时，线程从它自己的调用堆栈中停止的地方继续。当每个线程到达各自调用堆栈的末尾时，应用程序退出。

单线程事件循环和真正的并发多线程环境的区别之一是线程本地存储的概念。这种存储方式使 CPU 能够在异步事件发生时跟踪与线程相关的数据。还是那句话，超高级和手波浪，但大体概念在这里对我们有用。

# 线程和 API

![](img/b8e1a140f276501f5f671ca6be80ee11.png)

Building a multi-threaded application: Photo by [sarathy selvamani](https://unsplash.com/photos/VO-7ZroQtJA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/thread?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

如果你要用。Net 并在微软 Windows 上的 IIS 中运行它(并活下来讲故事😃)，对服务器的每个请求将由不同的线程处理。对于进行异步调用(例如对数据库或其他 API)的 API，每个线程将能够维护自己的上下文，并在这些异步调用完成后从它停止的地方继续。

。Net 使用`HttpContext`类来公开当前线程，而当前线程代表请求的状态。你可以读和写 cookies 和 headers，查看已经发生的错误，以及仅仅通过查看这个上下文的许多其他事情。这意味着无论您在代码中的什么位置，您都可以访问当前请求范围内的数据。

ExpressJS(我目前选择的 Javascript API 框架)在处理多个并发请求方面做得非常好，但是在提供对请求和响应范围的数据的访问方面做得不好。这是因为单线程事件驱动的执行模型不能很好地维护来自队列的每个消息的上下文。因此，如果您想要访问与请求相关的数据，比如 auth header 或 correlation ID，您必须在整个代码库中传递请求或至少来自请求的数据。

对于较小的 API，这不是问题。很多时候，所有的代码都存在于少数几个文件中。但是随着代码规模的增长和健康的代码分离变得至关重要，Express 中缺少请求范围的存储变得显而易见。

# 如何在 Express 中实现这一点

Express-http-context 通过使用相对模糊的`[async_hooks](https://nodejs.org/api/async_hooks.html)`模块添加了非常基本的`HttpContext`风格的功能来表达。根据文档，“`async_hooks`模块提供了一个 API 来注册回调，跟踪 Node.js 应用程序中创建的异步资源的生命周期。”

像大多数开发人员一样，我不想为我构建的每个 web API“注册跟踪异步资源生命周期的回调”,所以我将所有这些作为一个快速中间件放在了一起`express-http-context`。

以下是展示如何使用 http 上下文设置项目的最基本的示例:

第 6 行运行一个中间件，为每个请求创建一个新的上下文。在请求过程中添加到该上下文中的任何值对于同一个请求来说只能被*访问*。让我们添加另一个中间件，它为每个请求生成一个惟一的请求 ID，然后将其添加到上下文中:

正如您所看到的，这个中间件创建了一个新的 ID，然后它被添加到上下文中，并作为响应的头。现在我们可以在项目的任何地方获得当前的请求 ID，即使我们不能简单地通过运行`const reqId = httpContext.get('requestId');`来访问原始的`res`。

# 给我看些酷的东西！

我实际上是专门为了管理[相关 id](https://blog.rapid7.com/2016/12/23/the-value-of-correlation-ids/)来记录日志而写的这个包。回顾一下，关联 ID 是一个在分布式系统的许多部分中传递的值，以便可以关联不连贯的调用。

以下是我通常用于此目的的中间件:

如您所见，我们实际上创建了两个 id。首先，我们通过查看请求头来检查是否已经向我们传递了一个现有的相关 ID。如果我们找不到，我们就创造一个，然后继续前进。接下来，我们创建一个请求 ID。这两个 id 都被添加到上下文中，然后作为消息头添加到响应中。

我喜欢创建不同的关联和请求 ID，以确保我们可以始终维护一个有保证的唯一请求 ID。有些情况下，消费者会用同一个关联 ID 多次调用您的 API。这通常发生在更基础的服务中。如果消费者需要在一次操作中多次调用您的 API，他们可能会使用相同的关联 ID。因此，相关 ID 表示分布在多个应用程序上的单个唯一操作，而请求 ID 表示对单个应用程序的单个唯一调用。

通过在日志中包含相关性和请求 id，它极大地减少了跟踪整个生态系统中的任何错误或性能问题所需的工作量。

最后，还要确保将关联 ID 传递给您可能使用的任何 API。我通常为`request`包创建一个助手来自动添加`X-Correlation-ID`头。也许有更好的方法，但这是我的解决方案:

注意，这个助手模块可以完全从快速请求处理程序中分离出来，但是它仍然可以访问当前上下文，从而访问相关 id。

总之，`express-http-context`增加了请求范围的上下文特性来表达 API，类似于你在更重的多线程平台中发现的。

# 喜欢你看到的吗？

希望你觉得这个项目有趣！如果是这样，以下是你可以提供帮助的方式:

1.  怪异的行为？令人困惑的自述？Github 问题超级有帮助！😃
2.  给它一颗星:[https://github.com/skonves/express-http-context](https://github.com/skonves/express-http-context)⭐
3.  给这个帖子几个👏