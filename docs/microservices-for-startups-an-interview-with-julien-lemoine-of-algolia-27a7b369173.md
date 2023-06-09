# 创业公司的微服务:阿尔戈利亚的朱利安·莱莫因访谈

> 原文：<https://medium.com/hackernoon/microservices-for-startups-an-interview-with-julien-lemoine-of-algolia-27a7b369173>

*这次采访是为我们的* [*创业微服务*](https://buttercms.com/books/microservices-for-startups/) *电子书做的。一定要去看看关于微服务的实用建议。感谢 Julien 的时间和投入！*

![](img/9d0bb61b030ce6024518a962f97c8f84.png)

[*朱利安·莱莫因*](https://twitter.com/jlemoine_algo?lang=en) *是* [*阿尔戈利亚*](https://www.algolia.com/) *的联合创始人兼 CTO。Algolia 为产品团队提供资源&工具，他们需要使用强大的托管搜索引擎 API 来创建快速、相关的搜索。*

对于上下文，您的工程团队有多大？您是否正在使用微服务，能否概述一下您是如何使用它们的？

我们有一个 49 人的工程团队。

我们试图将我们的服务尽可能分成不同的部分，并为此使用不同的技术。例如，为了提高性能，我们的核心搜索 API 是用 C++编写的，但只关注核心搜索任务。我们有许多围绕搜索 API 的不同服务来使服务工作。一些例子包括:

*   将日志从搜索引擎推送到我们的分析基础设施(在 Go 中开发)
*   检查服务状态的探测器(用 Java 开发)
*   监控收集探针状态的基础设施(在 Scala 中开发)
*   监控仪表板(在 Rails 中开发)
*   日志处理—分析(在 Go 中开发)
*   点击跟踪服务(在 Go 中开发)
*   产品仪表板(在 Rails 中开发)

**你是从一个整体开始后来采用微服务还是直接从微服务开始？为什么？**

我们从微服务方法开始。主要目标是能够使用不同的技术来构建我们的服务，原因有两个:

1)我们希望为每项服务使用最好的工具。我们的搜索 API 在最底层进行了高度优化，C++是实现这一点的完美语言。也就是说，使用 C++做任何事情都是对生产力的浪费，尤其是构建仪表板！

2)我们需要最优秀的人才，只使用一种技术会限制我们的选择。这就是为什么我们公司有不同的语言。当您希望在毫秒级别优化一切时，Go 不如 C++完美，但当性能仍然是关键时，它是完美的语言(例如，在日志处理中，我们每天处理几 TB 的日志——使用 Ruby 或 Python 会浪费 CPU)。

作为一个团队/工程组织，你是如何处理微服务这个话题的？

> *微服务不应以对最终用户的性能产生明显影响为代价。*

我们有使用微服务的文化。一些客户认为我们的搜索 API 本身就是一个微服务，即使产品实际上是由几十个微服务组成的。我们并不真正关心服务的规模，也不关心它是否是“微”的。我们认为研究将我们开发的服务分成几个部分的不同方法是很重要的。如果有方法进行拆分，并且性能影响可以接受，那么我们就进行拆分。否则，由于性能原因，我们不接受拆分。

换句话说，微服务不应该以对最终用户的性能产生明显影响为代价。我们非常关注性能，从用户的角度来看，我们从不考虑会导致性能下降的事情。

工程团队在过去几年中成长了很多，我们很自然地围绕我们拥有的不同服务来组织团队。

**你有没有把一个单一的应用程序分解成更小的微服务？如果是的话，你能给我们介绍一下这个过程吗？**

我们没有将一个单一的应用程序分解成更小的微服务，但是我们已经替换了很多微服务。一个很好的例子是我们的日志处理在四年内被替换了两次。

V1 是在 Rails 中为 2013 年 9 月我们的产品发布而快速开发的，它只有几百行代码。我们知道它不会扩展，但对于最初几个月的服务来说已经足够了。

我们在 2014 年 4 月推出了带有多线程 Java 版本的 V2，以便与服务一起扩展。

2017 年，我们引入了使用 Google Cloud Pub/Sub 在 Go 中开发的 V3，主要是出于扩展的原因，但也是为了准备这项服务的未来发展，我们将有新的微服务连接到它，这是以前不存在的。

**您是如何确定服务边界的？您从规模估算服务中学到了什么？**

我们通过定义服务的输入和输出来定义服务的边界。

有时服务是一个网络 API，但它也可以是一个消耗文件和在数据库中产生记录的过程。这就是我们的日志处理服务的情况。

在实践中，关于边界的讨论是通过一个共享给所有工程师的规范文档来完成的，以收集反馈并确保我们有最好的设计。我们有时会讨论过度工程，以确保我们不会为发生概率较低的事情引入复杂性。保持务实而不是发明问题是工程团队的关键。

**微服务对您的开发流程有何影响？您的运营和部署流程？**

就开发过程而言，主要的挑战是确保我们的团队中有足够的人了解每项服务。我们符合 SOC2 标准，并且有一个变更政策，要求每次变更至少有三个人:一名工程师编写 PR，一名工程师审核 PR，第三名工程师部署到生产中。

为了解决这个挑战，相对于团队的规模，控制堆栈中引入的新编程语言的数量是很重要的。

部署方面的第二个挑战是，拥有更多服务意味着运营变得更加复杂。

我们通过定义每项服务的 SLA 和重新定义我们的随叫随到战略解决了这一问题。我们最初只有一个随叫随到的团队，然后我们转移到三个不同的随叫随到的团队:一个团队负责我们的主要 API，一个团队负责我们的网站/仪表板，第三个团队负责所有其他服务。

**微服务对您的测试方式有何影响？**

每个团队都在编写他们自己的测试，虽然测试的水平相当好，但它从来都不是完美的。挑战是在所有服务中有一个统一的测试级别。我们尝试了一些方法来提高同质性。每个季度，我们都会组织一次与所有工程团队的异地会议，并在团队之间分享最佳实践，以复制运行良好的东西。测试和质量就是其中之一。

**微服务对安全性和控制数据访问有何影响？**

总的来说，它简化了我们的安全审计和测试，尤其是在合规性方面。与大型整体系统相比，每个服务都很容易分析。也就是说，挑战在于确保我们不会忘记任何服务。我们已经多次看到，拥有大量服务很容易导致在讨论数据访问时经常忘记一个服务。这个问题可以通过在团队中共享知识和记录生产中的不同服务来解决。

*再次感谢 Julien 的时间和投入！这次采访是为我们的* [*创业微服务*](https://buttercms.com/books/microservices-for-startups/) *电子书做的。一定要去看看关于微服务的实用建议。*

*此采访原载于*[*buttercms.com*](https://buttercms.com/blog/microservices-for-startups-an-interview-with-julien-lemoine-of-algolia)*。这是《创业公司的微服务》一书* [*采访系列的一部分。新章节一发布，你就可以通过电子邮件收到。*](https://buttercms.com/books/microservices-for-startups/)

如果你喜欢这篇文章，请在下面鼓掌帮助它传播！更多类似内容，请关注我们的 [*推特*](https://twitter.com/ButterCMS) *和* [*订阅我们的博客*](https://buttercms.com/blog/) *。*

如果你想在你的网站上添加一个博客或者内容管理系统，而不是用 Wordpress， [*你应该试试黄油内容管理系统。*](https://buttercms.com/)