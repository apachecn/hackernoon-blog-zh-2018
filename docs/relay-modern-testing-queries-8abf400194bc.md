# 中继现代:测试查询。

> 原文：<https://medium.com/hackernoon/relay-modern-testing-queries-8abf400194bc>

![](img/bf5089a68ea5a15d1fd87b8371c3c046.png)

在以前的故事中，我分享了我对如何测试 [react](https://hackernoon.com/tagged/react) 组件和 graphql 的想法，现在轮到 Relay 了。 [Relay](https://hackernoon.com/tagged/relay) 是来自脸书的一位伟大的数据管理者。你可以在我以前的故事中找到一些相关信息。我们开始吧。

**角落案例**

使用 relay 的主要问题是，您使用的任何查询都可能随时间而改变，除非您尝试编译 Relay，否则您不会发现这一点。如果您不编译并尝试运行一个获取字段的查询，而该字段在服务器上已不存在，您将会得到一个错误消息:“无法查询类型为“用户”的字段“名字”。

我敢打赌，您不想这样，我也不想。这就是为什么我决定测试中继查询。中继查询看起来与 graphql 查询完全一样。

**解决方案**

我们需要针对 graphql 模式执行一个查询，以了解该查询是否一切正常。检查的唯一方法是使用 relay-test-utils 库中的 parseGraphQLText。我们需要用一个模式和一个查询文本来调用这个函数。如果抛出一个错误，这意味着查询有一些问题，否则这个函数只是保持沉默。

仅此而已。非常简单明了！如果你喜欢这个故事喜欢并分享出来:)件。