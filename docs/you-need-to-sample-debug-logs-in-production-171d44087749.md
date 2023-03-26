# 您需要在生产中对调试日志进行采样

> 原文：<https://medium.com/hackernoon/you-need-to-sample-debug-logs-in-production-171d44087749>

由于流量过大，通常会将日志级别设置为生产警告。这是因为我们必须控制各种成本因素:

*   **日志记录成本** : *云观察日志*收取每 GB 0.50 美元。以我的经验来看，这通常比羔羊肉的成本要高得多
*   **存储成本** : *云观察日志*每月每 GB 收费 0.03 美元，其默认保留策略为**永不过期**！一个常见的做法是将您的日志传送到另一个日志聚合服务，并将保留策略设置为 X 天。详见[本帖](https://theburningmonk.com/2017/08/centralised-logging-for-aws-lambda/)。
*   **加工成本**:如果你用 *Lamb da* 加工原木，那么你也必须计入 *Lamb da* 加工成本。

但是，这样做使我们在生产过程中没有任何调试日志。当生产中出现问题时，您将没有调试日志来帮助确定根本原因。

相反，您必须浪费大量时间来部署新版本的代码以启用调试日志记录。不要忘记，在部署修补程序时，不应该禁用调试日志记录。

对于微服务，您经常需要对多个服务执行此操作，以获得您需要的所有调试消息。

所有这些，**增加了事故发生期间**[](https://en.wikipedia.org/wiki/Mean_time_to_recovery)****【MTTR】的平均恢复时间。这不是我们想要的。****

**![](img/ee0aa3bffe80ca2cdf38395abc98ce06.png)**

**不一定要那样。**

**在没有调试日志和有所有调试日志之间有一个折中的办法。相反，我们应该从一小部分事件的调试日志中抽取样本。**

**![](img/30fdec4005565a0e5b8269897c690113.png)**

**我在视频课程[生产就绪型无服务器](https://bit.ly/production-ready-serverless)的*日志*章节中演示了如何做到这一点。你需要两件基本的东西:**

*   **允许您动态更改日志级别的日志记录器，例如通过环境变量。**
*   **中间件引擎，如 [mid dy](https://github.com/middyjs/middy)**

**![](img/98e198dfd62328b5c66680dec3c69b48.png)**

**有了 *Lamb da* ，我不需要像 [pino](https://github.com/pinojs/pino) 这样的成熟日志程序的大部分特性。相反，我建议使用一个简单的日志模块，就像[这个](https://github.com/theburningmonk/manning-aws-lambda-in-motion/blob/master/lib/log.js)一样。它写在满手的线条中，给了我一些基本的东西:**

*   **[用 JSON 构造日志](https://theburningmonk.com/2018/01/you-need-to-use-structured-logging-with-aws-lambda/)**
*   **能够在不同级别记录**
*   **能够通过环境变量动态控制日志级别**

**使用*中间件*，我可以创建一个[中间件](https://github.com/theburningmonk/manning-aws-lambda-in-motion/blob/master/middleware/sample-logging.js)来动态更新日志级别进行调试。这是针对可配置的参与年龄百分比而言的。在调用结束时，中间件将恢复以前的日志级别。**

**![](img/11f7b2958ab708da38dc9d8ed8c6171a.png)**

**您可能会注意到，我们还有一些特殊的处理方法，用于处理交易出错的情况。**

**![](img/e87f6625fd7cd92eda74df1dc1c3b1d5.png)**

**这是为了确保我们尽可能多地记录错误，包括:**

*   **唯一的 AWS 请求 ID**
*   **调用事件，因此我们可以在本地[重放调用事件](https://theburningmonk.com/2017/08/running-and-debugging-aws-lambda-functions-locally-with-the-serverless-framework-and-vs-code/)并调试问题**
*   **错误消息和堆栈跟踪**

**拥有一小部分参与时间的调试日志是非常好的。但是当你处理微服务时，你需要确保你的调试日志覆盖了整个调用链。**

**这是阿尔收集整个通话链中发生的所有事情的唯一方法。否则，您将会得到来自许多调用链的调试日志的片段，但永远得不到一个调用链的完整图片。**

**您可以通过强制决定将调试日志记录作为更正 ID 来实现这一点。链中的下一个功能将尊重这个决定，并传递它。详见[这篇文章](https://theburningmonk.com/2017/09/capture-and-forward-correlation-ids-through-different-lambda-event-sources/)。**

**![](img/0c9f17d2b7f62937a560d3e797d1c755.png)**

**这就是关于如何在无服务器应用程序中构建观察能力的另一个专业技巧。如果你想了解更多关于如何使用更少的服务器，请点击这里查看我的 10 步指南。**

**下次见！**

**喜欢您正在阅读的内容，但需要更多帮助？我很乐意作为一名**独立顾问**提供服务，帮助您完成无服务器项目——架构审查、代码审查、构建概念验证，或者提供关于领先实践和工具的建议。**

**我在**伦敦，英国**，目前唯一在英国的 [**AWS 无服务器英雄**](https://aws.amazon.com/developer/community/heroes/yan-cui/) 。我有近 **10 年**的[经验](https://www.linkedin.com/in/theburningmonk/)在 AWS 中大规模运行生产工作负载。我主要在英国开展业务，但我愿意出差一周以上。要了解我们如何合作，请在这里告诉我更多关于您试图解决的问题的信息。**

**我还可以举办一个内部研讨会，帮助您的无服务器架构进入生产准备阶段。您可以在这里找到关于为期两天的研讨会[的更多信息，该研讨会将带您从 AWS Lambda 的基础知识一直到日志聚合、分发跟踪和安全最佳实践的通用操作模式。](https://theburningmonk.com/workshops/)**

**如果你喜欢按照自己的进度学习，那么你也可以找到与我为曼宁制作的 [**视频课程**](https://bit.ly/production-ready-serverless) 相同的研讨会内容。我们将讨论的主题包括:**

*   **认证 *&* 授权与 API 网关 *&* 认知**
*   **本地测试 *&* 运行功能**
*   **CI/CD**
*   **日志聚合**
*   **监控最佳实践**
*   **X 射线分布式跟踪**
*   **跟踪相关 id**
*   **性能 *&* 成本优化**
*   **错误处理**
*   **配置管理**
*   **金丝雀部署**
*   **VPC**
*   **安全**
*   **Lambda、Kinesis 和 API 网关的最佳实践**

**代码 **ytcui** 也可以获得**票面价格 6 折优惠**。不过，这个数字只有在我们参加曼宁的早期访问计划(MEAP)时才有效。**