# 静态站点生成器指南

> 原文：<https://medium.com/hackernoon/guide-into-static-site-generators-120514a22e25>

![](img/1df55efdc4e07a9d511622a64cb57f0a.png)

Image by Anna Pascale (Unsplash)

## 目前，当涉及到静态站点生成器时，有多种选择可供选择。你喜欢哪种语言并不重要。

你想用 [JavaScript](https://hackernoon.com/tagged/javascript) 编码吗？最受欢迎的有 Next.js 和 Gatsby。你喜欢写围棋吗？雨果会是你最好的选择。

如果 Ruby 是你选择的语言，你应该选择 Jekyll⁴或者 Middleman⁵.你可以浏览 458 个静态站点生成器的完整列表[点击这里](https://staticsitegenerators.net/) ⁶.更多过瘾版榜单[此处](https://www.staticgen.com/) ⁷.

有这么多可供选择的，选择一个将是一个棘手的问题。在本文中，我将介绍一些最流行的选项，并帮助您为您的项目做出最佳决策。

# 吉基尔博士

这个静态站点生成器是建立在 Ruby 之上的。Github 的第一个版本发布于 2009 年，是我在本文中描述的前四名中最古老的 SSG。Jekyll 也是 GitHub 页面背后的引擎，这解释了为什么它现在是最受欢迎的选项。

随着时间的推移，Jekyll 在 GitHub 上的受欢迎程度:

Jekyll Stars on GitHub (data fetched with [https://github.com/timqian/star-history](https://github.com/timqian/star-history))

Jekyll 相对于其他 SSG 的最大优势是它的简单性。你不需要花太多精力去建立一个简单的博客网站。

# 哲基尔的优点**👍:**

**👍简单性**

在 Github 页面上设置和部署您的站点非常简单快捷，因为它们可以很好地结合在一起。

**👍庞大的社区**

这意味着过多的教程会告诉你如何开始。当你面临困难时，你也一定会找到支持。

**👍液体模板引擎**

如果你来自市场上最流行的 CMS 选项，比如 WordPress，你应该对 Liquid templating structure 感觉良好。

**👍大量可用的开源插件**

在大多数情况下，你想做更多的事情，而不仅仅是生成静态文件。这时一长串 Jekyll 插件就来帮你了。

# 哲基尔骗局👎：

**👎需要 Ruby 环境**

这可能是一个棘手的问题，尤其是对 Windows 用户来说，因为 Jekyll 没有正式支持它。

**👎建造时间**

插件的可用性也是一个问题。如果你要用几个，那么它肯定会减慢你的建设过程。

# 雨果

Hugo 第一次发布是在 2014 年下半年。它由 Go 编程语言 aka 提供支持。Golang⁸.雨果的大体结构和哲基尔提供的很像。值得一提的是，在建筑时代，雨果是无与伦比的。事实证明，在处理大量 posts⁹时，Hugo 比 Jekyll 快 63 倍！

Hugo 在 GitHub 上的受欢迎程度:

Hugo Stars on GitHub (data fetched with [https://github.com/timqian/star-history](https://github.com/timqian/star-history))

# 雨果专业版👍：

**👍令人难以置信的快速建筑**

说到建筑时代，雨果是不可战胜的。我个人不知道有比 Hugo 更快的静态站点生成器，这是大网站的绝佳选择。

**👍灵活**

你不必搜索和安装任何插件，因为 Hugo 单独支持多种内容类型。

**👍跨平台**

Hugo 拥有适用于 Windows、Linux、FreeBSD、NetBSD、macOS 和 Android 的二进制文件，支持 x64、i386 和 ARM 架构。

# 雨果·康斯👎：

**👎Golang 语法**

Go 语言肯定不是最流行的编程语言，所以一开始你可能会对 Hugo 的语法有些困难。

**👎无内置资产处理**

Hugo 没有内置资产管道，所以你必须使用外部工具。

# Next.js

Next.js 的第一个版本于 2016 年 10 月 26 日发布，这使得它成为本文中最年轻的竞争者。尽管 Next.js 的受欢迎程度一直在疯狂上升，但对于一些人来说，它可能有点令人担忧，因为 next . js 不是一个纯粹的静态站点生成器，而是可以用作一个静态站点生成器。那么 Next.js 到底是什么？简而言之:它是一个用于服务器渲染或静态导出的 JavaScript 应用程序的框架。

Next.js 随着时间的推移在 GitHub 上的受欢迎程度:

Next.js Stars on GitHub (data fetched with [https://github.com/timqian/star-history](https://github.com/timqian/star-history))

# Next.js Pros👍：

**👍单页 App 感受**

因为 Next.js 是建立在 React.js 之上的，它带来了 React 的所有优势，比如预取和 JSX 模板系统等等。

**👍自动代码分割**

页面从不加载不必要的代码。

**👍优秀的学习材料**

如果你不熟悉构建单页面应用程序，你可以很快上手[点击这里](https://nextjs.org/learn/) ⁰.

**👍您可以随时跳转到完整 PWA**

如果`static page`不够用，可以启用 Node.js 服务器。

# Next.js 缺点👎：

**👎学习曲线**

与你必须学习的哲基尔/雨果的方式相比，如果你对 React、js、JSX 等完全陌生，那么进入 Next.js 要困难得多。

**👎静态页面构建时间**

在这方面很难打败雨果。大型网站的建设时间可能会很长。

# 盖茨比（姓）

盖茨比的第一次发布发生在 2015 年 10 月 6 日。值得一提的是，它拥有所有静态站点生成器项目中最活跃的社区之一——目前有 807 个贡献者。长话短说，Gatsby 是一个静态的渐进式 Web 应用程序生成器。

随着时间的推移，盖茨比在 GitHub 上的受欢迎程度:

Gatsby’s Stars on GitHub (data fetched with [https://github.com/timqian/star-history](https://github.com/timqian/star-history))

# **盖茨比的优点👍:**

**👍内部 GraphQL】**

数据直接存储到我们的组件中——以我们想要的形状和形式。GraphQL 是一个相当大的话题，你可以在这里阅读更多关于它的[。](https://www.howtographql.com/)

**👍默认 PWA**

你不用花太多力气就能把你的盖茨比静态网站变成线下可用的 app。设置盖茨比服务人员非常简单！只有一个盖茨比插件。

**👍成长中的插件生态系统**

有几十个插件，你可以轻松连接最流行的无头 CMS，谷歌分析，图像优化，服务工作者等等！

**👍数据&代码预取**

站点会感觉像一个单页应用程序，同时仍然受益于静态站点的性能。

**👍优秀的社区和学习材料**

从盖茨比的循序渐进教程开始很容易[这里](https://www.gatsbyjs.org/tutorial/)

# **盖茨比的缺点👎:**

**👎学习曲线**

JS，React，GraphQL。

**👎龙建筑时报**

与上面所有的静态站点生成器相比，在点击 build 命令后，Gatsby 要花很多时间来丢弃文件。

# 摘要

这是我们到目前为止通过在 Bejamas 使用静态站点生成器所了解到的。让我在最后总结一下:

如果你寻求简单和非常短的学习曲线，你最好选择 Jekyll。建立一个大网站，想改变和增加一大堆新职位？选择雨果。在任何其他情况下，我都会建议你选择 Gatsby 或 Next.js。

为什么？因为 javascript 正在蚕食 web 开发世界。如果你是这个领域的新手，并且正在寻找有一个大的社区、有发展趋势并且有一个好的未来前景的东西，你肯定应该选择本文中描述的最后两个框架。

不过，这只是我的想法。你觉得怎么样？有没有我没有提到的优点或缺点？

*本文原载于我公司网站:*[*https://bejamas.io/blog/guide-into-static-site-generators/*](https://bejamas.io/blog/guide-into-static-site-generators/)

## *脚注*

1.  [*https://nextjs.org/*](https://nextjs.org/)
2.  [*https://www.gatsbyjs.org/*](https://www.gatsbyjs.org/)
3.  *【http://gohugo.io/】T5[T6](http://gohugo.io/)*
4.  *[*https://jekyllrb.com/*](https://jekyllrb.com/)*
5.  *[*https://middlemanapp.com/*](https://middlemanapp.com/)*
6.  *[*https://staticsitegenerators.net/*](https://staticsitegenerators.net/)*
7.  *[https://www.staticgen.com/T21](https://www.staticgen.com/)*
8.  *[*https://en . Wikipedia . org/wiki/Go _(编程 _ 语言)*](https://en.wikipedia.org/wiki/Go_(programming_language))*
9.  *[*https://forestry.io/blog/hugo-vs-jekyll-benchmark/*](https://forestry.io/blog/hugo-vs-jekyll-benchmark/)*
10.  *[*https://nextjs.org/learn/*](https://nextjs.org/learn/)*
11.  *[*https://www.gatsbyjs.org/plugins/*](https://www.gatsbyjs.org/plugins/)*
12.  *[*https://www.gatsbyjs.org/tutorial/*](https://www.gatsbyjs.org/tutorial/)*