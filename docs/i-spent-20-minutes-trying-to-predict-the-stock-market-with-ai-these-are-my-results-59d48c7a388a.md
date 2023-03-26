# 我花了 20 分钟试图用人工智能预测股票市场——这是我的结果

> 原文：<https://medium.com/hackernoon/i-spent-20-minutes-trying-to-predict-the-stock-market-with-ai-these-are-my-results-59d48c7a388a>

![](img/a76f683d0a1af26884f221402f5094f7.png)

这一切都始于 7 月份我应邀在一个人工智能金融技术论坛上发言。对我来说，这是一个向新观众介绍[机器盒子](https://machinebox.io/?utm_source=Medium&utm_medium=Post&utm_campaign=Predicting%20the%20stock%20market)的绝佳机会。但是有一个问题。我对金融科技一窍不通！

我的第一个想法是，“谷歌机器学习在金融科技中的用例”。我照做了。结果主要是关于异常检测和欺诈防范。机器学习的伟大用例，但这是一个已经解决的问题。鉴于这是一个关于金融科技中人工智能的论坛，我想已经有很多来自异常检测专家的演讲了。

Machine Box 的[关键用例之一是人脸检测](https://blog.machinebox.io/face-verify-js-monitoring-who-is-looking-at-a-website-for-additional-security-1d6025a8fedd)，只使用一张人脸照片进行训练。我们已经有几个客户在使用 [Facebox](https://machinebox.io/docs/facebox?utm_source=Medium&utm_medium=Post&utm_campaign=Predicting%20the%20stock%20market) 来验证身份，所以我想我应该在这和确保当面信用卡交易之间建立联系。

![](img/3ed0c289bfb25ba3bee4ce957ea44c5b.png)

[https://www.webroi.ca/blog/tag/boring-industry/](https://www.webroi.ca/blog/tag/boring-industry/)

BOOORRINNNNGG！

是的，面部识别可以用来确保交易安全，我最后确实谈到了如何使用 [Facebox](https://machinebox.io/docs/facebox?utm_source=Medium&utm_medium=Post&utm_campaign=Predicting%20the%20stock%20market) 和其他方法来实现这一点，但我不认为它真正谈到了用更好的工具简化机器学习的问题，而这正是我们所要做的。

> [如果你关注我的帖子](http://aaronedell.com)，那么你会知道我经常使用[预测股市](https://hackernoon.com/what-machine-learning-isnt-d70f8a48d1ce)作为如何不使用机器学习的主要例子。股票市场是一个高度复杂的多维度的怪物，具有复杂性和相互依赖性。不是尝试机器学习的好用例。

但是……如果你**可以**用机器学习预测股票市场会怎么样？

处理这类事情的第一步是尽可能地简化问题。我决定把它变成两类问题；给定一些输入，市场要么上升，要么下降。我把 T2 的市场限定在道琼斯工业平均指数。

什么是好的输入？我决定，有点武断，新闻标题作为输入。我会使用自然语言处理对一天中尽可能多的新闻标题训练一个分类模型,将它们分成两类；头条新闻之后市场上涨，或者，市场保持不变或者下跌。

现在是最困难的部分；收集数据。

> 非常幸运的是，快速的谷歌搜索显示了这个优秀的数据集。这是一个巨大的新闻标题表，标有道琼斯当天的表现。

因此，在这个过程开始 5 分钟后，我就有了一个很棒的数据集和一个计划。接下来是处决。

因为我的开发人员技能极其有限，所以我决定让自己轻松一些，使用 [**这个工具**](https://github.com/machinebox/toys/tree/master/textclass) ，它遍历文件夹寻找标记数据，然后自动训练[分类框](https://machinebox.io/docs/classificationbox?utm_source=Medium&utm_medium=Post&utm_campaign=Predicting%20the%20stock%20market)。但为了在文件和文件夹上运行它，我必须首先将数据集转换成许多包含标题文本的小文本文件，并将它们放入标记为 0 或 1 的文件夹中，以指示道琼斯指数的向上或向下移动。这是我用来为真正的开发人员执行可能不必要的任务的脚本。

修改数据集的脚本大约需要 10 分钟。运行它花了不到 30 秒。下一步是对包含所有数据的文件夹运行`Textclass`。这将通过随机选择 80%的数据来训练[分类框](https://machinebox.io/docs/classificationbox?utm_source=Medium&utm_medium=Post&utm_campaign=Predicting%20the%20stock%20market)。然后，它将使用剩余的 20%来验证模型。

结果；54%的准确率。

通常，这么低的精确度意味着你的模型没有用。你需要大约 80%的投入，才能让模型在现实世界中开始有意义。但是，当我告诉一屋子的金融人士，只有模型*有 54%的准确率时，我以为会有人笑，相反，我的表情非常严肃。几秒钟后，有人小声说，“你可以卖出 4%”。*

也许吧。但我的结论是，新闻标题无法预测道琼斯指数，至少就我所拥有的数据集而言。我强烈建议你试一试，看看你会得到什么结果。可能是我剧本写错了！