# 我放弃了 React，转而支持 Hyperapp——原因如下

> 原文：<https://medium.com/hackernoon/i-abandonded-react-in-favor-of-hyperapp-heres-why-df65638f8a79>

像 React、Vue 和 Angular 这样的框架似乎在过去的一年里风靡一时，理由很充分:它们使得创建和维护可伸缩的 web 应用程序变得更加容易。在过去与 React 和 Vue 一起工作过，主要是在项目和课程中，我发现他们都有一个相当陡峭的学习曲线。然而，出于需要，我选择 React 和 Redux 作为工具，这是我最不喜欢的工具。

![](img/50037a263f8ca6fdeed10ff22ee70fae.png)

Photo by [广博 郝](https://unsplash.com/photos/_z0FV0lrOg4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/construction-site?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

随着时间的推移，我意识到在使用这些工具的每一步中，我都在不断地寻求帮助。我甚至尝试过 Vue，但也没有像我希望的那样。在这一点上，我应该提到，我在 web 开发方面的技能水平介于初级和中级之间，这取决于我所构建的技术和性质。但是，就 React、Redux、Vue 和 Angular 而言，我当然觉得自己是一个绝对的初学者，即使在使用了大约一年之后。

> React 是一个非常酷的框架，但是它会带来很多麻烦，尤其是当你的 web 应用变得越来越复杂的时候。

因此，我四处搜索了一下，令我惊讶的是，我可以使用另一个工具— [Hyperapp](https://hyperapp.js.org/) 。它当然没有其他选项那么受欢迎，性能也可能稍差一些，但它的复杂性要低得多，而且占用 1KB 内存就能完成很多工作。看到这样一个框架，我非常兴奋，所以我泡了一杯咖啡，坐下来查看文档，最终花了大约一个小时来阅读和理解。在那之后，我将它整合到我正在工作的一个项目中(期待一篇关于这方面的文章迟早会出现)，结果证明它甚至比最初看起来还要好。

[](https://github.com/hyperapp/hyperapp) [## 超应用/超应用

### 用于构建 web 应用程序的 1 kB JavaScript 框架。

github.com](https://github.com/hyperapp/hyperapp) 

细心的读者和/或 React 爱好者此时会问——你到底为什么认为 Hyperapp 是一个好的选择？以下是我的推理:

## 简单的 API

当我通读 Hyperapp 的文档时，一个想法变得清晰了:该框架的 API 非常简单，远没有 React 复杂。在过去与其他框架一起工作过之后，我可以确定，由于框架的简单性，与其他任何框架相比，记住 Hyperapp 的工作方式都是轻而易举的事情。反过来，这将避免在发布 web 应用程序的前一天晚上试图找出那个讨厌的 bug 时出现很多令人头疼的问题。

## 与您选择的工具相同的原则

如果你习惯了反应或者 Vue，Hyperapp 会非常容易进入。它的核心原则与 React 非常相似，如果不是完全相同的话，而且你知道的大多数东西都非常相似，这意味着你可以立即开始使用它，而不必学习很多新东西来尝试它。它甚至支持开箱即用的 JSX，并为自己能够编写跨框架共享和重用的组件而自豪。

## 虚拟 DOM 和状态管理

Hyperapp 对我来说的主要卖点是，状态管理和虚拟 DOM 都很好地整合到了框架中，这两者对于开发现代 web 应用程序都是必不可少的。这意味着您不需要像 Redux 这样的工具来正确管理状态，减少您的 web 应用程序的大小，并帮助您在这个过程中保持部分理智。

## 易于维护

多年来，我在使用各种框架和软件包的过程中发现，其中一些需要大量的样板代码才能开始。React 和 Redux 在这一点上特别内疚，因为有很多事情你需要考虑。另一方面，Hyperapp 几乎不需要这么多，而且它的代码更容易维护，因为它不需要您浏览样板代码或任何像 reducer 那样复杂和混乱的东西。最重要的是，你可以阅读 Hyperapp 的源代码，并准确理解它的功能，因为它大约有 400 行代码。这给了你一个优势，如果你对它的工作方式有任何不满意的地方，你可以调整它并参与它的开发。

我希望这篇文章能够说服您给 Hyperapp 一个机会，看看它是否适用于您可能正在从事的项目。如果你喜欢它，记得在 Github 上发布它，以帮助围绕它发展社区。

如果没有，请在下面评论您选择的框架，以及为什么 Hyperapp 可能不是合适的替代品。