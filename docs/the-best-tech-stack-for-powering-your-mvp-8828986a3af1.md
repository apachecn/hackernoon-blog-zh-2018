# 为您的 MVP 提供动力的技术堆栈考虑因素

> 原文：<https://medium.com/hackernoon/the-best-tech-stack-for-powering-your-mvp-8828986a3af1>

![](img/3e8bd6e674766d0c3f70d920a59f4fc7.png)

你不必费力地在一大堆时髦词汇中寻找适合你创业的技术。当你准备好[向工程师](https://hackernoon.com/how-do-i-present-my-app-idea-to-an-engineer-9da92657fb9c)展示你的应用程序时，你还需要考虑你最终会使用什么技术。

这是创始人最容易被[炒作驱动的发展](https://blog.daftcode.pl/hype-driven-development-3469fc2e9b22)所迷惑的时候。问一个“催眠师”,你一定会听到当前的本周 JS 框架是你创业的唯一*选择；问一个程序员的程序员，你会得到一些关于榆树或仙丹的道德说教。我相信这些都有它们的定位，但我怀疑它们中的任何一个都不是你创业的最佳选择。*

十多年来，我一直致力于技术的商业方面，包括帮助初创公司开发他们的 MVP。由此我可以说几个每个创始人都应该考虑的简单点:

1.  采用成熟的技术；随着你的成长和需要更多的开发人员，这将是招聘的关键。
2.  不要多此一举；在创业初期，获取客户是第一位的，而不是新技术的 R&D。

这让我有了一个老的备用和一个新的作为我的第一推荐来构建一个 MVP: Ruby on Rails，用于你的后端和基于浏览器的应用，带有 React Native，用于 iOS 和 Android 应用。这种组合拳让你更快更便宜地进入市场，给你一套强大的工具来打造一个伟大的产品。

# Ruby on Rails 具有商业意义

Ruby on Rails 在开发人员中拥有强大的追随者。清晰的最佳实践使编写和调试代码变得更快更容易。一个健壮的社区意味着 ruby 爱好者可以从大量的开源宝石中吸取经验来加速开发。

除了拥有快乐的开发人员的好处之外，所有这些对企业都很重要。事实上，与类似的技术相比，使用 Ruby on Rails 开发要快 30 %- 40%。开发人员的时间通常是创建 MVP 的最大开销，因此不言而喻，减少三分之一或更多的时间对创业公司非常重要。Martin Fowler 对 Ruby 和企业巨头 Java 和。不出所料，大多数项目经理都说用 Ruby on Rails 构建项目更快。

节省开发成本不仅仅是对资金紧张的初创公司而言。老牌公司也用 Ruby on Rails: Basecamp、GitHub、Airbnb、Hulu。Ruby on Rails 不可伸缩的神话到此为止！Rails 用更少的时间、更少的钱完成工作，让您的开发人员满意。

![](img/8d94ac3dbfe3cef048817d790a831a8d.png)

Photo by [Artem Sapegin](https://unsplash.com/photos/ZMraoOybTLQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/reactjs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# React Native 是移动应用之王

精益创业公司削减开发成本的另一种方式是对 Android 和 iOS 应用程序都使用 React Native。当你用 React Native 创建 Android 和 iOS 应用时，你可以重用 90%的代码。您仍然可以获得真正的原生应用程序带来的提升。

React Native 不仅仅适用于希望将自己的第一款应用推向市场的创业公司。脸书、Instagram、Skype、优步、沃尔玛和[等大公司使用 React Native](https://facebook.github.io/react-native/showcase.html)

# Ruby on Rails 并不总是答案

对工具的真正掌握是知道什么时候特定的工具不是最好的选择。因此有句古老的谚语说，如果锤子是你唯一的工具，那么一切看起来都像钉子。Ruby 很棒，但是当 Rails 不是很合适的时候，我们还有其他锦囊妙计。

我们有一个超高负载应用程序处理数百万个请求的客户端。Golang 显然是最好的选择，所以我们用 Go 编写了这个应用程序。我们的一名工程师甚至设计了一个方案，用 Rails 对 Go 应用进行[黑盒测试。](https://medium.freecodecamp.org/how-to-black-box-test-a-go-app-with-rspec-421e786f4103)

其他项目需要机器学习和高级数据科学。Python 更适合这种情况，所以我们的很多项目都使用 Python。

我们也用遗留代码继承项目，尤其是 PHP。在大多数情况下，用 Ruby 从头开始重新发明轮子和重写代码库没有商业意义。最常见的情况是，当客户向我们提出为他们的 Magento 商店添加新功能时。虽然我们建议客户[在 Solidus 或 Spree](https://eteam.io/blog/magento-vs-spree-vs-solidus/) 中开设新店，但是将现有的 Magento 商店迁移到这两个平台几乎没有意义。

# 要考虑的其他框架

Rails 肯定不是唯一的框架。在您进行尽职调查时，值得看一看其他一些竞争者。Laravel，Django 和后端有 Node 的全 JavaScript 应用都是创业公司的合法选择。

探索其他选择没有坏处，但是最终这些框架会产生或多或少相同的结果。Rails 仍然有一个优势，那就是构建起来更便宜、更快。

# 技术仍然只是一种工具

最终，你会建立一个有利可图的企业。技术是让你达到目的的工具，但不要为了技术而陷入技术的陷阱。你的终端用户不会关心你的技术是否时尚；他们所关心的是你的应用程序实际上是有效的。

从商业角度来看，你需要确保你的技术支持你的需求，在成本和扩张计划方面是可维护和可持续的。这就是为什么后端的 Ruby on Rails 加上带有 React Native 的 iOS 和 Android 应用程序是大多数创业公司的出路。

最初发布在 [eTeam 的博客](https://eteam.io/blog/ruby-on-rails-and-react-native/)上。