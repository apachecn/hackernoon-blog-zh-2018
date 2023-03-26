# 为什么选择 MobX？

> 原文：<https://medium.com/hackernoon/why-mobx-e0530aacd482>

## 消除国家管理的负担

状态管理和变更传播可以说是 GUI [编程](https://hackernoon.com/tagged/programming)中最困难的[挑战](/@eugenkiss/challenges-in-gui-programming-65d360466e3f)。许多范例承诺将我们从他们的负担中解救出来。只有少数人留下来了。其中就有 [MobX](http://mobxjs.github.io/mobx) 和它的风味[透明反应式编程](https://github.com/meteor/docs/blob/version-NEXT/long-form/tracker-manual.md#transparent-reactive-programming)。

![](img/f29280253ebb6aeab86247ebf86d69ec.png)

The MobX logo

要理解 MobX 的吸引力，首先理解 React [如何革新](/techboi/why-react-d35dc6b2a2f3) GUI 编程是有帮助的。传统方法允许描述 GUI 的初始状态。进一步的 GUI 状态转换必须通过引用 GUI 元素和逐段突变来完成。这很容易出错，因为边缘案例很容易被遗漏。使用 React，您可以在任何给定的时间点描述 GUI *。换句话说，处理 GUI 状态转换，例如操作 DOM，已经是过去的事情了:你的 GUI 代码已经变成了声明性的。*

React 的关键优势是使“如何”更新 GUI 变得透明。MobX 重新应用了这个想法。不是用于 GUI 操作代码，而是用于状态管理和变更传播。事实上，React 和 MobX 的结合是协同的，因为即使 React 很好地解决了如何更新 GUI 的问题，但是没有 MobX，何时更新 GUI 仍然很麻烦。组件之间的交叉通信是最大的痛点。

让我们探索 React 和 [JavaScript](https://hackernoon.com/tagged/javascript) 中的一个例子来说明 MobX 的优势:

这个例子展示了一个非常简单的电子商务页面。这里有一个[演示](https://941km8pp9o.codesandbox.io)和它的[可实时编辑的源代码](https://codesandbox.io/s/github/eugenkiss/mobx-cart-example)。行为如下。最初，您的购物车是空的。当您点击“购买洗发水”时，您的购物车商品计数会增加一，最近购买的组件会显示“洗发水”。点击“购买肥皂”对“肥皂”做同样的事情。你也可以清空你的购物车。在真实的例子中，您将看到只有标题中的购物车被重新呈现，而不是标题本身。您还会看到，当您连续购买同一产品时，最近购买的组件不会重新呈现。

请注意，未明确指定可观察变量`itemCount`和`lastItem`与组件之间的相关性。然而，组件可以正确有效地响应变化。你可能想知道这是如何实现的。答案是，MobX 在组件呈现功能的执行过程中隐式地构建了一个依赖图，用于跟踪当可观察变量发生变化时哪些组件需要重新呈现。一种看待 MobX 的方式是从电子表格的角度来看，其中的组件是可观察变量的公式。不管“魔法”是如何工作的，也不管使用哪种类比，结果都很清楚:您从显式管理变更传播的负担中解放出来了！

总而言之，MobX 是一个实用的、非礼仪的、高效的解决方案，可以应对状态管理和变更传播的挑战。它的工作原理是在可观察变量和组件之间建立一个运行时依赖图。同时使用 React 和 MobX 具有协同作用。我甚至可以说 MobX 不仅仅是一个库。它代表了一种态度，可能会从整体上改变你的编程方法。通过以下链接深入了解 MobX 世界:

*   [纯粹根据时间和状态渲染](/@mweststrate/pure-rendering-in-the-light-of-time-and-state-4b537d8d40b1)
*   [变得完全反应式:MobX](/@mweststrate/becoming-fully-reactive-an-in-depth-explanation-of-mobservable-55995262a254) 的深度解读
*   数据绑定有什么好大惊小怪的？
*   [计算状态:模型视图问题](https://hackernoon.com/computed-state-the-model-view-problem-9cbe8cf8486f)
*   [MobX —类似 React，但针对数据](http://danielearwicker.github.io/MobX_Like_React_but_for_Data.html)

## 附录

MobX 的作者 Michel Westrate 用“管理数据流”来描述 MobX 的工作。

> “也许，更准确的说法是 MobX 是一个数据流库，它使您能够以最小的努力推出自己的状态管理架构” [*来源*](https://codeburst.io/the-curious-case-of-mobx-state-tree-7b4e22d461f)

在本文的上下文中，“管理数据流”和“状态管理和变更传播”是等价的。

这篇文章重点介绍了 MobX 和 React 的用法。值得一提的是，MobX 与 React 没有任何关系——MobX 是一个独立的库。例如，它被成功地用于角形项目中。甚至还有 GUI 编程范围之外的用法。

文章[8 React 组件通信的无流量策略](https://www.andrewhfarmer.com/component-communication)很好地概述了在没有 MobX 的 React 中组件通信的不同方法。

现在，我没有忘记房间里的大象和它的味道。这是另一篇文章的主题。无论如何，对于好奇的人来说，已经有相当多的比较文章了。我也没有提到 MobX 状态树。主要是因为我还没用过。这似乎是一个可怕的项目，虽然！

这篇文章已经[发表在 Zalando Tech 博客](https://jobs.zalando.com/tech/blog/why-mobx/)上了！