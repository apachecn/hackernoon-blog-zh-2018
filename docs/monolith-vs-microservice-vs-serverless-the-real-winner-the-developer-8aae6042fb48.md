# 整体服务器 Vs 微服务 Vs 无服务器——真正的赢家？开发商

> 原文：<https://medium.com/hackernoon/monolith-vs-microservice-vs-serverless-the-real-winner-the-developer-8aae6042fb48>

微服务和无服务器在过去几年里确实越来越受欢迎。如今，每个人都想复制网飞等公司的成功，创造一个具有高度容错和弹性的系统。

许多领先的开发人员和架构师现在意识到了微服务和无服务器 T2 架构风格的一些主要优势，并在全球范围内取得了不同程度的成功。这些新建筑风格的福音派似乎有一个潜在的主题，那就是世界是黑白的。如果你打算利用微服务架构，**一切**都必须遵循这种架构风格。

## 2018 年微服务疯狂之死

我最近在 Dave Kerr 的网站上看到了他的帖子，谈到了“2018 年微服务疯狂的死亡”。他似乎强调了购买这种微服务和无服务器风格的团队每天都会遇到的大量问题。

 [## 2018 年微服务疯狂之死

### 在过去几年中，微服务成为了一个非常热门的话题。“微服务疯狂”类似于…

www.dwmkerr.com](http://www.dwmkerr.com/the-death-of-microservice-madness-in-2018/) 

在我看来，在 [/r/programming](https://www.reddit.com/r/programming/comments/7pxriw/the_death_of_microservice_madness_in_2018/) 上找到的一些评论对这些新的架构风格过于苛刻。我觉得这有点不公平，抨击这些新的方法似乎与我们开发人员社区想要实现的目标背道而驰。

我们将永远建造越来越复杂的系统，以我们 20-30 年前做梦也想不到的方式解决新问题。有了这些新的架构风格，我们可以用以前从未想过的方式来解决问题。

可以理解的是，这种从传统整体结构的转变最终将是痛苦的，因为必须开发新的实践来尝试和最小化自残的量。随着时间的推移，我们将继续看到围绕这些分布式系统的工具的改进，因为更多的公司继续感受到 Dave Kerr 的帖子中强调的痛点。

## 无服务器—优势

想象一下这样一个场景，你有一个来自上层管理人员的新需求。这个需求相对来说是突如其来的，并且不太适合您当前的任何系统。

这些选项包括:

*   将您的新需求融入到您现有的系统中
*   设计一个新的系统来处理这个需求
*   利用 AWS Lambda 之类的工具。

现在，你们中的许多人可能认为选项 1 在这里听起来很理想，但是如果您用这些新的相对左边领域的需求来扩展您的一个关键系统，那么某些东西使您的系统停机并花费您公司资金的可能性就会增加。

选项 2 也是一个非常有效的选项，但这样会增加开发时间，让您有更多时间担心新系统的操作方面，并在初始设计阶段花费更多时间，因为您必须确保新系统具有弹性。

选项 3 似乎是这里最受欢迎的。您只需编写满足这一需求的代码，将其部署到 AWS lambda 之类的平台上，并将 jira 标记为完整的。这可能过于简化了开发，但 AWS lambda 为您做了大量繁重的工作，包括弹性、水平伸缩等。

## 微服务—优势

想象第二个场景，您有一个目前性能最佳的整体系统。来自管理层的一个要求是，你需要开始在你的公司内部做 X。

理论上,“x”可能会成为 monolith 的性能瓶颈，您可能不得不开始在更强大的机器上部署您的系统，或者跨更多的机器部署，从而增加成本。

在这种情况下，基于微服务的方法的优势在于，您可以有效地设计、开发满足“X”的微服务，并且可以通过您喜欢的通信协议从您的 monolith 内部调用。

这允许您:

*   独立扩展“X”以应对系统的可变需求。
*   为了降低“X”在凌晨 3 点使您的生产系统停机的风险。

## 独石——优势

假设你是一家初创公司，上市时间非常重要，你需要尽快推出你的产品。在这种情况下，优势在于:

*   减少担心分布式系统复杂性的时间
*   减少担心系统部署的时间
*   简化的架构

从我们大学生涯的一开始，我们就倾向于这种建筑风格。这是默认的，目前使用最广泛，也最容易开发。虽然它确实带来了很多问题，但它确实简化了产品完全成熟之前的早期阶段。

## 混合方法—折中方案

在需要的时候，绝对没有什么可以阻止你采用混合方法和整体架构风格。绝大多数遗留系统都采用这种架构风格，这也是它们仍然存在的部分原因。

> 需要注意的关键点是，遗留系统之所以是遗留系统，是因为它们足够成功，可以持续这么长时间。

我觉得我们应该积极避免做的一件关键事情是为了改变而改变。如果一个遗留系统已经运行了 10 年，几乎没有出现任何问题，那么就让它继续运行，并致力于以其他各种方式提供价值，而不是将系统重新架构成最终不会在旧的架构风格上提供任何真正价值的东西。

## 结论

归根结底，随着我们的不断改进，这些新的架构风格只是代表了优秀的软件开发人员可以利用的另一种工具，以便更快地推出产品。

作为开发人员，我们有责任了解这些新趋势的优势，并尝试理解如何最好地利用它们来改进我们正在开发的系统。

我们需要提供建设性的反馈，用马克·沃特尼的话说:*【解决问题】*

![](img/f1a845afc2f9d7ed5e76af41ab394e10.png)

希望你觉得这篇文章发人深省！我总是渴望听到你的反驳或者你是否同意，所以请在下面的评论区留下你的评论！如果你想支持我的写作，并向 EFF 捐款，那么请随时查看我目前正在制作的新书:

[](https://leanpub.com/an-introduction-to-cloud-development) [## 云开发简介

### 这本书温和地介绍了云开发的世界。我们将深入了解…

leanpub.com](https://leanpub.com/an-introduction-to-cloud-development) 

我也在 LinkedIn 和 twitter 上: