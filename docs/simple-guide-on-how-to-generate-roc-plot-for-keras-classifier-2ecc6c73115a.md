# 如何为 Keras 分类器生成 ROC 图的简单指南

> 原文：<https://medium.com/hackernoon/simple-guide-on-how-to-generate-roc-plot-for-keras-classifier-2ecc6c73115a>

![](img/a459955bb3effb0d3ea2afa7ed0e6217.png)

阅读本指南后，您将知道如何通过 ROC 和 AUC 评估 Keras 分类器:

*   为二元分类器生成 ROC 图；在此过程中应用交叉验证。
*   计算 AUC 并使用它来比较分类器性能。
*   将 ROC 分析应用于多类分类。创建 ROC 来评估单个类和整体分类性能。

***完整*** [***源代码***](https://github.com/Tony607/ROC-Keras) ***本教程可在我的 GitHub 上获得。***

# ROC 和 AUC 是什么，能做什么？

*它们是什么？*

来自维基百科:**接收器工作特性**曲线又称 ROC 是一个图示，说明了[二元分类器](https://en.wikipedia.org/wiki/Binary_classifier)系统在区分阈值变化时的诊断能力。这里的临界点是“二元分类器”和“可变阈值”。我还将向您展示如何通过一对一方法绘制多标签分类器的 ROC。

**曲线下面积**，又名 AUC 是该面积在该 ROC 曲线下的百分比，范围在 0~1 之间。

他们能做什么？

ROC 是可视化二元分类器性能的一种很好的方式，AUC 是通过评估关于两个类别分离的排名来总结分类器性能的一个单一数字。越高越好。

在接下来的两节中，我将向您展示如何绘制 ROC 并计算 Keras 分类器的 AUC，包括二元和多标签分类器。

# 二元分类器的 ROC，AUC

首先，我们用 Sklearn 的 **make_classification()** 函数生成一些训练/测试数据。

接下来，让我们像往常一样构建和训练一个 Keras 分类器模型。

然后，我们对保留的测试数据调用 **model.predict** 来生成概率值。之后，使用概率和地面真实标签生成绘制 ROC 曲线所需的两个数据数组对:

*   fpr: **每个可能阈值的假阳性率**
*   tpr: **每个可能阈值的真阳性率**

我们可以调用 sklearn 的 **roc_curve()** 函数来生成这两个。下面是实现它们的代码。

AUC 值也可以这样计算。

为了使图看起来更有意义，让我们训练另一个二元分类器，并在同一图中稍后将其与我们的 Keras 分类器进行比较。

现在，让我们为这两个分类器绘制 ROC 图。

结果如下:

![](img/c42d1069ac28caab51ae1617089ef63a.png)

如您所见，给定 AUC 度量，Keras 分类器优于其他分类器。

# 类别分类器的 ROC，AUC

ROC 曲线延伸到三类或更多类的问题，这就是所谓的一对多方法。

例如，如果我们有三个类，我们将创建三条 ROC 曲线，

对于每一个类，我们将其作为正类，其余类共同分组为负类。

*   1 级与 2 级和 3 级
*   2 级与 1 级和 3 级
*   3 级与 1 级和 2 级

让我们从创建一些带有 3 个类输出的训练/测试数据开始。

然后，我们像以前一样建立和训练分类 Keras 分类器。

训练模型后，我们可以用它来预测测试输入，并绘制 3 个类别的 ROC 图。

在此之前，让我们定义度量标准来评估所有类的整体性能。有两个略有不同的指标，微观和宏观平均。

在“微平均”中，我们将从 k 类模型的各个真阳性、真阴性、假阳性和假阴性计算性能，例如精度:

![](img/184ad80eb9a8bf0dfb17d05cc340b3a8.png)

在宏观平均法中，我们对每个班级的表现进行平均:

![](img/b9a93848d9ae7c30633ca4ac984ea2f8.png)

下面是绘制 ROC 曲线和 AUC 值的代码。

这是结果，第二个图是图表左上角的放大图。

![](img/bd7196b31a8624975b7454ce16922b3a.png)

你可以看到，对于每个类别，它们的 ROC 和 AUC 值略有不同，这很好地表明了我们的模型在分类单个类别方面有多好。

# 总结和进一步阅读

在本教程中，我们介绍了如何使用 ROC 曲线和 AUC 值评估二元和分类 Keras 分类器。

ROC 曲线可视化了测试集上的排名器或概率模型的质量，而没有承诺分类阈值。我们还学习了如何计算 AUC 值，以帮助我们评估分类器的性能。

如果你想了解更多关于 ROC 的知识，你可以阅读它的维基百科页面，[接收器工作特性](https://en.wikipedia.org/wiki/Receiver_operating_characteristic)，它向你展示了如何通过迭代不同的阈值来绘制曲线。

此外，在计算 ROC 上查看 [Sklearn 的 API 文档](http://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_curve.html)有助于进一步了解如何使用该功能。

你可以在我的 GitHub repo 中找到这个教程的[源代码](https://github.com/Tony607/ROC-Keras)。

*原载于*[*www.dlology.com*](https://www.dlology.com/blog/simple-guide-on-how-to-generate-roc-plot-for-keras-classifier/)*。*