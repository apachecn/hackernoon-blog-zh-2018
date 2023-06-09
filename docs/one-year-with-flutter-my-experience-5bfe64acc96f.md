# 颤动的一年:我的经历

> 原文：<https://medium.com/hackernoon/one-year-with-flutter-my-experience-5bfe64acc96f>

![](img/05626327950d0402da3781e381f0fcd1.png)

**更新:看一眼我的新网站，**【fluttercrashcourse.com 

对于 Flutter 来说，这是令人惊奇的一年。

大概去年这个时候，我写了“[为什么 Flutter 会在 2018](https://codeburst.io/why-flutter-will-take-off-in-2018-bbd75f8741b0) 起飞”。虽然在 2018 年的几乎整个测试阶段以及现在的 1.0 版本中，Flutter 社区和产品都有了巨大的增长，Flutter 现在是 Github 上前 20 名 repos 中的[。回想这篇文章，是时候更新一下我一年前使用**Flutter**的经历了，以及我一路上发现的所有**优点和缺点**。](https://twitter.com/timsneath/status/1079436636344049664)

在过去的一年里，我到底是如何“与 Flutter 一起工作”的？我已经:

*   把我在 App Store 里的一个 [iOS 应用](https://steadycalendar.com)重新写成了 Flutter。
*   在 Flutter 上开发了一个免费的[速成班](https://fluttercrashcourse.com)，录制了超过 5 个小时的教学视频内容。
*   一些更小的、尚未发布的 Flutter 应用。

所以在我列出我的想法之前，请注意我的背景，就纯粹的移动开发而言，主要来自于 **iOS 世界**。此外，去年我在日常工作中也做了大量的 T21 土产。我不打算将 Flutter 与这些技术进行比较，但是基于移动开发人员的其他需求来塑造我对 Flutter 的印象是没有价值的。

下面是我使用 Flutter 一年来学到的东西:

## **1。Dart 学习简单，使用愉快。**

与去年我在 React 本地开发中广泛使用的 [TypeScript](https://www.typescriptlang.org/) 或 [Flow](https://flow.org/) 相比，Dart 更容易学习，语法也更简单。我之所以能够快速运行，是因为我有一个合适的编译器，它有清晰的、定义良好的错误消息，很少出现意外的隐藏运行时错误。如果有足够多的人问我这方面的经验，我可以写一个更详细的例子比较。我要说的是，当编写中等大小的应用程序时，开发人员应该珍惜强类型语言，因为它在快速移动和编写可靠代码方面有很大的回报。

## **2。有时我仍然不得不“自己动手”。**

另一个涉及新技术的常见情况是需要“开发自己的”库，以便与第三方服务集成。例如，为了使用 Mixpanel 在我的应用程序中进行分析(因为他们有一个慷慨的自由层选项和非常简单、清晰的 UI)，我必须使用我自己的库， [pure_mixpanel](https://pub.dartlang.org/packages/pure_mixpanel) 。这没什么大不了的，实际上很有趣。

我个人在使用 [scoped_model](https://pub.dartlang.org/packages/scoped_model) 方面取得了很大成功，因为它很好地抽象了使用流的需求，并且工作起来很像 React 的新[上下文 API](https://reactjs.org/docs/context.html) 。你仍然可以清楚地将你的业务逻辑和渲染逻辑很好地分开，这真的很容易学。

## **2。架构和状态管理模式仍然需要时间来成熟。**

首先，Flutter 是一项新技术，所以仍然很难获得足够的关于经过战斗测试的、可信的架构模式和状态管理工具的意见。有些人遵循“ [BLoC](https://www.youtube.com/watch?v=fahC3ky_zW0) ”(或“业务逻辑组件”)模式。对我来说，这个问题还没有定论，因为我认为它有点不必要的复杂。还有用于 Flutter 的 [RxDart](https://github.com/ReactiveX/rxdart) 和 [Redux，这两个我都还没用过，因为它们看起来也过于复杂。另一方面，来自 Android 或 React 世界的人们似乎在这方面取得了很多成功，因为他们可能已经习惯了。](https://pub.dartlang.org/packages/flutter_redux)

我认为，随着越来越多的人编写越来越复杂的 Flutter 应用程序，这整个生态系统将在 2019 年自然成熟。

## **3。热重装对我来说还是一件大事。**

关于这一点没有太多要说的，除了颤振的这个特性本身就足够重要，值得在本文中单独列出一节。速度快，更重要的是，*可靠*。当谈到热重装特性时，我不能对我使用过的其他技术说同样的话(告诉自己我不会对其他技术持否定态度)。

## **4。跨平台设计很难。**

材料设计很棒，可以让人们快速上手。对于某些类型的网络应用程序以及 Android 应用程序来说，这也是显而易见的。但是向 iOS 用户展示它，除非它是一个谷歌应用或非常简单的东西，不是一个好主意。iOS 用户已经习惯了他们自己的 CocoaTouch 风格的 UX。

当我使用一个代码库为两个平台编写代码时，我越来越多地看到，使用了某种定制的定制设计，引入了设计中常见的设计元素(例如，标签栏)。虽然 Flutter 确实提供了大量的 iOS 风格的小部件，但是为了将代码维护保持在最低限度，大多数人只会定制 Flutter 的材料设计库，这非常容易做到。

我会就这个主题写另一篇文章，但我的建议是坚持材料设计，在某些领域，尽量让它不那么“像安卓”给所有的 iOS 用户。一个例子是表单。以一种两种类型的用户都足够熟悉的方式使用材料设计来设计表单域的样式。

## **5。在颤振中实现复杂布局很容易。**

我习惯于使用 React、CSS Grid、Flexbox 等库来实现布局。Flutter 的布局方法有充分的理由从这些工具中获取大量线索。如果你已经熟悉这些基于网络的布局概念，学习 Flutter 中的布局将会很容易。即使你不是，也很容易。如果你想感受一下，我有一个关于这个的[视频。](https://fluttercrashcourse.com/lessons/container-layout-column-row)

此外，从代码可读性的角度来看，Dart 和 Flutter 中的 UI 逻辑非常出色。总的来说，我更喜欢个人实现布局，而不是像 JSX 这样的东西。这让我想起了 Swift 和 iOS 中的布局逻辑是多么简单，如果你是以编程方式实现布局的话。

## **6。仍然需要更多地关注端到端应用程序示例。**

虽然有大量关于使用 Flutter 的可靠文档、教程、社区和整体帮助，但我认为人们过于关注小部件了。这很有意义，因为 Flutter 是如此新的事物。但最终，越来越多的人不仅仅是实现纯 UI 和动画，他们将开始编写更多成熟的应用程序，我认为 Flutter 的网站上应该有更多端到端的教程。其实这也是我开课程网站的主要原因。

我了解到编写 Flutter 应用程序不仅仅是小工具。我发现有很多更高级的 Dart 功能非常有用，你必须去挖掘。我提到的架构模式，你可以去挖掘。最后，集成 web 服务和其他 Dart 最佳实践仍然需要更多的文档和教程。

## 7.GraphQL 或 gRPC，我会为我的下一个项目选择一个。

我一直在推动减少样板代码。虽然有一些工具可以帮助我解决这个问题，而且对于简单的项目来说效果很好，但是我想在我的下一个项目中，我会使用 T2 的 GraphQL 或者 T4 的 gRPC。我认为对这两者的投资都是值得的。至于 gRPC，我不推荐它用于较小的项目，但是对于大中型项目，一旦你使用了它，就很难回头了。gRPC 在另一个使用 Swift 的项目中运行得非常好，我已经让它在生产中运行了几年。

## 8.为两个平台提交应用程序很容易

确实需要一些投资来适应为每个平台提交应用程序所需的工具和步骤，特别是谷歌 Play 商店和 iTunes Connect，但这非常容易。我要说的是，在 iOS 上提交应用程序肯定需要更多的学习过程。

## 9.Flutter 的插件太多了。

对于所有我认为必须学会使用 Flutter 的小部件，我最终使用了大概 20%。例如，[居中](https://docs.flutter.io/flutter/widgets/Center-class.html)小部件。为什么有一个单独的部件*只是*为中心的东西？虽然它让新手很容易上手，但当需要实现更复杂的布局时，它的小部件会产生太多嵌套的 Dart 代码。相反，我依靠基本的[容器](https://docs.flutter.io/flutter/widgets/Container-class.html)布局选项，因为它们非常灵活，我无论如何都要使用它们。

我的建议是专注于简单、基本的小部件，只在真正需要的时候学习更多。

## 9.我正在传递 Firebase(除了推送通知)

Firebase 似乎是一个伟大的产品。这让我想起了以前的[解析](https://parseplatform.org/)。这似乎是一个很好的选择，特别是在他们没有足够的专业开发人员来管理定制后端的情况下，为了开始简单的项目或者以后将项目移交给客户。

事实是，大多数人已经有了一个现有的后端，或者技术团队已经选择编写他们自己的后端。这尤其适用于较大的公司，甚至仅仅是初创公司。

对于独立开发者或“夫妻店”来说，如果流量激增，你每月的 Firebase 账单会发生什么变化？这确实是我避免使用 Firebase 的主要原因，因为万一我真的遇到了病毒式传播的“梦想问题”,如果 Firebase 按使用量收费，我该如何支付呢？

注意，我以写后端系统为生，所以我有偏见。如果你是一个初级开发人员，想把一个简单的后端交给一个客户端，或者你根本不写后端 API，我仍然会强烈关注 Firebase。

## 10.Flutter 的文档越来越好

小部件和类文档现在有越来越多的例子([例](https://docs.flutter.io/flutter/widgets/Container-class.html))。这是对其他库的一大胜利，这些库甚至缺乏合适的示例，更不用说编写良好的文档了。

除了文档之外，我发现在过去一年的大部分时间里，Stack Overflow 上有很多充满热情、知识渊博的人给我提供我需要的支持。

## 11.我被 iOS 开发宠坏了，对 Flutter 也是如此。

我做 iOS 工程师已经很多年了，所以我有点被 iOS 开发者的经历宠坏了。不仅是文档和支持，还有 iOS 生态系统的整体质量，从库到 Xcode，再到 CocoaTouch SDK 的组织方式。

我认为 Flutter 符合这种体验。它简单得令人耳目一新，还采用了某些 React 原生组件的简单性，如 ListView(伙计，你在 iOS 上用过 UITableViewController 吗？总之，学习和使用 Flutter 非常顺利，有了成熟的工具。不再需要像 Xcode 这样复杂的东西真是令人耳目一新。

## **12。我再也不会回到“单一平台”移动开发了。**

视频游戏开发者可能永远不会考虑只为单一平台编写一个代码库。现在，“非视频游戏”开发者可以随着 React Native 和 Flutter 的出现做同样的事情。

例如，在我的空闲时间，我是一家“夫妻店”的成员，和我的妻子一起开发应用程序，她是 UX 的一名设计师。随着我们的用户群将我们的 iOS 应用转化为 Flutter 的数量增加了一倍多，现在它已经在两个平台上发布了，我现在不能再回到为一个平台写东西了。

# 最后的想法

一年后，当我开始我的下一个 Flutter 应用程序时(不久将会发布一些关于我如何开发它的视频！)，我仍然*真的真的很高兴我投入了这么多时间来学习 Flutter。我现在不能回去了。对于企业来说，这是一种可行的技术选择，可以为多种平台编写程序，作为开发人员来说，这是一种享受。如果你将这一事实与像[蜂鸟](/flutter-io/hummingbird-building-flutter-for-the-web-e687c2a023a8)这样的“Web Flutter”技术的潜力以及谷歌对新的 [Fuschia 操作系统](https://en.wikipedia.org/wiki/Google_Fuchsia)的长期投资结合起来，这些事实本身就表明谷歌在这项技术上投入了很多。*

请随时在 [Twitter](https://twitter.com/seenickcode) 上联系我，告诉我您的想法。如果你感兴趣的话，我在 fluttercrashcourse.com[也有免费的颤振课程！](https://fluttercrashcourse.com)

2019 年快乐编码！

尼克·曼宁