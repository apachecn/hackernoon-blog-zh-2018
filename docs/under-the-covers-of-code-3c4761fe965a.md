# 在代码的掩护下

> 原文：<https://medium.com/hackernoon/under-the-covers-of-code-3c4761fe965a>

![](img/0e485f6810fd48d3c9a14a6f11257da5.png)

我记得大约 20 年前，当我还有点天真并且对自己的观点更有信心的时候，我认为代码是真理的最终来源。文件对我来说是次要的。当代码出现在你的梦里，你开始质疑当它的创造者死去时，代码会发生什么。

从非常狭隘的意义上来说，代码是真理的最终来源。这就是将要被执行的。但是请听我说——我读代码(我的或其他人的)越多，我就越害怕阅读代码是一种猜谜游戏。有些东西丢失了——原作者的思路，他们的推理，见鬼，甚至他们的思维模式。

想到这一点，我不禁问自己一个问题:如果我们不太理解代码，它真的是真理的来源吗？它是什么真相的来源？我已经浪费了很多时间思考这些问题和相关的问题。

大约两年前，我有幸更多地了解了 Pieter Hintjens 提出的想法，甚至与他进行了几次电报式的交谈。我看了我能在 YouTube 上找到的他的所有视频，读了他的一些文章。他希望作为一名作家被人们记住。他花了很大力气去捕捉他的经历、想法和主意。为什么这对我很重要？

我发现这很重要，因为这是关于传递知识，而不是简单地分享你的工作成果(比如工作代码)。

引起我注意的一个简单想法是将提交消息准备为问题/解决方案陈述。我以前写过关于这个的文章，关于这个如何捕捉到一个特定补丁被编写的真正原因——不是做了什么，而是为什么。我已经了解了五个“W”的规则(谁/什么/哪里/什么时候/为什么),以及这些对于问题分析和最终解决方案的发现有多么重要。

我已经开始用这种方式编写我所有的提交消息，即使是最简单的问题，甚至是在个人存储库中。我希望能够回到过去，告诉人们事情发生的原因。我一直在向我周围的人推广这种方法，我很高兴地告诉大家，有些人也采用了这种方法！

过去，我认为选择问题跟踪系统并不重要。一旦一个问题被关闭，它就永远消失了。因此，我们可以通过在新系统上创建新问题并在旧系统上关闭剩余问题来迁移到新系统。我认为这样的系统……更像是待办事项清单。一旦检查完毕，我就少了一件担心的事。

但是在我采用了问题/解决方案陈述方法后，我发现我现在更有可能写详细的报告。更有可能在问题中记录一些想法，比如为什么事情是这样做的。也许这是因为我开始将提交消息视为不那么烦人的辅助位(“修正错误行为”，有人吗？)而是值得留给后人的东西。

然而，有几个问题困扰着我。

首先，这些系统中的大多数缺乏精确性。例如，如果没有额外的努力，很难判断某个问题是在分支上解决了，还是在主服务器上也解决了？到底是哪个代码解决了这个问题？

其次，他们是他们的作者的领域模型和他们熟悉的工作流的受害者。当然，一定程度的定制是可能的，但是我们仍然必须处理作为一张票写下来的内容及其属性字典。

最后，让我感到好笑又难过的是，我们经常使用集中式问题跟踪器，即使我们的 SCMs 没有。我不是唯一一个担心这个问题的人，但是这个领域的大多数项目最终都被放弃了，并且从未被广泛采用。其余的人最终将这些信息存储在他们或其他人的服务器上。所以如果出了什么差错，这些东西就会*噗！*走开。

受到“事件源错误”的感染，我很清楚，记录问题和发生的事情的唯一合理的方法是记录每一个事件，并把它们作为事实的来源。而不是处理问题属性及其与其他实体(附件、评论等)的关系。)，我们可以记录发生在这些问题上的一系列事件，并在此基础上得出我们感兴趣的任何预测。

![](img/3fc4c483d7be44d9fb999ce2251fdf70.png)

High-level overview

那么，如果问题是一个文件，事件被记录在其中，会怎么样呢？它会很好地工作…除了实际的协作努力。合并冲突不是任何人的朋友，所以这不会飞。我不得不进一步思考。我们如何避免合并冲突？

嗯，如果我们从不写入同一个文件呢？如果一个问题的每个记录都是它自己的文件，或者甚至是文件的集合(为什么不呢，文件只是另一种抽象！).这样，无论何时我们合并，我们都不会与项目中的其他人发生冲突。

![](img/44a4ffb12b21ead8cfeb9b9372a1b712.png)

Record ordering example

[坐](https://github.com/sit-it/sit)就是这样诞生的。我就是忍不住花了一天时间来概述这个概念，并写了一些代码草案。所以，几天前，我就这么做了。

这既是一种通行的权利，也是一件经常被人诟病的事情:开发自己的问题跟踪器。无数项目开工。许多人被遗弃，很少有人幸存。一些公司成长为运转良好的商业机器。这些年来，我是[偏向](https://hackernoon.com/tagged/leaning)的“皱眉头”阵营。

那什么是坐？它基本上是一个简单的问题跟踪器，离线工作，使用可以存储在任何 SCM 中的纯文本文件(它甚至不需要任何人在场！)，它松散地基于源自事件源的想法，并且(当然)它是合并友好的。它还没有一个伟大的(或任何)用户界面，但这只是一个时间问题。

我认为它会幸存吗？我对此既怀疑又兴奋。我见过很多好项目被放弃，我自己也是。但是当涉及到我喜欢做的事情时，我从来没有停止尝试，所以我正在努力，让我们看看我们会从中得到什么。

> SIT 最终是为了在项目的整个生命周期甚至更长时间内保存决策、笔记和想法。

去看看，并尝试使用自动合并机器人打开一个问题[！](https://github.com/sit-it/sit#send-it-to-upstream)