# 概念的发展

> 原文：<https://medium.com/hackernoon/growth-of-a-concept-a67061146e4f>

马克斯·范德沃夫

## 你上一次遇到有人过于执着于自己的想法是什么时候？

什么时候，

> “这是我的想法，请给我反馈。”

实际上意味着，

> “这是我的想法，请告诉我它很棒，你不敢改变它。”

进入这个团队，我承认我有点厌倦了。*两年*入行，已经不行了？没错。我曾见过这样一个世界，那里的想法往往不冷不热，半生不熟，到处都是创始人的污迹。

我不太乐观，我知道我并不孤单。我认为想法是有生命的实体——这是我在实践中一次又一次看到的反驳。进入这种没有人是珍贵的环境，想法被挑选出来变得更好，并因此而改进——这是不可思议的。

概念就是想法。想法需要空间，需要时间和精力。他们可以是需要帮助的孩子，他们可以是一个完全正常的成年人，也可以是一个不守规矩的青少年。

概念需要一个健康的环境来成长，它们应该被添加，被激励，被戳，随着时间的推移被剥离和改进。有时很难让一个想法的美丽宝石离开你的视线，让别人影响它。

好了，介绍完毕——*系好安全带*，准备技术交流。

## 这个概念

我要讲的例子是一个很好的例子，一个想法的演变和改变超越了它最初的概念，并因此变得更好。

例如*目前*是我们基础设施中相对核心的部分。这是我们知道我们需要的东西，是我们想要处理一些情况的东西，但仅此而已。

我们目前运行基于服务的架构，许多不同的服务围绕其业务运行，然后我们有可能需要这些服务的数据组合的服务。我们不想让这些服务有能力打击我们的核心服务，而是想要一个缓存/聚合服务来保护我们的软肋——可以这么说。

## "要求"

这项服务将被称为[行星吞噬者](https://en.wikipedia.org/wiki/Galactus)(是的，世界的吞食者)它有非常广泛的中风要求，它需要；

*   拥有我们内部服务的缓存
*   遭受重创
*   立即回应
*   处理子文档更新

最后一个“需求”是:我们需要知道它何时改变。这一点很重要，因为我们希望以尽可能小的延迟来更新关心这些数据的服务。*看好这个空间。*

还有其他一些值得拥有的东西，但它们只是值得拥有的东西。比如托管服务——我们目前托管在 AWS 上，并利用了很多他们的托管服务(我们不太担心)、简单的冗余、复制或类似的东西，或许还有版本控制。

## 选择

我们做了一点研究，衡量了各种选择，我们选择了*试用* [*CouchDB*](http://couchdb.apache.org/) (一个非常灵活的文档存储库)，因为它提供了*我们几乎开箱即用的大部分*。

特别是提供了一个[连续流](http://docs.couchdb.org/en/2.1.1/api/database/changes.html)的更新信息，如果你想听的话。这是一个轻触，运行在 Erlang 中，有一些可爱的集群技术(利用这个博客的名字)，在 JavaScript 中有函数，运行在内存中以获得一些闪电般的快速响应。

## 建筑

![](img/6dff8fdc0c1cc3bdd27daab9f6e3313e.png)

Galactus Architecture

围绕行星吞噬者的架构是在图的左侧有多个内部服务，这些是事实的来源，当它们被更新时，服务发布到通知服务(对我们来说是 AWS [SNS](https://aws.amazon.com/sns/) ，替换为您选择的发布/订阅)。

这将触发一个流程来拾取已更改的数据，并将其放入行星吞噬者中适当的子文档中。这将出现在上述连续变更流的变更日志中。会有一些东西监听这个流，并通知行星吞噬者右手边的所有服务，那些渴望更新的服务，准备向外部方推送更新。

## 变化

你会注意到上面一些架构故意模糊的本质，因为时代在变，思想在变，思想在成长，你认为重要或次要的东西可能会倒退。

“倾听变化之流的东西”在被尝试之前已经多次改变了形状；

*   边车码头集装箱？
*   致力于倾听的服务？
*   它只是将信息流导入社交网站吗？还是向数据库询问更多细节？

## 发育期痛

第一次迭代是一个守护进程，它与 CouchDB 实例共存，这被证明是不一致的，很难监控，也很容易被遗忘(有点看不见)。就像渡渡鸟☠️.一样

接下来是 lambda，它会每分钟轮询一次变化，遍历文档以找出什么发生了变化，这很慢，受到 cron 作业的限制，并且可能会在负载下挣扎。

最终这个“监听器”被完全移除了，我们有东西把数据输送到行星吞噬者，只要我们有👍从行星吞噬者回来，我们知道我们已经准备好了一切，我们可以通知那些饥渴的服务人员。

## 烧成

随着时间的推移，我们打破了行星吞噬者，我们踢他有点太多，有点太快，我们发现，痛点更可能出现在可用性和可伸缩性方面，这是 CouchDB 擅长的。我们向群集中添加了额外的节点，并在它们之间进行复制。我们越来越依赖子文档更新。

回顾过去，你可以看到我们的需求发生了变化，一些得到了不同的重视，而其他我们认为绝对核心的需求基本上完全消失了。

## 后见之明

我们会再做一次吗，我们会以不同的方式做吗，我们会再次选择 CouchDB 吗，我们现在应该放弃一些东西，建立一些别的东西吗？

*   我们没有意识到交互子文档更新有多重要——或者它在其他产品中有多罕见。
*   我们非常重视倾听变化，结果完全偏离了我们的要求。
*   尽管围绕数据库构建一个小的 web 服务并不是一个大问题，但是 CouchDB 有一个现成的 HTTP 端点意味着它可以单独运行，不需要包装或手动操作。
*   版本控制被认为是一件很好的事情，但是这些版本有助于跟踪、比较和其他许多事情。它多次救了我的命。开箱即用是一种享受。

就我个人而言，我认为我们已经站稳了脚跟，我们可能会搞砸这次着陆(可能会在我们的最终得分上掉一两个点)，但我们总体上恢复了，我们的服务已经克服了困难，它已经获得了街头智慧，并且正在我们的架构中愉快地向前发展。

行星吞噬者作为一个概念，一种思想，经历了它的“青少年”阶段，并作为一个正直的社会成员出现。

这是我们经验的核心，让这些想法脱离你的掌握并改进。

**关于作者**

工程师@ TravelNest。问题的解决者🔎，骄傲的书呆子😎兔子洞的调查员🐇和散漫列表的作者✏️.

![](img/ee99176c8c7b7001b7ad0c8fa18413b1.png)