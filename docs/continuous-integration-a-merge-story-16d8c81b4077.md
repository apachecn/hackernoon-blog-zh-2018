# 持续集成:一个合并的故事🔊

> 原文：<https://medium.com/hackernoon/continuous-integration-a-merge-story-16d8c81b4077>

## 一个 6 个月的项目如何变成一个蒸汽工具的真实故事

![](img/1aa37fc8512095349e4f498fc03b1f7f.png)

The Cover Art for the North American game [Duke Nukem Forever](https://en.wikipedia.org/wiki/Duke_Nukem_Forever). Released in 2011 after 15 years of development, it’s a notable example of a [Vaporware](https://en.wikipedia.org/wiki/Vaporware).

Listen to the audio version!

上次我写了关于[持续集成:一个战争故事](https://hackernoon.com/continuous-integration-a-war-story-e442ad463473)。这篇文章描述了二战时期的[反坦克犬](https://en.wikipedia.org/wiki/Anti-tank_dog)项目，以及他们是如何在为时已晚的时候才发现它的问题。

这让我想起了过去真实的软件开发情况。

那是在我职业生涯的初期。

许多年前…

我在一家为汽车经销商开发企业软件的公司工作。它的设计是为了帮助销售。

像 [AWS 这样的云服务提供商。](https://aws.amazon.com/)不存在。客户负责购买计算机并在自己的内部网中运行软件。他们中的一些人还必须支付专门的 IT 部门来配置和维护他们的网络。

该系统是基于网络的。它建于 2003 年，延续了十年。

在前端，屏幕的每个部分都是在 iframe 中创建的。JavaScript 仅用于基本的交互。

在后端，我们使用了 [Java 平台，企业版](https://en.wikipedia.org/wiki/Java_Platform,_Enterprise_Edition)。所有的类和[包彼此紧密耦合](/@fagnerbrack/why-do-you-need-to-know-package-coupling-fundamentals-8e0fa8e33e20)，并且有一个 PostgreSQL 数据库。

这是一块巨石。

有一个 Java 类太大了，以至于 Eclipse 打开时会冻结。

> 许多年前，我在一个单片应用程序中工作，它有一个 Java 类，大到 Eclipse 打开时会冻结。

该系统的高级模块之一是客户模块。我的任务是改变它的设计，并添加大量积压的所需功能。

客户模块非常混乱，无法扩展。我建议使用 [jquery U.I.](https://jqueryui.com/) 重写它，这样我们就可以通过改变一个 CSS 文件而不是许多 CSS 文件来修改设计，并添加所需的功能。

这项任务看起来不简单，但我相信事情最终会得到解决。我开始一点一点地改变最大的 iframe 中的页面，然后改变与之相关的其他地方，直到整个模块被重写。

我不知道的一件事是，我正在更改的页面与另一个 iframe 的页脚相耦合。它还连接到其他 iframe 中的其他页面，这些页面又连接到另一个 iframe 中每个页面的列表界面。

> 客户模块又大又乱。然而，我建议全部重写，相信事情最终会得到解决。

哦，我说过没有测试吗？

10 年的代码，不可能验证其功能。甚至那些从一开始就在那里的人也不记得系统许多部分的用例。

尽管如此，我还是继续努力。我是唯一一个有勇气重写的人。在这种情况下，主人公不是错误修复者，而是**我**天真地一头扎进了这个大怪物，不知道其中的含义。

由于几个月前发生的事情，我确实从我的同事那里获得了一些信任。我做了同样的跳跃，用 JavaScript 将一个小的日程安排系统重新构建成一个功能更加完整的系统。从客户的角度来看，重写是成功的。虽然，现在回想起来，我可以看到它有很多可维护性的问题。

每个人，包括我自己，都相信事情会以同样的方式解决。

但事实并非如此。

> 客户模块没有测试，也没有人知道系统是如何工作的。然而，我建议全部重写，相信事情最终会得到解决。

我们当时用的是 CVS。我已经写了很多代码，并且在我的本地机器上运行良好。

过了一段时间，我开始被问到事情进展如何，还有多少剩余。我总是一遍又一遍地重复同样的事情:

> 不可能预测要花多长时间，但一切都很顺利。

在我看来的确如此。

即使没有测试，系统在那个时候也在为标准“工作”。当时的想法是，也许我在这里或那里错过了一些用例，但过一段时间后它们会被重新发现，我会修复它们。

然后 6 个月过去了…

一开始，我开始将主干集成到我的开发分支中。然而，没有人每天都做出小的改变，大多数情况下，一旦工作完成，就会有大的改变。我浪费了很多时间解决冲突而不是编码，所以几个月后我放弃了。

最后，我意识到团队的其他成员已经在相同的代码基础上开发了许多其他功能。由此产生的冲突数量和必须重写的特性数量使得任何合并都不可能。

除此之外，与此同时，另一个同事发布了一个移动网页版的客户模块。在新客户模块中编码的许多业务规则也在移动 web 版本中编码。桌面上的功能将不得不在手机上完全重写，重复这项工作。

那是一场噩梦。

但有趣的是:管理层没有预料到这一点。

他们的期望是，移动版本将很容易适应桌面需求，并且新的客户模块将成功发布。

然而，事情并没有按计划进行。

客户模块项目变成了[蒸汽器](https://en.wikipedia.org/wiki/Vaporware)并被放弃，就像[反坦克狗](https://hackernoon.com/continuous-integration-a-war-story-e442ad463473)一样。它不能集成到主干上。

此外，新功能不容易适应移动版本。

6 个月的工作就这样浪费了。

> 客户模块变成了一个[软件](https://en.wikipedia.org/wiki/Vaporware)，它的功能不容易移植到手机版上。

这是一个清晰的例子，说明持续的[整合](https://hackernoon.com/tagged/integration)是多么重要。

一开始，我试图将主干合并到我的分支中。然而，因为没有其他团队成员提交到主线中的小变化，所以很难跟上。

如果我每天都将一些东西合并到主干中，这将迫使我和团队的其他成员尽早处理冲突，而不是让它们累积起来。

这将允许每个人，包括管理层，更早地发现代码与移动设备的工作方式不兼容。这可能会让他们在一开始就改变整体战略，而不是等到其他事情都做不了的时候。

您可以称之为持续集成、基于主干的开发、“[早期发布、经常发布](https://en.wikipedia.org/wiki/Release_early,_release_often)”、“[通过早期和持续交付有价值的软件来满足客户需求](http://agilemanifesto.org/principles.html)”，或者任何其他通用术语。

总而言之，重点是:

> 避免不可逆转的代码冲突的唯一方法是每个人都尽快尝试合并。优选在同一天多次。如果有人花了太长时间，他们只会发现问题时，为时已晚。

那时候我没有今天的知识。今天，事情会完全不同，这些问题都不会发生。

我打错了电话。因此我是罪魁祸首。没有别人。

回想起来，除了持续集成代码之外，我还能想到一些小事情，可以用不同的方式来改进这个过程。

我本可以避免个人主义。

在那个团队里，没人练过[结对编程](/@fagnerbrack/pair-programming-8cfbf2dc4d00)或者[群殴编程](https://hackernoon.com/how-mob-programming-will-make-you-more-effective-590a1b7e0418)。没有人有足够的经验看到这些好处。

如果我提出建议并进行实验，或者至少理解了交流的好处，那么我们可能会知道每个人都在做什么。我们早就发现，客户模块重写不容易与移动版本集成，反之亦然。也许这将使我们改变我们的策略和编码方式。

我本可以在编码前想得更多。

少编码，多思考。对于那些寻求“黑客”头衔的人来说，这是一种反直觉的思维模式。如果你只想着编码，你只能成为一个[牛仔编码员](http://wiki.c2.com/?CowboyCoder)。

我就是那样的人。

我可以优化我的学习，不仅是为了[编程](https://hackernoon.com/tagged/programming)，也是为了软件开发团队如何工作。作为一名开发人员，[我是帮助塑造组织的专业人士](https://hackernoon.com/why-corporate-culture-is-toxic-for-software-development-e4f0a2a203c6)，我必须对出错的事情负责。

我最终责怪这个过程。我最后[责怪手术刀](https://hackernoon.com/the-doctor-and-the-scalpel-78656f508c9a)。

如果你从未经历过这些问题，那么恭喜你。通过别人的失败，你已经学到了软件开发中最重要的事情之一:不要花太长时间去做别人的工作。

> 如果你能解决一个复杂的问题，那你就不聪明。如果你能从别人的错误中吸取教训，从一开始就避免复杂的问题发生，那你就是聪明的。

互联网上充满了成功的故事和害怕暴露自己失败的人。

我没有。

失败和成功一样好，不同的是现在你可以从中吸取教训了。

> 我没有失败。我刚刚发现了一万种行不通的方法。
> ——[托马斯·A·爱迪生](https://www.brainyquote.com/quotes/thomas_a_edison_132683)

没有一个软件开发人员希望 6 个月的工作被浪费掉，我已经从惨痛的教训中学到了这一点。

希望这能帮助其他人不要浪费 6 个月的时间在一些本可以在 6 分钟内避免的事情上。

希望…

参见[如何防止这些问题发生的技术](/@fagnerbrack/code-less-think-more-incrementally-98adee22df9b)。

感谢阅读。如果你有一些反馈，请通过 [Twitter](https://twitter.com/FagnerBrack) 、[脸书](https://www.facebook.com/fagner.brack)或 [Github](http://github.com/FagnerMartinsBrack) 联系我。