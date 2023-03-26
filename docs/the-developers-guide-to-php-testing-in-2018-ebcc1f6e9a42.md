# 2018 年 PHP 测试开发者指南

> 原文：<https://medium.com/hackernoon/the-developers-guide-to-php-testing-in-2018-ebcc1f6e9a42>

![](img/7a30445f787ca98fd11d39b593acfeb5.png)

## 由工程师[**顺便提一下:**](https://medium.com/u/d8be98cdccce#和 Perl 的尊重，PHP 似乎一直是一种事后的想法。现在，几乎每个专业的静态代码分析服务都支持 PHP。</p><p id=) [Codacy](https://www.codacy.com/) 支持 PHP 作为他们产品的一部分。

# 作为一等公民进行测试

我相信 2018 年 PHP 中测试的状态极其积极的最后一个原因是，测试是一等公民。我这样说有很多原因。

首先:所有领先的框架都支持它。从 Laravel、Zend Expressive 和 Symfony，到 Phalcon、Yii、FuelPHP 和 Aura(仅举几例)都使得测试基于它们的应用程序变得非常容易。

**其次:**因为关于测试的讨论。如果你参加任何大型会议，无论是社区会议还是商业会议；如果你去 PHP 聚会；如果你阅读 PHP 杂志和博客，你会读到、看到和听到关于测试的内容。

无论您是 PHP 新手还是测试新手，无论您是经验丰富的开发人员还是测试人员，都有适合您的内容。你总是有机会开始，以及进一步建立你的技能。

我同意，在 PHP 中测试曾经被许多人认为是事后的想法。然而，那些日子已经一去不复返了。

# 这是许多人辛勤工作的证明

PHP 的测试水平已经有了长足的进步，至少在我看来，和其他语言一样好。也就是说，如果没有社区中许多人的辛勤工作，它就不会有今天，特别是[克里斯·哈特杰斯(坏脾气的程序员)](https://twitter.com/grmpyprogrammer)和[塞巴斯蒂安·博格曼(PHPUnit 的创始人)](https://sebastian-bergmann.de/)。

![](img/b132eaf20060eb699df63e6163c170a1.png)

先说 Chris(我把 Chris 当朋友算，所以有点偏心)。多年来，他一直鼓励 PHP 开发人员(以及所有软件开发社区的开发人员)测试他们的代码。

无论是通过[他的书](https://grumpy-learning.com/)，他的[多视角课程](https://www.pluralsight.com/courses/play-by-play-chris-hartjes)，他的会议演讲，还是他的[推特存在](https://twitter.com/grmpyprogrammer)，他长期以来一直是更好测试的不知疲倦的倡导者。从个人经验来说，如果没有他的鼓励，我知道我不会像现在这样频繁地测试我的代码。

![](img/d9a240ff9ceb122772df7727b5d1e9eb.png)

对于 Sebastian，你能说什么呢，他创造了 PHP 事实上的测试工具 PHPUnit。

如果不是因为 PHPUnit 的安装、配置和使用非常简单，而且它的特性非常丰富和灵活，我认为测试他们代码的 PHP 开发人员会比现在少得多。

更重要的是，PHPUnit 为它之后的许多测试工具和框架奠定了基础，包括 Codeception、more 和 Behat。我并不是说它直接做到了这一点，而是说它帮助实现了这些目标。

Sebastian 是一个不知疲倦的测试倡导者，无论是通过写作、会议演讲，还是他倡导的所有其他测试方式。所以感谢这两位杰出的人。我知道我们都因为他们的持续努力而变得更好。

# 就这样结束了

这是对当今 PHP 测试状态的一个概括。虽然许多人很快预测 PHP 的消亡，但我相信现在是使用这种语言的最佳时机之一。

除了和其他语言一样丰富和成熟的语言之外，PHP 的测试基础设施使得开发丰富和经过良好测试的应用程序成为可能。

无论您是使用 PHP 的事实工具 PHPUnit，还是其他工具，如 Behat、Codeception 和 Mockery，或者您是组合使用它们中的几个，您都有很多高质量的选择。

再加上测试需求的减少，因为 PHP 采用了静态类型提示，那些过去讨厌 PHP，声称它是一个双头锤子的人应该重新审视那些以前的假设，看看它们不再有效。

如果你是 PHP 新手，你使用过哪些测试工具，有哪些成功之处？如果你是个老手，你会使用哪种工具的什么组合，为什么？

[了解更多关于 Bitbucket 代码审查的信息](https://www.codacy.com/bitbucket-code-review/)

> 这篇文章最初出现在[博客](http://bit.ly/2MeFqJq)上。Hacker Noon 的每周赞助商 Codacy ，自动对每一个提交和拉取请求进行质量标准和代码审查，这样你就可以在每两周的 sprint 中提前两天发货。[使用 code HACKERNOON 享受八五折优惠。](http://bit.ly/codacyhackernoonblogpostjune)