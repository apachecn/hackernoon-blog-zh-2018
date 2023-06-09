# 如今，在无服务器和开放源代码的环境下

> 原文：<https://medium.com/hackernoon/serverless-and-open-source-where-do-we-stand-today-dff8aec67026>

![](img/0219ed145fc821fce47feb236786b758.png)

看看科技领域的新进展总是很棒的。最近，Cloud Foundry Foundation，一个与 Linux Foundation 和一个关注开源平台的非营利组织[合作的项目，进行了一项全球调查](https://www.cloudfoundry.org/multi-platform-trend-report-2018/)，调查显示 46%的 it 决策者已经在使用[无服务器](https://hackernoon.com/tagged/serverless)。

由此看来，无服务器正迅速成为云生态系统中最热门的趋势。但是开源的无服务器社区做得怎么样呢？它站在哪里？开源无服务器的前景如何？

让我们探索并框定其中一些问题的答案。

# 无服务器的简史

无服务器计算是一种范式，是云计算世界的下一个抽象层。在部署代码时，您不必担心基础设施的供应。除此之外，由于您的代码不是连续运行和按需运行的，您只需为代码运行的时间付费。

在当今激烈的竞争中，更短的上市时间成为关键的成功因素，[无服务器被证明是](http://simform.com/serverless-architecture-guide/)的一大优势。更重要的是，它正在改变 DevOps 团队的工作方式。它增加了技术堆栈和资源方面的自主权。

亚马逊网络服务是 2014 年第一个推出 AWS Lambda(功能即服务)的服务。从那时起，FaaS 供应商的名单越来越多。截至目前，主要的无服务器提供商是 AWS Lambda、GCP 函数、微软 Azure 和 IBM OpenWhisk。

# 无服务器和开源

如果我们谈论传统的无服务器产品，您必须使用信用卡注册，并从 web 浏览器编辑代码或上传 zip 文件来运行您的功能。与企业级 CI/CD 管道相比，许多开发人员觉得这种工作方式不太合适。有更好的方法来做事情，这就是引发开源无服务器平台和框架的动力。

Azure 的 CNCF 董事会成员兼产品负责人 Gabe Monroy 提出了对云锁定的担忧，并指出开源抽象是前进的方向。

此外，尽管这些商业 FaaS 提供商取得了实至名归的成功，但他们并没有满足每个人的需求，这为更多的定制产品腾出了空间。在选择无服务器供应商时，价格确实是一个考虑因素，但肯定不是最大的因素。开发人员的目标是为他们提供高速部署应用程序的平台。

在我们讨论开源无服务器生态系统之前，我们需要了解它们出现的原因。慢慢地，逐渐地，企业开始尝试开源的无服务器技术，并引用其相对于供应商的优势。

广义来说，开源无服务器可以分为两类:平台和框架。我们将在下一节讨论这个问题。

# 我们的立场是什么？

让我们分析一下我们在无服务器和开源社区中到底处于什么位置，以及专家对此有什么看法。

# 1.OpenFaaS

OpenFaaS 由 Alex Ellis 创建，旨在以尽可能少的限制运行函数。它在 2017 年的 DockerCon 上赢得了 Mobi 的 Cool Hack 比赛，势头之球仍在滚动。目前，它在 GitHub 上有超过 10，000 次启动。

OpenFaaS 的一些特殊特性是，您可以用任何语言编写它。您还可以移除云开销，并且可以不受任何运行时限制地运行您的函数。该项目获得了麻省理工学院的许可，这使得它成为企业和初创公司的低门槛进入点。

# 2.努克利奥

[Nuclio](https://github.com/nuclio/nuclio) 是一个开源的无服务器框架，由来自以色列的 [Iguazio Systems](https://www.iguazio.com/) 提供支持。它被认为是 AWS Lambda 的替代方案。

由于无服务器功能是单线程的，它们在资源访问、内存分配/释放和编码方面受到的影响最大。在许多情况下，每次调用都要重新启动连接，这最终会降低性能。Nuclio 不仅解决了这些问题，还简化了公共事件结构，将协议从事件头和上下文体中分离出来。

# 3.Effe

这是一个非常简单的构建块，用于构建使用单一 Lambda 函数的无服务器架构。要创建函数，你必须使用 [effe-tool](https://github.com/siscia/effe-tool) 。这个工具会自动创建一个新函数。你可以把自己的逻辑写进去，然后编译。

Effe 的想法是为每个函数运行多个 docker 容器。然而，这些函数不知道响应哪个触发器/URL，这需要您路由流量。这使得它本来就很慢，并进一步增加了可伸缩性问题。

# 4.关键功能服务

Pivotal Function Service 是一个基于 riff 项目的开源功能即服务平台，可以处理事件集成、web 事件和大规模集成数据。目前，它通过早期访问入口提供给选定的用户。它将于 2018 年底左右公开发布。

它通过一个语言中立的函数和一个带有可插入事件代理的容器接口来连接各种消息主题。它也可以在 Kubernetes 上本地运行，这样就很容易调用容器来响应事件。

# 5.Fn 项目- Oracle

由 Oracle 开发，这是一个开源的无服务器平台，是一个容器原生的，可以在任何地方运行。Fn 项目背后的主要目标是加速完全无服务器编程模型的采用，并为多云计算铺平道路。

这包括

*   fn Server——一个在多云环境中构建和扩展您的功能的平台。
*   Fn 负载均衡器——一个让开发者在 Fn 集群上智能地部署和平衡负载的平台。
*   这些是开发工具包，用于在多种语言中引导函数，并为函数输入提供数据绑定模型。
*   Fn 流程——这让开发人员可以根据自己选择的编程语言，编排所有功能的更高级别的工作流程。

# 6.一无所有

Kubernetes 是一个为无服务器应用构建的本地框架。它利用 Kubernetes 的底层资源，让您在其上开发 FaaS 应用程序。它是一个开源解决方案，提供了 AWS Lambda、Azure 函数和 GCP 函数的功能。

它使用 Kafka 消息系统和 HTTP 作为事件触发器。它使用定制资源定义，这样您就可以为 Kubernetes 上的函数创建定制资源，并最终控制相同的函数运行时。它也兼容无服务器框架。

# 7.Apache OpenWhisk

希望获得商业成功的开源项目的主要问题是找到贡献者和合作者。Adobe 和 Red Hat 决定采用 [OpenWhisk](https://github.com/apache/incubator-openwhisk) 加速了这一势头。

Red Hat 中间件产品管理高级主管 Rich Sharples 认为 OpenWhisk 拥有最先进的功能集和最大的合作者社区。这通过设计提供了异步功能、可伸缩性和弹性，这是大多数企业级项目的需求。

# 8.分裂

裂变是一个建立在 Kubernetes 上的 FaaS 平台。使用裂变，您可以轻松地在 Kubernetes 上从 function 创建 HTTP 服务，因为它在源代码级别工作，并且消除了容器映像的抽象。通过裂变，函数在空闲时消耗存储资源，在运行时消耗 CPU &内存。

由于 Kubernetes 提供了一个灵活而强大的编排系统，具有严格的 API 后端，在其上构建裂变意味着将容器编排机制留给 Kubernetes，并只关注 FaaS 特性。

# 9.Iron.io

[Iron functions](https://github.com/iron-io/functions) 用任何语言编写，可以运行在任何选择的平台上，包括混合云。这为您提供了直接从 Lambda 导入函数并运行它们的功能。

它采用单一系统进行管理和监控。您不需要独立扩展每个应用程序。只需在现有的函数中添加更多的铁节点，就可以实现缩放。

# 10.完全形态

银河之雾的格式塔是为中间层的 DC/操作系统构建的无服务器框架。它具有基于任务的能力，允许开发人员在没有任何容器环境管理的情况下构建功能。这是一系列微服务，其主要重点是为希望将其应用程序迁移到云中的企业设置正确的策略环境。

该框架的主要目的是提供一个自顶向下的调度层，它可以提供策略和 ID 管理。除此之外，还有一个基础设施层，为中间层 DC/操作系统和多语言 Lambda 服务提供可插拔接口。

# 11.OpenLambda

到目前为止，我们讨论的所有项目都是为了商业和生产用途。然而，根据展示项目目标的[论文](https://www.usenix.org/system/files/conference/hotcloud16/hotcloud16_hendrickson.pdf)，OpenLambda 的主要目的是探索无服务器计算的新方法。

到目前为止，它已经有了相当基本的功能，由于其固有的松散和不安全的设计，因此不建议将其用于生产。

# 开源无服务器的未来

截至目前，AWS 在无服务器游戏中领先。由于[无服务器框架](http://serverless.com)支持大多数流行的云提供商，它的发展速度明显快于其他无服务器框架。但是开源的无服务器社区需要建立[引人注目的用例](https://www.simform.com/serverless-examples-aws-lambda-use-cases/)，这些用例基于在多种云、内部和私有云系统之间的事件驱动的服务集成。

截至目前，无服务器市场正高速增长。预计 2021 年底[将以 37.2%的速度](http://www.businesswire.com/news/home/20170227006262/en/7.72-Billion-Function-as-a-Service-Market-2017---Global)增长。引用这一点，以下是开源无服务器将成为无服务器不可分割的一部分的主要原因:

*   这些首先是开发者。OpenFaaS 就是最好的例子之一。它简单的 UI 和简单的 CLI 通过一键式部署等功能受到了社区的广泛关注。
*   操作简单。由于大多数产品都构建在云原生应用之上，因此运营团队很容易与之合作。如果您已经有了一个 Kubernetes 集群，那么所需的舵图和 YAML 文件可以让您轻松开始(最多 60 秒)。
*   它以社区为中心，并赋予开发者权力。时至今日，IT 组织中的决策制定不再是首席执行官和架构师的专利。开发人员在采用新技术方面发挥着关键作用。
*   锁定自由:特定于供应商的产品有可能陷入锁定，尤其是在无服务器领域。缺乏灵活性和不可避免的价格上涨也成为一个严重的问题。

不仅如此，根据我的经验，任何将组织限制在单一平台上的方法，在效率和成本方面都要付出很大代价。市场发展太快，不能被单一平台所限制。

此外，应用程序的可移植性是企业关注的首要特性之一。这就是容器编排之上的 FaaS/PaaS 如此重要的原因。通过在 Kubernetes 上运行 FaaS，你可以对冲你今天押注的抽象概念，同时让自己从未来的抽象概念中受益。

例如，如果您的框架或 FaaS 平台是在另一个开源产品(如 Kubernetes)的基础上开发的，那么他们可以将他们的应用程序转移到任何地方。这正成为一种有趣的方法，它影响着今后的商业前景和决策。

虽然开源无服务器的未来看起来很有希望，但是到达那里的路径仍有待确定，因此看到它如何展开会很有趣！