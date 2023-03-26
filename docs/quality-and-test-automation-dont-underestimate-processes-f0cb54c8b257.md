# 质量和测试自动化:不要低估过程

> 原文：<https://medium.com/hackernoon/quality-and-test-automation-dont-underestimate-processes-f0cb54c8b257>

![](img/6669322d27d93b811e56f992c633ea3f.png)

Photo by [Chunlea](https://unsplash.com/photos/HN3-ehlNwsc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

## 关于质量和错误解决方案的真实故事。

*Cet article est*[*également dispén Fran is*](https://jp-lambert.me/qualit%C3%A9-et-tests-automatis%C3%A9s-ne-sous-estimez-pas-les-process-55f2f3000adb)*。*

我绝不否认测试自动化的有用性。我是测试自动化的强烈拥护者。然而，这个话题经常被从错误的角度讨论。下面是一些例子！

# 我们需要帮助来开始测试自动化！

一个团队寻求我作为*测试主持人*的建议。他们不能控制回归。他们来找我，以便我可以帮助他们定义一个测试自动化策略。

我向他们推荐什么？

> 在与他们讨论了解情况后，我的建议主要是关于流程:

*   要被认为是有效的，一个错误修正必须伴随一个自动测试，如果没有这个错误修正，测试就会失败。(一个单元测试就足够了)
*   它们的分支寿命很长，在合并之前要花太多时间。要么他们切换到另一个[分支模型](https://jp-lambert.me/git-gitflow-and-continuous-integration-for-dummies-5e4300148fbf)，要么他们彻底检查他们当前的分支模型并严格遵循它。
*   他们的释放过程并不清楚，最重要的是，他们并没有真的假设。他们什么时候检查没有回归？怎么会？他们需要正式化他们的过程并严格遵循它。

只有当他们解决了这些基本问题，我们才会开始谈论测试自动化。

# 自动化测试是我们所有问题的答案！

啊！这句话我听了太多次了…

> 来吧，我们真的要自动化这些测试！在那之后我们所有的问题都会消失。

的确，*是的*，自动化这些非回归测试将会是一个好主意。顺便说一句，这是一个如此好的主意，我在想:为什么还没有完成呢？

我仍然很难过地告诉你，不，自动化测试不会神奇地解决你所有的问题。

> 即使有很好的非回归测试工具，你也必须不断挑战产品。

测试自动化太棒了。但这不是灵丹妙药。测试自动化并没有标志着手工测试的消亡。更糟糕的是，有了测试自动化，你仍然需要*思考*，建立*策略*，使用产品。

# 严谨足以解决大多数问题

太好了。*过程多么无聊。没人在乎。我们为什么要烦恼？*

*哎哟。*

然而过程——并遵循它们！—[是运营良好的团队](https://hackernoon.com/stop-doing-agile-under-perfusion-497f716218f7)的关键。正如我喜欢说的:

> 敏捷要求极其严谨。

[](https://hackernoon.com/stop-doing-agile-under-perfusion-497f716218f7) [## 灌注下停止做敏捷！

### …不要再表现得像英雄一样！

hackernoon.com](https://hackernoon.com/stop-doing-agile-under-perfusion-497f716218f7) 

当我谈到过程时，我并不期望复杂的决策图消失在公司的内部网中。不，过程可以是简单的、可视化的、可理解的……最重要的是，[它必须符合团队的工作方式](https://jp-lambert.me/un-process-est-un-outil-pas-une-contrainte-dd228b22d31c)。

[](https://jp-lambert.me/un-process-est-un-outil-pas-une-contrainte-dd228b22d31c) [## 一个过程是一个结局，而不是一个矛盾

### 注释 faire for que vos process solent plus qu ' une doc！

jp-lambert.me](https://jp-lambert.me/un-process-est-un-outil-pas-une-contrainte-dd228b22d31c) 

## 过程>测试自动化

我们长话短说吧。人们喜欢和我谈论测试自动化，因为他们认为这是一个银弹。

不幸的是，事实并非如此。嗯，*【不出所料】*会是更好的选择。你现在应该更清楚:银弹根本不存在。

> 什么是好的自动化测试？

如果你不知道什么是好的手工测试，你甚至不知道。

> 自动化测试成本太高了！我们应该从哪里开始？

那么，为什么不从最关键的场景开始呢…我猜你知道它们，对吗？

> 我们害怕退步！我们必须自动化测试。

你为什么害怕退步？

如果你不信任你的非回归测试场景，自动化它们将没有任何好处。你已经定义了那些测试场景了吗？

如果您没有手工运行非回归测试场景，那么回归显然不是什么大事。你确定你害怕吗？

> 我们没时间自动测试了！

如果您浪费时间手工运行非回归测试场景，您会找到时间。计算投资回报既简单又直接。

因此，情况看起来没那么糟…

总的来说，事情是这样的:

> 人们问我关于质量和不回归的指导，人们想被告知关于测试自动化，我的回答是关于过程和严谨性。

从彻底检查你的流程开始，*并严格遵循它们*。仅凭这一点，你就能走得非常远！以我帮助的这个团队为例:

[](https://jp-lambert.me/our-recipe-for-daily-releases-fdbda205cc1f) [## 我们每日发布的食谱

### 曾几何时，一个团队努力释放…

jp-lambert.me](https://jp-lambert.me/our-recipe-for-daily-releases-fdbda205cc1f) 

# 喜欢这篇文章吗？秀出来！

*请* ***鼓掌*** 👏****分享*** *文章！因为有你，我才全身心地投入写作。**

*****关注*** *我上* [***我的博客***](https://jp-lambert.me/) *当我发布新文章时通知我！***

***非常感谢！***