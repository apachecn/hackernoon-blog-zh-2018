# 走向强大的人工智能——缺少几个部分。

> 原文：<https://medium.com/hackernoon/towards-strong-ai-few-missing-parts-e8576ea1537f>

![](img/50c0311d60b38f23b784305d641252bd.png)

> 你好。我强烈地感觉到，走向一般/强人工智能的进步不仅应该由计算资源和医学成像来推动，还应该由自我反思和人类心理学来推动。我是机器学习工程师，也练习各种冥想。在这里，我分享一些关于当前人工智能架构缺失部分的见解，基于内心的见解。
> 
> 我可能完全错了，过于简单化，可能会不小心跳过我的论点中的一些逻辑粘合——如果需要，请要求解释。

# 中国室问题和随机数发生器。

如果你正在读这篇文章，很可能你知道什么是中国式的房间辩论。如果没有，请阅读维基，这很简单。我们就想象一下，强 AI 已经造好了，放在中文室里。它运行完美，通过了典型的图灵测试，你不能说房间里是人还是机器。我想假设两个想法:

*   给定无限的时间和资源，外部研究人员将能够绘制所有可能的输入和输出，以及中文室算法内部所有可能的互连。所以，他能够在房间里复制算法，证明确实有算法，而不是人类。
*   如果有人类在里面，外部研究人员将无法绘制出房间内系统的所有可能状态，也永远不会得出“里面有机器”的结论。这不是因为某种形式的“自我重新编程”或“意识”,只有人类有能力，而是因为人类的湿大脑线路有一个内置的噪声组件，驱动不可预测性。

这是我最喜欢的部分。有人可能会争辩说，人工智能算法可以有一个有意内置的噪声组件(丢弃层)。但是，请记住，我们计算机中的大多数随机数发生器实际上都是伪随机的。每个工程师和黑客都知道。因此，给定一个无限时间的行为，任何系统的任何复杂性，使用伪随机数发生器可以映射，从而失败图灵测试。

所以，我的观点是——强人工智能将被创造出来。很有可能像“F”或“G”这样的大公司已经有了通过某种不太严格的图灵测试的对话机器人。这种人工智能将会发展，这些算法肯定会在其功能中使用随机数生成器。总有一天，我们会有完美的人工智能通过非常复杂的图灵测试。这个机器在我看来会有心智/意识吗？不。但是，如果你把伪随机函数换成基于量子噪声的真随机数发生器——我会平等地对待这个大脑。我确实认为我们(人类)制造了某种噪音，这种噪音有一个非常基本的属性。

另一个疯狂点——虽然这是边缘科学和伪科学的一部分，但我认为量子波动在意识中的作用仍有待发现。去看看普林斯顿的 Noosphere 项目，从中寻找灵感。

# 用相反的损失函数解决激励问题。

好吧，交换随机数来源是一种伦理问题，而不是技术问题。还有一件事，更接近真实世界的结果。正如我在上一篇文章中粗略解释的那样，我对当代神经网络的发展方向很不满意。这是因为一般的“过滤泡沫”的事情，由社交媒体，搜索引擎和社交互动规则创建。

神经网络训练实际上是基于同样的“过滤气泡”思想，并将其完善到最大限度。由于该系统基于历史数据，它学会利用现有趋势。当应用到现实世界时，它开始支持那些预先存在的趋势，开始一个潜在的无休止的反馈循环。*人类的思维不是这样运作的*。

想想这个例子——强化学习。强化学习(像任何其他形式的监督学习一样)有一个目标、一个度量标准或一个分数——不管你怎么称呼它，它会优化自己以改进那个度量标准。*人类的思维也不是这样的。最主要的区别是，人类没有唯一的目标衡量标准，他们试图优化。我认为我们总是至少有两个，通常是相反的。可以说，人类的目标可以归结为一件事——生存。我认为还有一种——为死亡而斗争。佛洛依德用他的爱欲和塔纳托斯概念抓住了这一点。没有死亡的进化是不可能的，死亡驱动是我们每个人的天性。他们之间的极性和紧张关系是让你每天早上醒来，变得有创造力和进化的原因。*

所以，这就是为什么我喜欢生成性对抗网络。当你查看正确配置的 GAN 的训练进度时，生成器和鉴别器之间总是存在冲突和紧张。这在某种程度上可以转化为构造函数和析构函数形式的强化学习。

> 总之，我的重点是。人工智能的总体进展可以而且应该由来自冥想等自我反思实践的发现推动，最好是由研究人员自己进行。

请，评论，分享，鼓掌把我带入一个正反馈循环，被迫继续写作。联系我，如果你想分享你的或可能实现的东西。萨沙(点)萨维苏年科(at) gmail(点)com