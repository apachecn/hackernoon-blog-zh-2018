# 超越应用:机器学习开发者入门指南

> 原文：<https://medium.com/hackernoon/beyond-apps-a-developers-guide-to-getting-started-in-machine-learning-39eec82a965d>

这是一系列博客文章的第一部分，详细描述了我作为一名移动应用程序开发人员进入[机器学习](https://hackernoon.com/tagged/machine-learning)的旅程。我主要是一名 iOS 开发者，我将 2018 年称为我的机器学习年。在决定开始这个过程之前，我被一些障碍和误解所阻碍，这些障碍和误解是关于这个领域到底是什么，需要做些什么才能开始，以及它会带来多大的范式转变。我希望在这篇博客文章结束时，你会对开始机器学习的一些主要考虑因素有所了解，以及你开始自己的旅程的可能的下一步。

作为开发人员，我们已经能够使用移动应用解决许多问题:社交网络、账单支付、食品订购和交付、电子商务等等。机器学习为我们提供了各种机会来解决非常独特类型的问题。例如，机器学习可以应用于商业，以预测股票价格，或应用于体育管理，以预测克利夫兰·布朗队接下来应该选哪个四分卫，或应用于医疗保健，以预测疾病爆发，并对扫描和 x 光等图像数据进行自动分类。(或者在其他情况下:[识别热狗](/@timanglade/how-hbos-silicon-valley-built-not-hotdog-with-mobile-tensorflow-keras-react-native-ef03260747f3)！)

现在谈谈这篇文章的关键:作为一名开发人员，我如何开始学习机器？当考虑机器学习领域时，我想到了几个问题。其中包括:

*   机器学习入门需要数学吗？多少数学？
*   我应该使用什么语言？
*   应该学什么框架？
*   应该用什么开发调试工具？
*   我应该学什么课程？

让我们来逐一回答这些问题。

**我需要数学来开始机器学习吗？**

简答: ***是*** 。数学很重要。然而，这可能是一个错误的问题。广泛理解高级(“超越基本”)数学概念的相关性实际上取决于你进入该领域的目标是什么。如果您有兴趣采取更学术的方法，学习高级数据科学，研究和构建底层算法和框架，那么是的，对线性代数、统计学、多元微积分和其他数学概念的扎实理解是绝对必要的。如果你的目标主要是开始学习机器学习/数据科学，以便在行业中工作(入门级)，那么并不需要深入的数学知识。基本掌握大一水平的统计学就足够了，因为你将能够利用许多现代机器学习工具/库来做数学。当然，擅长数学通常会对你有所帮助——这里的关键信息很简单，这不是机器学习入门的要求。就像一个 iOS 开发人员可以利用 AFNetworking 库来制作一个功能强大的应用程序，而不知道库的内部工作原理一样，作为一个机器学习开发人员，你也可以利用各种库来进行计算。当你开始 iOS/Android 开发时，你可能没有从零开始学习如何构建自己的网络库，那么为什么要采用机器学习的方法呢？随着时间的推移，你可以不断地学习和掌握更多的数学知识。

也就是说，图书馆不是一切。作为一名开发人员，你将不得不知道机器学习中不同类型的算法，包括何时以及如何使用它们来解决问题，以制作优秀的产品；就像你现在在移动开发中所做的一样。没那么糟吧。

**如果数学不是真正的先决条件，那什么是呢？**

数据分析。作为初学者开始掌握机器学习所需的最重要的技能是收集数据、准备数据、探索数据和可视化数据的能力。有几种资源可以帮助您加快数据可视化、数据争论和数据聚合的速度。例如，如果你正在用 Python 进行机器学习，你应该考虑学习基础 Python、Pandas 或 Matplotlib。精通数据分析将为您创建生产就绪的机器学习产品做好充分准备。

我应该使用什么语言？

在移动开发中，平台限制了我们开发可用的语言:iOS 版的 Swift 和 Objective C，Android 版的 Java 和 Kotlin。然而，在机器学习中，我们面临着一个微妙的对话，即我们应该专注于哪种语言:Python？还是 R？还是 C/C++？互联网上可能有几种观点认为某种特定的语言最适合机器学习。也许 Python？然而，这种断言不应被视为事实。这实际上取决于几个因素，比如你的背景(你已经熟悉哪些语言)，更重要的是，你想解决什么问题。语言只是达到目的的一种手段。尽管如此，根据我目前的经验，Python 似乎是机器学习中最容易上手的语言。它是最流行的语言，有很多库和活跃的开发者社区。

**应该学习哪些框架？**

选择一个框架是机器学习中最具挑战性和最令人困惑的方面之一，因为有太多非常好的选择可供挑选。然而，就像决定使用哪种语言一样，决定使用哪种框架仍然取决于您希望解决的问题类型。真正做到这一点的最好方法是阅读各种可用的选项、它们的优点、缺点和最常见的用例。比较流行的一些框架有 TensorFlow、Torch、Caffe2、Keras、微软 CNTK、亚马逊机器学习、Theano 等。例如，TensorFlow 是由 Google Brain 团队开发的一个开源项目，最适合研究和开发。Caffe2 是一个轻量级、模块化、可扩展的[深度学习](https://hackernoon.com/tagged/deep-learning)框架，由脸书开源。Caffe2 非常适合建筑应用；Caffe2 网站上有很多快速入门教程，可以帮助你相当快速地开始开发。到目前为止，在我的过程中，我已经看到了几篇文章，它们揭示了在选择正确的框架时需要考虑的事情。你可以看看这些，以便进一步阅读:

*   [选择开源机器学习库:TensorFlow、Theano、Torch、scikit-learn、Caffe — Altexsoft](https://www.altexsoft.com/blog/datascience/choosing-an-open-source-machine-learning-framework-tensorflow-theano-torch-scikit-learn-caffe/)
*   [十大机器学习框架—提示云](https://www.promptcloud.com/blog/top-10-machine-learning-frameworks)
*   [看看流行的机器学习框架——red monk](http://redmonk.com/fryan/2016/06/06/a-look-at-popular-machine-learning-frameworks/)

**我应该使用什么开发和调试工具？**

这对我来说不是一个特别大的问题。根据您决定使用的语言，有几种很好的机器学习开发和调试工具。例如，如果您正在使用 Python，一些流行的开发环境是 PyCharm、PyDev、Spyder 和 Jupyter Notebook。像 Visual Studio Code 或 Atom 这样的基本文本编辑器也可以做到这一点。你可以试一试，看看你更喜欢哪一个。当然，这同样适用于其他语言。

**我应该选什么课程？**

有很多关于机器学习的课程、文章、演讲和书籍；有些是给初学者的，有些是给更高级的人的。由于我们的重点是从这里开始，我将重点介绍一些迄今为止我认为非常有用的课程和资源。他们在这里:

*   [机器学习——斯坦福大学&Coursera——吴恩达](https://www.coursera.org/learn/machine-learning):这门课程是迄今为止机器学习初学者最值得推荐(并且好评/极好)的课程。吴恩达教授是机器学习领域的杰出人士，因其在谷歌大脑项目中的关键作用而闻名。他是 Coursera 的联合创始人，也是百度的首席科学家和副总裁。本课程包括视频、阅读材料、测验和课堂作业。如果你仍然担心你的数学背景，这门课程还教授必要的数学主题，如线性回归和线性代数。自定进度且自由自在！10/10
*   [机器学习介绍——uda city](https://www.udacity.com/course/intro-to-machine-learning--ud120):由凯蒂·马龙和塞巴斯蒂安·瑟恩教授的 10 周免费课程。就像每一门 Udacity 课程(nanodegree)一样，这门课程的结构非常好。我还没有上过这门课，但是评论非常鼓舞人心。

如果你喜欢采取更渐进的方法，你可以专注于将机器学习集成到你的 iOS 和 Android 应用程序中。这里有一些资源可以帮助你迅速进入状态:

iOS 开发者:

*   [介绍核心 ML](https://developer.apple.com/videos/play/wwdc2017/703/)
*   [岩心深度 ML](https://developer.apple.com/videos/play/wwdc2017/710/)
*   [愿景框架:建立在核心 ML 之上](https://developer.apple.com/videos/play/wwdc2017/506/)
*   [自然语言处理和您的应用](https://developer.apple.com/videos/play/wwdc2017/208/)

Android 开发者:

*   [谷歌对机器学习的愿景](https://www.youtube.com/watch?v=Rnm83GqgqPE)
*   [无处不在的机器学习:智能安卓应用——Margaret Maynard-Reid](https://academy.realm.io/posts/360andev-margaret-maynard-reid-making-android-apps-with-intelligence/)
*   [神经网络 API](https://developer.android.com/ndk/guides/neuralnetworks/index.html)
*   [Android 神经网络 API 示例](https://github.com/googlesamples/android-ndk/tree/master/nn_sample)

感谢你花时间阅读这篇文章！我希望这有助于你揭开机器学习的神秘面纱，这样你就可以开始为世界问题创造惊人的智能解决方案。祝您机器学习之旅愉快！❤

**延伸阅读:**

*   [机器学习的真正先决条件不是数学，而是数据分析——Sharp Sight Labs](http://sharpsightlabs.com/blog/machine-learning-prerequisite-isnt-math/)
*   [机器学习的数学——Wale akin faderin](https://towardsdatascience.com/the-mathematics-of-machine-learning-894f046c568)
*   [机器学习最好的编程语言是什么？——克里斯蒂娜·沃斯科格鲁](https://towardsdatascience.com/what-is-the-best-programming-language-for-machine-learning-a745c156d6b7)
*   [机器学习的最佳编程语言——杰森·布朗利](https://machinelearningmastery.com/best-programming-language-for-machine-learning/)
*   最流行的机器学习语言是……—Jean Francois Puget
*   [十大机器学习框架—让大数据变得简单](http://bigdata-madesimple.com/top-10-machine-learning-frameworks/)
*   [机器学习能做什么，不能做什么——马克·博伊德](https://thenewstack.io/what-machine-learning-can-and-cant-do/)
*   [面向初学者的 8 个有趣的机器学习项目——elitedata science](https://elitedatascience.com/machine-learning-projects-for-beginners)