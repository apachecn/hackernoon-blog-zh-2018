# DL 和统计学的区别

> 原文：<https://medium.com/hackernoon/a-difference-between-dl-and-statistics-c3939b999f75>

![](img/707a3d5c7986f4983faa37d9b4216b00.png)

Photo by [Robert Anasch](https://unsplash.com/photos/h7dl6upIOOs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/connection?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

我喜欢读研的一个原因是无休止的创新在走廊里回响。当然，我坐在隔间里兼职编码打发时间，但也有人们走出洞穴与周围人交谈的时刻。

这些结构化的方法之一是通过每周更新会议，以及我们激发了这篇文章的许多对话之一。

让我们从统计数字开始。

统计学的主要焦点之一是创建一个可概括的模型，如线性或多元回归模型，以最佳地拟合数据来代表您正在研究的模式。

统计学中还有其他关键主题，如统计显著性、[相关性与因果关系](/me/stats/post/c9cdf4fc6563?source=main_stats_page)、概率论和模型评估，与[机器学习](https://hackernoon.com/tagged/machine-learning)社区共享。然而，我们喜欢抽象，所以现在让我们坚持回归。

线性回归功能强大，易于理解。然而，与每个数据点相关联的误差被累积，使得该点的固有性质失去，而目标是最小化全局损失/误差。这是一个明显的权衡。

现在，不要误会，[深度学习](https://hackernoon.com/tagged/deep-learning)系统仍然试图最小化整个系统的损失，并且有许多聪明的方法来完成这些优化。

然而，**将**深度学习方法与上述方法(尽管有许多方法)区别开来的一点是，现在我们可以扰动模型，以了解是什么驱动算法影响其决策。

我们可以通过逐点调查看到*个体* *层面*上的特性的重要性，而不是依靠统计在全局层面上进行概括。

我们现在可以窥视复杂的黑匣子，人们肯定已经开始这样做了。

![](img/965daeab26a9d15594002f4e25c951cd.png)

Photo by [Dhruv Deshmukh](https://unsplash.com/photos/N9UuFddi7hs?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/black-cube?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

现在有更多的[方法](https://github.com/marcoancona/DeepExplain)出来剖析驱动模型决策的激活或神经元。这些在图像应用程序中是视觉上有用的，因为我们可以实际看到哪些部分帮助了模型。

此外，这些方法可用于更好地理解模型的性能，即模型在哪里预测正确，模型在哪里出错，模型在哪里从噪声中学习。或者最重要的是，该模型可以通过揭示以前认为不重要的重要特征来推动新的发现。

也许深度学习可以成为一种工具，用于进一步了解像神经退化或时间敏感领域的问题**以及预测工具**。

*激活探索*可以通向下一个前沿或科学发现。

感谢阅读。