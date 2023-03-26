# 为什么你的创业需要无服务器 Slack 机器人

> 原文：<https://medium.com/hackernoon/why-your-startup-needs-serverless-slack-bots-dd02f1d9d21b>

在这篇文章中，我将解释我们如何在 Epsagon 中利用无服务器 Slack 机器人，展示它们的价值，并提供一个示例——包括相应的开源代码，使用 Python 和无服务器框架来实现您的定制无服务器 Slack 机器人。

Slack 无疑是初创公司最著名的消息软件。初创公司对速度和敏捷性有独特的需求，而 Slack 正是这种需求的完美体现。没有开销—只有纯粹的效率。

Slack 中我最喜欢的功能之一是它的大量应用程序和集成，以及开发定制应用程序的简单方法，这些应用程序通常被称为“ [Slack 机器人](https://hackernoon.com/tagged/slack-bots)这些应用背后的要点很简单——你希望所有的信息都在一个位置，如果 Slack 是沟通和知识共享的主要方式，为什么不把其他来源的信息也放入其中呢？

你可以找到很多推荐应用的列表，比如[这个](https://blog.producthunt.com/20-slack-apps-you-ll-love-a295ef2c0f9c)。我强烈建议寻找符合您需求的现有应用程序并添加它们。内置应用程序很棒，但如果你需要一个特定的、量身定制的、完全符合你要求的应用程序呢？幸运的是，Slack 的人想到了这一点，并创造了一种开发定制应用和集成的简单方法。Slack 有很好的文档，你可以在这里找到，它将指导你一步一步地构建你的应用。

![](img/95cb9311d90a176e422850a08e2fa9a6.png)

这只是我们在 Epsagon 使用的一些机器人。通常，每个 Slack bot 都向一个专用的 Slack 通道报告。

1.  **GitHub** (内置):跟踪代码和问题变化的最简单方法。
2.  **TravisCI** (内置):这个持续集成工具有一个平滑的松弛集成。
3.  **提一下**(内置):一个追踪互联网周围事物的有用工具，里面有一个现成的 Slack bot。
4.  **RSS 提要**(内置):AWS 提要、[谷歌](https://hackernoon.com/tagged/google)提醒等等。这些可以使用“/feed …”语句轻松添加。
5.  **定制 RSS 提要**(内部):我们实现了自己的 Google Alerts Slack bot，因为我们想改进内置集成的格式。
6.  **栈溢出**(内部):观看并评论栈溢出问题。
7.  Reddit (内部):我们监控互联网首页与我们相关的讨论的方式。
8.  Meetup(内部):关于我们可能感兴趣的世界各地的新聚会的通知。
9.  **生日机器人**(内置):因为好玩！

如你所见，我们喜欢 Slack 机器人——包括内置的和定制的。开发定制 Slack bot 最简单的方法是什么？

![](img/9b68dc4e63e6b13971b76109570262dd.png)

要运行 Slack bot，需要运行代码(很奇怪吧？).这段代码通过 Slack API 发送事件，然后将它们发布到相应的 Slack 通道。机器人运行的触发条件是什么？嗯，它可以由用户操作触发(就像交互式 Slack 机器人一样)，但通常情况下，它是基于时间的。每 1 分钟、每 1 小时等等。

这段代码还需要某种计算单元。它可以在服务器上运行，也可以作为无服务器功能运行。

无服务器完全符合 Slack 机器人的用例。使用 AWS Lambda 等无服务器计算服务，您可以立即部署 Slack 机器人，再也不用担心它们了！

我将展示我们在 Epsagon 中实现的 Reddit Slack 机器人。它跟踪我们跟踪的子编辑中的提交。

![](img/d663db072d4108413323d5446934e913.png)

这是一个开源项目，可以在我们的 [GitHub](https://github.com/epsagon/reddit-slackbot) 中找到。我不打算介绍配置 Slack 的整个过程——您可以在网上找到优秀的文档。相反，我将把重点放在我们为使事情对我们更方便而采取的变通办法上。注意:虽然这是一个 Reddit 机器人，但一般思想对于任何预定的无服务器 Slack 机器人都是一样的。Reddit 机器人可以用作其他机器人的模板。

**松紧网钩**:使用[这个](https://api.slack.com/incoming-webhooks)导轨可以很容易地制作出松紧网钩。

**配置文件(config.json)** :该配置包含 Slack URL、Reddit 客户端参数以及要遵循的子编辑。子编辑是以 Reddit 的格式编写的。在这种情况下，我们将收到关于在子编辑“编程”和“Python”中包含关键字“lambda”的提交的通知。

![](img/f3bbf3e1c96fb00e7411c9565bd5be2c.png)

**Lambda 代码(main.py)** :该代码包含三个函数。

1.  handler—Lambda 处理程序，在本例中每 60 分钟调用一次。
2.  get_submissions —使用 Reddit API 检索新的提交。
3.  update_slack —格式化消息并将其发布到 slack 通道。

![](img/a7ca7a8652fc72e8f0810cdc5f6ab427.png)

请注意，处理程序函数包含函数配置的更新—具体来说，是 LAST_SUBMISSION 环境变量。它用于记住最后一次提交的时间，并且只发布新的到 Slack。**这是一种在功能中保存状态的方法，无需建立数据库**。我们待会再谈。

**serverless.yml** :我们是无服务器框架的大 fonds。使用框架部署新的 Lambda 函数非常简单。

![](img/afeedec3acc074fda3ce22fda381219b.png)

该 serverless.yml 文件包含该函数的特殊权限—编辑其配置(lambda:UpdateFunctionConfiguration)。需要此权限，因为处理函数正在更新环境变量 LAST_SUBMISSION。可以使用 AWS 区域、帐户 ID(可以使用本指南找到)和函数名(取自文件顶部的服务字段)来计算资源字段。

这种方法确实很“蹩脚”，但是它让我们能够忘记设置额外的 AWS 资源，比如 DynamoDB。因为现在没有办法在 Lambda 函数中保存状态，所以这是一个简单的解决方案。虽然让一个 Lambda 函数改变它的配置(包括它的代码)是一个潜在的安全风险——这个函数只由一个预定的定时器触发，并且不公开一个外部 API，这将风险最小化。然而——为你所有的 Slack 机器人设置一个 DynamoDB 也是一个相当简单的解决方案。

如前所述，该项目在我们的[资源库](https://github.com/epsagon/reddit-slackbot)中可用。另外，**它可以作为任何基于时间的无服务器 Slack bot** 的模板。我们在 Epsagon 使用了相同的模板来创建 Google Alerts bot、Stack Overflow bot 等。

你对 Slack 机器人有什么体验？你对改进我们的 Slack bot 有什么想法吗？请与我们分享您的故事，并为该项目做出贡献！

【blog.epsagon.com】最初发表于[](http://blog.epsagon.com/why-your-startup-needs-serverless-slack-bots)**。**