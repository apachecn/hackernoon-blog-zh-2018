# 神经网络—简介

> 原文：<https://medium.com/hackernoon/neural-networks-introduction-6048f69b68b0>

![](img/9074d19266c4a91bf28eb8436b73c7cc.png)

神经网络已经占据了中心位置，几乎统治了整个人工智能领域。学习神经网络已经成为一种需要，特别是由于它的高性能和所有领域的适用性。神经网络是基于动物大脑建模的计算机系统。这些系统通过迭代过程学习执行任务，例如分类或预测。当我们拥有的数据量增加时，神经网络能够胜过传统算法(SVM、RF)。现在，在一个神经[网络](https://hackernoon.com/tagged/network)中到底发生了什么？最简洁的答案是，数学计算。

让我举一个例子来说明神经网络功能的细节。考虑一下房价问题，你想知道一栋房子的价格。例如，你有房子的大小、位置和卧室数量等详细信息，并被要求提供房子的估计价格。神经网络将是找出价格的恰当方法。

![](img/f6dab05061b75647314cbecab6e5858a.png)

上图描述了神经网络的样子，你有三个不同类型的层，输入层，隐藏层和输出层。每个隐藏层可以包含任意数量的神经元/节点。输入层中的节点数将等于我们问题中存在的特征数(在我们的例子中是 3，即。大小、位置、卧室数量)。输出层中的节点数将等于我们想要预测的值(在我们的例子中是 1，即。房价)。

现在，让我们再深入一层，并尝试了解每个节点中发生了什么。

![](img/e83080d24af7cb8a9aa37229ddb3a389.png)

在输入层的每个节点中，将输入要素、权重和偏差作为输入，并如上所示计算输出 z。我们将这些值作为矩阵，这使得计算更容易和更快。什么是权重和偏差？权重和偏差是首先随机初始化的值(来自高斯分布),这些值用于计算节点的输出。通过调整这些值(权重和偏差)，我们能够使神经网络适合我们的数据。

![](img/cd2eb5631ebd7ce4d0b6e68325d03ce9.png)

计算值 z 后，我们对 z 使用激活函数(ReLu，Sigmoid)。激活函数用于向模型引入一些非线性。如果我们不应用任何激活函数，输出将只是一个线性函数，可能无法成功地将复杂的输入映射到输出。

在隐藏层中，节点的输入是前一层节点的输出。最后，输出层预测一个值，并将该值与已知值(地面真实值)进行比较，然后计算损失。直观上，损失代表了预测值与实际值的差距。使用该损失值，改变网络中的权重和偏差，以获得确保正确预测输出的最佳值。

我相信这个故事会提供一些关于神经网络如何工作的基本直觉。谢谢你。