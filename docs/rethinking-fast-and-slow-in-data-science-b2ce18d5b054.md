# 重新思考数据科学中的快与慢

> 原文：<https://medium.com/hackernoon/rethinking-fast-and-slow-in-data-science-b2ce18d5b054>

## 如何让数据科学实验变得敏捷

长期规划和短期灵活性之间的紧张无处不在，包括[数据科学](http://bit.ly/quaesita_datasci)方法论。产品开发团队是否有可能调和快速迭代和深度研究过程的缓慢移动的庞然大物，或者他们必须选择一个？

![](img/e3cb9faf28fc28d3e7a11862024f9c3a.png)

Agile iteration or rigorously planned deep research: can data science have both, or must it choose?

在我们的案例研究中，我们将采用 [BrainQ](http://brainqtech.com/) 。**剧透提示:**不仅有可能调和数据科学实验的快方法和慢方法，而且 BrainQ 在此过程中吸取的经验教训提供了一个[路线图](/the-lever/running-agile-machine-learning-experiments-4d500314ab11)供您的团队遵循。

BrainQ 的任务是用人工智能技术治疗神经疾病。如果你想迎合我们对敏捷性的讨论，那可能会让你止步不前——我们面对的是一个庞然大物的庞然大物:*医学研究数据科学。*

传统的[数据科学](http://bit.ly/quaesita_datasci)已经够烂了；大多数从业者都同意，如果你的心放在可信的统计推断上，你的未来会有很多思考和规划。每一堂统计学 101 课都会教你在开始艰苦的数据收集过程之前，就已经想好了你的假设、方法和假设。测两次，切一次，想都别想迭代！

![](img/b3acca49fbb519ba72290fd4dd018fc0.png)

Medical research is an especially difficult place to inject an agile approach into data science experiments. BrainQ’s [case-study](/the-lever/running-agile-machine-learning-experiments-4d500314ab11) with EEG applications shows it can be done, which is good news for other startups whose industries come with less red tape.

每当你加入哪怕一点医学研究，一切都会变慢。现在，这个过程开始被监管批准和临床试验协调的强力胶所包裹。敏捷——将您的过程分解成小的、可预测的、迭代友好的组件——是一个梦想，但是您如何将敏捷方法注入到深度医学研究中呢？

> 数据科学非常适合探索和严谨，尽管并不总是同时如此。

诀窍是两次出拳的方法。数据科学非常适合探索和严谨，尽管并不总是同时如此。事实证明，数据科学探索和分类值得做的事情的最佳实践都与灵活性有关。不是每件事都要慢慢地、小心翼翼地做...

# 修复破碎的心态

首先要改变的是传统统计学和 T2 数据科学培训中的思维模式。一个典型的大学统计学考试提出了一系列假设供初学学生测试，同时还有数学上的假设。大部分的技巧都是小心翼翼(恰当地！严谨！)测试他们。从我的第一次 STAT101 期中考试到我的统计学博士资格考试，我经历的形式基本相同。这类事情占了我们培训的大部分，所以它经常是新上任的[统计学家](http://bit.ly/quaesita_damnedlies)珍藏的部分。

> 你有没有注意到假设一直存在？

你有没有注意到这些假设一直都存在——教授想得很好——而学生很少质疑它们的起源？一旦这个神圣的问题出现了，我们当然要以最严肃的态度来回答它。现在把整个事情颠倒过来:你必须提出假设和假设。你是怎么做到的？

![](img/465b0f560833f4de89eaa78c154d33d8.png)

It’s time to think about where the rigid mindset commonly encountered with classically-trained statisticians and data scientists comes from. Could it have something to do with traditional statistical education?

一种选择是模仿你在课堂上习惯的东西。在壁橱里沉思，提前想出假设和设想。在任何数据之前，设计数据收集策略和统计测试。做好一切准备，然后一步到位。

听起来不错？我们忘记了谦逊。很有可能我们在设置中犯了一个错误。作为一个在这方面有十多年经验的人，我学到的最好的教训之一是:预先考虑所有事情太难了。

> 事先想好每件事太难了。

预先锁定一种方法并严格遵循它意味着我们最终会得到一个解决错误问题的完美方案。(爱称之为在统计学中犯第三类错误。)

你在课堂上永远不会看到的是，如果你搞砸了该如何提问，一切会变得一团糟。那些生活课程很难模拟，当你想象没有想象你忘记想象的一切时，你的头可能会弹出来。

# 获得敏捷的许可

因此，如果从[数据科学](http://bit.ly/quaesita_datasci)阵营中感觉舒适的不灵活方法不起作用，该怎么办？当然，融入一些敏捷的思维。这里的大脑黑客:允许你的方法在第一次[草率](http://bit.ly/quaesita_analysts)和烧伤你的一些最初的时间，精力和数据在告知一个好的方向以后。

> 这里的大脑黑客:允许你的方法是草率在第一次通知一个好的方向以后。

我们该怎么做呢？**允许你自己处于这样的阶段，你追求的唯一结果是如何更好地设计你的最终方法。**这意味着鼓励您从以下几个方面开始:

*   **低质量数据:**使用小样本量、合成数据和非随机采样数据来了解数据收集过程本身。
*   **粗糙和肮脏模型:**寻求对最小努力的回报是什么的理解。从糟糕的算法开始，你知道这些算法只会给你一个基准，而不是你的最佳解决方案。
*   **多重比较:**不要选择单一的假设检验，而是随意用厨房水槽扔你的数据来寻求灵感。您这样做是为了发现值得您最终采用的信号。添加截止日期和 MVP 里程碑，以避免无限抛光、戳和戳的陷阱。

> 这个建议几乎打破了你在课堂上学到的每一条规则。

如果你体内的统计学家还没有尖叫，我钦佩你的冷静。这个建议几乎打破了你在课堂上学到的每一条规则。那么，我为什么支持这些“不良行为”？因为你处于哪个项目阶段很重要。**我完全赞成以后遵循标准建议，但早期试点阶段有不同的规则。**

重要的是通过记住这两个原则来避免新手犯错误:

*   **不要把早期的发现看得太重。**
*   **准备好最终版本时，做**收集一个干净的新数据集。

要了解更多关于使用独立的项目阶段进行探索和精确化的信息，请在这里阅读我关于数据分割的文章[。](http://bit.ly/quaesita_sydd)

# 数据科学试点研究

您正在使用您的初始迭代探索性工作来告知您的最终方法(您将像最勤奋的统计学家一样认真对待)。诀窍是利用探索的灵活性来告知沿途值得考虑的事情。如果您已经习惯了传统统计推断的僵化，那么是时候重新发现科学试点研究的好处，并找到将等效研究嵌入数据科学的方法了。

> 防弹最终版本的最佳灵感来源是在 MVP 的过程中吸取的经验教训的集合。

![](img/86c9336b35b313d0608c3455ff287b0d.png)

这是 BrainQ 所采用的方法，它为他们创造了奇迹。如果你想了解 BrainQ 过程的更多细节，请查看关于 [The Lever](https://medium.com/the-lever) 的完整案例研究，这是一个面向初创公司的应用人工智能建议的技术来源，由[谷歌开发人员 Launchpad](https://developers.google.com/programs/launchpad/) 运营，由[彼得·诺维格](https://en.wikipedia.org/wiki/Peter_Norvig)和我共同编辑。

寻找一个详细的指南来帮助你开始一个应用的 ML/AI 项目？我会支持你的。尽情享受吧！更喜欢狼吞虎咽一个 YouTube 课程？我做了一个给你看。

Enjoy the entire course playlist here: [bit.ly/machinefriend](http://bit.ly/machinefriend)

# 喜欢作者？与凯西·科兹尔科夫联系

让我们做朋友吧！你可以在 [Twitter](https://twitter.com/quaesita) 、 [YouTube](https://www.youtube.com/channel/UCbOX--VOebPe-MMRkatFRxw) 、 [Substack](http://decision.substack.com) 和 [LinkedIn](https://www.linkedin.com/in/kozyrkov/) 上找到我。有兴趣让我在你的活动上发言吗？使用[表格](http://bit.ly/makecassietalk)取得联系。