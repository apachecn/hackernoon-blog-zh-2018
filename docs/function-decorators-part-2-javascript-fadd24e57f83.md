# 函数式 JavaScript:函数装饰器第 2 部分#JavaScript

> 原文：<https://medium.com/hackernoon/function-decorators-part-2-javascript-fadd24e57f83>

![](img/0067269ac029f6575eb863b66eb26fc6.png)

[geralt @ pixelbay](https://pixabay.com/en/think-switch-arrows-rethinking-2177840/)

# 开始

> **函数装饰器**允许你在不修改原函数的情况下增强现有函数。

在第 1 部分中，我演示了函数装饰者如何将回调转换成承诺，然后再转换回来。但是函数装饰器比有限范围的回调和承诺有用得多，所以我认为这个主题需要重启。

**第一部分:**

[](https://hackernoon.com/transforming-callbacks-into-promises-and-back-again-e274c7cf7293) [## 函数装饰者:将回调转化为承诺，然后再转化回来

### 我在 JavaScript 领域工作的每一天，都会遇到回调、承诺或 async/await。我有我的…

hackernoon.com](https://hackernoon.com/transforming-callbacks-into-promises-and-back-again-e274c7cf7293) 

我认为展示一堆例子将是展示函数装饰者的最好方式，所以**这篇文章将稍微轻于文字，更多地关注代码。**

# 一个函数装饰者的 Hello 世界

基本的函数装饰非常简单(它什么也不做)。

为了支持 n-arity 函数，我们可以把它扩展成这样(仍然什么也不做)。

现在让我们创建并使用一个`helloWorld`装饰器来装饰`add`函数。

使用这个基础装饰器作为您想要创建的任何函数装饰器的模板。

# 日志功能装饰器

轻松地将您的日志逻辑包装到现有函数中。

**作业:**你如何修改它来支持异步函数？要得到提示，请看下面的`timed`函数装饰器。

# 计时器函数装饰器

同步和异步代码都可以使用的基本定时器函数。

第 15 行检查该值是否是一个承诺，并将返回值放到一个`then`中，而不是返回它。

# 函数参数保护装饰器

防止所有参数为空或未定义。

**家庭作业:**如何改进这个装饰器？如何添加参数名？你如何防范一些争论？

# 异常处理

您可以返回一个包含值或错误的对象，而不是引发异常。这类似于任一单子如何处理它的值。(现在不用担心单子)。

**作业:**研究并学会使用[或](https://github.com/sanctuary-js/sanctuary)单子。更改此代码以返回。

# 获取 JSON 函数装饰器

使用 fetch 时，经常会看到这样的代码散布在您的代码库中:

要访问那个 json，你必须首先调用`response.json()`。

# Currying

如果你熟悉像 Ramda 的 curry 这样的 curry 函数，那么你可能已经熟悉了函数装饰器。

注:我推荐使用更成熟的[库里功能](http://ramdajs.com/docs/#curry)，比如 Ramda 的那个。尽管这种方法可以很好地工作，但它只是作为示例提供的。

# Next.js 浏览器检查

在我创建的一个 Next.js 项目中，我不得不限制几个函数只能在浏览器端执行。我可以用一个简单的函数装饰器干净利落地做到这一点。

# 多种装饰方式

有几种方法可以修饰函数。你如何决定使用 decorators 将取决于你的用例。

# 组合函数装饰器

因为每个装饰器也返回一个函数，所以函数装饰器可以很容易地组合起来创建一个大型函数。

还可以使用函数组合来组合装饰器

# 反应

反应和整个生态系统充满了功能装饰。如果你用过 React，很有可能你已经用过函数装饰器了。`react-redux`的`connect`是一个函数装饰器。`redux`的`bindActionCreators`是一个函数装饰者。

# 结束了

函数装饰器是用来增强现有函数的强大工具。它们不是什么新东西，如果你还没有使用过函数装饰器，你很可能会在不久的将来使用它们。

即使它们如此强大且易于创建，我也没有看到很多人在他们的代码中创建函数装饰器。这告诉我，函数装饰器是未被充分利用的工具，值得更多的探索。

别忘了做这篇文章里的作业！

我很想在下面的评论中听到你如何使用函数装饰器来改进你的代码库！😃

干杯！

## 跟我来

推特:[https://twitter.com/joelnet](https://twitter.com/joelnet)中:[https://medium.com/@joelthoms/latest](https://medium.com/@joelthoms/latest)dev . to:[https://dev.to/joelnet](https://dev.to/joelnet)LinkedIn:[https://www.linkedin.com/in/joel-thoms/](https://www.linkedin.com/in/joel-thoms/)