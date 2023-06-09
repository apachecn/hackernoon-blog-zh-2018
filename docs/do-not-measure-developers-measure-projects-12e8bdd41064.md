# 不要度量开发人员——度量项目

> 原文：<https://medium.com/hackernoon/do-not-measure-developers-measure-projects-12e8bdd41064>

![](img/811cb0ec984b8b00195d5c5566d425b7.png)

*A kitty that is saying “Why would you measure developers individually?”*

你听说过通过度量来管理团队吗，比如 bug 关闭率或者每周产生的代码行数？根据这些标准，表现最差的员工将被解雇。接下来会发生什么？该团队将只关注那些无意义的易于修复的错误，以"[juke stats](https://www.youtube.com/watch?v=_ogxZxu6cjM)"！而且到最后，产品质量变得更差，而不是更好，也许一些有价值的开发者会离开。难怪[开发者](https://hackernoon.com/tagged/developers)不信任[生产率](https://hackernoon.com/tagged/productivity)指标。"我真的从来没有见过没有功能失调的度量标准."

在这篇文章中，我想揭穿软件开发生产率度量的神话。我们来看看以下列表:

*   您应该谨慎使用这些指标——这意味着它们可能在特定的条件下以及以特定的方式使用时令人感兴趣；
*   那些真正有用的，你应该在你的团队中介绍的，不管你是贡献者、领导者还是管理者。

好，我们开始吧！

# 您应该非常小心地使用指标

## 1)门票成交率

如果你不包括故事点或类似的东西，这可能是你可以使用的最容易误导人的指标。使用这个指标假设所有的票据都有大致相同的工作量，但这是不正确的。你永远不应该用这个标准来评估开发人员的个人表现。一个开发人员可以修复一个没人能解决的 bug，这个 bug 会影响到你的产品性能的方方面面，这可能会花去他或她整整一周的时间。而另一个开发人员可以同时修复 20 个细节错误。哪一个对你的团队和公司影响最大？

即使你有故事点，在上述场景中最好的情况下，bug 的故事点是 5，或者 20 个 bug 的故事点是 1。这还没有考虑到大多数团队不把分数用于 bug，而只用于特性。

也就是说，您可以使用这个度量来识别问题，比如开发人员陷入了一个特定的任务。重点是不要把它作为评估开发人员表现的一种方式，否则**你的团队只会玩弄这些指标，而不会产生任何有意义的工作**。仅将此指标作为理解您作为经理如何更好地帮助您的团队的一种方式。

## 2)生成的代码行(LoC)

在上面的同一个例子中，巨大的 bug 修复可能是一行代码的改变。你怎么能把这和一个开发人员导入一个库或者改变每个文件的头相比呢？你不能。同样，你永远不应该使用这个标准来评估开发人员的个人表现。您可以以同样的方式使用 LoC 了解您的团队何时遇到困难，或者为了项目的质量导入了太多的库！

有些人根据变更中的代码数量、变更的严重性以及变更影响的文件数量来计算“影响”指标。总体目标是对 LoC 进行改进。这很有趣，但我强烈建议不要将它们用于个人绩效评估。事实上，上面提到的一行错误修复示例仍然不起作用，正如许多其他现实生活中的示例所示。

## 3)代码变动与代码吞吐量

关于什么是代码变动，有许多不同的定义。它通常以开发人员自己的代码占他们最近工作的编辑的百分比来衡量。流失率的突然增加可能表明开发人员在解决特定问题时遇到了困难。

有些人认为代码变动是非生产性的工作，不在“代码吞吐量”之内，这就是危险所在。事实上，代码变动可能表明开发人员正在优化部分代码以获得更好的性能。或者产品团队优柔寡断，让开发人员在原地打转。规则是监控任何大的变化，以识别开发人员可能遇到的潜在问题，以便更快地帮助团队。

## 你能使用任何衡量个人表现的标准吗？

嗯，既然你(嗯…我)提出了问题:不！是的，你可以引用我的话。

> ***指标是主观的、信息性的。你不能根据指标对个人表现做出任何判断。他们只是帮助你进行调查，以了解到底发生了什么，从而更好地理解项目和管理团队的复杂性。***

现实是，管理很难，而且总是与环境相关。您需要更深入地了解问题的根源。有时候你会发现，事实上，一个开发人员实际上是一个糟糕的执行者，但是这是因为你努力去了解你的团队，并且理解他们是如何一起工作的，如果有一个成员拖了你的后腿，你就能识别出来。这就是你如何成为一名更好的经理，在你的团队中努力提高生产力和留住人才。

而且，如果你有这样的想法，你可以正确地使用上面提到的前三种指标。您还将使用更多有用的度量标准，这将帮助您真正很好地理解您团队的工作流速度和质量。

# 您绝对应该使用的指标

我将这些指标分为两个不同的类别:速度和质量。如果我忘了什么，请告诉我；我很乐意更新这篇文章。

## 速度相关指标

这些指标是最有争议的，因为很多人(包括我)开始讨厌敏捷故事点。但是这里有一些选择，我希望能让你三思，你不应该测量“速度”

## 4)提交频率

如果您想要引入每天提交的最佳实践，那么这个度量是很有趣的。这也是一个很好的方式来看到干扰的隐藏成本。非编码任务，如计划、会议和追踪规格是不可避免的。团队通常每周至少会因为这些活动而损失一天时间。监控提交频率使您能够看到哪些会议对您的团队推进代码的能力有影响。

经理应该努力保护他们团队的注意力，确保流程开销不会成为负担。

## 5)服务水平协议

每个团队都有自己的 SLA 定义。但是这里有一个 Airbnb 用的，我个人觉得很有趣的。SLA 是您的团队在特定时间内修复和部署的拦截器错误的百分比(例如，拦截器错误为 24 小时，关键错误为 5 天)。我真正喜欢这个指标的是，它让你从用户的角度更好地理解你的产品质量。

请注意，在我看来，这个度量是与速度相关的，因为它显示了您团队的速度，而不是所生产软件的质量。

## 6)拉式请求相关的速度

*   每周打开的拉请求的数量
*   每周合并的拉请求数
*   每周部署的生产数量
*   平均合并时间(或在特定阈值下合并的拉请求(PRs)的百分比)。这在某种程度上等同于“提交到部署的时间”(代码从提交到部署所花费的时间:在这之间，它可能会经历测试、QA 和准备阶段，这取决于您的组织)。这是一个非常有趣的指标，它向您展示了您在工作流程中遇到的障碍。

这些度量可以让你感觉到你的工程团队的持续生产能力。例如，如果当你雇佣更多的人时，这个数字没有增长，那么可能有一个与新流程相关的问题或者一个需要解决的技术债务。然而，如果它增加得太快，你可能有一个质量问题。

不要忘记，在没有评估工作质量的情况下衡量团队的速度可能会非常误导人，并且是极其危险的。

## 质量相关指标

质量本身并不是目标。重要的是对能够以安全的方式成长和改变行为的信心。

## 7)测试覆盖率

当然，你不需要 100%的保险。然而，知道你的立场并跟踪它有助于了解你是否在用速度换取质量。

## 8)拉式请求质量

拉请求可以让您更好地了解代码库的整体复杂性。代码库越复杂，下列指标越高的可能性就越大:

*   拉请求中断生成或未能通过测试套件的次数百分比；
*   已合并的请求与已拒绝的请求的百分比；
*   拉取请求的评论数量——你不想要太低的数量，但也不想要太高的数量。

## 9)项目中的错误数量

一般来说，bug 的数量会在项目生命周期的中期开始增加。在截止日期前的几天或几周(取决于项目的大小)，团队将专注于减少 bug 的数量，直到 bug 的数量达到某种渐近线。这条渐近线最终代表了项目产品的整体质量。所以跟踪缺陷总数(区分它们的优先级)是一个很好的指标。

另一个指标可以是每周或每月发现的 bug 的数量，或者 bug 修复与发布的特性的对比。它应该指出实现的质量水平。

## 10)依赖年龄

技术债务的另一个指标是你的代码库中使用的依赖关系有多过时。追踪这个可能会很有趣。

技术债务是正常的，会出现在每个项目中，质量的概念是主观的。使用我在本文中讨论的度量标准，可以帮助您与您的团队一起定义一组标准，这将帮助您获得对团队工作质量的一些看法。

—

本文的要点是，如果您在项目或团队级别上度量指标，您的开发人员不会玩弄这些指标。[正如@raffi 所说](https://twitter.com/raffi/status/725044879097782274)，“你不能游戏你没有衡量的东西。”游戏标准总是肤浅的。

如果您不同意我列出的任何指标，或者我遗漏了任何指标，请告诉我。让我们把这篇文章当作一次谈话的起点。

# 在你走之前…

你觉得这篇文章有趣吗？请按住👏说“谢谢”并帮助别人找到它！
如果您对关于**我们在**[**Airbyte**](http://airbyte.io)**用我们所有的知识**打造世界级组织的旅程的文章感兴趣，请订阅我们的时事通讯！

在 Twitter 上关注我，保持联系。谢谢大家！

*原载于 2018 年 11 月 25 日*[*anaxi.com*](https://anaxi.com/blog/2018/11/25/do-not-measure-developers-measure-projects/)*。*