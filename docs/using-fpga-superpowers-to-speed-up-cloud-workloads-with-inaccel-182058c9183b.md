# 利用 FPGA 的超强能力，通过 InAccel 加速云工作负载🚀

> 原文：<https://medium.com/hackernoon/using-fpga-superpowers-to-speed-up-cloud-workloads-with-inaccel-182058c9183b>

![](img/fdd5ba7aac3a81047998a6cb45770aff.png)

俗话说，天底下没有新鲜事，这也适用于现场可编程门阵列。然而，随着主要云供应商提供 FPGA 即服务，芯片制造商重新对该领域产生兴趣，FPGA 又卷土重来。在其他事实中，英特尔以 160 亿美元收购了 Altera，现在正在发运集成 FPGA 的新英特尔至强处理器。

与此同时，像数据分析、机器学习和基因组学这样的应用程序是计算密集型的，需要大量的处理能力来管理集群，更不用说人力了。为了应对这一挑战，仓库数据中心和云提供商最近开始采用硬件加速器。

硬件加速器(如 FPGAs)可以加快应用处理速度，同时降低数据中心的能耗。亚马逊网络服务(AWS)、IBM、英特尔、百度和阿里巴巴等科技巨头最近宣布，他们将开始以 IP(知识产权)核心的形式在其公共云中托管硬件加速器。

FPGAs 并不是一种新颖的技术，但是它的采购和编程成本很高。FPGAs 用于实现单一算法，以追求更高的性能。笔记本电脑或服务器等典型计算机使用通用 CPU。你可以把 CPU 想象成一个计算器，把 FPGA 想象成一个特殊用途的计算器，可以编程只解一个方程。通过牺牲灵活性来提高性能，使用专用计算器(FPGA)可以极快地求解该方程。

随着行业受到摩尔定律放缓的挑战，大数据分析变得至关重要，但成本也很高，公司越来越多地寻找替代计算平台来加速工作负载。我们已经看到了 GPU 和 Nvidia 的崛起，因为这些芯片非常适合深度学习应用(大型稀疏阵列代数)。FPGAs 也出现在大公司的组合中，导致我们所说的垂直计算或垂直集成，其中我们有特定问题的硬件。

由于 FPGAss 在公共云中的可用性，以及 Apache Spark 等开源计算框架提供的多种标准算法被越来越多的人采用，InAccel 正乘着 FPGA 激增的浪潮。 [InAccel](https://www.inaccel.com) 提供硬件加速器，是 Apache Spark MLLib 的替代产品，速度提高了 3 到 10 倍。所要做的就是在 F1 AWS 实例上运行 Apache Spark 的一个节点，运行你可以在 [AWS Marketplace](https://aws.amazon.com/marketplace/pp/B07D2GWCJN) 上找到的 [InAccel](https://www.inaccel.com) IP 核，并替换你在 Spark 上运行的代码中的一行代码！你的首席信息官会感谢你的。

我们很高兴能与 [Christophoros Kachris](https://www.linkedin.com/in/kachris/) 合作，他是一名研究人员，整个职业生涯都致力于 FPGAs 领域，同时合作的还有他的联合创始人 [Elias Koromilas](https://www.linkedin.com/in/elias-koromilas/) 和 [Ioannis Stamelos](https://www.linkedin.com/in/stamelos-ioannis-a7aba680/) 。垂直集成计算是密集和关键工作负载的未来，我们期待 InAccel 成为硬件加速器的中心。

👉如果你对垂直计算的未来感兴趣，InAccel 正在招人。