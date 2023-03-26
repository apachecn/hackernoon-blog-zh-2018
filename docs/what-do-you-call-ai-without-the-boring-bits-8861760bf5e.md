# 没有那些无聊的部分，你怎么称呼人工智能？

> 原文：<https://medium.com/hackernoon/what-do-you-call-ai-without-the-boring-bits-8861760bf5e>

## 库伯弗洛。

如果你继续读下去，你可能会爱上[***【kube flow】***](http://bit.ly/kubeflow)*。这不仅仅是一种可爱的使用* [*机器学习*](http://bit.ly/quaesita_emperor) *的方式，它对* [*数据科学*](http://bit.ly/quaesita_datasci) *社区来说意义重大。已经警告过你了。*

![](img/c75757e90c46d13bbf63ed523218d1ee.png)

# 艰难的道路

先说一个古老的现象:敬神*敬神*。我们中的许多人都为此感到内疚，但这在经历了几十年欺凌的 STEM 人群中尤为常见。这种捉弄是通过你能多好地重新发明一个轮子，你自己能多好地做每一件事，以及你接受的帮助有多少来衡量你的价值。当你处于这种心态的最深处时，你会认为任务的难度或复杂性比它的价值更重要。(也许有一天你会利用假期从头开始建造一台电脑及其操作系统，以确保你真的了解所有的一切。)

> 艰难的道路意味着在苦差事的粘稠糖浆中游泳，永远做不完事情。

我年轻时就有这种态度，但今天我为那些无缘无故让生活变得艰难的势力感到尴尬。进步往往朝着让事情变得更容易的方向发展。我更喜欢庆祝进步，而不是骄傲我仍然可以艰难地做事。当然，我可以用纸和笔[反转一个矩阵](http://bit.ly/moorepenrose)…凭什么我会这么做？因为世界上所有的电脑都可能被欣喜若狂？

![](img/5af3c9f584f33335f40b34bd20be89b7.png)

Still waiting for this tech to make a comeback. Any day now. I’d better practice my [backpropagation](http://bit.ly/backpropagate) by hand.

让我们欢迎科技带来的辛劳减少。让每个人都慢下来的糟糕工具和流程属于濒危物种——让我们开始为做有价值的事情而不是困难的事情感到自豪，如果你坚持应对真正的挑战，为什么不努力让困难的事情对其他人变得容易呢？

在体现反对强硬路线的工具中， [**Kubeflow**](http://bit.ly/kubeflow) 是一个我非常喜欢的项目——[大卫·阿龙奇克](http://bit.ly/aronchick)和我在一起喝了第一杯咖啡后，在狂热的狂热中写下了一些最初的蓝图。我很确定那天我没有起床吃饭，尤其是当设计文档的第一稿还在我们脑海里的时候。

# 什么是库伯流？

那么，什么是 [**Kubeflow**](http://bit.ly/kubeflow) 以及为什么它激发了所有的强烈热情？

我和[大卫](http://bit.ly/aronchick)写的文件是以不同的名字开始的。最终它被命名为 Grace，但是我们称第一个版本为美丽的机器学习。好吧，也许这不是我最原始的命名时刻，但放我一马吧。我们这些统计学家喜欢完全按照罐头上写的去做。(谢天谢地后来有个有点创造力的人修好了，对吧？)

这就是 [**Kubeflow**](http://bit.ly/kubeflow) 的重点:漂亮的机器学习。具体来说，[数据科学家](http://bit.ly/quaesita_datasci)在将多云混合环境中的机器学习作为一个整体堆栈进行提交时的体验之美。如果你在库伯流之前的日子里尝试过自己动手做，它一点也不漂亮。

In case you prefer videos to reading stuff, here’s me summarizing [David’s talk on Kubeflow](http://bit.ly/kubeflowdavid) from Next 2018.

如果你和我一样，你几乎不能忍受数据科学过程中的无聊部分，当你做涉及最少的狡猾和最大的苦差事时，很难在心里唱歌。(一个个人例外是我对数据清理的反常热爱，我喜欢把它作为一种冥想活动，和玩 [2048](http://bit.ly/2048time) 一样。嗯……在混乱中注入秩序。好吃。)

# 科学家想要什么数据

你想在那些有趣的模型上工作，你想专注于测试你的假设，你想制作华丽的情节(你是那种当我说互动的，闪亮的，动画的时候心跳有点快的人吗？)，并且您希望获得可操作的见解。是啊，我也是。但首先，我们必须在设置、操作系统和扩展以及各种令人痛苦的无聊事情上花费大量时间。我的意思是，拜托，相对于小数据，真的有人喜欢给大数据特殊待遇吗？

> 就大多数数据科学家而言，如果我们编写的所有代码对于大型或小型、笔记本电脑或云、原型或生产都是一样的，那就太好了…

在内心深处，我们知道不是这样的唯一原因是我们生活在黑暗时代，我们的工具很糟糕。

如果我们让他们不被怀疑呢？而且，当我们在做这件事的时候，如果我们可以有一个理想的数据科学工作流程，每个梦想的铃铛和哨子都布置好了，这样使用它们就毫不费力，我们就可以继续我们工作中有趣的部分了，怎么样？

> Kubeflow 是关于给数据科学家们一种体验，如果他们摆脱了所有他们不喜欢的复杂部分。

*那个*就是漂亮的机器学习，最终 [Kubeflow](http://bit.ly/kubeflow) ，就是这么回事。这是一幅实现梦想的蓝图，给数据科学家们带来了他们在摆脱了所有他们不喜欢的繁琐细节后的体验。Kubeflow 还不完美(它于去年年底首次与世界见面)，但它正朝着正确的方向快速前进。

![](img/55b0c1a361e56bda0cee99d4d45e1082.png)

# 让我们的工具不再受支持

如果我们想着手解决问题，我们应该从哪里开始？Kubeflow 团队选择了可组合性、可移植性和可伸缩性。

*   **可组合的 ML** 容纳了所有不同的数据科学工具。
*   **便携式 ML** 意味着可以轻松地从笔记本电脑上的原型模型转换到生产中运行它们。
*   **可扩展的 ML** 意味着从你的小型数据原型到庞大的数据管道是毫不费力的。

您知道什么在可组合性、可移植性和可伸缩性方面真正优秀吗？[集装箱](http://bit.ly/hellodocker)和[库伯内特斯](http://bit.ly/k8scomic)！

> 对于大多数数据科学家来说，即使谈论这些技术术语也是一种即兴练习。

呃，明白了。对于大多数数据科学家来说，即使谈论这些技术术语也是一种即兴练习。如果你还不是专家，在 Kubernetes 上使用机器学习会带来一个痛苦的世界——不仅因为有太多你必须成为专家的事情与你的舒适区无关，还因为所有的初级读本都是为不同类型的工程师编写的。除了你的[机器学习](http://bit.ly/quaesita_emperor)黑带外，你还需要获得第二条黑带。我们又一次面对这个问题了！

# 第二条黑带？

大多数数据科学家认为学习所有这些东西是一种特殊的折磨，那些对此感到兴奋的人可能根本没有多余的时间。难道其他人不能去帮我们学习 [Kubernetes](http://bit.ly/k8scomic) 吗，这样我们就可以继续我们的实际工作了？

把 [Kubeflow](http://bit.ly/kfstart) 当做你的志愿者伙伴吧。它的全部目的是让每个人都能在 Kubernetes 上轻松开发、部署和管理可移植的分布式机器学习。不需要额外的黑带。我们的目标是让你的整个堆栈变得令人惊讶，并完成你想要的每一个漂亮的玩具。我们还没到那一步，但我们正在加速前进。例如， [Kubeflow](http://bit.ly/kfstart) v0.2 可以让您完全设置一个(！)单行代码。

![](img/e0abfce7a00c8d73c29efd60dd3cdc49.png)

You can deploy Kubeflow with the one-step script included in the download. For more info, see [David’s talk](http://bit.ly/kubeflowdavid).

只需一系列努力，就能让 [Jupyter](http://bit.ly/hellojupyter) 笔记本电脑、分布式培训和模型服务适合混合云环境。哦，还有可定制的 ksonnet 打包，H2O.ai 工具，以及强大的超参数调整。数据科学家们，想想这些额外的事情，看着我的眼睛，告诉我你们想找出基于作业提交、特定于云的虚拟机和数据泄漏预防的自动扩展。没有吗？好吧，幸运的是你不需要。祝贺你等待了足够长的时间，让它为你照顾，有点像你不需要建立自己的电脑了。

![](img/afeef3086d4041059be174aa2e7ce75c.png)

A glimpse of what hyperparameter tuning with Katib looks like. What’s a hyperparameter? It’s that dial on your toaster. When your [machine learning algorithm](http://bit.ly/quaesita_emperor) has a lot of knobs and dials, figuring out what to set them to can be annoying — luckily tools like Katib are here to help.

# 对包容的态度转变

[数据科学](http://bit.ly/quaesita_datasci)是让数据变得有用的学科，由于世界正在以前所未有的方式产生数据，数据科学家[所做的工作变得比以往任何时候都更有必要。我们都希望有一个信息被用来改善生活而不是收集蜘蛛网的世界。有如此多的数据和如此多的工作要做，我们需要尽快降低进入分析的门槛。我为库伯弗洛在其中扮演的角色感到骄傲。](http://bit.ly/quaesita_roles)

> 我们正在进入一个时代，这个时代赋予每个站在巨人肩膀上的人力量。

事实上，通过谈论 Kubeflow 有多可爱闪亮，我可能会分散你对更重要的一点的注意力:它代表了什么。这是对包容性的态度转变，也是我们社区在一个加速每个人站在巨人肩膀上的能力的时代所采取的早期步骤之一。它指向一个光明而激动人心的未来！

> 想象一个工具如此简单的世界，每个人都可以使用它们。那就是我们要去的地方！

我感到自豪的是，作为一个数据专业人士的社区，我们开始避免艰难的方式，并逐步使复杂的事情对其他人来说变得简单。我们开始说，“*不知道血淋淋的幕后细节？没关系！我们拒绝丢下你。过来坐在我们的桌子旁，和我们一起创造不可思议的东西。”*

对我来说，那闻起来像是进步——我们再来点儿吧！

![](img/2f24044970268ba56ea6c3cb3815bdaf.png)

如果你有兴趣尝试 Kubeflow，从这里开始[。或者在大卫精彩的](http://bit.ly/kfstart)[演讲](http://bit.ly/kubeflowdavid)中了解更多。

# 感谢阅读！YouTube 课程怎么样？

如果你在这里玩得开心，并且你正在寻找一个为初学者和专家设计的有趣的应用人工智能课程，这里有一个我为你制作的娱乐课程:

Enjoy the entire course playlist here: [bit.ly/machinefriend](http://bit.ly/machinefriend)

# 喜欢作者？与凯西·科兹尔科夫联系

让我们做朋友吧！你可以在 [Twitter](https://twitter.com/quaesita) 、 [YouTube](https://www.youtube.com/channel/UCbOX--VOebPe-MMRkatFRxw) 、 [Substack](http://decision.substack.com) 和 [LinkedIn](https://www.linkedin.com/in/kozyrkov/) 上找到我。有兴趣让我在你的活动上发言吗？使用[表格](http://bit.ly/makecassietalk)取得联系。