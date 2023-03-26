# 无服务器:现在谁在待命？

> 原文：<https://medium.com/hackernoon/serverless-whos-on-call-now-81193fbfe9d7>

![](img/596f6d3de096353464dcc57fb1ca8fdd.png)

Kudos to [Fabian Grohs for the pic](https://unsplash.com/photos/T1SrR7Jht8c) (via Unsplash).

***TL；*** *当采用无服务器时，你需要决定谁将随叫随到来排除故障和修复功能。如果您无法想象您的开发人员随叫随到，那么您需要更加努力地思考。*

正如我在 2016 年奥赖利关于[无服务器操作](http://www.oreilly.com/webops-perf/free/serverless-ops.csp)的书中试图论证的那样，当采用[无服务器](https://hackernoon.com/tagged/serverless)计算时，组织需要考虑操作方面的事情(同时，感谢创造 NoOps 的人，而不是帮助):

*   围绕**平台**本身有一个操作部分。如果你正在使用公共云提供商的产品，如 [AWS Lambda](https://aws.amazon.com/lambda/) 、 [Google Cloud Functions](https://cloud.google.com/functions/) 、[Microsoft Azure Functions](https://azure.microsoft.com/en-us/services/functions/)或 [IBM Cloud Functions](https://www.ibm.com/cloud/functions) ，那么你可能永远不会遇到运行该平台的优秀人员。然而，如果你在自己的基础设施上运行无服务器——例如流行的 [Kubernetes](https://hackernoon.com/tagged/kubernetes) 产品 [OpenFaaS](https://www.openfaas.com/) 和[Kubernetes](http://kubeless.io/)或类似 Oracle 的 [fn](http://fnproject.io/) 或 Pivotal 的 [PFS](https://pivotal.io/platform/pivotal-function-service) 之类的新兴产品——那么你至少有机会了解维护它所运行的 Kubernetes 集群的人。我们将不会在下面讨论这个 ops 方面。
*   然后是围绕 **app(或函数)**的操作部分。由于代码本身的错误或意外加载，函数可能会中断。安全性如何？需要有人(a)在出现问题时得到提醒(即随叫随到)，以及(b)能够解决问题(日志、指标、跟踪、故障排除)。这是我感兴趣的。*已经采用无服务器的用户是如何应对的，计划采用无服务器的用户认为他们能够完成这项运营任务吗？*

现在，不要误解我的意思，我是无服务器的忠实信徒(从 ca。2015 如果我觉得它飞不起来)。我也基本同意西蒙·沃德利的观点，他说:

> 不管人们会怎么想，我是容器的忠实粉丝。我将它们视为无形的子系统。那不是战斗的地方。这场战斗是代码执行环境，特别是功能计费。

我在云中使用过各种无服务器产品(AWS Lambda、Google Functions 和 Azure Functions)，也在 Kubernetes 上使用过(比如 T2 的 OpenFaaS 和 kubeless)。见鬼，我甚至[自己也实现了一个](https://github.com/mhausenblas/fob)。我知道 UX 有多棒。不用再考虑什么样的虚拟机或什么样的容器基础映像。这是我的代码，我们走吧！

然而，我要说的是，尽管人们正试图将讨论引向某个方向(唉，这不是关于无服务器还是容器的问题，参见上面 Simon 的陈述)，人们必须想出一个策略来处理 appops 的无服务器方面。*有吗？*

从组织和个人那里，我基本上听到两种答案:

*   进步的、有远见的人会说:“我们的开发人员当然是随叫随到的，不需要服务器”，[例如](https://twitter.com/sallamar/status/964439201247883264) Expedia 的工程主管 Subbu Allamaraju。
*   我在各种会议和用户组中交谈过的大多数人，主要是在欧洲和美国，都说了一些类似的话:“我们付钱给开发人员是让他们生产代码，而不是随叫随到”。嗯嗯……

我很想在这里学习你的方法。你如何处理无服务器计算中的应用程序操作部分？你的团队中谁在待命？