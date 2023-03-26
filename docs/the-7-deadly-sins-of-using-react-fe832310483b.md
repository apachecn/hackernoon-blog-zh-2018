# 使用 React 的七宗罪

> 原文：<https://medium.com/hackernoon/the-7-deadly-sins-of-using-react-fe832310483b>

## 如何搞砸一个 React 项目，甚至可能想完全放弃这个库。

当我写完这篇文章时，我六岁的女儿问我在做什么。我告诉她我在写有时候人们如何以错误的方式构建软件。她疑惑地看了我一眼:“怎么会有人想那么做！?"

现实是，从来没有人带着失败的意图开始一个项目，但是在过去的几年里，我和 React 一起工作，我在自己和周围的人身上看到了一些常见的失败模式。这里他们没有特定的顺序。

![](img/845f36771bd699fa0a06800e2dd590a8.png)

Photo by [NeONBRAND](https://unsplash.com/photos/A59lWOrZVnw?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/skull?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 1.边学边反应

React 是一个非常棒的库。这不是火箭科学，但确实需要一点努力来开始"[思考，做出反应](https://reactjs.org/docs/thinking-in-react.html)。你一次学的东西越少，就越容易关注什么是反应，什么是其他东西**。**

请不要把这当成“大人桌旁没有孩子”之类的事情。React 是一个强大的工具，真正值得你全力以赴。下面是一个很好的操作顺序，可以确保学习一个最流行的 UI 框架将是一个富有成效的、令人满意的体验。

## 从 Javascript 开始

从根本上说，React 是一个 javascript 库，如果你是第一次进入前端编程，这是一个值得记住的好东西。经过十年的后端工作，也许你正在尝试构建一个 web 应用程序。也许你是第一次接触软件开发。不管你的专业水平如何，都要花时间去理解普通的 Javascript。

我推荐买一本 [Javascript:精彩部分](https://www.amazon.com/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742)。这是一篇短文，它是理解语言的 20%的一个很好的资源，这将导致你 80%的成功。

## 搜索 ES6 语法

没有 ES6 可以使用 React 吗？绝对的。然而，该库的大多数示例和教程都利用了 ES6 语法。让我们面对现实吧，新的语法是对这门语言真正有用的补充。即使不与 React 合作，ES6 也是个好东西。如果您对语法有一定的了解，下面的一些内容会更有意义:

[导入](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) / [导出](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)语法是新的 require 语法:

```
import React, { PureComponent} from 'react';
```

你会经常看到[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)。这看起来像是巫术，直到你明白发生了什么:

```
const { title, isActive, onClick } = this.props;
```

而[类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)是[创建组件](/@the.benhawy/3-ways-to-create-react-components-8b3620e4ea0)的多种方式之一:

```
export class MyComponent extends PureComponent {
  componentDidUpdate(prevProps, prevState){
    ...
  }
  render() {
    ...
  }
}
```

## 考虑打字稿

Typescript 是微软的一个扩展 Javascript 语法和 transpiler。对于大型项目和大型团队来说，这无疑是一种增值。对于小型个人项目，你的里程可能会有所不同。React 确实可以不用 Typescript 使用，[甚至有人建议不用](https://hackernoon.com/why-i-no-longer-use-typescript-with-react-and-why-you-shouldnt-either-e744d27452b4)。但是，如果你想尝试它，这是一个语法级别的事情，所以我建议在拿起 React 之前熟悉它。这样，当您浏览 React+Typescript 示例时，就更容易区分这两个概念。

## 学习反应

既然您已经熟悉了语言和语法级别的结构，那么您就可以集中精力学习 React 了。虽然您可能不使用 it 产品，但 [Create React App](https://github.com/facebook/create-react-app) 是快速搭建新应用的一个好方法。

使用“搭建”的应用程序可以让您专注于从示例直接构建组件，而不是必须通过文件夹结构、设置 babel 和许多其他非 React-proper 关注点进行推理。当然，你最终应该学会这些东西，但在这一点上，它们会比其他任何东西都更让人分心。

除了阅读网上的例子，我强烈推荐通读所有的 [React 文档](https://reactjs.org/docs/getting-started.html)。一个强大的库不可或缺的一部分是世界级的文档，React 也不例外，所以要充分利用这一点。

## 探索其他东西

现在，您已经准备好开始探索 React 生态系统中所有精彩和糟糕的部分。这些包括像 Redux 这样的状态管理工具，像[降档](https://github.com/paypal/downshift)这样的组件原语，各种[拖放](https://github.com/react-dnd/react-dnd)库，以及[其他省时省力的漂亮包](https://github.com/enaqx/awesome-react)。

# 2.直接进入高级反应模式

当我们谈到学习 React 的话题时，不要试图先学习所有花哨的技巧。在 React 中，我怎么强调[思维的价值都不为过。](https://reactjs.org/docs/thinking-in-react.html)

即使没有任何额外的库或包，React 也可以做很多很酷的事情。但是与任何生态系统一样，有一些反应习惯模式可以减少重复并增加可重用性。[肯特·C·多兹](https://blog.kentcdodds.com/)有一些[奇妙的材料](https://blog.kentcdodds.com/updated-advanced-react-component-patterns-51c34b44b1df)用于学习一系列非常酷的高级反应模式。以下是其中的几个例子:

**高阶组件:**接受一个组件并返回一个新组件的函数，这是一种复用组件逻辑的技术。

**渲染道具:**一种使用一个值为函数的道具在 React 组件之间共享代码的技术。

[**状态减速器**](https://blog.kentcdodds.com/the-state-reducer-pattern-%EF%B8%8F-b40316cfac57) **:** 非受控组件和受控组件之间的最佳点。

我建议不要马上学习所有这些东西的主要原因是，在没有它们的情况下工作，你会开始感到一点痛苦，这让你想使用它们。解决方案是一个没有原因的问题。如果你没有真正将问题内在化，你很可能会实施错误的解决方案。

# 3.在 React 中重建任务关键型应用程序作为您的第一个项目

学习的一部分，尤其是在软件领域，是尝试新事物。有时这些事情会得到最好的结果，有时只有通过犯错误，你才知道什么是“正确的方式”。换句话说，除非你是一个文字天才，否则你的第一个 React 项目将会有一些严重的错误。这很正常。

但是知道这一点，不要让第一个项目成为关键任务。最好是和几个有经验的 React 开发人员一起工作，或者先做几个较小的幕后项目。

生产中的应用程序总是比使用 React 的半成品重建要好。利用现有的应用程序并利用 React 对其进行改进是很有诱惑力的。这并不是不可能的，但是改造一个现有的应用程序比开发一个新的要复杂得多。此外，当您使用一个已经在生产中的应用程序时，除了掌握 React 之外，还有很多问题需要处理。

总而言之，一旦你或你的团队积累了一定的经验，最好只处理那些有风险的项目。

# 4.在真正需要之前使用 Redux

Redux 是大家又爱又恨的库。其中一些只是你日常生活中普通的火焰战争。从根本上说，Redux 是一个管理应用程序状态的工具。其他工具也做同样的事情，包括 React 内置的组件状态和上下文特性。

Redux 是异步可变性和间接性之间的权衡。对于较小的项目，异步修改状态的痛苦可能没有那么糟糕。对于大型应用程序，还有其他管理状态的方法吗？绝对的！

使用错误的工具(或者至少不是这项工作的最佳工具)会给你留下不好的印象。在这种情况下，甚至很容易归咎于工具。这其实也是如此强烈推荐在 Redux 之前学习 React 的原因之一。如果你能在没有 Redux 的情况下成功构建一个 app，那么添加它的成本和复杂度绝不是增值。

也就是说，理解*为什么*Redux 权衡提供价值也是至关重要的。在项目开始时加入 Redux 比在“状态达到狂热状态”时强行加入要便宜。理解项目的远景并从一开始就知道是否需要 Redux 需要一些经验。

# 5.在生产中使用 alpha 功能

React 团队一直在开发令人惊叹的新东西。如果你在 React 工作了至少 10 分钟，你现在应该听说过 React Hooks。尽管钩子很酷，但你可能不应该在生产中使用它们。

现在，开始研究新特性是什么以及它们是如何工作的并不是一个坏主意。随着新功能的推出，对思想领袖来说，试验新模式至关重要。如果你想坐在那张桌子旁，无论如何，卷起袖子，把手弄脏。

但也要记住，需要几次尝试才能正确反应上下文。仅仅因为某样东西是新的，并在社交媒体上获得大量曝光，并不意味着它已经准备好进入黄金时段。不代表不是，用好判断力就行了。

尽管如此，React 团队在添加新特性时，在确保向后兼容性方面做得非常出色。因此，很容易引入 React 的最新版本，然后慢慢试验新特性如何在您的应用程序中工作。这种策略保持低风险，让你逐步适应新事物。你认为脸书会在一夜之间重构 11 亿行代码来移除类组件吗？我也不知道。😃

这可能很容易让人一头扎进去，使用所有闪亮酷的新东西。学习新东西，但不要在生产中使用。

# 6.直接从 _____ 移植一个项目到 React

所有的语言和框架都有个性。他们每个人都有自己的一套怪癖和模式，导致惯用的编码方式。React 有 ___ 个上下文。Angular 有指令。Vue 看了物业。榆树没有`null`。

将一个应用程序从一个 UI 框架移植到另一个 UI 框架有很多合理的理由。也许您已经尝试了一些选项，并开始标准化一套标准的工具。也许你有一个新的代码库，作为收购的一部分。有许多正当的理由。请记住，端口比“构建相同的东西，在 React 中完成它”更复杂

任何两个 UI 框架之间都没有一对一的相似性，这意味着您不能简单地开始一个组件一个组件地移植 Angular project 来做出反应。可能合法地存在于 Angular 组件中的代码可能存在于新代码库中的 Redux Action Creator 中。像 RxJS 这样的东西根本没有直接的模拟反应。

一般来说，对于产品化团队来说，让新旧框架都有一个主题专家是一个很好的实践。通过这样做，你将更好地处理如何翻译语义和意图，而不仅仅是简单地音译代码结构。

# 7.忽略服务器端呈现

不管是好是坏，Javascript 也在服务器上运行。通过在 NodeJS 中运行服务，可以在页面到达浏览器之前在服务器上运行 React 来呈现页面。服务器端渲染(SSR)并不是必需的，但是我发现它并没有被充分利用。

对 SRR 的两个主要好处是:

**第一次有意义的绘制时间更短:**浏览器可以立即开始呈现页面，而无需评估任何 Javascript 代码或发出任何进一步的网络请求。

更好的 SEO: 搜索引擎抓取你的应用程序将会得到一个实际的页面，而不是一个巨大捆绑包的空白文档。

但并不全是雏菊和阳光。弄清楚如何在服务器端呈现期间设置初始应用程序状态是一项重要的任务。它还要求您运行一个实际的服务器(或支持 NodeJS 的“无服务器”替代方案)，在其上运行 React 应用程序。

整个努力中最困难的部分之一是确保仅服务器的代码不包含浏览器 API 依赖，反之亦然。例如，`window`和`document`在服务器上是未定义的。如果组件渲染函数中的每个代码路径都依赖于`document`，那么该组件在服务器上渲染时将会中断。如果你进入一个项目时只考虑客户端渲染，那么在未来实现 SSR 会变得更加困难。

这并不是说每个网站都需要呈现在服务器端。从 CDN 提供简单的 SPA 有很多价值。然而，也值得花时间让您的大脑了解 SSR 是如何工作的，以便您可以在需要时轻松使用它。

# 喜欢你看到的吗？

我喜欢帮助人们在 React 上获胜，我希望这些信息对你有用。如果你见过其他常见的反应错误，我很想听听。和往常一样，如果你喜欢这篇文章，请随时给它一个👏还是两个！