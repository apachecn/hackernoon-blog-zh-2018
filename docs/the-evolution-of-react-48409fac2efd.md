# React 的进化

> 原文：<https://medium.com/hackernoon/the-evolution-of-react-48409fac2efd>

![](img/be2977bc87f157fb8dd7ed997dd49714.png)

您会同意我的观点，React 已经改变了前端开发的进程。

这个奇妙的库使得开发人员构建用户界面变得更加容易，可以说是有史以来最好的框架之一。

React 并不是突然变得著名和有用的，它在成为我们今天拥有的强大的库之前经历了几个阶段。

让我们看看 React 库的演变。

# 2013 年之前:

对像 React 这样的库的需求产生于脸书广告公司。随着脸书变得更大——对于一个开始很简单的代码库来说，功能的数量增加了，开发产品的工程师数量增加了，代码库也变大了。

随着这种增长的经历，代码维护变得非常困难，应用程序的特性使事情变得很慢。

那么问题是什么呢？这是:

对于新数据流入的应用程序的一些更改，需要在代码基础树的较低位置进行一些小的更改，以重新呈现整个应用程序，这被称为级联更新。

但是由于代码库越来越大，使得级联更新变得更加困难。甚至很难判断重新渲染应用程序需要做哪些更改。

![](img/995f632977e733ff87a8fe89a3e7fa12.png)

Jordan Walke

它们带来了迫切解决问题的需要。Jordan Walke 构建了一个原型来简化这一过程(它还不叫 React，他称之为 [FaxJS](https://github.com/jordwalke/FaxJs) ，这就是一切开始的地方。

就在这个时候，XHP 出现了，它是一个在脸书使用的 PHP HTML 组件框架。XHP 作为脸书精简版的用户界面渲染层，可以很好地创建定制的和可重用的 HTML 元素。

XHP 是 React 创作的灵感来源。

React 为脸书广告公司创造了奇迹，该公司新收购的 Instagram 引起了人们对 React 的兴趣，并推动其与代码库分离，并使其开源。

# 2013:

React 开源的时候到了，在 5 月 29 日至 31 日举行的 JS ConfUS 期间，Jordan Walker 向全世界介绍了 React。

用 Jordan 的话说，“当我们构建组件框架时，我们努力争取的事情之一是，我们希望最大限度地减少开发人员面临突变的数量。”

接下来是向全球的开发者传播福音。

# 2014:

随着越来越多的开发人员采用 React 库，ReactJsWorldTour 应运而生。

为了推动采用，React 开发者工具成为了 Chrome 开发者工具的扩展， [React 热加载器](https://github.com/gaearon/react-hot-loader)插件被开发出来。

# 2015:

React v0.13 发布于 2015 年 3 月。React 的这个版本有一个受欢迎的新特性，就是支持 ES6 类。

在以前的版本中，对 **setState** 的第一次调用是同步的；然而，在新版本中，它们现在是异步的。

在 React v0.13 发布的同一个月，React Native 向全世界推出。React Native 将发展成为一个有用的框架，用于使用 React 构建移动应用程序。

![](img/6be934f2dec237aabeb914ba1a6dfdd7.png)

React Native 并不致力于成为“一次编写，随处运行”的工具，而是致力于成为“一次学习，随处编写”的工具。这确保了同一批工程师可以为他们选择的任何平台构建应用程序，而不必改变语法。

今年晚些时候，React 迁移到 Babel(这是一种 JavaScript 编译器，主要用于将 ECMAScript 2015+代码转换为 JavaScript 的向后兼容版本)作为首选编译器。

React v0.14 于 2015 年 10 月发布，进行了一项重大更改。这一变化包括将主 React 库分成两个包 React 和 React DOM。

React v0.14 的发布导致 react-tools 包和 JSXTransformer.js 文件贬值，Babel 成为主要编译器。

到 2015 年，React 得到了必要的认可，并被一些有影响力的公司如网飞和 Airbnb 所实施。

# 2016:

如果你想知道 React 是如何从 0？XX 版本(比如 2015 年的 v0.14)到一个 XX。x(比如现在的 v16.3)。嗯，事情发生在 2016 年。

React 15.0 版的发布带来了本年度的第一条新闻。脸书将 React 15.0 版从 0.14 版升级到了 15.0 版。React 15.0 版带来了几项变化，其中两项主要变化是:

这是对浏览器支持的所有 SVG 属性的新支持，并且使用了 **document.createElement** 而不是设置 innerHTML 来挂载组件。

React 获得了更多的认可，发布了基于 React 的库，如 MobX(用于简单和可伸缩的状态管理)和 BlueprintJS(基于 UI 的 web 工具包)。

# 2017:

2017 年 9 月，React 有了 v16.0 版本。有了新的特性，比如组件现在从 **render** 返回数组和字符串，引入了错误边界(这改进了错误处理)。此外，服务器渲染器被完全重写。

React Fiber(React 核心算法的重新实现)在 2017 年 F8 上开源。

# 2018:

React 在今年发布的版本中增加了几个新特性。在许多请求之后，React 得到了包含在库中的指针事件。指针事件将允许开发人员以一种简单的方式处理事件。

React Select v2.0 也在今年发布。这充当 React 的选择控件，允许开发人员构建开箱即用的 power React.js 组件。

到目前为止，React 今年最大的新闻仍然是 React 16.4.0 的发布。为了跟上 React 的所有事情，你可以观看 Dan Abramov 在 JSConf 冰岛 2018 上的演讲 Beyond React。

此次活动的一些亮点包括:

*   一个即将到来的叫做悬念的 React API 的可能性。
*   对 Reason 和 React 的介绍(用于向 OCaml 添加 JavaScript 风格的语法和工具)。
*   使用 MobX 和 React 构建手机游戏。

# 结论:

自从 [React](https://dashbouquet.com/blog/frontend-development/top-10-react-libraries-on-github) 向世界公布以来，五年过去了，它已经有了相当惊人的发展。有一点我毫不怀疑，那就是它会变得越来越好，会有更多的公司采用 React 技术来解决他们的问题。

![](img/5a0839d009210bb19206f7c7b08e7aaa.png)

[作者安东·沙列尼科夫](https://www.linkedin.com/in/anton-shaleynikov-45812a1/)