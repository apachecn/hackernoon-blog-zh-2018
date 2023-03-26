# 用 Jupyter 笔记本制作网络应用

> 原文：<https://medium.com/hackernoon/making-web-apps-with-jupyter-notebook-75eab8bdb092>

本文将解释如何**将 Jupiter notebook 制作成 web 上的 GUI 应用**。

# 什么是木星笔记本

[木星笔记本](http://jupyter.org/)是基于浏览器的 [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) 。

**REPL 使你能够在一个交互式的环境中编程**，你可以在前面所有代码行都处于执行状态的时候，编写并执行下一行代码。

这个微不足道的特性使我能够**减少原型开发时间**，因为为了测试下一行代码，我不需要再次运行整个程序。(REPL 只在某些情况下有用)

我知道如果你不知道什么是编程语言，也没有 REPL 风格原型的经验，这种解释是没有用的，但是如果你属于这一类，我不知道如何解释(可能是不可能的)。

重点是，它**使编程原型更快**，因为测试代码中的下一行时，你不需要一遍又一遍地运行前面的代码。

以前 [Jupiter](https://hackernoon.com/tagged/jupiter) notebook **叫做 IPython Notebooks** ，当时只有 [Python](https://hackernoon.com/tagged/python) 可以作为编程语言使用。

现在可以用多种编程语言来使用 Jupiter notebook 了，尽管我的经验只限于 Python。

我个人使用木星笔记本进行**探索性数据分析**。
给熊猫加载数据，然后尝试用可视化理解数据(Seaborn，Bokeh)。

# 与非技术人员分享木星笔记本

我经常用不同的参数运行相同的代码，以产生稍微不同的可视化效果。

如果你熟悉 Jupiter 笔记本环境，你就会知道这意味着通过 SHIFT + ENTER，从单元格菜单或其他快捷方式运行同一个单元格。

这让我想到，如果我想把我的笔记本给一个非技术人员(知道如何使用 Word，但不知道如何编写公式的人)，这个人使用它将是微不足道的。

此外，一个人可能会更改代码并得到意想不到的结果(语法错误或错误的结果)。

# Ipywidgets

这个问题可以用 [Ipywidgets 小部件](https://github.com/jupyter-widgets/ipywidgets)来解决。

使用 Ipywidgets 小部件，您可以**在 Jupiter notebook** 中制作 GUI，当您希望有人(甚至是您)使用 GUI 元素展示您的 Jupiter notebook 的某些功能时，这是一个完美的选择。

拥有这种图形用户界面

![](img/546edf6da2d1204d19cfe21ece03fc3f.png)

这是必要的代码:

> # UI
> 
> 滑块=小部件。IntSlider(最小值=10，最大值=100，值=10)
> 
> 标签=小部件。标签(value= '选择游戏数量')
> 
> 按钮=小部件。按钮(description= '开始模拟'，button_style= '信息'，tooltip= '开始游戏')
> 
> 进度=小部件。IntProgress(描述= '进度:')
> 
> label2 =小部件。标签()
> 
> 互动次数
> 
> 按钮。点击(开始游戏)
> 
> #用户界面布局
> 
> top_box =小部件。HBox([标签，滑块])
> 
> down_box =小部件。HBox([按钮、进度、标签 2])
> 
> ui =小部件。VBox([上框，下框])
> 
> 显示.显示(用户界面)

# Appmode

这很好，但仍有两个问题:

*   用户必须首先运行所有单元
*   用户仍然可以访问代码

幸运的是， [Appmode](https://github.com/oschuett/appmode) 是 Jupyter 扩展，**将笔记本变成网络应用**。

默认情况下，用户仍然可以回到“代码模式”,但它可以被[轻松移除](https://github.com/oschuett/appmode#customization)

# 托管您的木星笔记本

如果你在你的网络内部托管它，你只需要[运行笔记本服务器](https://jupyter-notebook.readthedocs.io/en/stable/public_server.html)，就像本地开发一样**，但是增加一些安全性**。

Github 将只让你查看托管在他们服务器上的任何笔记本，还有许多网站具有相同的功能。

如果你想要交互式托管你的 Jupiter 笔记本，以便人们可以执行它们，那么有[活页夹](https://mybinder.org/)。

目前，它处于测试阶段，你的 Jupiter 笔记本需要在公共的 Github 库中。

# 结论

通过以下因素的正确组合:

你可以将你的 Jupiter 笔记本作为一个网络应用程序免费运行。

[抛硬币代码](https://github.com/sasa-buklijas/coin_toss)可以作为一个**例子，说明如何将 Jupiter notebook 作为 GUI 应用程序托管在 web 上**。

部分[灵感](https://www.youtube.com/watch?v=i40d8-Hu4vM)来自彭博 [bqplot](https://github.com/bloomberg/bqplot) 项目。

我个人觉得它对[分享](https://github.com/sasa-buklijas/coin_toss) [互动](https://github.com/sasa-buklijas/Monty_Hall_problem) [可视化](https://github.com/sasa-buklijas/Iterative_trading_game)很有用。

*原载于 2018 年 10 月 1 日*[*buklijas . info*](http://buklijas.info/blog/2018/10/01/making-web-apps-with-jupyter-notebook/)*。*