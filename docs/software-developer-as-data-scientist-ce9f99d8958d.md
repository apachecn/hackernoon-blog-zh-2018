# 作为数据科学家的软件开发人员

> 原文：<https://medium.com/hackernoon/software-developer-as-data-scientist-ce9f99d8958d>

![](img/12a13808844d0ca5f67df117341de5f0.png)

“当代”软件开发人员投身数据科学潮流的一个优势是，对他/她来说，技术变化的速度是已知的，没有令人沮丧的经历。

这里有一个相关的例子:

我已经涉足 Python 栈的数据科学领域很长时间了。在试图寻找加快我的超参数调优时间的解决方案时——更具体地说是 [scikit-learn 的 GridSearchCV](http://scikit-learn.org/stable/modules/generated/sklearn.model_selection.GridSearchCV.html) ，我偶然发现了一个 [youtube 视频](https://www.youtube.com/watch?v=HhsIisXpaT4)，它展示了 [dask-learn](https://github.com/dask/dask-learn) 库如何能够拯救我，并几乎完全取代 scikit-learn 的 GridSearchCV。

但当我试图安装这个库时，我意识到这个库改变了它的名字，并转移到了一个不同的模块和空间- [Dask-SearchCV](https://github.com/dask/dask-searchcv) 。它被进一步移动并作为 [Dasl-ML](https://github.com/dask/dask-ml) 的一部分。

对正确的库进行归零，并再次检查它是否是我需要使用的最新版本，我尝试对它进行测试(对它进行测试？是的，作为一个有经验的软件开发人员，你学会了不相信任何东西，甚至是你自己的证书:)。

猜猜测试结果是什么？这表明超参数调整的计算时间没有改进。所以，我查了视频，它是一年前发布的。在这段时间里，可能会发生很多事情，鉴于 scikit-learn 的主要作者资格，可以有把握地认为，在这段时间里，他们也对库进行了调优，以获得更好的性能。如果您对细节感兴趣，scikit-learn 可以通过在其 API 中设置 param *n_jobs=-1* ，自动神奇地利用您系统的多个内核。

你如何解释这些事件？这是我的观点，从某种意义上来说，我很高兴我的加速实验失败了，并确信 scikit-learn 团队正在努力工作，以保持今天的库的良好状态，并且我可以将我的重点转移到加快计算时间的其他方法上，其中一种方法是将这项任务委托给一个节点/计算机集群。

顺便说一句，如果你不是一个软件开发人员，而你所在的团队正在投身于数据科学的潮流，那么你可以看看 Python、Go 或 R 等的堆栈。，而不是像 [SAS](https://www.sas.com/en_in/home.html) 或 [SPSS](https://www.ibm.com/analytics/spss-statistics-software) 这样的工具堆栈，我会祝你——“欢迎来到现代软件开发的混乱世界！”。这是混乱和复杂的，但如果你(作为一个团队)能够驯服野兽，你就拥有了让你的竞争对手羡慕和恐惧的力量。

> 一如既往，我渴望从你的经历中学习。所以，请在这篇文章的评论中分享你的想法。至少，你可以点击喜欢按钮，分享这篇文章，以了解你圈子里的人对这篇文章的反应。请记住，更多的人参与意味着我们一起形成更好的合奏:)