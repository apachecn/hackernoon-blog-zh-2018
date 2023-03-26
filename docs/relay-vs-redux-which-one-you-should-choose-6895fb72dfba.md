# Relay vs Redux:你该选哪个？

> 原文：<https://medium.com/hackernoon/relay-vs-redux-which-one-you-should-choose-6895fb72dfba>

![](img/a4ffee2c5672242ecc9203c7ba99d7ab.png)

如果许多公司和开发人员开始谈论在 react 应用程序中管理数据，他们会问自己这个问题。通常，标准很简单:易于设置、易于使用、体面的文档、大型社区。如果我们开始用这些标准来考虑[接力](https://hackernoon.com/tagged/relay)和[接力](https://hackernoon.com/tagged/redux)哪一个会赢？我们来想办法吧！

**什么是接力？**

[Relay](https://facebook.github.io/relay/) 是来自脸书的数据管理员。它最初是为 react-native 应用程序构建的，但后来它也成为了 web 应用程序的工具。

中继的想法很简单:你的应用只需要获取你的用户需要的数据。这是描述需要什么数据的声明性方式。一个简单的中继查询如下所示:

我们会询问用户的名字和姓氏。服务器只会返回给我们这两个字段。不会了。

Relay 要求你的服务器端支持[查询语言](https://graphql.org/)请求。

Relay 有一些开箱即用的突出特性，比如[缓存请求](https://github.com/facebook/relay/blob/master/packages/relay-runtime/network/RelayQueryResponseCache.js)、[乐观更新](https://facebook.github.io/relay/docs/en/mutations.html#optimistic-updates)、[智能数据更新](https://facebook.github.io/relay/docs/en/mutations.html#updater-configs)(移除、添加、更新数据)。

**Redux 是什么？**

我猜想每个读到这个故事的人都知道或者至少听说过 [redux](https://redux.js.org/) 。它是 javascript 应用程序的状态容器。它给你一个机会把你的状态保持在一个地方。redux 的想法是拥有一个真实的位置。任何数据操作都应该使用 redux 来完成，无论是调度一个动作来更新数据还是获取数据。

Redux 本身相当简单，只有几个内部的东西([存储](https://redux.js.org/basics/store)、[调度/动作](https://redux.js.org/basics/actions)、[减速器](https://redux.js.org/basics/reducers))，其他的都是你的想象。它是一个非常可靠和稳定的图书馆，在世界各地都有一个庞大的社区。

Redux 没有 Relay 拥有的任何功能。无论你想从 Redux 得到什么，你都必须自己构建(或者在 GitHub 中找到)。您决定如何提取、提取什么数据、如何存储以及如何更新数据。Redux 对你的服务器端没有任何要求，它只要求你遵循简单的[规则](https://redux.js.org/introduction/three-principles)。

**什么时候使用继电器？**

*   显然，如果您的服务器端支持 graphql 查询，您需要使用 Relay。
*   Relay 接受所有需要从服务器端获取的查询，并且只向服务器端发送**一个**请求，这显著提高了应用程序的速度。为每个请求创建一个 XHR 是昂贵的，这就是为什么来自脸书的工程师们决定收集和合并所有的查询到一个 XHR。
*   Relay 是为处理大量数据而设计的，所以如果你的应用程序在客户端存储的数据量很大，你一定要试试 Relay。
*   如果你打算使用 React-Native，你可能也会发现 Relay 是合适的，因为 Relay 只要求它需要的数据，所以对于使用 3G 网络连接的用户来说，节省一些流量是至关重要的。
*   你将尽你所能提供最好的用户体验，所以你也可以考虑 relay，因为它有很酷的特性:乐观更新、缓存请求等等。
*   你有时间花在调查一些问题和事情上，因为接力的文档不是很理想。有时候你需要深入源代码。

**什么时候用 Redux？**

*   你的应用在数据使用方面很简单。你不需要更新商店里依赖于其他东西的很多东西。一个很好的例子是一个 todo 应用程序，你真的不需要有任何数据管理器，但 redux 就可以了。
*   你的应用正在使用网络套接字。Redux 与 web-sockets 一起工作要简单得多，你所需要做的就是在消息到来时分派一个动作。轻松点。
*   你的服务器端没有 graphql。
*   您没有时间设置数据管理器，您的截止日期是昨天。
*   你不需要给最好的 UX。没有乐观更新，没有缓存。

**你该选哪个？**

当然，这取决于你正在构建的应用程序，一方面我们有 Relay:

*   最好把 UX 从箱子里拿出来。
*   更好的数据获取。

另一方面，我们有 Redux:

*   易于安装和使用。
*   不需要关于服务器端的额外知识。

你选择哪一个完全取决于你自己。这种决定必须在乞求发展中做出，所以小心你的欲望。

**结论**

无论你使用 relay 或 redux 或任何其他工具，重要的是你如何使用它们。没有不好的工具或语言，只是一开始就做出了错误的决定。你可以和我一样使用 Relay 和 Redux。我使用 Relay 来存储业务数据，并为一些临时的东西如主题、地区、过滤器进行 Redux。

喜欢这个故事吗？分享&鼓掌吧。谢了。