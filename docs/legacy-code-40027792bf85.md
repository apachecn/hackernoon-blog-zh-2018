# 遗留代码

> 原文：<https://medium.com/hackernoon/legacy-code-40027792bf85>

## 它是什么，我们能做些什么。

[遗留代码](https://hackernoon.com/tagged/legacy-code)是大多数开发者的克星。但这是我们都必须面对的现实。

这是你从别人那里继承来的代码，可能是从你的应用程序的老版本那里继承来的。而且通常它没有任何合适的[文档](https://hackernoon.com/tagged/documentation)，有很多气味，并且有一种“我们可能应该返工……但是它仍然工作正常”的困扰感。

![](img/b3b3f2e4068bbe30342d915771ca2c45.png)

我确信一些[开发人员](https://hackernoon.com/tagged/developers)会把大部分时间花在相对较新的代码库上，他们会从头开始构建代码库——他们永远不会像这样处理遗留代码。

> 即便如此，我认为你可以从你自己那里继承遗留代码——或者更准确地说，从“5 年前”——你自己那里。

我记得我使用的代码似乎是基于许多选项、参数和集合构造了一个很长的字符串。然后，它对它进行评估。

当我去见构建这个库的同事时，发现他们当时根本不知道在 Ruby 中不需要调用 *eval* 就可以完成多少[元编程](https://hackernoon.com/tagged/metaprogramming)。

代码的其他部分是框架本身可以做的事情的简单的重新实现。“我们 8 年前从版本 1 dot 什么的开始”——我们现在是版本 4.2。

我想时间会让一切变得陌生。

我应该澄清，遗留代码本身并不是坏代码。它只是太旧了，或者比它应该的要复杂一点，而作者却无处可寻。

我讨厌它。

![](img/aab196134fb1bfce4d4f4ff8faff7fe2.png)

我认为这是我们对待遗留代码最常用的方法。我们倾向于使用核武器，重写一切，诅咒那些在我们之前的人。

事实证明这并不太好。

缺乏预先编写的测试，缺乏对正在重写的代码的全面了解，以及我们重构操作中糟糕的结构，经常给我们留下一堆代码，这些代码在我们看来只比上一个好，因为这一次，它是由我们制作的。

我留下了相当混乱的代码。然后转化成它自己的遗留代码。

那怎么办呢？

事实证明，处理遗留代码不是大扫除。更像是改善你的环境。每当你偶然发现你的能力有所提高时，你就一个接一个地升级它的一部分。

它为您必须对那个旧库进行的修改编写了一个测试，它在花了一段时间弄清楚之后注释了一些 hackey 代码，它用 for 或 each 替换了 while 循环。

遗留代码从来都不是业务优先级。试图使它成为一个通常会把它变成新的，很快成为遗留代码。让我们把它考虑到我们的任务评估中，并且尽我们所能做一点点。