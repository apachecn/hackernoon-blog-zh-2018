# 你应该停止的 3 个 JavaScript 性能错误

> 原文：<https://medium.com/hackernoon/3-javascript-performance-mistakes-you-should-stop-doing-ebf84b9de951>

如果我告诉你你所知道的一切都是谎言，会发生什么呢？如果你知道我们亲爱的 ECMAScript 在最近几年发布的一些关键特性实际上是危险的性能陷阱，包裹在一行漂亮的回调函数代码中，会发生什么呢？这个故事开始于几年前，回到 ES5 的天真时代…

![](img/d264241f54bb268dec0351ecd0e6cb3b.png)

我仍然清晰地记得这一天，ES5 发布了，伟大的新数组函数引入了我们亲爱的 JavaScript。其中有 forEach、reduce、map、filter——它们让我们感觉到语言在成长，功能越来越多，编写代码变得更加有趣和流畅，结果也更容易阅读和理解。

大约在同一时间，一个新的环境出现了——node . js，它让我们能够从前端平稳过渡到后端，同时真正重新定义了全栈开发。

如今，Node.js 使用最新的 ECMAScript over V8，正试图被视为主要服务器端开发语言的一部分，因此，它需要证明其性能是值得的。是的，有这么多的参数需要考虑，是的，没有比所有语言都优越的银弹语言。但是，使用像上面提到的数组函数这样的现成特性编写 JavaScript 对您的应用程序性能有帮助还是有害呢？

此外，随着最终用户的计算机变得更强大，网络变得更快，客户端 javascript 声称不仅仅是表示\视图的合理解决方案——但是当我们的应用程序需要极快的性能，并且可能是非常大而复杂的应用程序时，我们能依靠它吗？

为了测试这些问题，我尝试比较几个场景，并深入了解我得到的结果。我在 macOS 上的 Node.js v10.11.0 和 Chrome 浏览器上执行了以下测试。

## **1。循环遍历一个数组**

我想到的第一个场景是对一个 10k 项的数组求和，这是一个有效的现实解决方案，我是在尝试从数据库中获取一个很长的项目表并用总和来增强它时偶然发现的，无需对数据库进行额外的查询。

我使用 for、for-of、while、forEach 和 reduce 比较了随机 10k 项的总和。运行测试 10，000 次会返回以下结果:

```
For Loop, average loop time: ~10 microseconds
For-Of, average loop time: ~110 microseconds
ForEach, average loop time: ~77 microseconds
While, average loop time: ~11 microseconds
Reduce, average loop time: ~113 microseconds
```

在谷歌搜索如何对数组求和时，reduce 是最好的解决方案，但它是最慢的。我的目标也没好多少。即使是最新的 for-of (ES6)也只能提供较低的性能。事实证明，良好的旧 for 循环(以及 while)提供了迄今为止最好的性能——好 10 倍！

最新推荐的解决方案怎么会让 JavaScript 慢这么多？这种痛苦的原因来自两个主要原因，reduce 和 forEach 需要执行一个回调函数，该函数被递归调用并使堆栈膨胀，以及对执行的代码进行额外的操作和验证(此处描述[为](https://www.ecma-international.org/ecma-262/5.1/#sec-15.4.4.21))。

## **2。复制数组**

虽然这听起来不那么有趣，但这是不可变函数的支柱，它在生成输出时不会修改输入。

这里的性能测试结果再次显示了同样有趣的趋势—当复制 10k 随机项目的 10k 数组时，使用旧的学校解决方案会更快。同样，最时髦的 ES6 扩展操作`[…arr]'和 Array from `Array.from(arr)`加上 ES5 映射` arr.map(x => x)`不如老牌切片` arr.slice()`和 concatenate `[]。concat(arr)`。

```
Duplicate using Slice, average: ~367 microseconds
Duplicate using Map, average: ~469 microseconds
Duplicate using Spread, average: ~512 microseconds
Duplicate using Conct, average: ~366 microseconds
Duplicate using Array From, average: ~1,436 microseconds
Duplicate manually, average: ~412 microseconds
```

## **3。迭代对象**

另一个常见的场景是遍历对象，这主要是当我们试图遍历 JSON 的和对象，而不是寻找一个特定的键值时所必需的。还有一些老的解决方案，比如 for-in `for(let key in obj)`，或者后来的` Object.keys(obj)`(在 es6 中出现)和` Object.entries(obj)`(来自 ES8 ),它们返回键和值。

使用上述方法对 10k 个对象迭代进行性能分析，每个迭代包含 1，000 个随机键和值，结果如下。

```
Object iterate For-In, average: ~240 microseconds
Object iterate Keys For Each, average: ~294 microseconds
Object iterate Entries For-Of, average: ~535 microseconds
```

原因是在后面的两个解决方案中创建了值的可枚举数组，而不是在没有键数组的情况下直接遍历对象。但是底线结果仍然引起了关注。

## **底线**

我的结论很明确——如果超快的性能对您的应用程序至关重要，或者如果您的服务器需要处理一些负载——使用最酷、可读性更强、更简洁的选项将对您的应用程序性能产生重大影响——速度可能会慢 10 倍！

下一次，在盲目采用最聪明的新趋势之前，确保它们也符合您的需求——对于小型应用程序，编写快速且可读性更好的代码是完美的——但是对于压力很大的服务器和大型客户端应用程序，这可能不是最佳实践。

以上是在创建我的[特拉维夫顶级餐厅](https://www.resty.co.il/?locale=en)网站时调查和实施的。