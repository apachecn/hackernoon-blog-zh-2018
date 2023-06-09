# “一次编写，随处使用”是一种反模式

> 原文：<https://medium.com/hackernoon/write-once-run-everywhere-is-an-anti-pattern-2e32e1dd5b93>

![](img/aca2c5ed5d77c58681b70134b15e655f.png)

*这篇文章的内容来自于我一直在做的三个不同项目的工作:*[*【get human】*](https://gethuman.com)**Swish**，以及我即将推出的一个新的秘密应用*[*ng-conf 2018*](https://sketchpoints.io/ngconf2018)*talk 叫做* [*超级的、服务器渲染的渐进式原生应用*](https://sketchpoints.io/ngconf2018/super-powered-server-rendered-progressive-native-apps) *。**

*在一个理想的世界里，你可以构建一个在所有平台(比如 web、iOS、Android 等)上一致且完美运行的应用程序。)而不用担心任何一个单独平台的独特细节。只要用某种通用的方式编码，它就能神奇地在任何地方工作。不幸的是，**我们今天并没有生活在那样的世界里**，任何以其他方式构建大型多平台应用的尝试都不可避免地会导致一个充满痛苦、苦恼和挫折的世界(和/或一个低质量的应用)。*

*其核心问题是，每个平台都有自己独特的优势，而这些优势往往会被严重的抽象削弱甚至完全消除。这并不意味着多平台开发不好。只是(在大多数情况下)遵循稍微不同的方法更有意义。一个更专注于构建多平台产品而不是多平台应用的平台。*

*然而，在我们讨论这个解决方案之前，让我们先更详细地讨论一下这个问题。*

# *问题:沉重的抽象*

*如前所述，多平台开发的主要问题是当一个更高层次的抽象(例如，一个门面)太重，最终会对底层平台的一些优势产生负面影响。然而，这个上下文中的术语“太重”完全是主观的，完全取决于使用特定外观的应用程序的需求。*

*所以，这就引出了一个问题:我们如何确定一个多平台的门面对于一个应用程序来说是否太重了？*

## *当外观好的时候*

*只要满足以下条件，抽象多个平台的外观就非常有用和有价值:*

*   *可用的关键特性——一个应用程序所需的底层平台的所有关键特性都需要易于使用(即，不需要经历很多麻烦)。*
*   *足够的性能——对应用程序至关重要的性能特征必须在外观和底层平台之间足够接近。*
*   *易于实现和维护 facade 需要通过简化开发来节省您的时间和金钱。不然何苦呢？*

## *当门面不好的时候*

*一般来说，在以下情况下，门面更可能不适合更多应用程序:*

*   *门面试图做的太多——门面中的功能越多，它试图做的越多，就越难做好。反之，门面越薄，成功几率越大。*
*   *外观层次太高——一个通用的包装器，围绕着不同平台的底层实用程序，通常很容易构建和维护。旨在抽象不同平台特定视图的通用前端视图确实很难实现。*
*   *外观是定制的——这乍一看似乎有悖直觉。你可能会认为，如果一家公司生产一件东西，他们会确保它符合他们自己的所有要求。也许有时这是真的，但在我的经验中，更可能的结果是，定制的多平台外观随着时间的推移很快退化为一个复杂的精致黑客，没有人愿意接触，因为害怕破坏整个系统。*

## *为什么“写一次，到处跑”是反模式*

*当你把它当作目标而不是达到目的的手段时,“写一次，到处跑”的想法是一种反模式。当您的主要目标是最大化代码重用时，您更有可能选择缺少关键功能和/或有重大性能问题和/或难以维护的外观。*

> *你真正的目标应该是**打造**你的产品的最佳版本。这可能涉及也可能不涉及多个平台，可能涉及也可能不涉及不同级别的代码共享。*

# *解决方案:具有单一平台应用的多平台产品*

*这篇文章使用了很多术语，这些术语对不同的人可能有不同的含义。我为多平台开发提出的解决方案要求我们对平台、产品和应用程序有相同的定义，以及它们应该如何组合在一起。*

## *什么是平台？*

> *用于本讨论目的的“平台”是为视觉呈现提供一组约束和低级能力的软件环境。*

*需要记住的一件重要事情是，平台有不同的级别。高层平台充当低层平台的门面。以下是不同平台级别的一些示例:*

*   *第 1 级— iOS、Android、网络浏览器*
*   *第 2 级— NativeScript，React Native(iOS 和 Android 之上的抽象)*
*   *第三级——Ionic(Cordova 之上的抽象，它本身也是 iOS 和 Android 之上的抽象)*

## *什么是产品？*

> *本讨论中的“产品”是指一个或多个前端应用程序，它们使用相同的语言创建，共同管理，旨在满足特定细分市场或任务的特定需求。*

*这里的关键点是，一个产品不仅仅是一个 app。在许多情况下，一个产品包括许多不同的应用程序，它们为了一个共同的目标而一起工作。例如，一个产品可以同时包含一个 NativeScript 应用程序和一个单独的渐进式 web 应用程序，以及一个 Web 上的管理员控制台和一个静态营销网站。*

## *什么是 App？*

> *这里讨论的“应用程序”是最小的可部署的前端视图包，为一个平台上的一组用户解决特定的问题。*

*这里有相当多的东西需要解释，但是我可以想象这个定义不一定与你自己的相匹配。大多数人都加入了太多的功能，把太多的责任交给了单个应用程序。总的来说，简单地在现有的应用程序上增加一个新的功能似乎比开发一个新的要容易得多。这就是为什么倾向于构建一个大型的、定制的多平台外观，最终成为一个组织的沉重负担。*

*所以…我们不要那样做。*

*与其创建一个庞大的应用程序，不如让我们创建一个由许多小应用程序组成的产品，其中每个可部署的应用程序单元为一个平台做一件真正好的事情。*

## *所有这些部分应该如何组合在一起？*

*有许多不同的方法可以用小型的单一平台应用程序来配置产品。下面是一个基于 JavaScript 的产品 foo 的例子:*

*![](img/b2c3bbec53ec4b33bea88cf5e278da16.png)*

*此处的文件夹结构描述了具有“平台夹层”架构的[产品 monorepo](https://www.youtube.com/watch?v=oW9AhP8l3aU) 中的代码:*

1.  *应用——位于最顶端的一组精简的、特定于平台的应用。在本例中，有一个用于 NativeScript 的“查看器”应用程序(称为 nativescript-viewer)和一个单独的用于 web 的“查看器”应用程序(称为 web-viewer)。每个查看器应用程序的功能都是相似的，但是代码对于目标平台是唯一的。*
2.  *libs——中间是所有平台无关的共享代码。这一层具有可以在所有应用程序之间共享的业务逻辑和实用程序。*
3.  *xplat —底层的一组精简的、特定于平台的低级库。这一层包含可由给定平台的所有应用程序使用的共享代码，以及可覆盖库(例如 xplat/NativeScript 中的 uploadFile()可以覆盖 libs 中的无操作 uploadFile()存根。*

*同样，这只是一个如何设置产品代码的例子。这不是唯一的方法。*

## *这种方法的好处*

*从高层次来看，这种方法的目标是两全其美:轻松访问任何给定平台的全部功能，同时仍然最大限度地提高代码重用。具体优势包括:*

*   *平台-应用程序匹配—您可以为您的应用程序选择合适的平台级别。有时候用爱奥尼亚是有道理的。其他时候，拥有独立的 iOS 和 Android 应用程序更有意义。*
*   *应用程序是轻量级和一次性的——能够为现有产品快速构建一个新的前端真是太好了。前端技术的世界瞬息万变，保持应用的精简性给了你很大的灵活性，可以根据需要进行重构/替换。*
*   *更容易扩展团队——随着你的团队变得越来越大，避免团队成员互相影响变得越来越难。使用这种方法，团队成员可以在不同的应用上工作，并且不太可能不经意地互相阻止。*
*   *更容易理解——在过去的几个月里，我和我的团队在 Swish T1 使用了这种方法的一个版本，新的开发人员能够如此快速地提高工作效率，并且很少的错误进入生产环境，这令人惊讶。巨大的胜利。*

# *结束语*

## *未来*

*我开始这篇文章的前提是，几乎任何重要的多平台应用程序都不能以完全通用的方式构建(即，应用程序将不可避免地在某个地方具有底层平台的逻辑)。*

*但是……事情会变的。*

*10 年前，web 开发人员经常需要编写专门针对 Internet Explorer 的代码。也许在 10 年后，网络将会有另一个巨大的飞跃，成为所有设备上真正的一等公民。*

*谁知道这是否会发生，但如果真的发生了，那么只为一个平台(即网络)而建也许有一天会变得可行。*

## *关于这个话题的更多信息*

*正如我前面提到的，我们在 Swish 的[支出追踪应用](https://swish.com/?utm_source=hackernoon&utm_content=writeonce)中使用了本文描述的技术，但我还有一些其他相关的即将到来的事件:*

*   *[内森·沃克](https://twitter.com/wwwalkerrun)和我正在用这种方法构建一个新的秘密应用，我们将在 [ng-conf 2018](https://sketchpoints.io/ngconf2018) 的演讲中展示这款名为[超级功能、服务器渲染的渐进式原生应用](https://sketchpoints.io/ngconf2018/super-powered-server-rendered-progressive-native-apps)。*
*   *我正在写一篇博文，深入探讨用 Angular 实现这一切的技术细节。[在 twitter 上关注我](https://twitter.com/jeffwhelpley)以了解何时发布。*
*   *我还在撰写一篇博文，专门讨论 SSR 应用程序，以及本文中概述的方法如何与 [AMP-to-PWA UX](https://www.ampproject.org/docs/integration/pwa-amp) 配合得非常好。*

*如果你对这个话题有想法，请在这里发表评论或联系我们。我在这个领域做了很多工作，可以利用任何和所有的反馈。谢谢！*