# 使用 Node.js，socket 对每个用户强制执行单个 web 套接字连接。IO 和 Redis

> 原文：<https://medium.com/hackernoon/enforcing-a-single-web-socket-connection-per-user-with-node-js-socket-io-and-redis-65f9eb57f66a>

![](img/d7e963cabb61693927577fbd166a7ca1.png)

“A black-and-white shot of a person working with a MacBook on their lap” by [Sergey Zolkin](https://unsplash.com/@szolkin?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

最近，我一直在做一个实时多人浏览器游戏，遇到了“单会话”问题。本质上，我想防止用户通过 web 套接字多次连接。这一点很重要，因为多次登录同一个帐户可能会造成不公平的情况，并使服务器逻辑更加复杂。因为 web 套接字连接是长期存在的，所以我需要找到一种方法来防止这种情况。

## 愿望单

*   一个用户只能连接一次，不管他们打开了多少个浏览器标签。可以通过用户的认证令牌来识别用户。
*   该系统必须在集群环境中工作。单个服务器节点应该能够在不影响系统其余部分的情况下关闭。
*   授权令牌不应通过查询参数传递，而应在连接建立后通过专用的身份验证事件传递。

对于这个项目，我们将使用 Node.js，Socket。木卫一和雷迪斯。

# 卑微的出身

让我们建立我们的项目，开始行动吧。你可以点击查看完整的 GitHub [回购。首先，我们将设置我们的套接字。IO 服务器接受来自前端的连接。](https://github.com/mariotacke/blog-single-user-websocket)

A Socket.IO server in its simplest form.

默认情况下，服务器将侦听端口 9000，并将每个客户端的连接状态回显到控制台。插座。IO 提供了一个内置的机制来生成一个惟一的套接字 id，我们将使用它来标识我们的客户机的套接字连接。

接下来，我们创建一个示例页面来连接到我们的服务器。该页面包括一个状态显示、一个用于输入我们的秘密令牌的输入框(我们将使用它进行身份验证)以及连接和断开按钮。

Sample front-end mark-up with inputs and buttons to connect and disconnect.

此外，我们需要设置一些非常基本的逻辑来执行连接/断开，并连接我们的状态和令牌输入。

Our basic front-end logic… for now.

这是设置基本 web 套接字客户机和服务器所需的一切。此时，我们可以连接、断开连接，并向用户记录连接状态。所有这些都是用普通的 JavaScript 编写的！🍻接下来:认证用户。

# 证明

让用户在不知道自己是谁的情况下连接，对我们来说用处不大。让我们将基本令牌身份验证添加到连接中。我们假设连接在部署后使用 SSL/TLS。切勿使用未加密的连接。永远不会。😶

此时，我们有几个选择:a)在用户连接时将用户的令牌附加到查询字符串，或者 b)让任何用户连接，并要求他们在连接后发送身份验证消息。Web Socket 协议规范( [RFC 6455](https://tools.ietf.org/html/rfc6455#section-10.5) )并没有规定一种特定的身份验证方式，也不允许自定义头，而且由于服务器可能会记录查询参数，所以我选择了这个例子中的选项 b)。

我们将通过 Socket 的认证模块 [RFC 6455](https://medium.com/u/f89b781338d4#section-10.5)

*   Redis [设置](https://redis.io/commands/set)和[删除](https://redis.io/commands/del)命令