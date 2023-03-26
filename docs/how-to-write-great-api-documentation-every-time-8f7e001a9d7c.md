# 如何每次都写出很棒的 API 文档

> 原文：<https://medium.com/hackernoon/how-to-write-great-api-documentation-every-time-8f7e001a9d7c>

![](img/34d443c3b8f22a87af926bd497d4218e.png)

Photo by [广博 郝](https://unsplash.com/@awmleer?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

如何创建最好的 API 文档是一个有争议的话题。

大多数开发人员对于 API 文档中应该包含什么和不应该包含什么都有自己的看法。难得大家意见一致。

从你放进去的东西到物品的摆放方式，一切都会影响顾客的体验。这就是为什么做好这件事如此重要。

如果做得不好，你可能会让潜在客户不敢使用你的 API，因为他们不明白事情是如何工作的，或者更糟——大量的支持请求会占用每个人的时间。

我采访了 Cronofy 的开发团队，了解他们如何编写我们的产品。

# 在开发新功能时考虑这一点

![](img/849233aff42361d7accc66ab13aacbd7.png)

Photo by [rawpixel](https://unsplash.com/@rawpixel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

“不把 API 文档作为产品的一部分通常是糟糕文档的根本原因，”我们的高级开发人员 Stephen 说。

“每当我们开发新功能时，API 文档是我们首先考虑的。我们经常就每项财产的正确术语进行长时间的讨论，尽可能避免含糊不清。”

这对于确保它尽可能简单易懂非常重要。

你对某个功能或元素的描述越模糊，就越有可能让客户感到困惑，你就越有可能收到更多的支持请求。

> "不把 API 文档作为产品的一部分通常是糟糕文档的根本原因."— Stephen，Cronofy 高级开发人员

在构建新特性的同时考虑文档也给了你解决问题的时间。客户可以在 Alpha 和 Beta 阶段给你反馈，进一步允许你在文档上线前对其进行优化。

Stephen 接着说:“在我们的开发阶段，API 设计确实会发生变化。通常，当我们编写 SDK 和格式化文档时，我们会在公开之前发现我们想要整理的东西，因为我们从不改变我们的公共 API。

“这其中的另一个关键方面是我们的 Alpha 和 Beta 阶段，在这两个阶段，我们依赖客户让我们知道文档有多符合目的。”

当产品上线时，你可以确定你已经解决了大部分问题，这将有助于你的用户掌握新功能。

# 有明确的例子

![](img/6adc90ee6adaa49123adfcb279b0d981.png)

Photo by [rawpixel](https://unsplash.com/@rawpixel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

对于最复杂的 API 方法，给出例子和额外的支持文档是必不可少的。如果您不这样做，您通常会通过额外的支持请求来偿还，”我们的首席技术官 Garry 说。

> "对于最复杂的 API 方法，给出例子和额外的支持文档是必不可少的."— Garry，首席技术官，Cronofy

“我们的许多端点都有可选参数，但有一组公共参数是必需的，我们推荐的可选参数已设置。因此，我首先提出一个反映这些最佳实践的请求，以及期望得到的回应。

这提供了一个解释所有可能参数的环境，它们的潜在值是什么，如果没有提供它们，它们的默认值是什么，以及最终所有这些意味着什么

从一个例子开始，你在人们的头脑中建立了一个形象。这张图片有助于他们理解。有了这样的解释，客户和潜在客户会更快地理解你的产品/功能。

常见的用法示例还包括讲故事，向用户展示他们可以创造什么。

您为客户提供的上下文越多，他们就越有可能理解该功能，并对将其添加到他们的软件中感到兴奋。

# 可用性和一致性是关键

![](img/de458741c9723acc742164fc8c79066e.png)

Photo by [NESA by Makers](https://unsplash.com/@nesabymakers?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

> "一些简单的事情，比如让文档可以离线使用或者添加深层链接，真的很有帮助."— Stephen，高级开发人员

可用性在软件开发的所有方面都是必须的，API 文档也不例外。

“可用性、一致性和功能等关键因素经常被忽视。斯蒂芬说:“让文档离线可用或添加深度链接等简单的事情确实很有帮助。

“我最讨厌的是当你的部分文档自相矛盾或者含糊不清的时候。如果你知道这种行为是什么并不重要，把它写下来，这样其他人也会知道。

例如，如果属性的定义规定值必须大于零，但验证规定值必须为正数，则实施者必须判断 0 是否为有效输入

# 不要依赖工具来为你生成它

![](img/620871cbb7943fbbf2fe4227712db7e4.png)

Photo by [Tyler Franta](https://unsplash.com/@tfrants?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

想要依靠工具为你做事很容易，但是如果你想为你的用户创造最好的体验，这不是最好的方法。

“你会看到一些东西，比如一个东西有一个名为‘评论’的字段和‘这个东西的评论’的描述。这并没有告诉你它是什么类型，或者可能是什么类型，尽管你可能会猜测它是一个字符串。它也不会告诉你这个字符串的格式。它可以是纯文本、HTML、Markdown 或其他内容。它没有告诉您该字符串可能有多长，因此您可以清理您的请求并适当地调整数据库字段的大小。Garry 说:“它不会告诉你它是 ASCII、UTF 8 还是其他什么，这样你就可以使用正确的编码类型。

“作为开发人员，你需要做的事情太多了，这对于每个使用该 API 的开发人员来说都是复杂的。因此，为出版商节省几个小时的东西，会让他们的客户各自损失几个小时。”

> “想要依靠自动化工具来为您解决问题很容易，但如果您想要为您的用户创造最佳体验，这并不是最佳途径。”—首席技术官 Garry

当您考虑最终用户体验时，有时为您节省的时间和成本是不值得的。说真的，顾客的感受难道不是最重要的吗？如果他们不喜欢你做的事情，他们就不太可能向他们的网络推荐你，这意味着你的客户减少。

另一方面，良好的体验意味着他们更有可能向他们的网络推荐你，进一步增加你的影响力，吸引新客户，并给你更多的钱来开发更多的功能。

# 结论

![](img/29ee8b1118ee48667581921a6497057c.png)

Photo by [Rishi Deep](https://unsplash.com/@_rishideep_?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

什么是优秀的 API 文档可能是一个有争议的话题，但是在很大程度上，基础是非常简单的:

*   把它当作你的特色的一部分，而不是事后的想法
*   有明确的例子
*   保持一致
*   想想用户体验
*   不要依赖工具来为你生成它

所有这些元素一起工作，以确保您的客户在使用您的 API 时完全理解他们可以构建什么。这意味着他们更有可能充分利用它。

一个有良好文档记录的 API 也意味着你不太可能从那些不理解 X 或 Y 特性如何工作或者为什么有些东西不能按照他们想要的那样工作的客户那里得到频繁的支持。

从长远来看，花额外的时间整理一个一致的、考虑周全的文档会有好处。

你认为是什么造就了伟大的 API 文档？

# 尝试我们的 API

如果你是一名开发人员，想看看如何简单地**将双向日历同步添加到你的软件**，[前往我们的网站](https://app.cronofy.com/sign_up/developer)，在那里你可以在几分钟内进行你的第一次 API 调用。

不是开发商？**使用我们免费的[日历连接器](https://www.cronofy.com/calendar-connectors/)，将你的日历连接到你最喜欢的应用**——包括 Trello、Slack 和 Evernote。