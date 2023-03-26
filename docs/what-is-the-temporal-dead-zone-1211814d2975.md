# 什么是时间死区？

> 原文：<https://medium.com/hackernoon/what-is-the-temporal-dead-zone-1211814d2975>

![](img/91a4882de3eb531e5ec3afc6e19bdae8.png)

我第一次听说“时间死区”时，我想我们可能已经离开了 javascript 领域，在谈论某种虾[天妇罗](https://en.wikipedia.org/wiki/Tempura)。的确，我错了。当时我一定是饿了。

当用 let 和 const 写 es6 时，时间死区或简称“tdz”是相关的。因为 let 和 const 是块范围的，并且在声明之前不能被访问，所以可能会遇到 let 和 const 没有定义的情况。

让我们来看一些使用天妇罗主题的例子:

```
var favoriteRoll = “Salmon Avocado”;
console.log(favoriteRoll); 
```

**你认为会打印到控制台上吗？**
如我们所料的“三文鱼鳄梨”。让我们看看当我们交换顺序时的结果。

```
console.log(favoriteRoll); 
var favoriteRoll = “Salmon Avocado”;
```

**这会给我们带来什么？**
undefined
由于变量在 javascript 中被提升，favoriteRoll 的声明而非初始化被提升到作用域的顶部。如果您想[了解更多信息，请查看 MDN 关于 var 提升的部分。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var#var_hoisting)

现在让我们试试我们的新朋友 let 和 const。

```
let favoriteSauce = “soy sauce”;
const WESTCOASTROLL = “California Roll”;
console.log(favoriteSauce);
console.log(WESTCOASTROLL);
```

**这会产生什么？** 【酱油】
【加州卷】都是正确的。

让我们调换一下顺序。

```
console.log(favoriteSauce);
console.log(WESTCOASTROLL);
let favoriteSauce = “soy sauce”;
const WESTCOASTROLL = “California Roll”;
```

**这产生了什么？**
这不是我们用 var 得到的未定义。
我们得到“未捕获的引用错误:favoriteSauce 未定义”。

**为什么会这样？**
因为与 var 不同，let 和 const 在声明之前都不能被访问。
MDN 很好的解释了 let 和 const:“在 ECMAScript 2015 中…在初始化之前引用块中的变量会导致 [ReferenceError](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/ReferenceError) (与用 [var](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var#var_hoisting) 声明的变量相反，它只会有未定义的值)。从程序块开始直到初始化处理完毕，变量处于'[时间死区](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let#Temporal_dead_zone_and_errors_with_let)'

在用 es6 编写代码时，认识到时间死区是有好处的。为了帮助防止 tdz，建议在作用域的顶部声明变量(let / const)。

> 纽约市的高级、首席或主要开发人员？[跨步正在招人](https://www.stridenyc.com/careers)！想要提升你的技术团队？[看看我们怎么做](https://www.stridenyc.com/our-work)！【www.stridenyc.com 

*原贴于* [*跨步博客*](https://www.stridenyc.com/blog/what-is-the-temporal-dead-zone) *。作者:* [*琳达·冈萨雷斯*](https://www.linkedin.com/in/lingonzalez/)