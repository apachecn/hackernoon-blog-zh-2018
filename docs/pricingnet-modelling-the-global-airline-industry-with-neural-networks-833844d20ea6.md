# PricingNet:用神经网络模拟全球航空业

> 原文：<https://medium.com/hackernoon/pricingnet-modelling-the-global-airline-industry-with-neural-networks-833844d20ea6>

基兰·麦克休

![](img/beac9b19cd62780d776a6158fd0484e8.png)

**在 Skyscanner，我们一直在寻找通过机器学习的新颖应用来改善旅客体验的方法。**

每天，数百万人开始在 Skyscanner 上计划他们的下一次旅行。每次搜索都能产生数千个价格，供旅行者浏览和比较。所有这些价格都通过 Skyscanner 的数据平台:在那里它们被摄取、过滤、处理并最终被存储。在我们的规模上，由于我们被大量的定价数据轰炸，提取可操作的见解成为一个真正的挑战。

# 背景

我们都经历过不得不为产品或服务支付超出预期的费用的沮丧和失望:无论是令人讨厌的公用事业价格上涨，激烈的在线拍卖，还是错过了一次快速销售。之后，我们觉得被骗了——为了筹集必要的现金，我们可能不得不对财务计划做出重大且不方便的调整。

这是一个不幸和令人遗憾的事实，当人们为即将到来的旅行预订航班时，这种情况一直在发生。旅游业是一个动态的、竞争激烈的市场，航空公司和旅行社制定的价格非常不稳定，可能会在不通知的情况下修改。有时，价格每小时会上涨几倍。

事实上，航空业作为一个整体被认为是“在试图最大化其收入时使用动态定价策略最复杂的行业之一”[1]。

![](img/3467188bbef3cef7b0273fb7cd53df3d.png)

The price of the cheapest flight for any given route can vary wildly over time. The above chart illustrates price changes for a one-way short-haul flight over a 14-day window.

这种情况让我们和我们的旅行者非常沮丧。每天，我们都非常努力地工作，以最大限度地提高我们网站上显示的价格的及时性和准确性。我们不知道价格何时会发生变化，也不知道价格会上升或下降多少。我们有责任预测市场，并尽量减少这些波动对我们用户的影响。

# 一个想法

我在大学期间一直与 Skyscanner 合作，我一直在寻找如何将我的学术经验应用到 Skyscanner 面临的最大商业挑战中。因此，当我为论文选择主题时，我知道我想与 Skyscanner 合作解决一个难题。我已经深入研究了机器学习的原理，并决定专攻人工神经网络。我对神经网络如何应用于与计算机视觉和图像识别无关的回归问题产生了浓厚的兴趣。

我想知道我们是否可以利用 Skyscanner 的大型历史数据集来设计、构建、训练和评估一些简单的神经网络——给定一些关于航班的上下文信息，这些神经网络可以为我们提供一些关于从现在到出发日期机票价格会如何上涨或下跌的见解。

我们为什么要这么做？已经有几个网站建议旅行者是应该“现在购买”还是“等一会儿”。在后一种情况下，人们希望票价会下降，让旅行者节省一些现金。然而，在大多数情况下，**机票价格永远不会下降**——它们会单调上升。因此，我推断，专注于“购买/等待”分类的机器学习模型对客户来说不是很有用。

我们能给旅行者的最好建议是尽早购票。然而，生活很少这么简单:马上预订航班通常不方便，而且可能有必要推迟预订。例如，我们经常不得不等待发薪日的到来，或者等待一个(不太有条理的)朋友确认他们的出席，以便可以一起预订所有的票。我相信如果我们能告知我们的旅行者，那将会更有价值…

*   在价格开始大幅上涨之前，他们可以推迟购买多长时间；
*   如果他们因为任何原因需要推迟购买，他们可以预计价格会上涨多少。

因此，与其进行简单的“购买”或“等待”分类，不如开发一种输出为指示性价格的机器学习模型。换句话说，我们为模型提供了我们航班的详细信息(比如出发地、目的地和旅行日期)以及一个表示出发前天数的参数。理论上，当我们在 365 天(提前 1 年)和 0 天(出发当天)之间改变这个参数时，模型应该产生不同的价格。

在我提出这个建议的时候，我不知道构建这样一个模型会有多困难，甚至不知道它是否可行。我的整个项目笼罩在不确定性中，有大量的未知因素，这使得我很难知道应该从哪里开始。

# 训练数据

在试图进一步分解这个问题之前，我集中精力获取尽可能多的高质量训练数据。我与 Skyscanner 法律团队合作，建立了一个学术数据共享协议。这项协议让我史无前例地获得了 2016 年 9 月至 11 月 60 天内全球航班价格的快照。

具体来说，对于这段时间内在 Skyscanner 上执行的每一次搜索，我们都记录了显示给旅行者的*最低直接价格(MDP)* 。MDP 是所有航班时间和供应商中最便宜的**直达**票价(无中转)。这相当于超过 200，000，000 个候选训练模式。

数据真多啊！

# 选择项目范围

面对如此大量的训练数据，我觉得如果我没有为我的调查建立一个明确的焦点，我会让自己失败。

考虑到这一点，我决定只考虑直达的单程航班。我认为这是一个合理的起点，它将为未来研究更完整的模型(包括返程航班和中转航班)提供基础。

此外，我还假设用户愿意在一天中的任何时间旅行，以尽量减少他们支付的费用，即使最便宜的机票是在特别不适合的时间。

# 巨大的挑战

我发现，为我的研究建立一个集中的范围，并对旅行者的行为做出一些假设，可以减少很多复杂性。也就是说，有相当多的突出挑战需要考虑。

*   航空业建模尤其具有挑战性，原因有很多。在某种程度上，它的行为就像股票市场:航空公司在选择价格时会考虑大量不同的因素，并且存在大量的外部性。收入管理分析师将复杂软件的建议与他们自己不可预测的直觉结合起来，以确定应该向客户收取多少费用。我如何确定票价和影响票价的因素之间的关系存在确定性？
*   收入管理系统考虑的因素通常没有有意义的数字表示，这意味着它们不能直接作为神经网络的输入。**我如何以网络能够理解的方式来表示分类实体，比如机场？**
*   我渴望建立一个单一的、通用的神经网络，能够预测任意目的地之间的航班价格。正如我们所见，训练任何通用航班定价模型所需的数据量都非常大。在这种规模下，存储、预处理、拆分和重组数据都是不简单的任务。**我如何在有限的时间内有效地处理这么多数据来完成这个项目？**

# 选择架构

虽然我知道我想将神经网络应用于这个问题，但我必须从大量可能的不同神经网络范例和架构中进行选择。我可以采取两种可能的方法。

*   **方法 1** :选择一个真正简单的前馈神经架构。虽然这种方法风险较低，但也有可能不够复杂，无法提供准确的模型。
*   **方法 2** :全力以赴，尝试利用更复杂架构的力量，如递归神经网络(RNN)。rnn 有时更适合时间序列数据。由于构建和训练这些网络涉及额外的复杂性，采用这种方法是有风险的。

我决定采用方法 1。我的项目是将神经网络应用于这个问题的第一次有记录的尝试，它更有意义的是从简单开始，然后扩展复杂性——而不是从复杂开始，在失败的情况下降低复杂性。

# 工具和框架

为了保持我的研究效率，我想最大限度地减少我花在实施网络和管理数据管道上的时间。我查阅了几个机器学习框架，最终选定了 TensorFlow。我决定利用 Keras:一个建立在 TensorFlow 之上的机器学习库，它提供了强大的、富有表现力的、现成的神经网络组件。

我利用 Amazon 的 Elastic MapReduce 来清理、预处理和重新格式化训练数据，以提取我需要的变量。这些数据被直接输入到 Pandas 的数据框架中，并一字不差地输入到 Keras 提供的训练工具中。Keras 和 TensorFlow 支持在*小批量*中处理数据:一组训练模式可以委托给 GPU 并并行处理，以显著加快训练过程。

# 分割训练数据

为了可靠地评估我们的模型的性能，不是所有的数据都可以用于训练:必须保留一部分来评估在看不见的情况下的性能。凭直觉，我们可以想象，通过按时间顺序对数据进行排序，使用“早鸟”航班价格进行训练，并保留最后一分钟的价格进行评估，可以实现适当的拆分。我认为这不一定是最好的方法，原因有几个。

首先，这种方法仅测量网络的能力，即*外推*(基于一组历史数据点的未来项目趋势)，而不是*内插*(填充已知数据点之间的缺失信息)。在模拟航班价格时，良好的外推和内插法性能至关重要，因为不太受欢迎的航线的数据通常很少。其次，时间分割可能导致评价数据不代表总体趋势的情况。换句话说，我将只根据每个模型预测起飞前最后几天发生的事情的能力来评估其性能，而不是整个预订窗口。这很糟糕。

随机分割数据通常是更可靠的方法。但是，由于数据集中的每个价格都属于与特定航班相对应的更广泛的时间序列，我们不能简单地随机分割数据。我建议一个更有效的方法是首先根据它们所涉及的航班对培训点进行分组。我们将保留这些航班的 10%用于评估，并使用剩余的 90%来训练网络。这样，训练过程就不可能泄露任何关于我们打算用于评估的飞行的秘密提示。

# 输入变量

在对航空公司定价的经济学进行了广泛的研究之后，我对收益管理系统在决定航班定价时所考虑的因素有了一定的了解。我确定了这些因素的一个子集，为模型提供一组基本的输入。

*   **始发机场:**描述航班出发机场的离散分类变量。
*   **目的地机场:**也是描述航班降落的机场的离散分类变量。可能的始发机场的集合等于可能的目的地机场的集合。
*   **星期几:**一个介于 1 和 7 之间的离散整数变量，表示航班在一周中的哪一天起飞，从周一到周日。
*   **一年中的第几周:**一个从 1 到 53 的离散整数变量，描述一周
*   **持续时间:**描述飞行时间的连续整数变量，以分钟为单位。
*   **出发前剩余天数:**连续整数变量，表示价格预测日期与出发日期之间的天数。

# Airport2Vec

这里有一个问题。作为输入，神经网络接受连续数据。我们提出的六个输入中有四个是不连续的。对此，我有几个可能的解决方案:

*   **整数 id**:每个可能的变量值被分配一个整数标识符，该标识符被传递给网络。例如，伦敦希思罗机场可能被分配一个 ID 1234。然而，现在让我们想象一下，巴黎戴高乐机场的 ID 是 2468。从网络的角度来看，如果我们将伦敦希思罗机场乘以 2，那么我们得到的就是戴高乐机场。这完全没有道理。整数 id 在这种情况下不起作用。
*   一键编码:我们可以为每个可能的机场提供单独的二进制输入。每当我们想要引用一个机场时，我们只需将相应的输入设置为‘1’，而其余的则保持为‘0’。这确实解决了由整数 ID 表示引起的虚假关系的一些问题，但是它导致了其他问题。也就是说，我们需要超过 3000 个始发地和目的地机场的输入(总共 6000 个),这产生了巨大的计算资源需求，并且指数级地增加了所需的训练数据量。
*   **实体嵌入:** *实体嵌入*是一种令人兴奋的新方法，可以解决一些与一次性编码相关的问题。在独热输入被传递到网络之前，它们首先被称为*嵌入层*的神经元中间层压缩。这意味着我们的 3000+维输入被映射到一个 *n* 维**实值**向量上。在这个新的向量空间中,“相似的”机场将会彼此靠近，从而使网络能够区分它们。*本文中的相似性*由网络在正常的监督训练过程中学习。

我决定实现第三种方法:实体嵌入。根据我提出的地图，每个机场将由一个 12 维向量表示。我将这个映射称为 Airport2Vec。

# 网络设计

我决定实现、训练和评估四种前馈神经网络设计。

*   **LR-1** :第一个网络设计是一个非常原始的线性回归模型，只使用了两个输入(出发前的天数和持续时间)。我预计这个模型的结果会很差——但是我可以用它们作为基准来比较后续的设计。
*   **NN-1** :第二个网络设计使用了与 LR-1 相同的两个输入，但是具有更复杂的拓扑结构，有两个隐藏层。
*   **NN-2** :倒数第二个网络设计引入了机场嵌入——允许网络区分特定航线的定价策略。我将隐藏层的数量增加到了 3 层，并在每层增加了更多的神经元，以增加建模额外复杂性的“能力”。
*   **PricingNet** :最后一次迭代使用了所有六个输入，四个分类输入有实体嵌入。

通过逐渐增加模型的复杂性，我能够测量添加新输入和调整拓扑的影响。它还允许我在过程的早期执行健全性检查，并验证我的一些“信仰跳跃”假设。除了 LR-1 之外的所有网络都使用了 Adam 优化和 ReLU 激活来保持其“面向未来”。

# 结果

令人欣慰的是，我的模型的性能随着每次迭代都有了实质性的提高。不出所料， **LR-1** 表现最差。下面的图表显示了网络对我提供的两个输入变量的线性拟合:飞行持续时间和离起飞的剩余天数。

![](img/4035f342f2e380ec090691d978328acf.png)

在 **NN-1** 中添加隐藏层意味着网络能够对训练数据拟合非线性曲线。这使得看不见的训练样本的平均绝对误差(MAE)降低了 40%。该模型了解到的持续时间、出发前天数和价格之间的关系在绘制时看起来也更酷。

![](img/4463898bab9c8b1bc44eb017938b6dac.png)

在这一点上，很明显，网络没有足够的信息来解释数据中的所有变化。对于 **NN-2** 来说，下一个合乎逻辑的步骤是包含每个航班的始发地和目的地机场的信息。

与 **NN-1** 相比，使用 Airport2Vec 压缩的机场信息的增加导致未见过的评估样本的 MAE 进一步降低了 48%。奇怪的是， **NN-2** 能够识别某些航线上的不对称定价策略。例如，下图显示了从伦敦飞往纽约的单程机票通常要比反过来便宜得多。不同国家司法管辖区的不同税收可能是发生这种情况的原因之一。

![](img/cb8965ebcfd777a8f3de73efa79b5640.png)

当我构建并训练 **PricingNet** (最后一次迭代)并整合关于出发日期和星期的信息时，MAE 比 **NN-2** 又有 13.5%的提高。下面的回归图展示了 10，000 个随机看不见的评估样本 PricingNet 的预测与实际价格的对比。

![](img/8d851a44b13a1ec62fdd4ca6cb983062.png)

这个图的总体形状看起来很令人放心，尽管很明显还有一些工作要做，以解释剩余的变化。

# 评估和学习

我获得的结果表明，开发一个通用的神经定价模型不仅是可行的，而且与该领域以前的一些工作相比也是简单明了的。一个简单的网络只用六个输入变量就能解释定价中的大部分差异。我怀疑，如果该网络是根据至少两年的价格数据进行训练的，PricingNet 显示的性能改进会更显著。这是因为该网络必须真正观察至少两个完整的年度周期，以确定周期性的季节性趋势。

虽然网络表现良好，但显然仍有工作要做，以提高整体准确性。剩余的残差变化意味着向旅行者展示网络输出可能会产生误导，而不需要说明该信息仅代表一般趋势，不一定是用户预期会遇到的准确价格。提供特定的数值可能会给客户这样的印象，即当前形式的网络比实际情况更准确。在进一步的工作开展之前，提供一个带有未标记价格轴的趋势图可能是明智的。

我在整个过程中了解到，即使在开发机器学习模型时，应用“精益”原则也非常重要。这意味着**开始简单的**，**限制输入变量**，**约束网络拓扑**。然后有可能**迭代**，测量每个模型的性能，并使用它来通知后续设计的开发。

## 参考

[1] O. Etzioni、R. Tuchinda、C. A. Knoblock 和 A. Yates，“购买还是不购买:挖掘机票数据以最大限度地降低机票购买价格”，载于第九届 ACM SIGKDD 知识发现和数据挖掘国际会议的会议记录*，ACM，2003 年，第 119-128 页。*

# 和我们一起看世界

在 Skyscanner，我们为旅行者提供旅行者服务。根据我们的 SEE (Skyscanner 员工体验)计划，我们的员工每年可以在我们 10 个全球办事处中的任何一个工作长达 30 天，如果您在您称之为家的国家以外的办事处工作，甚至可以在本国工作长达 30 天。当然，也总有机会去其他办公室出差或开会。

喜欢这个声音吗？看看我们目前的 [Skyscanner 产品工程职位。](http://9nl.es/calumfullstackfest_engjobs)

![](img/2381bbef38f9fcc4469fdc6d8de94496.png)

[Join the team](http://9nl.es/calumfullstackfest_engjobs)

# 关于作者

![](img/4b1ddf3d775107d7f242d5d53c889912.png)

我叫基兰，是伦敦 Skyscanner 公司的一名软件工程师，刚刚从约克大学毕业。我的团队管理着 Skippy，负责将数百万日常旅行者重定向到航空公司网站购买机票。工作之外，我是一个热心的钢琴家，喜欢学习更多关于技术、商业、金融、航空和法语的知识。