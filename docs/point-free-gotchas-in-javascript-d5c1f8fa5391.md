# JavaScript 中的无要点陷阱

> 原文：<https://medium.com/hackernoon/point-free-gotchas-in-javascript-d5c1f8fa5391>

我是函数式[编程](https://hackernoon.com/tagged/programming)的忠实粉丝，我喜欢它的简洁，它更适合我的思维方式。我也喜欢尽可能少冗余的干净代码。话虽如此，无要点(有时也称为心照不宣)风格吸引我也就不足为奇了。在过去的几天里，我在 [JavaScript](https://hackernoon.com/tagged/javascript) 中应用这种风格时遇到了几个问题，并决定把它们写下来。

# 什么是无点风格

正如维基百科所说:

> *【无点】是一种编程范式，其中函数定义不标识它们所操作的自变量(或“点”)。*

这乍一看似乎很奇怪，但是让我们用一个简单的例子。假设我们有一个函数，它获取一个字符串，并返回首字母大写的字符串。接下来，我们有一系列我们都想资本化的字符串。这是`[map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)` [功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)的一个简单用例:

注意第二个`map`用法，它没有说明参数的名称，也没有创建新函数。这样做的原因是`map`调用它的第一个参数作为一个带三个参数的函数:

*   要处理的数组项(这是唯一的强制参数)，
*   该项目的索引，
*   正在处理的整个数组

Out `capitalise`函数碰巧也把要处理的项目作为它的第一个(也是唯一的)参数，所以在这种情况下，它在使用时是自由的。

这种样式有更多的用途，我们将在阅读本文时看到它们。

# 问题 1:函数接受的参数比预期的多

第一个问题来自于这样一个事实，即您可以用任意多的参数在 JavaScript 中调用一个函数——无论是太少还是太多。

如果您提供的参数太少，那些没有提供的参数将被设置为默认值(即`undefined`，除非另有说明)。

如果您提供了太多的参数，该函数会忽略多余的参数(除非它使用了`[arguments](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)` [对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments))。

这可能对你来说并不陌生，但是在无点的情况下，它会导致一些意想不到的结果。

让我们举一个最简单的例子:编写一个函数，它接受一个字符串数组并返回项目的数值。为了举例，我们假设输入是正确的。很简单，有`[Number.parseFloat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/parseFloat)`来表示:

正如我们所看到的，无点数版本非常有效。

那么，如果有人告诉我们数字总是整数，我们不需要把它们解析成浮点数，那会怎么样呢？然后我们会把`Number.parseFloat`换成`[Number.parseInt](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/parseInt)`，对吗？

哇，那是什么？无点版本突然表现得很奇怪。

原因是`Number.parseFloat`只接受一个参数——要解析的字符串——`Number.parseInt`接受一个额外的可选参数——要输出的数字的基数(例如十六进制字符串为 16)。因此，在这样的地图中使用时，实际情况是这样的:

正如我们看到的,`Number.parseInt`的基数参数是使用当前项目的索引设置的。这解释了`11`输入的`3`输出为二进制的 3 = 11。

这是 JavaScript 中无指针可能产生的第一类问题:函数接受的参数比预期的多。

除了对您知道其签名并且知道不会改变的函数使用免点之外，没有什么简单的方法来保护您自己，否则您的代码可能会意外中断。

# 问题 2:意外的`this`

这是我不久前参加的一次求职面试中出现的一个问题:

问题是修复错误。

人们可能会期望`"Hello"`被输出(我知道我做到了)。然而，`undefined`被输出到控制台。

原因是`setTimeout`执行回调函数的方式。[回调在不同的执行上下文](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout)中执行，如果`this`没有被显式设置，它将被设置为`global`对象。由于`global`(如果在浏览器中运行，则为`window`)没有`message`成员，我们的示例打印了`undefied`。

有两种方法可以解决这个问题:

第一个使用闭包来隐式地将`getMessage`调用的`this`设置为适当的值。

第二种(无指针)使用[绑定方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)显式设置`this` *的值*。

还有另一个看起来不错的代码示例——简单的常规模式使用:

然而，这最终抛出一个`TypeError`说:

> `*Method RegExp.prototype.test called on incompatible receiver undefined*`

或者在 Safari 中更有用一点:

> `*RegExp.prototype.test requires that |this| be an Object*`

同样，问题是`this`有一个意外的值(在本例中是`undefined`)。解决方案与前一种情况相同:

这里要指出的是，如果你想无指针调用的函数使用了`this`，你应该非常清楚它被设置为你所期望的。

# 结论

尽管无点式风格在其他(函数式)语言中很有用，但在 JavaScript 中，它经常带来一些问题，这些问题可能不值得它带来的简洁性。当被调用的函数在我的控制之下时，我有时仍然使用它。不过，有了这些经历之后，我会更加小心。