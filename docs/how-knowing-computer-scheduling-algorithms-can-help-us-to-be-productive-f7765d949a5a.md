# 了解计算机调度算法如何帮助我们提高工作效率？

> 原文：<https://medium.com/hackernoon/how-knowing-computer-scheduling-algorithms-can-help-us-to-be-productive-f7765d949a5a>

![](img/39f3982894e4d796d51f4b5f33fe7e51.png)

我们都想高效快速地完成更多的事情。电脑最擅长了。因此，让我们试着理解一些计算机调度算法，看看我们如何将它们应用到我们的日常生活中，以提高我们的工作效率。

重要的事情先来！你不想迟到。

# 如何在截止日期前完成？

如果你在为顾客服务，你会想尽可能快地为每个顾客服务。假设为每位顾客服务花费的时间几乎相等，那么你可以按照他们到达的顺序来帮助他们。对客户来说，最理想的承诺或到期时间是他们一走进你的门。

将相同的策略应用于任务:

```
*You should start with the task due soonest and work your way toward the task due last*
```

这种策略称为**最早到期日。**哪种方法最能减少迟到？但是，我们有一个问题，如果每个任务需要不同的时间来完成。

# 如何减少待定任务列表？

回到客户服务的例子，如果服务每一个客户需要不同的时间，那么你应该服务最多的客户。假设我们可以按服务类型划分客户，并且我们知道每种服务类型的典型完成时间。然后，最好的方法是挑选需要最快完成时间的服务类型的客户。

这个策略是基于**摩尔的算法**说，

```
*We start out just like with Earliest Due Date — by scheduling tasks as they arrive, but when deciding which task to do next, choose the quickest one and repeat this process.*
```

这就像首先关注减少你的待办事项清单的长度。还有，每一件未完成的任务都可能像是背上了一个精神负担。浏览最简单的项目会带来某种程度的放松。

不足为奇的是，这种方法与《把事情做完》一书中的建议一致，即立即执行任何耗时不到两分钟的任务。

这是完成更多事情的好方法，但是所有的工作不可能同等重要。

# 如何完成大部分贵重物品的工作？

扑灭厨房里的真火可能应该在给客户发一封快速的电子邮件“灭火”之前完成，即使前者需要更长的时间。

在调度中，这种重要性的差异被捕获在一个称为`weight`的变量中。当你仔细检查你的待办事项清单时，这个重量可能感觉是字面上的——通过完成每一项任务，你从肩膀上卸下的负担。一项任务的完成时间显示了你承担这一负担的时间，因此最小化加权完成时间的总和(即每项任务的持续时间乘以其权重)意味着在你完成整个议程时最小化你的总压力。

实现这一目标的最佳策略是对摩尔算法的简单修改:

```
*Divide the weight of each task by how long it will take to finish, and then work in order from the highest resulting importance-per-unit-time to the lowest.*
```

如果你是一名顾问，体重可以从你的收入中推断出来。因此，简单地将每个项目的费用按其规模划分，从最高的时薪到最低的时薪。

可能很难给你的每一项任务分配一个重要程度，但是有一个快速的经验法则:

> 如果一项任务重要两倍，只优先考虑花费两倍时间的任务。

但是如果任务依赖于其他任务，这会给我们带来新的问题。

# 如何摆脱困境？

我们有时可能会陷入困境，因为一项重要的任务只有在另一项不太重要的任务完成后才能完成。在计算机科学中，这个问题被称为[优先级反转。](https://en.wikipedia.org/wiki/Priority_inversion)

> **优先级反转**是调度中的一个问题场景，其中一个高优先级任务被一个低优先级任务间接抢占，有效地“反转”了两个任务的相对优先级。

这个问题的实际解决方案是**优先级继承**。即

```
*To get unstuck is to treat the unimportant things as being as important as whatever it’s blocking.*
```

# 如何完成连续不断的任务？

如果我们有有限的任务清单，生活会很简单。事实上，并非如此。如果任务在不可预知的时刻丢给你。有效的方法是切换任务，这在计算机科学中被称为[抢占](https://en.wikipedia.org/wiki/Preemption_(computing))。

> **抢占**是暂时中断计算机系统正在执行的任务的行为，目的是在稍后的时间恢复该任务。

它可以在作业调度中用作

```
*Each time a new piece of work comes in, divide its importance by the amount of time it will take to complete. If the figure is higher than for the task you’re currently doing, switch to the new one; otherwise stick with the current task.*
```

但是先占权不是免费的。这是以上下文切换为代价的。

# 职业倦怠是如何发生的？

每次你切换任务，你都要付出代价，这在计算机科学中被称为上下文切换。当计算机处理器将注意力从给定的程序转移开时，总会有一定数量的必要开销。它需要有效地标记它的位置，并将所有与该程序相关的信息放在一边。然后它需要决定下一步运行哪个程序。最后，它必须找出该程序的所有相关信息，找到它在代码中的位置，并开始工作。反复快速的上下文切换会导致计算机性能下降或崩溃。这种现象称为[颠簸](https://en.wikipedia.org/wiki/Thrashing_(computer_science))。

你可以认为这就像是在玩一组球。如果变戏法的人拿的球比他能拿的多一个，他就不会丢下那个**球；何滴* ***一切*** *。**

*颠簸是一种非常容易识别的人类状态。如果你曾经有过这样的时刻，你想停止做所有的事情，只是为了有机会写下你应该做的所有事情，但是抽不出时间，你已经试过了。你将一事无成。你感到筋疲力尽。*

# *如何减轻一心多用的负担？*

*减轻上下文切换负担的一些技巧。*

## *不要把你的盘子装得满满的。*

*矛盾的是，完成事情的最佳策略可能是放慢速度。一种在动荡开始前避免它的方法是学习说不的艺术。*

## *有时候随机的顺序比完美的时间表更好*

*切换上下文中元工作的最大来源之一是选择下一步做什么的行为。因此，即使以错误的顺序完成任务也比在混乱状态下什么都不做要好。*

*按照同样的思路，几年前，Linux 核心团队用一个在计算进程优先级方面不太“智能”的调度程序替换了他们的调度程序，但通过减少计算时间，大大弥补了这个不足。*

*例如:与其先回复最重要的邮件——这需要对整个画面进行评估，这可能比工作本身还要花时间——也许你应该以随机的顺序回复它们，或者以它们出现在屏幕上的任何顺序。*

## *承诺在最短的时间内完成一项任务*

*计算机操作系统调度程序通常定义一个“周期”,在这个周期内，每个程序都保证至少运行一点点，系统给每个程序一个“时间段”。*

*为了利用这个策略，你应该学会在两个原则 ***响应性*** 和 ***吞吐量*** 之间取得平衡:你对事情的反应有多快，以及你总体上能完成多少。*

*总的想法是:*

```
**Stay on a single task as long as possible without decreasing your responsiveness below the minimum acceptable limit. Decide how responsive you need to be — and then, if you want to get things done, be no more responsive than that.**
```

*实现这一点的方法是[时间框](https://en.wikipedia.org/wiki/Timeboxing)，即给每个计划活动分配一个固定的时间段，称为**时间框**。*

*另一个非常有用的技术是 Pomorado，它使用一个计时器将工作分成间隔，传统上长度为 25 分钟，由短暂的休息分开。*

## *批处理相似的任务*

*如果你发现自己在做大量的上下文切换，你也可以采用计算机科学中的另一个想法:[中断合并](https://en.wikipedia.org/wiki/Interrupt_coalescing)。计算机通过等待直到某个固定的时间间隔并检查一切来做到这一点，而不是通过上下文切换来处理来自各种子组件的单独的、不协调的中断。*

*例如:如果你有五张信用卡账单，不要在账单到达时支付；第五单来的时候一口气搞定。*

**另一个例子是——为了避免因经常查看邮件而分心，大约每 4 小时查看一次。**

*在工作场所，控制办公时间是消除同事干扰的一种方式。定期安排的会议是我们对抗自发中断和无计划的上下文切换的最佳防御手段之一。*

# *摘要*

*除了您想要解决的调度问题，您还需要选择正确的度量来优化。因为我们选择的度量将直接影响哪种调度方法效果最好。*

> **在计算机科学中:在你有一个计划之前，你必须首先选择一个度量标准。**

*下面是一个快速指南，总结了何时根据要优化的指标选择每种策略。*

*Scheduling Strategies*

# *信用*

*这篇文章的灵感来自于的精彩著作 [Algorithms to live。如果你喜欢这篇文章，可以考虑阅读](http://amzn.to/2F1ZyLy)[书](http://amzn.to/2F1ZyLy)，它提供了类似的策略*

*   *最佳停止——何时停止寻找？*
*   *探索新事物与利用最有效的方法。*
*   *整理*
*   *贮藏*
*   *拜尔斯法则——预测未来。*
*   *过度适应——什么时候应该少思考。*
*   *放松——顺其自然。*
*   *随机性——什么时候听天由命。*
*   *网络——我们如何联系。*
*   *博弈论——他人的思想。*

**最初发布于*[*http://erajasekar . com/posts/how-known-computer-scheduling-algorithms-can-help-us-productive/*](http://erajasekar.com/posts/how-knowing-computer-scheduling-algorithms-can-help-us-productive/)*。**