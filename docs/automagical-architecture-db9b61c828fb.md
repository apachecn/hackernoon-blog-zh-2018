# 自动建筑

> 原文：<https://medium.com/hackernoon/automagical-architecture-db9b61c828fb>

一个现代创业公司的技术堆栈的分解，重点是 JavaScript 和简单性。

![](img/2c99773a0ae533cdf5f7d15fc5fba437.png)

[Automagical](https://automagical.ai/)’s React-based storyboard editor.

# 概观

[automagic](https://automagical.ai/)是一个平台，它使从现有内容创建高质量的营销视频变得简单而有趣。

> 声明:我是 Automagical 的创始人兼首席技术官。

我想概述一下它的架构，并思考一下**工程简单性**对一个产品的成功有多重要。

Automagical 大量使用 JavaScript，因为后端是用 [Node.js](https://nodejs.org/en/) 编写的，前端是通过 [create-react-app](https://github.com/facebook/create-react-app) 标准用 [React.js](https://reactjs.org/) 构建的。源代码由 git 作为 monorepo 在 [GitHub](https://github.com/) 上管理，部署由极其简单的[现在的](https://zeit.co/now)服务处理。

主后端数据存储是托管版本的 [mongoDB](https://www.mongodb.com/cloud) ，两个托管的 [redis](https://redislabs.com/) 实例分别用于存储用户认证令牌和异步任务信息。一个[异步任务队列](https://github.com/Automattic/kue)用于管理长时间运行的操作，如视频渲染和视频修剪。

[谷歌云平台](https://cloud.google.com/)用于长期存储(类似于 AWS S3)和托管有一些特殊要求的视频渲染器组件。

> 我们之所以能够用很少的工程师来扩展 Instagram，很大程度上是因为我们选择了简单易懂、值得信赖的解决方案。([来源](https://hvops.com/articles/simplicity-is-key/))

# 服务

该平台分为以下逻辑服务。每项服务都可以根据使用情况进行水平扩展。

## 后端 API

该服务提供了支持自动逻辑平台的核心后端 API([https://API . auto magic . ai](https://api.automagical.ai))。这是一个[Koa](https://koajs.com/)web 服务器，它公开了一个由前端 webapps 使用的 REST API。它是通过[现在](https://zeit.co/now)用单一命令部署的。

## 后端队列

该服务提供了一个异步任务[队列](https://github.com/Automattic/kue)工作器，用于呈现和处理其他长时间运行的任务。这是唯一托管在谷歌云平台上的服务，因为渲染器模块需要更多的计算资源。

## 前端 Webapp

这项服务提供了核心的自动视频 web app([https://app . automagic . ai](https://app.automagical.ai))，它是用 [create-react-app](https://github.com/facebookincubator/create-react-app) 构建的，用户可以在其中创建、编辑和呈现故事板。它是通过[现在是](https://zeit.co/now)用单一命令部署的。

## 前端营销网站

该服务提供用 [create-react-app](https://github.com/facebookincubator/create-react-app) 构建的自动营销网站([https://automagic . ai](https://automagical.ai))。它是通过[现在是](https://zeit.co/now)用单一命令部署的。

# 模块

该平台还包括以下模块，这些模块封装了定义明确的核心功能。

## 渲染器模块

该模块由后端队列任务运行器使用。这是一个 2D 渲染器，用于将自动故事板转换成视频。它是使用 Node.js、 [headless-opengl](https://github.com/stackgl/headless-gl) 和 [FFmpeg](http://www.ffmpeg.org/) 构建的。

## 总结模块

后端 API 服务使用这个模块从 html 生成摘要。它使用各种度量、NLP 和机器学习技术来从源文章中生成高质量的提取文本摘要。

# DevOps

一旦部署，Automagical 被设计为以最少的维护运行。万一服务器崩溃，受影响的服务会自动重启，并使用 [Google Cloud 错误报告](https://cloud.google.com/error-reporting/)记录一个错误。

所有环境机密，如条带访问密钥、数据库密码等。都由当地的[处理。env](https://github.com/motdotla/dotenv) 文件，现在通过[与代码一起部署](https://zeit.co/now)。为了能够运行和部署 Automagical 平台，新开发人员必须做的唯一一件事就是访问 git monorepo 和附带的私有。环境文件。

## 监视

*   每个 API 调用都用 [Google Cloud Trace](https://cloud.google.com/trace/) 记录。
*   [正常运行时间](https://uptime.com/)用于在服务不可用的情况下发送电子邮件和短信提醒。
*   [Google Analytics](https://analytics.google.com/analytics/web/) 设置为监控营销网站和主 webapp，因此您可以查看实时和历史流量数据。
*   所有主要的 webapp 操作(如创建故事板、添加新场景、呈现视频等。)被发送到 Google Analytics 和 [Intercom](https://www.intercom.com/) ，因此您可以跟踪特定用户通过应用程序的路径。

# 外卖食品

*   对于快速迭代来说，Node.js 是一种优秀的编程语言，但是您仍然需要花时间去理解和勾画出系统的哪些部分应该隔离到单独的服务或模块中。
*   一旦你需要扩大规模，微服务是时尚和伟大的，但 99%的早期创业公司不需要它们。从简单开始，着眼于应用中的逻辑、整体服务，只有在需要时才考虑增加解决方案的复杂性，如微服务和容器编排。
*   使用 [Zeit now](https://zeit.co/now) 进行部署已经取得了巨大的成功，让我们能够将精力集中在构建核心产品上，而忽略大多数围绕部署/扩展/编排的问题。它还具有如此简单的 API，几乎没有供应商锁定，因此一旦我们需要扩展，我们可以评估最佳选项并快速改变路线。
*   我们的工程消耗率非常低，大约每月 500 美元，其中大部分预算来自第三方 API 供应商。当考虑构建一个功能而不是购买一个等效的第三方 API 时，如果预算允许，尽可能倾向于使用 SaaS API，这样你就可以专注于构建你的核心产品。如果这个成本成为了一个障碍，那么你只需要在需要的时候自己制作一个定制版本*。*
*   充实和执行一个 [MVP](https://en.wikipedia.org/wiki/Minimum_viable_product) 是非常非常困难的。如果对某个特性或修饰项目是否应该出现在 MVP 中有任何疑问，跳过它，在花额外的时间开发之前，先和潜在客户验证你的产品。早期采用者更关心产品的想法及其未来的潜力，而不是具体的功能，他们的反馈对你的产品路线图的优先排序是非常宝贵的。这听起来很容易&显而易见，但在实践中，保持这种心态需要大量的经验和持续的警惕。
*   在你确信你已经找到产品/市场的契合点之前，专注于保持你的工程努力尽可能的简单和轻便。在创业初期快速迭代产品变化的能力比其他技术问题重要得多。开发速度和可维护性之间的权衡需要经验来优化，即使是最好的工程师也倾向于在早期过多地使用过多的结构和过程。 [**吻**](https://en.wikipedia.org/wiki/KISS_principle) 。

> 工程中的一切都是交易&接吻主宰一切。

# 摘要

Automagical 是一个现代的 Node.js 平台，精心设计，注重简单性。它分为几个主要服务:后端 API、后端队列、前端 webapp 和前端营销网站。核心 IP 分为封装良好的组件，即渲染器模块和摘要模块。Zeit 极其简单的 [now](https://zeit.co/now) 服务用于单命令部署，所有数据库(mongodb 和 redis)由第三方托管，便于维护。

如需了解更多详情或具体问题，欢迎致电[dev @ automagic . ai](mailto:dev@automagical.ai)联系我们。

## 在你走之前…

*如果您喜欢这篇文章，请点击👏下面，并与他人分享，这样他们也可以享受它。*