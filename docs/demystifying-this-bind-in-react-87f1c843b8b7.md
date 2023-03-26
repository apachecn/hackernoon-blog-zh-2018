# 揭秘这个. bind in React

> 原文：<https://medium.com/hackernoon/demystifying-this-bind-in-react-87f1c843b8b7>

您是否有过这样的经历:在阅读现有的 React 代码库时，想知道为什么 React 组件构造函数中需要`this.functionName = this.functionName.bind(this)`？更令人困惑是，并不是所有的函数都有绑定语法。

> 如果你在寻找一个快速解决这个问题的方法，其中一个方法(也是最好的方法)就是把你所有的函数都变成箭头函数。Arrow 函数从外部词法范围获取`this`。这里有一个基于以上的例子。你需要将[这个](https://babeljs.io/docs/en/babel-plugin-transform-class-properties/)添加到你的通天塔配置中

# 这不是由于反应

这就是 JavaScript 的工作方式。其他地方有很多文章解释了 JavaScript，尤其是`.bind()`的含义(这是如何传递上下文的)，以及 JavaScript 如何为您绑定。相反，我将直接进入 JavaScript 中的*深渊*。

Reference code for the below

在 JavaScript 的深处，当你做`cat.sayHi()`时，JavaScript 返回`(cat, "sayHi", true)`的[引用类型](https://tc39.github.io/ecma262/#sec-reference-specification-type)值。ReferenceType(基本、名称、严格)其中

*   基础是对象
*   名称是属性(或方法)
*   `use strict`时严格=真

当 JavaScript 返回引用类型值`(cat, "sayHi", true)`时，意味着属性`sayHi`将拥有`cat`作为`this`上下文。如果对象是`undefined`，最后一个参数决定`this`的值。`true`为`undefined`或`global window object`为假。

然而，当您执行`var dogHi = dog.sayHi`时，JavaScript 会丢弃引用类型值，只将函数存储到 dogHi 中。因此，当您执行`dogHi`时，它不知道`this`的值是 dog and throw `cannot read property 'name' of undefined`

# 回到 React 的例子

回到前面的例子，当我们做`onClick={this.handleClick}`时，我们基本上是在给 callback 分配一个类似于我们做`var dogHi = dog.sayHi`的函数。

为了解决这个问题，我们可以使用 arrow 函数，依赖于创建函数的外部词汇上下文(Animal 类):

我希望这有所帮助，箭头函数在更多的上下文中是有用的。我将把这些留在另一篇文章中。