# 什么是数据湖以及如何为您的企业创建数据湖

> 原文：<https://medium.com/hackernoon/what-is-a-data-lake-im-confused-8e12d554f7a0>

如果你正在关注数据科学的趋势，你很可能听说过大数据、分析和机器学习这些词。如今，每个人都想进入数据科学的这个领域。许多软件巨头，如谷歌、亚马逊、微软等。已经在引领潮流了。

![](img/f7489b75dc04e73791e0c929243341b5.png)

然而，由于许多原因，一个新企业进入这个专业领域并不容易。其中一个核心问题是，数据分散在不同的系统和各自的数据库中。这些数据集可能会存在很多年，很难为其业务提供任何价值。

# 为什么不创建一个数据仓库呢？

尽管如果我们能够首先创建一个数据仓库那就太好了(查看我的关于在构建一个无服务器数据仓库之前需要考虑的事情的文章以获得更多细节)。

然而，在业务的早期阶段创建数据仓库有几个实际的挑战。一个主要原因是，很难确切地知道哪些数据集是重要的，以及应该如何清理、丰富和转换它们来解决不同的业务问题。

想象一下，您需要付出多大的努力来预先从所有不同的系统中识别数据集，并提取这些数据，进行清理、丰富和转换，以便可以在非常早期的阶段将它们推入数据仓库。除非您有内部的数据科学专家，他们也深入了解业务领域，以确定所需的数据集并做好数据准备，否则很可能会把事情搞砸。

# 什么是数据湖？

数据湖是存储所有结构化和非结构化数据的集中存储库。数据湖的真正优势是，它可以按原样存储数据，您可以立即开始从不同的系统推送数据。

这些数据可以是 CSV 文件、Excel、数据库查询、日志文件等。可以与相关联的元数据一起存储在数据湖中，而不必首先构造数据。

一旦在一段时间内数据湖中的数据可用，就可以在以后处理数据，以运行不同类型的分析和大数据处理来实现数据可视化。也有可能将来自数据湖的数据用于[机器学习](https://en.wikipedia.org/wiki/Machine_learning)和[深度学习工具](https://missinglink.ai/)以更好地指导决策。

# 为您的企业创建数据湖

对于一个企业来说，开始创建一个数据湖并确保不同的数据集在很长一段时间内被一致地添加需要一个过程和自动化。要朝着这个方向发展，首先要选择数据湖技术和相关工具来建立数据湖解决方案。

## 1.建立数据湖解决方案

如果您计划在云中创建一个数据湖，您可以在 AWS 上部署一个[数据湖，它使用底层的无服务器服务，而不会产生大量的前期成本，并且数据湖解决方案的大部分成本是可变的，主要根据您输入的数据量而增加。](https://aws.amazon.com/answers/big-data/data-lake-solution/)

## 2.识别数据源

然后，确定数据源和数据被添加到数据湖的频率是很重要的。一旦确定了数据源，请确保决定是按原样添加数据集，还是对数据进行所需级别的清理和转换。确定各种类型数据集的元数据也很重要。

## 3.建立流程和自动化

由于数据集来自不同的系统，甚至可能属于不同的业务部门，因此建立一致性流程非常重要。

例如，可以通知人力资源部门在每年进行一次调查后向数据湖发布员工满意度。另一个例子是，会计部门每月将工资单上的数据发布到数据湖。

对于需要更高频率的数据发布或耗时工作的操作，可以实现数据源流程的自动化。这可能包括自动化数据的提取、转换和发布到数据湖，或者至少自动化一些单独的步骤。

## 4.确保正确的治理

在建立数据湖之后，确保数据湖正常运行是很重要的。它不仅仅是将数据放入数据湖，还允许或促进其他系统的数据检索，以生成数据驱动的明智的业务决策。否则，从长远来看，数据湖最终将变成一片数据沼泽，几乎没有任何用处。

## 5.使用来自数据湖的数据

在正确设置数据湖并运行一段合理的时间后，您将已经使用适量的关联元数据将数据收集到您的数据湖中。在使用 ETL(提取、转换和加载)操作来驱动不同的业务决策之前，需要实现不同的流程。这就是数据仓库和数据可视化工具的重要性所在。如果需要对来自其他系统的不同数据集进行更多处理，您可以将数据发布到数据仓库，或者直接输入数据可视化和分析工具，如 [Microsoft Power BI](https://powerbi.microsoft.com/en-us/) 和 [AWS QuickSight](https://aws.amazon.com/quicksight/) 。

# 那么下一步是什么？

接下来最重要的事情是询问正确的业务问题，这些问题可以根据数据的可用性来回答。虽然这似乎太明显了，但这是许多企业将事情变得如此复杂的领域之一。

虽然有一个功能齐全的数据湖，可以为业务提供有用的见解，但重要的是不要止步于此。数据湖的优势在于持续的开发和对解决方案的评估。