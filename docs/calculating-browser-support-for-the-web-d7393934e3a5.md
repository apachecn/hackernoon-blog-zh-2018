# 计算浏览器对 web 的支持

> 原文：<https://medium.com/hackernoon/calculating-browser-support-for-the-web-d7393934e3a5>

随着时间的推移，web 也在发展，并带来了新的 API。当开发一个*产品*时，包含一两个尖端的 API 可能会在用户的浏览器中抛出一些危险信号。

作为开发者，我们可以采取一些预防措施来确保我们的产品不会出问题，但是说比做好。

# 预防措施

## 放弃

添加免责声明来警告用户产品在某些平台上可能出现的不稳定或不确定的行为应该是第一道防线。

大多数黑客，甚至一些 polyfills，对于一些旧的/不兼容的浏览器，不能保证与本地 API 的行为相同。将这种可能性告知用户是一种很好的做法。

## 我能用吗

如果你曾经想使用一种新兴技术，但不确定用户的机器是否支持它，那么你应该了解一下[can use](https://github.com/Fyrd/caniuse)。

> “我可以使用吗”提供了最新的浏览器支持表，以支持桌面和移动 web 浏览器上的前端 web 技术。

如果这对您来说是新事物，下次您使用现代 web APIs 进行比较时，请务必访问[caniuse.com](https://caniuse.com)。

## 修补构建过程

这个巴别塔预置`[babel-preset-env](https://babeljs.io/docs/plugins/preset-env)`根据你想要支持的浏览器自动确定你需要的巴别塔插件。它通过将您想要支持的版本与一个[插件列表](https://github.com/babel/babel-preset-env/blob/master/data/plugins.json)进行比较来做到这一点，并且仅在必要时拉入聚合填充。

我相当肯定，对于我所不知道其他构建管道，可能有一些等价的。如果你知道一些，请在评论中列出来。

# 缺失的部分

使用 babel preset 时，[开发者](https://hackernoon.com/tagged/developer)必须有意识地提供浏览器版本。这对于开发者来说是一个负担，并且相对容易错过[浏览器](https://hackernoon.com/tagged/browser)或者包含错误的版本。

另一方面，“我可以使用”一次只显示一个功能。如果你有很多瓶颈技术，就很难决定通用版本。

这里有些东西可以试试。你能告诉我支持[服务人员](https://www.caniuse.com/#feat=serviceworkers)和 [CSS 显示内容](https://www.caniuse.com/#feat=css-display-contents)的每个浏览器的最低版本吗？

# 🗲浏览器-支持🗲

我做了一个简单的网站来解决这个问题，我曾经聪明地命名为“浏览器支持”([网站](https://zhirzh.github.io/browser-support/build)，[来源](https://github.com/zhirzh/browser-support))。

这和“我可以使用”差不多，只是多了一些额外的功能。您可以加载多种技术，并通过 URL 共享“清单”。一旦你有了版本，如果有必要的话，你可以把它们插入到你的构建管道中，或者在网站的顶部贴一个相关的通知。

 [## 浏览器支持

### 计算您的尖端技术支持的浏览器版本

zhirzh.github.io](https://zhirzh.github.io/browser-support/build) 

**PS** :如果你对上一节末尾的练习有疑问，可以在这里找到答案[。](https://zhirzh.github.io/browser-support/build/?checklist=serviceworkers,css-display-contents)

# 结束了

今天到此为止——只是想分享我前几天做的一些东西，可能会节省开发人员的一些时间。

以防有人错过:

*   源代码: [zhirzh/browser-support](https://github.com/zhirzh/browser-support)
*   网址:[浏览器支持/](https://zhirzh.github.io/browser-support/build/?checklist=serviceworkers,css-display-contents)
*   演示:[浏览器支持？清单=服务人员，CSS-显示-内容](https://zhirzh.github.io/browser-support/build/?checklist=serviceworkers,css-display-contents)