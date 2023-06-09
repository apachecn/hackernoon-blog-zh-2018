# 微软“墨水到代码”方法的 3 个严重缺陷

> 原文：<https://medium.com/hackernoon/3-serious-flaws-to-microsofts-ink-to-code-approach-7da65d4a9672>

![](img/d5239ae68d2f1a55a8cf4ce841a0468c.png)

PHOTO ILLUSTRATION: BILL OXFORD/ISTOCK

最近，微软宣布了另一个车库项目。这一次是用[墨水来编码](https://www.microsoft.com/en-us/garage/blog/2018/01/napkin-disrupted-meet-ink-code-microsoft-garage-project/)，它在一张纸上画一幅画，然后把它翻译成代码。听起来很神奇…很简单。有人在餐巾纸上画了一个界面，然后…嘭…我们得到了一个实用的应用程序。人们只能希望事情有这么简单。

我确实同情解决这个问题的尝试。让应用程序开发工艺民主化，让任何人都能解决自己的数字挑战，这是[鼓舞人心的](/outsystems-engineering/what-on-earth-are-we-doing-here-8c5b2f05cea7)。这种方法，尽管在实验和探索的精神下是一种有效的尝试，却有太多的缺陷。

生成应用程序的方法本身有 3 个严重的缺陷。

## **缺陷 1:图纸没有充分抓住意图**

在一张纸上画一组带有手写标签的盒子远不能揭示其背后的用例是什么。通过分析图画，可以得出一个盒子是一张卡片，或者一张桌子，或者一些控件，比如一个开关或者按钮。但是它应该做什么呢？数据从哪里来？

我们可以想象可能会有一个 IDE(开发环境，如微软的 Visual Studio 或 OutSystems 的 Service Studio)来获取绘图并将其转化为更有用的东西。但是有争议的是，它会产生任何有价值的东西，就像你从现代 IDE 中得到的东西一样，[拖放用户界面](https://www.outsystems.com/platform/) (UI)。

**画画的人不是程序员**。因此，如果一张图纸是不够的，开发人员需要将图纸变成一个有用的应用程序，那么除了灵感之外，从一张餐巾纸开始是否有任何真正的优势是有争议的。

## **缺陷 2:绘图需要一些应用程序 UI 设计的感觉**

为了使应用程序易于使用，它们需要在已知的模式中得到支持。假设一个有用的应用程序将是目标，要么画的人需要知道这些模式，要么算法不仅能够解释，而且能够将怪异的盒子翻译成用户能够使用的有效模式。那是一种延伸。

假设使用现代 IDE，某种带有模板和模式的工具箱将可用于浏览和配置，消除“开发人员”的[设计](https://hackernoon.com/tagged/design)负担和正确的模式选择工作，从而大大增加构建合理易用的应用程序的机会。

**画画的人不是设计师**。从餐巾纸中的盒子到人们可以使用的相应有效界面之间最常见的距离，要么需要一个巨大的跳跃，要么需要从一开始就有一些设计方向来指导绘图。

## **缺陷 3:绘图对用户界面有效，但对其他方面无效**

人们已经习惯了应用程序界面。许多人能够从以前看到的 UI 模式中得到灵感，画出一些盒子。但是很少有人习惯于建模业务逻辑或数据模型。一个没有业务逻辑或数据结构的应用程序不会有太大的作用。最多是个玩具应用。

更好的方法是将某种业务意图(以书面或口头形式)映射到具有相关业务逻辑的特定设计模式。这将变得更容易，因为通过了解实际的使用案例，It 可以将其映射到通常的解决方案。这些都是机器学习实际可以解决的问题。

**画画的人不是业务开发人员**。对于能够模拟业务逻辑的人来说，已经有了比在纸上画图更好的工具。

# **主要进场缺陷**

主要的，也是最基本的缺陷是强迫某人在没有任何帮助的情况下开发应用程序。完全是自由形式。这对画画的人或必须从画中做出东西的机器没有帮助。最近的进展可以让我们相信人工智能将解决这一切。但是这里有太多的跳跃，看起来就像一个有缺陷的方法。

减少应用程序开发时间的方法肯定是更接近自然界面，利用人工智能来连接各个点。但这个方向看起来不像是值得追求的东西。

继续努力，微软。