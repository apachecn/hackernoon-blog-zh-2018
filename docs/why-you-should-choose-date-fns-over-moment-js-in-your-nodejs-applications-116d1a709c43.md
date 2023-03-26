# 为什么在 nodeJs 应用程序中应该选择 date-fns 而不是 moment.js

> 原文：<https://medium.com/hackernoon/why-you-should-choose-date-fns-over-moment-js-in-your-nodejs-applications-116d1a709c43>

很简单:目标模块导入

链接起来:

[](https://date-fns.org/v1.29.0/docs) [## 现代 JavaScript 日期实用程序库

### date-fns 提供了最全面、最简单、最一致的工具集，用于在

date-fns.org](https://date-fns.org/v1.29.0/docs)  [## Moment.js | Docs

### define(['moment']，console.log(moment()。格式(' LLLL ')；});定义(['moment '，' moment/locale/de']…

momentjs.com](https://momentjs.com/docs/) 

T 何 [Javascript 日期对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)有问题，下面是关于这个主题的一些阅读:

*   [https://codeof matt . com/2013/06/07/JavaScript-date-type-is-scarrely-broken/](https://codeofmatt.com/2013/06/07/javascript-date-type-is-horribly-broken/)
*   [https://jj09.net/javascript-date-a-bad-part/](https://jj09.net/javascript-date-a-bad-part/)
*   [http://blog . dy graphs . com/2012/03/JavaScript-and-dates-what-mess . html](http://blog.dygraphs.com/2012/03/javascript-and-dates-what-mess.html)

强调这些问题的是越来越多的 JS 日期包装器库，旨在解决问题和简化代码中的日期操作。到目前为止，Moment.js 是明显的赢家(也是我个人的首选)。Moment 很棒，几乎提供了我正在寻找的所有特性。

最近，我在工作中开始了一个新的 NodeJs 项目，webpack 捆绑包的大小目前对我们来说非常重要。我一直在做的微服务特别需要一些特定的日期验证和操作，我之前已经去过 Moment.js 了。但是后来，我发现 Moment.js **并没有导入特定模块**的能力，要么全有，要么全无。所以，我谷歌了一下替代品，发现了这个:

所以我检查了一下，我决定进口，让它有机会。我导入了 4 个特定的函数来完成这项工作。这四个功能几乎没花我一分钱。

我已经用了一周了，还在用。好奇想知道是否还有其他人在使用约会 fns。当我写这篇文章的时候，我决定去寻找答案，是的，人们正在使用它。到撰写本文时的上个月的 2MIL+ npm 下载量:

[](https://www.npmjs.com/package/date-fns) [## 日期-fns

### 现代 JavaScript 日期实用程序库

www.npmjs.com](https://www.npmjs.com/package/date-fns) 

你对 NodeJs 项目中的 date-fns 有什么想法？有更好的吗？让我知道你的想法和问题，并在推特上给我一个关注。坚持下去。

# 如果你喜欢这篇文章，请推荐分享，帮助别人找到！