# 我以前讨厌 JavaScript。现在我喜欢了。

> 原文：<https://medium.com/hackernoon/i-used-to-hate-javascript-now-im-not-sure-why-7fc58a524bd1>

![](img/fe20ee329c0fd5b3146d76effd33a473.png)

Credit: Pixabay ([https://pixabay.com/en/node-js-logo-nodejs-javascript-736399/](https://pixabay.com/en/node-js-logo-nodejs-javascript-736399/))

我的职业生涯开始时非常接近硬件。我已经学会了在 iOS 平台上使用 C 和 Objective-C。我对软件有很大的控制权，我不得不学会管理内存，并花费数小时或数天来修复由我自己的疏忽和疏忽造成的问题(这是在 [ARC](https://en.wikipedia.org/wiki/Automatic_Reference_Counting) 之前)。我的职业生涯随着对 iOS 的偏爱而增长，我开始学习 Java(最近又学习了 Kotlin ),以转向 Android。

随着 Android 的发展，由于宽容的垃圾收集器，内存管理不再是一个问题。然而，我仍然对应用程序的生命周期有很大的控制权，我被迫了解两个主要移动平台之间的差异，从界面到功能都有很多东西需要理解。

我呆在我舒适的泡泡里，同时嘲笑 [JavaScript](https://hackernoon.com/tagged/javascript) 开发者——嘲笑网络应用糟糕的性能。我很长一段时间拒绝接触 JavaScript，总是不情愿地尝试更熟悉的口味，比如 TypeScript。它仍然是 JavaScript，但至少我不用输入它。我喜欢我的变量和常量有一个明确的类型。多年的内存管理让我紧张不安。

如果你有一辆车，很有可能你和骑自行车的人合不来。这背后有心理学的原因，通常被称为[搭便车问题](https://en.wikipedia.org/wiki/Free-rider_problem)。我花了很长时间才意识到我不喜欢 JavaScript 开发人员的原因是因为这种心理。我在学习内存管理和应用程序生命周期上投入了多年的努力和痛苦。我心想" **JavaScript 开发人员可以分享我的头衔，而不用为内存管理或理解软件生命周期付出代价？！**”。

![](img/6be33509e50235b8a7e92ddfd5ea52d4.png)

Credit: News Corp Australia

随着我的成长，我对自己的职业越来越不满意，我开始有了自己的想法。我的想法需要一个具有访问控制和认证等一些基本功能的 web 应用程序。它需要一些端点和资源。我做了一些研究，发现了 [NodeJS](https://hackernoon.com/tagged/nodejs) 。我想我不妨试一试，因为它看起来很容易安装，而且得到了很好的支持。我花了一个晚上的时间让这个网络应用程序运行起来。**一天晚上**。这是我做的一叠。

*   节点(服务器)
*   蒙古数据库
*   PugJS(模板语言)
*   几个`npm install --save xxx`电话

经过几个小时的研究，我有了一个带有功能认证系统和访问控制的功能 web 应用程序。我还不关心 Redis，Docker，K8s 或任何好东西。我的 MVP 建成了。我可以测试我的产品。我有一个没有备份的沙盒数据库，我有一个托管在数字海洋上的不可扩展的产品。然而，我用真实用户测试了这个产品，发现它并不像我想象的那么神奇。哦，没有浪费，只是学到了。

我开始更多地了解 JavaScript 和它所拥有的不熟悉的包经济。在 iOS 和 Android 上开发，你会用到一些常见的依赖项，比如 [Alamofire](https://github.com/Alamofire) 和[改型](http://square.github.io/retrofit/)。我期望 JavaScript 中有类似的依赖性。我是 T4，所以错了。我很快意识到为什么它被称为万维网。这实际上是一个巨大的依赖网络。这对我来说是一次可怕的经历。

![](img/98ce12b162d511ff1e649680f5078482.png)

Credit: Pixabay ([https://pixabay.com/en/spider-silk-bokeh-rain-arachnid-1287407/](https://pixabay.com/en/spider-silk-bokeh-rain-arachnid-1287407/))

然而，在学习了一些规则之后，比如使用带有 polyfills 的 [transformer](https://babeljs.io/) ，最常见的依赖项和一般的生态系统——我开始变得舒服多了。我能够在几天内创建任何产品的 MVP。这是一个令人印象深刻的转变，这都要归功于 NodeJS。

一旦 MVP 产生了，就该把时间投入到其他地方了。我开始拓展业务，并考虑根据自己的特定需求进行扩展。 **NodeJS 不是银弹**。NodeJS 在某些用途上很棒，但是像任何其他语言一样，不能因为你熟悉它就使用它。

至于语言本身，我曾经很反感 JavaScript。这个的核心原因是我前阵子用的 ActionScript 3.0。JS 和 AS3 都是 ECMAScript，那么为什么 AS3 比 JavaScript 好得多呢？兼容性。我花了一段时间才意识到浏览器兼容性对 JS 有多重要。一旦 Babel 开始被采用，JavaScript 就开始快速变化。它做了一些巨大的改进。实际上，我现在很喜欢写 JavaScript，同时确信遗留代码是在幕后创建的。

这篇文章主要针对那些嘲笑 JavaScript 的人。我仍然不喜欢“原生”JS 应用程序的想法。然而，我明白这给桌面/移动应用带来了多大的灵活性，如果我坐在这里对 VSCode 大加挞伐，那就太虚伪了。我确实喜欢混合应用程序的想法，它有一些用低级语言编写的核心组件，这是许多密集型桌面网络应用程序正在使用的方法。

如果你看到一个讨厌 JavaScript 的开发者，那可能是因为他们认为你是一个骑自行车的人，占据了他们认为自己有权拥有的空间。

**如果你喜欢这篇文章，请鼓掌并跟随我阅读我未来的内容。**