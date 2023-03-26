# 我如何和为什么构建 Hamoni Sync——一个实时应用状态同步 SDK

> 原文：<https://medium.com/hackernoon/https-medium-com-pmbanugo-how-and-why-i-built-hamoni-sync-3951aba4bdf3>

![](img/c48413e8a336653e256bbd63878b7c65.png)

Photo by [Pankaj Patel](https://unsplash.com/photos/yEAOfWSdzgM?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/vs-code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

实时应用无处不在。从即时通讯应用到文档协作软件和游戏。您可能已经使用开源库构建了应用程序，如 [SignalR](https://www.asp.net/signalr) 、 [SocketIO](https://socket.io/) 或其他一些商业产品。我使用过 SignalR 和 SocketIO，最近在最近的 ProductHunt 黑客马拉松中用 SocketIO 构建了一个[聊天 SDK](https://www.producthunt.com/upcoming/hamoni) 。

# 我想解决的问题

过去，我和一些同事不得不围绕 SocketIO 和 SignalR 等实时引擎设计状态逻辑。大多数情况下，这是一个需要实时仪表板的项目。这些实现有相似的逻辑或架构，感觉像是从头开始重复设计。我想要一种方法:

1.  轻松将实时状态同步添加到任何应用程序。
2.  避免围绕插座室和事件从头开始设计，以及处理客户端断开连接和重新连接时发生的情况。
3.  一个我可以集成到移动和网络应用中的 SDK。
4.  处理基础设施问题的服务。

# 我建造的东西

所以我构建了 [Hamoni Sync](https://www.hamoni.tech/) ，一个作为服务的实时状态同步。

它提供了 SDK 来为 web 或移动设备添加实时状态同步。我当时只做了 web SDK。它将您的应用程序状态保存在云中，并可以作为您的应用程序的真实来源。当客户端重新连接时，它会与服务器重新同步。

您可以使用它来实时同步您的应用程序状态。您可以构建实时仪表盘，管理游戏排行榜或任何其他游戏状态，以及实时数据可视化或图表。

# 技术堆栈

在构建过程中，我使用了 ReactJS、NodeJS、RollUp、SocketIO、CouchDB、AWS Lambda、AWS API Gateway、Amazon Cognito、AWS Amplify、AWS ElasticBeanstalk、Gitbook、Git。

1.  **源码控制**:我用的是 Git 和 GitLab。我使用 GitLab 是因为免费计划允许我拥有私有库。
2.  **数据库**:我需要一个 NoSQL，并选择了 CouchDB，因为它有一个冲突解决策略，我想以后用它来处理这个项目的自动冲突解决。我使用 IBM Cloudant 的托管 CouchDB 实例，因为我不想自己管理数据库。
3.  后端:有几个开源的实时引擎，SocketIO 似乎更受欢迎。我选择 SocketIO 是因为我熟悉 SocketIO，它有 Swift 和 Java 的客户端库，我想用它们来构建这些语言的 SDK，这样我的解决方案就可以在移动设备上运行。我还使用 AWS Lambda 和 API gateway 来构建仪表板使用的 API。我假设我会对实时系统和 API 有不同的伸缩需求，所以我不想把它们放在同一个服务器上。为了节省成本，我将它们分开并使用了无服务器架构。
4.  **前端**:我在用 [MDB Bootstrap](https://mdbootstrap.com/react) 的 React with Material Design 组件。我想到了使用 Preact，但决定使用 react，因为 AWS Amplify 有一个 React 的集成包，我需要用它来管理 web 应用程序的认证逻辑。它也可以和 Preact 一起工作，但是我不想花时间去尝试看看它是否工作。
5.  **认证**:我用的是亚马逊 Cognito 和 AWS Amplify。Amazon Cognito 用于用户帐户管理，并作为一个库来处理 React 应用程序中的身份验证。使用这些服务轻而易举。
6.  **文档**:我正在用 SDK 构建一个服务，所以它需要有好的文档。我问过 WeAllJS slack 组的人，他们给了我一些选择。其中一个人继续做了一个[工具清单](https://github.com/jenniferlynparsons/awesome-writing)来考虑。我不得不在 [Gitbook](https://www.gitbook.com/) 、[斯芬克斯](http://www.sphinx-doc.org/en/stable/)和[石板](https://github.com/lord/slate)之间做出选择。Sphinx 要求我安装我不使用的工具，而且我当时也不想学习重构的文本格式。Gitbook 和 Slate 支持我所熟悉的 markdown。我使用 Gitbook 是因为我喜欢它的显示方式，并且允许我应用主题。我想在我的下一个项目中，我会尝试 slate，因为它易于使用和设置。
7.  **云提供商**:我使用 AWS 是因为我通过参加 ProductHunt 黑客马拉松获得了 1000 美元的积分。我还使用 IBM Cloudant 作为托管数据库提供商。
8.  托管:我从 get.tech 购买了域名，并使用 Netlify 托管网站、文档和仪表板。
9.  **SDK 打包**:我用 ES6 写了我的代码，用 Babel trans pile 到 ES5。我使用 RollUp 来捆绑它，以便在浏览器中使用。我认为汇总文档使入门变得容易，它最适合库作者打包他们的库。我不记得第一次尝试 Webpack 时有这么简单。

# 建造的过程

我在 3 月 1 日开始着手这个项目，目标是在 3 月 23 日完成所有的工作并发布。那总共是 20 天，因为我星期天不工作。

我从仪表板开始。用 create-react-app 和一个材质设计 react 库引导前端。添加了与 Amazon Cognito 和 AWS Amplify 的认证。然后，我创建了通过 API gateway 公开的 Lambda 函数，用于创建和查看应用程序。

完成后，我转移到 JavaScript SDK 和后端工作。我编写了第一个特性，它允许创建我称之为值原语的东西。它花了大约 8 个小时来建立，然后使它更容易使其余的目标功能。截至 3 月 10 日，我已经有了预期的功能。

随着主要功能的运行，接下来的事情是创建一个样例应用程序和文档来指导人们如何使用该产品。我花了几天时间完成文档的初稿。到 3 月 17 日，我已经完成了文档并制作了 2 个示例应用程序。编写代码似乎是容易的部分。编写文档比我计划的时间稍长。鉴于目前的进展，剩下的工作就是部署到生产环境中。我买了一个域名，并使用 Netlify 部署了网站和仪表盘。然后，我完成了部署到 AWS 和发布到 npm。

# 我面临的挑战

这段旅程中最困难的部分是通过文档和网站找到合适的词语来传达项目的意图。学习和使用我在这个项目中使用的 AWS 服务也很困难。AWS Cognito 和 Amplify 并不难用，但其他的很难用。也许文档对我来说很难理解。

# 主要学习内容

关键的学习是知道如何使用各种 AWS 服务。我还必须学习使用 RollUp 来打包 JavaScript 模块，以及如何部署到 npm。如果我有什么不同的做法，那就是使用不同的 React 组件进行材质设计，可能还会使用 Slate 进行文档记录。

# 最后的想法和下一步

旅途很有趣，我学到了很多。我期待着看到这个项目如何起飞。我将继续努力，因为我已经为它计划了其他功能，例如，允许用户看到一个应用程序的当前连接数。我希望得到反馈，看看人们如何使用它，以及他们希望看到增加或删除哪些功能。

尝试一下，给你的应用一个实时提升！随时联系，让我知道你的想法。

查看[快速入门指南](https://docs.hamoni.tech/quick-start/javascript-quickstart.html)查看工作示例

# 链接

*   [网站](https://www.hamoni.tech/)🚀
*   [文档](https://docs.hamoni.tech/)
*   [快速入门指南](https://docs.hamoni.tech/quick-start/javascript-quickstart.html)