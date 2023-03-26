# 无服务器监控——好的、坏的和丑陋的

> 原文：<https://medium.com/hackernoon/serverless-monitoring-the-good-the-bad-and-the-ugly-2b06e7ffd843>

![](img/e268f90bc49fd7e1ef6ead9c05622843.png)

不久前，一项工作要求将我推入了 FaaS 的世界，我激动不已。我梦想抽象——消除所有开发人员不喜欢做的繁琐工作。*“我们不是运营工程师！”我自豪地喊道。*“我们不需要涉足 Linux 外壳的黑暗艺术。”**

但是我不知道我错了。我们人类是有习惯的动物，作为 AWS 用户，我的一个习惯是虔诚地查看 AWS 控制台。这是我监控我需要了解的服务器健康状况的中心位置。

现在来了一个困难的问题:**当使用 AWS Lambda 和无服务器时，监控是如何工作的？**

## TL；速度三角形定位法(dead reckoning)

*   监控 101
*   监控无服务器应用程序是无情的
*   使用[离线工具](https://github.com/dherault/serverless-offline)让我的生活不那么痛苦
*   使用[仪表板](https://dashbird.io/)进行系统概述

# 监控 101

作为开发人员，所有应用程序都有我们需要监控的指标。这一点至关重要:停机时间和缓慢的应用程序会产生一些脾气相当暴躁的客户。

相信我，我偶尔会接到愤怒的电话和愤怒的邮件。那么如何才能避免被顾客吼呢？跟踪您的错误并监控您的软件！

实现一个好的通知系统，让你知道错误发生的时间和地点。确保有好的和容易查看的日志，记录所有的错误、警告和其他应用程序创建的重要数据。对自己写的软件负责。因为这是我们作为开发者的遗产。我们发过誓，要成为了不起的东西的创造者！

但是用户体验只是性能指标的一个方面。第二个关键指标是计算资源的度量。应用程序消耗了多少资源。如果太多，你需要缩小服务器的规模，否则，如果应用程序正在限制所有资源，你可以考虑更大的服务器或更多的服务器。

***注*** *:我最近偶然看到一篇关于这个话题的* [*牛逼文章*](https://hackernoon.com/node-js-monitoring-done-right-70418ecbbff9) *，作者不是别人，正是 RisingStack 的 CTO 彼得·马顿。他详细解释了如何做好监控。我劝你看一眼，这将永远改变你对监控的看法。*

请问…？能给我一些监控吗？但是为了不成为我申请的负担。

我们很幸运，在我们这个时代，这是天赐的。监控软件已经变得如此先进，以至于在今天的编程世界中，开销是最小的。当年的太阳并没有这么明亮。在监控应用程序之后，一个已知的事实是，它会显著影响您的应用程序的性能。

# 这如何转化为无服务器？

在过去的几年里，无服务器革命的势头越来越猛。我看不到停止的理由。炒作是真实的。

开发人员开始将**F**function**a**S**a**a**S**service architecture 视为救星，它使得自动扩展应用程序并只为所需数量的用户提供服务成为可能。现收现付的方法大幅削减了成本，并使初创公司能够以很小的成本创造出令人惊叹的软件。

但是，等一下。要使这成为可能，还需要削减什么？

我想到了几件事。首先概述代码性能和跟踪错误。无声的失败也是如此。如何监控不是服务器的服务器的性能？薛定谔的服务器？好吧，现在我头疼。

这个悖论需要一个新的视角。监控无服务器本身就是一个新的猛兽。传统方法是行不通的。一种新的心态正在形成。

```
server != functions
```

与其告诉我们的函数在每次调用时发送额外的数据，为什么不收集它们的剩余数据呢？这是一个很酷的想法！众所周知，所有 AWS Lambda 函数都会将日志发送到 AWS CloudWatch。

# 无服务器是无情的

与传统的应用程序不同，您没有系统每个部分的完整视图。更不用说无服务器测试有多难了。您必须将代码推送到 AWS 来查看它是否在工作，或者花费大量时间在本地机器上设置模拟器。这个过程极其乏味。不要一开始就给你的应用添加第三方服务。它会产生开销和额外的成本。尝试将监控服务附加到每个 Lambda 函数上。这永远不会有好的扩展！

让我们想象一个在 AWS Lambda 上监控一个简单函数的场景。目的是测试 CloudWatch 上的功能并检查日志的详细程度。

在用 Postman 测试了几次端点之后，我确信它工作得很好。

![](img/287747a600a9189f56ce0183dc9004c7.png)

打开 CloudWatch 我可以清楚地看到日志。列出了所有的函数调用。

![](img/e05e4ad92b8e6f414480299b5cc4eec1.png)

日志是广泛的，唯一的问题是我似乎不能理解他们。我能看到我们被调用的函数，但看不到其他的。失败函数的错误消息不够详细，所以经常被忽视。我也很难找到超时的函数。

我还尝试通过命令行登录。它能更好地显示可能的错误，但仍然不足以让你安心。

```
serverless logs -f my-function
```

![](img/542ffb0999ff4bb460f02323904adcd5.png)

更不用说每次你想尝试新事物时都要把代码推给 AWS 这种烦人的事情了。谢天谢地，一切还没完。

# 让我的生活不那么痛苦

如果我不需要每次想测试什么的时候都把代码推送到 AWS 会怎么样？所有的英雄都不穿斗篷。就像一个穿着闪亮盔甲的骑士，[无服务器离线](https://github.com/dherault/serverless-offline)冲进来拯救世界！至少现在我可以在把代码推给 AWS 之前在本地测试所有代码。那是一种解脱。

设置起来出奇的简单。安装一个 npm 模块，并在无服务器服务的 **serverless.yml** 中添加几行代码，瞧，API 网关在本地模拟运行 Lambda 函数。

切换到我创建示例函数和服务的目录，我只是在终端中运行以下命令:

```
npm install serverless-offline --save-dev
```

离线安装无服务器后，我在 **serverless.yml** 配置中引用了它:

```
############## 
... 
functions:
  hello:
    handler: handler.hello     
...     
... 
############## 
# Added these two lines! 
plugins:    
  - serverless-offline
```

回到我的终端，离线运行无服务器就像输入:

```
serverless offline start
```

就这样，一个 API Gateway 和 Lambda 的本地开发模拟就启动并运行了！

# 日志仍然是坏的，虽然…

我仍然不能接受原木是如此的平淡无味。更不用说缺少错误报告了。我花了很长时间才在日志中找到失败的函数。想象一下在大规模生产应用程序中跟踪它们的噩梦。这个问题是最让我困扰的。缺乏总览。就像在黑暗中游泳。我一点也不知道下面有什么。

我做了什么？我去打猎了。网上肯定有什么能帮我的。我在寻找一种方法来模拟服务器的监控和日志记录。我想也许有一种方法可以从更广阔的角度来看待整个无服务器系统。我的发现让我大吃一惊。有许多工具可以在帐户级别解析和分析系统中所有功能的日志。这很酷。

我决定试试我们的 [Dashbird](https://www.dashbird.io/) ，因为[是免费的](https://dashbird.io/pricing/)而且似乎很有前景。他们也不要求信用卡，这就成了“为什么不试试看”的情况。

他们说只需要 5 分钟就可以连接到你的 AWS 帐户，并准备好了，但是嘿。我是一个怀疑论者。我给自己计时。

入职流程非常简单。你只要按照他们的指示，[注册](https://dashbird.io/register/)，按几个*下一个*按钮，一切都会自动连接起来！他们有很棒的[入门教程](https://dashbird.io/docs/get-started/quick-start/)。

如果你想知道，计时器停在 4 分钟。我印象深刻。

不过，我对 [Dashbird](https://www.dashbird.io/) 的印象要深刻得多。我终于明白是怎么回事了。

![](img/a66a5948d0038580b82d1b9c57a0d143.png)

错误被突出显示，我可以看到我的系统的整体健康状况。突然感觉好极了。它还跟踪成本，这样我就不会超出预算。甚至包括实时的函数跟踪。这真是太酷了！

![](img/95165466ac19a08ddff06fd6f2e9660b.png)

在过去的几个月里，Dashbird 团队增加了大量新功能，包括 X 射线支持、创建项目、基于吞吐量的新计费以及全新的免费层。我很期待接下来会发生什么。

有了这一点，我可以放心地将无服务器应用于任何大型应用程序。我想到了解脱这个词。

# 最后的想法

哇……这真是一场情绪过山车。起初，我对监控和跟踪大规模无服务器应用的能力持怀疑态度，但现在我已经变成了一名信徒。

这一切都归结于开发人员的心态。从服务器的精神形象转换到 FaaS 需要一段时间。无服务器是一项令人难以置信的技术，我只能看到一个光明的未来，如果我们继续用像[无服务器离线](https://github.com/dherault/serverless-offline)、 [Dashbird](https://www.dashbird.io/) 、CloudWatch 和许多其他工具这样的令人敬畏的工具来推进边界。

我强烈建议你检查一下我上面使用的工具，因为它们对我有很大的帮助。

希望你们喜欢读这篇文章，就像我喜欢写这篇文章一样。直到下次，保持好奇，玩得开心。你觉得这个教程会对某个人有帮助吗？不要犹豫分享。如果你喜欢它，请在下面的评论中告诉我，或者给它一些掌声，这样更多的媒体人会看到它。

**工具:**

*   [无服务器离线](https://github.com/dherault/serverless-offline)
*   达什伯德
*   [云观察](https://aws.amazon.com/cloudwatch/)

**资源:**

*   [https://hacker noon . com/node-js-monitoring-done-right-70418 ecbf F9](https://hackernoon.com/node-js-monitoring-done-right-70418ecbbff9)
*   [https://blog . rising stack . com/monitoring-nodejs-applications-nodejs-at-scale/](https://blog.risingstack.com/monitoring-nodejs-applications-nodejs-at-scale/)
*   [https://en .维基百科. org/wiki/Application _ performance _ management](https://en.wikipedia.org/wiki/Application_performance_management)
*   [https://medium . com/dash bird/is-your-server less-as-good as-you-think-it-2 baa 3d 36 B1 de](/dashbird/is-your-serverless-as-good-as-you-think-it-is-2baa3d36b1de)

*最初发表于*[*serverless.com*](https://serverless.com/blog/serverless-monitoring-the-good-the-bad-and-the-ugly/)*。*