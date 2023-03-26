# ReactAF:永远新鲜(或作为 F%！)

> 原文：<https://medium.com/hackernoon/reactaf-always-fresh-or-as-f-d9f55aed596d>

## ReactAF 对 React 的作用就像 Babel 对 JavaScript 的作用一样

![](img/2a089bf0d71fae49a6f0ba2ef8de57b5.png)

假设你是一名组件开发人员，你正在维护或者编写一个 OSS React 包。由于您不能控制应用程序本身，所以最好的做法是在您的`package.json`中将`react`指定为`peerDependency`，类似这样的文件。

```
"peerDependencies": {
  "react": ">=15.0.0"
}
```

这说明您的包依赖于`react`版本 15.0.0 和更高版本，但是您期望为您提供依赖关系(这意味着应用程序必须在**的**依赖关系列表中包含`react`)。使用`peerDependencies`可以防止库构建中不必要的膨胀和混乱。

但是想一想这个—

您的组件**不知道消费应用程序指定了 React 的哪个版本，您的代码将在哪个版本上运行。从软件包的角度来看，你所能做的就是给出一个可接受的版本范围。您的代码必须能够处理任何版本的 React。**

所以…例如，如果您的软件包希望使用 React 16.2.0 中引入的 React 特性，如果应用程序使用 React 15.4.0，您就不太走运了。

例如，如果您想使用 React 16.3.0 中引入的新上下文 API，该怎么办？抱歉(好吧，除非你想强迫你的用户使用 16.3.0)。新静态`getDerivedStateFromProps`怎么样？同样的事情。

作为一名开发人员，这有点让人左右为难。要么你用各种各样的[特性检测](https://en.wikipedia.org/wiki/Feature_detection_(web_development))来填充你的代码，要么你编码到最小公分母。

如果不管 React 运行的是什么版本，您都可以依赖于较新的 React 特性，这不是很好吗？

Babel 用 [JavaScript](https://hackernoon.com/tagged/javascript) [网络](https://hackernoon.com/tagged/network)语言做到了这一点。当你使用 Babel 传输文件时，你可以像浏览器支持 ES2018 一样编码，即使你的代码可能运行在 2012 年发布的 IE10 上。

ReactAF 就是这么做的。

# 它是如何工作的？

ReactAF 对 React 的作用就像 Babel 对 JavaScript 的作用一样。它允许您使用旧版本 React 中没有的 React 特性进行编码。

但是它是如何工作的呢？ReactAF 进口在内部反应。如果 React 的版本已经具有它所模拟的特性，则返回“真正的”React。如果没有，它[polyfill](https://en.wikipedia.org/wiki/Polyfill_(programming))缺失的特性并公开一个[猴子修补的](https://en.wikipedia.org/wiki/Monkey_patch)版本的 React。

![](img/b1ce8f07df09288b32247ea7fc11f7e8.png)

How does it work? It’s like magic.

# 应用程序接口

ReactAF 在 React 的较低版本上提供/模拟以下 React APIs。

## `static getDerivedStateFromProps`

这是 React 16.3.0-alpha 中引入的关于类组件的静态方法。它旨在取代生命周期事件`componentWillReceiveProps`，后者在 17.0 版中将被弃用。

如果您运行的是 v16.3.0 或更高版本，您将使用本机实现。否则，此方法被聚合填充。

关于`getDerivedStateFromProps`的更多信息，请参见本文作者[巴托什·什切青斯基](https://medium.com/u/a5f578f5b81a?source=post_page-----d9f55aed596d--------------------------------)。

## 做出反应。碎片

`Fragment`是在 v16.2.0 及更高版本的`React`对象上公开的新组件。这是一种从`render`返回多个元素而不需要包装虚拟`div`元素的方式。

更多关于碎片的信息可以在[官方 React 博客](https://reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html)上找到。

## 做出反应。**创造环境**

`createContext`是让许多人兴奋不已的新的上下文 API。它也是在 React 16.3.0-alpha 中发布的。

你可以在 Kent C. Dodds 撰写的[这篇文章](/dailyjs/reacts-️-new-context-api-70c9fe01596b)中了解更多关于新的上下文 API 的信息。

# 还有更多…

其实没有…就这些。

但是随着 React 发布新特性，ReactAF 将被更新以通过 polyfill 支持它们，如果可能的话(欢迎 PRs😉).这样，无论消费应用程序使用的是哪个版本，您都可以在包中使用最新的 React APIs 进行编码。

# 更改您的代码

最棒的是。要开始使用 ReactAF，你需要做的就是添加 3 个小字符:`-af`。

而不是这个。

```
import React from 'react';
```

你会这么做。

```
import React from 'react**-af**';
```

# 你从哪里得到它？

ReactAF 在 npm 上[可用(当然)。你可以在 GitHub 上看到源代码和/或投稿。我希望你能帮助 ReactAF 做得更好！](https://www.npmjs.com/package/react-af)

如果你觉得慷慨，⭐️会很受欢迎。🙏

[](https://github.com/donavon/react-af) [## 多纳文/react-af

### react-af -允许你使用某些 React.next 特性来编码！非常适合组件库维护者。

github.com](https://github.com/donavon/react-af) 

# 结论

虽然 ReactAF 可能不适合所有人，但它确实可以让 OSS 包开发者受益，让他们可以自由地使用现代 React APIs 进行编码。我会说让这个包反应为 F%!

请…原谅这篇帖子的任何信息式的品质。\_(ツ)_/

*我也为美国运通工程博客写稿。在*[*American express . io*](http://americanexpress.io/)*查看我的其他作品和我才华横溢的同事的作品。也可以* [*在 Twitter 上关注我*](https://twitter.com/donavon) *。*