# 构建端到端深度学习 GitHub 发现提要

> 原文：<https://medium.com/hackernoon/building-an-end-to-end-deep-learning-github-discovery-feed-ace2f25405a5>

![](img/9660e8a9af09f25e58c53c50c0fedb13.png)

几乎没有一个开发者不使用 [GitHub](https://github.com/) 。通过所有这些启动、拉动、推动和合并，GitHub 拥有了大量描述开发者世界的可用数据。

作为 Stream 的数据科学家，我的工作是为我们的客户开发推荐系统，以便他们能够为他们的客户提供更好的用户体验。尽管如此，我想看看能否为我日常使用的产品建立一个推荐系统([类似于我为 Instagram](https://getstream.io/blog/instagram-discovery-engine-tutorial/) 建立的工具)，以及尝试一些新的深度学习架构和我一直想玩的“大数据”处理工具。

我选择使用 [Dask](https://dask.pydata.org/en/latest/) (它为分析提供了先进的并行性，能够为您喜爱的工具提供大规模性能)来满足我的“大数据”处理需求。我喜欢把它想象成核外、平行、Numpy 和熊猫。有什么不喜欢的？为了构建深度学习架构，我决定使用 PyTorch。PyTorch 在 Python 中提供了“[张量和动态神经网络，具有强大的 GPU 加速](https://pytorch.org/)”。其易于使用的界面和卓越的调试能力使 PyTorch 令人惊讶地令人愉快。

在我看来，建立这个推荐系统有三个主要部分:1)下载和处理数据，2)建立一个推荐系统，3)将该系统投入生产环境。

重要的事情先来。查看[演示](https://github.getstream.io/)！

![](img/f4c7fc5fb096a76b486aabde50a74dfd.png)

# 下载和处理数据

我知道数据管理并不总是有趣或性感的。然而，由于它是许多数据科学和机器学习工作流的一个如此大的部分，我想经历一下我是如何处理超过 6 亿个事件的。

## 从 GitHub 档案下载数据

“2012 年，社区主导的项目 [GitHub Archive](https://www.gharchive.org/) 在[启动](https://blog.github.com/2012-05-01-data-at-github/)，提供了人们在 GitHub 上构建软件的方式，这个 3TB+数据集包含了迄今为止最大的 GitHub 活动发布源。它包含超过 280 万个开源 GitHub 存储库的[活动数据](https://developer.github.com/v3/activity/events/)，包括超过 1.45 亿个唯一提交、超过 20 亿个不同的文件路径和 1.63 亿个文件的最新修订版内容”[1]。

这是一个很好的数据块。这个数据集包括超过 [20 种不同的事件类型](https://developer.github.com/v3/activity/events/types/)，从提交、评论到公共事件数据的开始。然而，它不包含私人回购信息(谢天谢地)，没有“'查看”或“克隆”数据。虽然这对于隐私来说很棒，但在提供推荐方面确实有一点障碍，因为查看和克隆回购会提供很好的兴趣指示。也就是说，仍然应该有大量的交互数据来提供一些很酷的见解。我最终使用 2017 年 1 月 1 日之后创建的数据来获得至少一年的交互数据，因为这听起来是一个不错的数字。

所以…..为什么不只用明星？这看起来确实是用户感兴趣的一个很好的指标，但是，在学术界和工业界，使用显式数据已经不再受欢迎，黄金标准是现在使用隐式数据(任何参与事件，都可能表明用户对某项活动的兴趣)。为了简单起见，我们将使用 GitHub 提供给我们的所有数据，并将其视为隐式数据。这意味着所有 20+事件(包括明星)将被视为一个隐式事件。这最终导致每年大约 5 亿次分析事件。不算太寒酸。不幸的是，我的电脑无法将所有数据存储到内存中，这就是 Dask 的用武之地。

GitHub 存档每小时更新一次，并允许最终用户下载。每小时 JSON 数据的 gz 文件。经过一点考虑，我最终将数据存储为 [parquet 文件](https://parquet.apache.org/)，因为这看起来很自然，加上它是[建议的用于存储 Dask 数据帧的文件格式](http://dask.pydata.org/en/latest/dataframe-performance.html#store-data-in-apache-parquet-format)。它读取内存的速度几乎与 HDF5 一样快(与多核一样快)，并且[在磁盘上的压缩效果更好](https://tech.blue-yonder.com/efficient-dataframe-storage-with-apache-parquet/)。

我用来迭代下载数据的函数如下所示。对于正在进行的更新，我简单地将“update_data”函数包装到一个简单的 cronjob 中，该函数在我的模型被重新训练之前每天运行一次。

## 处理数据

好了，现在我们有了大量的数据要处理，我们需要做一些认真的预处理，然后才能将它转储到任何类型的模型中。

最终目标是必须有一个数据数组，每个交互都是规范化的整数形式。

[表 id=1 /]

我使用的工作站有 32 个内核和 64 GB 的内存，然而，下面这些在标准的笔记本电脑上应该都是可行的。我确实在我的 MacBook Pro 上运行过，它有 16GB 的内存和 8 个内核。由于 Dask 利用了多个内核的并行性，一些步骤花费的时间要长得多。Dask 的一个非常好的地方是它提供了对任务的监控。下面的可视化例子帮助我调试了下面的步骤。

![](img/7465f54a5566d0e0605d33a1ef21d696.png)

这些步骤如下:

为 Dask 设置一个[本地计算集群，并定义一个计算图来从 Dask 数据帧内的 JSON 字符串中去除用户名和 repo 名](http://distributed.readthedocs.io/en/latest/local-cluster.html)

将 Dask 数据帧转换为 Dask 数组，以利用切片功能，并作为 Numpy 堆栈存储到磁盘，以强制冻结计算的当前状态。

遍历这些堆栈，找到所有唯一的 repos 和用户，以创建用户和条目到 id 的字典。在最后的聚合步骤中，我遇到了一些内存问题，使用 Dask 完全在内核之外进行惟一计数，所以我最后只是分块迭代，然后映射用户和条目，再一次存储到磁盘。

为了减少噪音，从训练集中删除了参与度低的回购(任何关联互动少于 50 次的回购)。然后，我将每个用户和条目映射到一个规范化的索引，以获得我们在上面努力争取的格式。

咻，那是一大堆数据。我知道这并不迷人，但我最终花了很多时间做这类工作，所以似乎值得覆盖管道，而不仅仅是酷的闪亮的东西。现在，开始有趣的事情吧！

# 构建推荐系统模型

自从网飞挑战赛以来，协同过滤和矩阵分解方法一直是推荐系统领域的王者。今天，基于序列的模型开始变得越来越普遍。幸运的是，深度学习技术可以应用于这两者。

## 基于神经矩阵分解的协同过滤。

[神经矩阵分解](https://arxiv.org/abs/1708.05031)是去年推出的一种协作过滤方法，它试图利用神经网络提供的一些非线性，同时保持矩阵分解提供的泛化能力。这是通过将从多层感知器提取的两个特征向量与来自项目和用户特征向量的逐元素乘法相连接来完成的。

下面是一个简单的例子:

![](img/105643761da136d148b616721d4a1c84.png)

PyTorch 被用作这个网络的构建模块，许多想法都来自于此:

[https://github.com/maciejkula/spotlight](https://github.com/maciejkula/spotlight)还有这里:[https://github . com/Lacey Chen 17/neural-collaborative-filtering](https://github.com/LaceyChen17/neural-collaborative-filtering)

让我们的网络看起来像这样:

## 基于序列的模型

基于序列的模型最近在推荐系统中非常流行。他们背后的主要思想是，不是将用户建模为唯一的标识符，而是将用户建模为他们过去的 x 次交互。这提供了几个非常好的属性。用户的新交互不需要触发新的模型重建来生成最新的推荐，因为它都基于过去的 x 项交互。此外，一旦新用户开始点击，他们可以立即推广到新用户。在像电子商务个性化推荐这样的情况下，您拥有的所有数据都是基于单个会话的，这是非常重要的。这些想法中有许多是从自然语言处理中改编而来的，在自然语言处理中，语言模型用于预测句子中的下一个字符或单词。

一个[混合口味](https://arxiv.org/abs/1711.08379)模型试图通过使用单独的口味向量对用户对一个项目的兴趣进行排序来代表用户可能拥有的不同兴趣。它通过使用 CNN 层将这些味道向量表示为不同的特征图来做到这一点，CNN 层的步幅为 1，深度输出为味道向量的数量。对于 CNN 架构的详细分析，我强烈推荐阅读[视觉识别卷积神经网络](http://cs231n.github.io/convolutional-networks/)

这个想法似乎在这里会很有效，因为开发人员往往有多种兴趣。例如，我最感兴趣的是与机器学习相关的回购，然而，我也对大数据处理工具和后端 web 开发感兴趣，如果模型可以以某种方式将这些视为不同的子群体，那就太好了。

我强烈推荐看一看马切伊·库拉写的论文，以及他对[模型的实现。我为自己的模型做的唯一调整是添加了一些丢弃层和一些层之间的激活函数。这是这个项目最终投入生产的模型。](https://github.com/maciejkula/spotlight/blob/master/spotlight/sequence/representations.py#L456)

## 培养

由于项目更新的数量，如果考虑所有负样本，则每次反向传递期间的每次嵌入在计算上可能变得昂贵。令人欣慰的是，我们可以从自然语言处理中获得一些技巧，并利用一些负面采样技术。由于不属于用户交互历史的任何数据点都被认为是隐式负值，并且样本大小(用户的交互历史)远小于我们总体(所有回复)中的项目数量，因此随机抽样的项目很可能是隐式负值。为了帮助我们的网络更有效地对事物进行采样，我们还可以从 word2vec 中获取采样分布的概念，其中选择事物的概率是:

![](img/5de2d8723948207be7bd3ee34d621436.png)

由于这是一个使用隐式数据点的学习排序问题，我最终使用贝叶斯个性化损失(这是成对损失的一种变体)作为我的损失度量。在 PyTorch 中，这看起来像

其中，正面预测是从给定的一批训练数据正向通过我们的网络获得的，而负面预测是从我们的交互中随机采样的值。

遵循标准的最佳实践，我将基于用户的数据分成 80%用于训练，10%用于验证，另外 10%用于测试。

在 15 个时期之后，模型实现了 0.0085 的训练损失和 0.0213 的验证损失。

我认为模型严重过度拟合。但看起来，在最初的几个步骤之后，验证和训练损失以相同的速度下降，所以我把它归结为估计人口时的泛化误差加或减标准差。

使用 [Mean Reciprocal Rank](https://en.wikipedia.org/wiki/Mean_reciprocal_rank) 作为评估指标，测试集被设置为对任何给定用户使用除最后一次交互之外的所有交互，并且对所有项目进行排序，以查看它是否可以将列表中的下一个项目评为最高。在我的测试集上(写这篇博文的时候)。我获得了 0.058 的 MRR。这意味着，在大约 140 万个项目的预测得分中，用户与之交互的最后一个项目在模型认为他们会与之交互的最高排名项目的大约 17 个以内。[【u】](#cmnt21)

# 服务

通常，在生产环境中，在数十毫秒的严格响应周期内对每个项目进行排序既昂贵又缓慢。最常见的解决方法是将响应分为两步:(1)生成候选项，然后(2)使用每个可用功能进行排序。

## 候选人生成。

在一个严格的响应周期内(至少在一个 CPU 上)对几百万个回购进行排序是不可能的。处理这个问题的一个标准方法是提供一个可能相关的候选子集(在数百个范围内)，然后使用我们上面的模型对这些候选进行排序。

我采取了一种类似于 [YouTube](https://ai.google/research/pubs/pub45530) 的方法，在神经网络之上使用近似最近邻方法，根据候选人最后一次互动的平均值来寻找候选人。然而，我没有构建另一个神经网络，我想我可以只使用从上面的混合模型中为给定用户计算出来的计算项特征向量。

我有点担心，计算不同回购之间的余弦相似性可能会产生糟糕的结果，因为嵌入并不完全彼此具有线性关系。然而，仅仅基于一些经验证据，最近邻似乎产生了一些好的候选者。

距离[https://github.com/pytorch/pytorch 最近的邻居](https://github.com/pytorch/pytorch)

*   "https://github.com/pytorch/pytorch"，**<——理智检查**
*   "https://github.com/caffe2/caffe2"，
*   "https://github.com/tensorflow/tensorflow"，
*   "https://github.com/beamandrew/medical-data"，
*   "https://github.com/Microsoft/CNTK"，
*   "https://github.com/onnx/onnx"，
*   "https://github.com/torch/torch7"，
*   "https://github.com/pathak22/noreward-rl"，
*   "https://github.com/ryankiros/skip-thoughts"，
*   "https://github.com/OpenNMT/OpenNMT"，
*   "https://github.com/dmlc/mxnet"，

距离[https://github.com/facebook/react 最近的邻居](https://github.com/facebook/react)

*   "https://github.com/facebook/react"，**<——理智检查**
*   "https://github.com/parcel-bundler/parcel"，
*   "https://github.com/typicode/json-server"，
*   "https://github.com/webpack/webpack"，
*   "https://github.com/dvajs/dva"，
*   "https://github.com/lodash/lodash"，
*   "https://github.com/react-bootstrap/react-bootstrap"，
*   "https://github.com/airbnb/javascript"，
*   "https://github.com/facebook/react-native"，
*   "https://github.com/babel/babel"，
*   "https://github.com/websockets/ws"，
*   "https://github.com/electron/electron"，
*   "https://github.com/ReactTraining/react-router"，
*   " https://github.com/vuejs/vue "

使用 [Spotify 的骚扰库](https://github.com/spotify/annoy)来计算[近似最近邻居](http://en.wikipedia.org/wiki/Nearest_neighbor_search#Approximate_nearest_neighbor)，生成 1000 个候选人，并对这些候选人进行排序，而不是对超过 140 万个候选人进行排序，这将我的响应时间从几秒钟减少到了几十毫秒。

构建索引相当简单:

现在，我只需要一个向量来查询。通过对过去 90 天的互动进行平均，可以快速生成 1000 个合理的候选人进行排名。

现在我有了候选人，我可以通过我的网络为每个候选人生成预测，使最终的响应周期看起来像:

1.  (90+%的时间都在这里度过。)查询 GitHub 以获取用户过去 90 天的交互
2.  获得这些交互的平均嵌入
3.  基于平均嵌入使用近似最近邻生成 1000 个候选。
4.  使用混合模型对这 1000 名候选人进行排名。

## 生产 PyTorch

我想让模型运行在严格的文件结构之外的 CPU 上(出于经济原因更是如此)，所以我序列化了模型的状态字典，而不是全部。

我只需要确保调用“model.eval()”来退出训练模式并进入评估模式。这一点很重要，因为它处理了一些事情，比如忽略推理中的辍学。

我对最近发布的 PyTorch 1.0 感到非常兴奋，它非常强调生产环境。然而，在它正式发布之前，我会以牺牲一些效率为代价，让生产变得更简单一些。所以，我把所有东西都放在 Python 中，并使用 Django Rest 框架来处理 API 响应。

现在，当用户发送推荐请求时，我们通过 GitHub API 获得他们最近 90 天的交互，将他们映射到我们的标准化 id，并通过我们的神经网络运行他们。响应是一个排序列表，列出了模型认为你接下来可能想要与之交互的内容。只要与已知回购至少有一次互动，它就能给出建议。然而，如果没有可用的公开信息，它默认为 GitHub 自己的发现页面。

我以前提到过这一点，但是基于序列的模型可以与您的最后一次交互相关，而不必重新训练整个模型。这意味着你可以开始/提交/公开问题(不要告诉 OSS 的人我这么说了),看看你的建议如何实时变化。

## TL；速度三角形定位法(dead reckoning)

*   从 GH 档案下载超过 6 亿个事件。
*   使用 Dask 处理单台机器上的所有事件。
*   建立序列神经网络模型来预测用户交互。
*   使用来自模型的嵌入和近似最近邻来生成候选。
*   提供回购排名列表，帮助您找到您的新宠！

当然，(BTW 感谢你读到这里！)如果你有什么想让我尝试/写的，一定要评论！[【y】](#cmnt25)

[1][https://blog . github . com/2016-06-29-making-open-source-data-more-available/](https://blog.github.com/2016-06-29-making-open-source-data-more-available/)