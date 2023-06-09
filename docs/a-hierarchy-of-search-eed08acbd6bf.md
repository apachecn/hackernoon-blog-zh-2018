# 搜索的层次结构

> 原文：<https://medium.com/hackernoon/a-hierarchy-of-search-eed08acbd6bf>

## 设计人们喜欢的搜索体验

![](img/2e4f35bef29d86e5bfbb3da911a56445.png)

> *披露:* [Manifold](https://goo.gl/Sm26zK) ，独立开发者服务市场*，之前赞助过黑客正午。* [使用 code HACKERNOON2018 获得任何服务 10 美元优惠。](https://goo.gl/Sm26zK)

作为 [Bonsai](http://www.bonsai.io) 的创始人，我收到了很多关于如何有效扩展 Elasticsearch 的问题。

我们的客户经常会问如何计算碎片数量，或者架构师指数。或者他们需要知道需要多少数据，或者处理某些流量负载需要什么。这些都是有效的问题，也是工程师们容易着手的地方。但是一个有效的搜索引擎除了它的机制之外，还有很多其他的东西。

在 Lucene Revolution 2017 的演讲中，Eric Pugh 将搜索引擎的需求与马斯洛的需求层次进行了比较。我认为这是一个非常有趣的比喻，它帮助拓展和激发了我每天与之谈论搜索的客户的想象力。

在他著名的图表中，亚伯拉罕·马斯洛概述了人类心理成长的五种状态。虽然你可能永远也不会带你的搜索引擎去见心理学家，但是让人类变得有效的东西和让你的应用程序的搜索变得有效的东西之间有很多相似之处。

下一次你设计或检查你的搜索引擎的设计时，在将它投入生产之前，一定要问自己以下问题:

# I .它是否做了它需要做的事情？

良好的搜索体验的基础是能够快速查询数据。

重要的是要指出，搜索引擎不是一个通用的数据存储。它旨在为您的数据创建一个索引，用于极其高效的查询。通过优化查询性能，当用户执行查询时，一个好的搜索引擎可以开始构建许多有趣的组合和推断。这实现了一种“模糊”匹配，以及一种可以被容易地调整和定制的排名，以最好地匹配终端用户真正试图寻找的内容。

这听起来可能有点复杂，的确如此。幸运的是，现在有很多很棒的开源搜索引擎，你可以在一个下午就开始玩。这些项目代表了几十年的专业知识和工程，特别是在搜索方面。开源搜索引擎将允许您下载并运行它，就在您的开发环境中。

底线，一个好的搜索引擎将有大量的特性和功能，为搜索优化。选择一个并将其集成到您的应用程序中是我们旅程的起点。

这是**功能**阶段。

# 二。靠谱吗？

因此，您已经将搜索引擎集成到您的应用程序中。恭喜你！

在某个时候，将这些新功能发布到产品中，并交付给客户和最终用户是有意义的。这意味着[学习](https://hackernoon.com/tagged/learning)搜索引擎的运作，以及如何确保一旦它启动并运行，它*保持*启动并运行。

如果你*真的*想深入这个领域，你可以在搜索引擎的运营和扩展上建立自己的事业。(我们当然有！)在当今以云为中心的世界，你不仅要学习系统工程和管理，还要学习计算机科学中具有挑战性的领域，比如分布式计算。

举几个有能力的运营工程师将涉及的主题为例，考虑以下内容:配置管理、不可变架构、应用程序打包、部署生命周期、数据备份和验证、监控和可观察性、警报、灾难恢复、容量规划、分片和数据分区、复制、共识算法、排队论、CAP 定理等等…

这里的目标是创建一个可靠的平台，当你在应用程序中构建越来越多的功能时，你可以信任它继续工作。

如果这一切看起来有点吓人，不要担心！云将提供！有很多质量管理的搜索供应商(完全披露:我们是其中之一！)很乐意与您合作，为您提供可靠的搜索服务。

这是**可靠性**阶段。

# 三。它吸引人吗？

作为一名产品开发人员，新产品生命周期中最激动人心的阶段之一就是首次发布。当你的想法、希望和假设最终符合实际使用的真实世界。一切正常吗？人们喜欢这个新事物吗？他们从中获得价值了吗？

当然，所有的工作都进入了第一步和第二步——特别是如果你走的是 DIY 的托管路线！—对于一些开发者在最初推出后的能量旗，可以理解。不要担心，因为最终用户会一直保持警惕，即使是这些编码骨干也可以期待最终的错误报告，一些明显的查询并没有完全返回预期的结果。

毕竟，如果搜索体验实际上并没有返回用户需要查找的内容，那么它有什么意义呢？

在这里，您需要考虑记录和观察集群实际运行情况的策略。当然，你会想要一些基本的操作指标，所以你可以回答这个问题，*这东西开了吗？*但从参与度的角度来看，更重要的是衡量*人们向搜索引擎*询问了什么。

对于一些人来说，这可能是对您的查询的一点额外记录。其他人可能会更进一步，实际上保存最终用户的查询来执行一些分析。

无论您采用何种方法，请考虑以下问题:

*   你最大的疑问是什么？随着时间的推移，这些查询是如何变化的？只要知道什么是流行的，以及这些趋势如何潮起潮落，就可以提供大量的商业价值，还可以转化为更好的应用程序设计。
*   没有任何结果的热门查询是什么？如果您的应用程序是为了保存查询而构建的，那么您也可以注意一下查询本身什么时候没有返回。理解这些查询是一个重要的信号，可以反馈到应用程序中搜索引擎的功能和实现的未来迭代中。

所以不要找借口！构建引人入胜的搜索体验比以往任何时候都更容易。从长远来看，在开始时对使用和参与进行一些投资会带来很大的回报。

这是**接合**阶段。

# 四。它达到目的了吗？

一般来说，将搜索整合到应用程序中是有目的的。一些企业甚至将其整个商业模式或市场差异化建立在他们的搜索体验之上！即使是最简单的内部搜索工具也能帮你的同事每周节省几个小时。

我的一个队友有一家大型电子商务公司的背景，这家公司每年收入数十亿美元。他喜欢讲述一个价值数百万美元的同义词变化的故事。总体而言，电子商务是一个很好的例子:考虑一下根据库存或利润率过滤或调整结果排序会如何影响底线。

即使与收入的联系不那么明确，搜索仍然可以服务于一个目的，不管是什么网站。一些使用我们服务的大规模社交媒体平台喜欢尝试主机搜索体验影响平均网站停留时间或每月活跃用户会话。

无论底层的业务用例是什么，在这个阶段，负责搜索的工程师或团队与业务 KPI 有一些联系或任务。这可能是购物车结帐率，或平均会话持续时间，或只是直接的收入预订。

达到这一层次的搜索需求的团队正在将所有先前的层次整合到一个反馈循环中；持续改进的良性循环。设定业务目标，分析参与模式，并对功能进行变更、发布和度量。冲洗，重复。

在这个级别，团队可以获得所有可用搜索功能[工具](https://hackernoon.com/tagged/tools)的专业知识。可靠性是努力维护的，但最终是事后的想法；现在感兴趣的是更高级的部署模式(比如分割测试)。敬业度模式得到了很好的理解，具备了良好的可观察性，并且趋势得到了定期审查。这一切都是为了促进新的更大目标的设定和实现。

这是你的搜索达到其**目的的阶段。**

# 动词 （verb 的缩写）它能在大范围内达到目的吗？

无论你是在为财富 1000 强企业建立搜索引擎，还是在刚起步的公司里发现了非常适合市场的产品:准备好迎接规模的挑战。

规模是一个有趣的想法，因为它意味着做很多很多次。随着搜索功能向越来越多的终端用户推广，这意味着会有很多很多的请求。这也可能意味着为许多不同的团队、微服务和部署环境支持许多集群。

有时，规模化经营会带来更多的监管和合规要求。建立一个安全的、可扩展的、符合 HIPAA 或 PCI 或 FINA 或 SOC 标准的搜索引擎有其自身的挑战、流程和实践。

从长远来看，没有一个放之四海而皆准的解决方案来扩展搜索。一些应用程序需要大量的查询，或者非常复杂的查询，或者两者都需要。有些需要存储多年的日志和分析数据，并使其可搜索。

当谈到规模时，拥有几年的经验总是有帮助的。

根据我们的经验，规模化经营的第一步是深入了解业务需求，以及搜索引擎的能力。有时会有大量的反复，因为应用程序的特性被设计成生活在两者的最佳交叉点上。

例如，当我们设计大规模性能时，我们遵循*做更少工作的原则。*搜索引擎都是将查询的复杂性转化为索引的生成，这样搜索请求本身就可以运行得非常快。知道在哪里留意不必要的额外工作是至关重要的，但这也需要转化为能够实现业务目标的实际解决方案。

在一定规模上，投资建立一个由搜索工程师和运营专家组成的完整团队是有意义的，尤其是在搜索任务至关重要的时候。然而，组织仍然可以从与密切合作伙伴的合作中获得很多价值，以管理更加商品化的集群操作的各个方面。

你现在已经达到了**规模**阶段。

# 不及物动词搜索是我的团队的竞争优势吗？

当有效实施时，搜索引擎几乎可以看起来通灵。然而，完全看不见。

考虑一下像 Amazon.com、网飞和 Pinterest 这样的网站。更不用说一个叫谷歌的小搜索公司了，也许你听说过他们？他们的搜索如此先进，似乎*甚至在我们做之前就已经得到了我们想要的东西。这些公司拥有庞大的团队，致力于打造卓越的搜索体验。他们正在拓展搜索的可能性。*

你可能永远也到不了这个阶段。没关系。如果你这样做了，搜索可以为你的竞争提供一个很大的优势。

如果你在这个阶段，你不需要我告诉你！

但是，因为这些进步可以帮助设计行业的其他发展方向，所以关注这些趋势无疑是有趣且有教育意义的。

今天可用的一个非常有趣且相对容易访问的项目是 Apache Solr 和 Elasticsearch 的 Learning to Rank 插件。这是一个由彭博开发的插件，用于将机器学习整合到搜索查询相关性函数的调整中。

在这一搜索领域，许多前沿工作都融入了对终端用户背景和偏好的更多了解。例如，考虑这样一个搜索请求，它理解用户的位置并能相应地调整自己。或者在结果排序中考虑用户的活动和偏好。随着时间的推移，实现更多的个性化和更好的互联网定制视图。

所以快去吧！你现在处于**创新**阶段。

# 搜索是复杂的。很好地解决基本问题会使其更容易扩展。

当你的用户在你的搜索引擎中输入一个查询时，他们希望它能够正常工作。永远不要让他们想起引擎盖下到底是什么，那是我们工程师经常喜欢居住的地方。

希望这种搜索的层次结构给了你开始探索自己的搜索之旅所需要的基础知识。你希望到达哪些地区？你需要在哪里支撑一些更基本的属性？

如果你只是不知所措，因为特别是搜索操作不适合你，或者你想借用我们多年的经验给任何级别的建议，你总是可以尝试一下 [Bonsai](http://www.bonsai.io) 。我们有一个超级方便的免费沙盒计划，供开发者测试他们的应用程序，并且自 2009 年以来一直支持和扩展数千家企业的搜索。