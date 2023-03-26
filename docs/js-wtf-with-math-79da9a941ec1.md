# JS WTF🦄用数学

> 原文：<https://medium.com/hackernoon/js-wtf-with-math-79da9a941ec1>

## 让我们用 JavaScript **Math** 来驾驭古怪。

![](img/5dcba17c2142867765fa3c7dfdb4a29d.png)

# 最大()与最小()

什么？`max`不大于`min`？**否**😱但这是有原因的。

[根据定义](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/max) `Math.max([value1[, value2[, ...]]])`返回给定数字中的最大值。然而，当没有提供参数时，它返回**最小可能值**，即`-Infinity`。

正如您所猜测的，当没有提供参数时，`Math.min()`返回`Infinity`，即**最大可能值**。

所以，对比`Math.max() > Math.min()`的时候甚至不公平。

> 这不公平。

`-Infinity`其道低于`Infinity`。

# 0.1 + 0.2

平心而论这不是 JS 的问题。这是**浮点数学**。

> 计算机本身只能存储整数，所以它们需要某种方式来表示十进制数。这种表示带有一定程度的不准确性。这就是为什么，通常情况下，`.1 + .2 != .3`——[0.300000000000004.com](http://0.30000000000000004.com/)

问题在于机器存储数值的方式(即**二进制表示**)。)在**整数值**的情况下，只有可以表示为数字乘以 2 的幂的整数才能被精确表示。

至于**有理数**(比如`0.1`，就是`1/10`)只有分母可以表示为 2 的幂的值才能精确表示。

虽然`0.1`和`0.2`可以在`base 10`中精确表示，但对于`base 2`来说却不是这样。当这种情况发生时，我们的机器会创建一个其真实值的近似值(这导致一个重复值)。

**因此，** `**0.1 + 0.2**` **的结果将是一个重复值。**

在`base 10`中由`0.30000000000000004`表示。

![](img/c480466e3db8d237146c86ba7353b5ad.png)

# [] + {}

在每个例子中,`+`操作符有不同的行为。

对于表达式`[] + {}`，它表现为 [**加法运算符**](https://www.ecma-international.org/ecma-262/6.0/index.html#sec-addition-operator-plus) ，对于表达式`{} + []`，它表现为 [**一元运算符**](https://www.ecma-international.org/ecma-262/6.0/index.html#sec-unary-plus-operator) 。

当解释表达式`[] + {}`时，JS 识别一个加法运算，但是在将两个操作数相加之前，它将它们转换成**原语**。这是通过执行静态方法`.toString()`来完成的。

结果，我们以操作`'' + '[object Object]'`结束。

只有在这个阶段 JS 才知道加法运算符`+`的目的是**连接字符串**。并且返回`'[object Object]'`的最终值。

下一个！

对于表达式`{} + []`，第一个操作符是一对花括号(即*一个空块*)。)这个空块表示**“没事做”**。因此，JS 在执行表达式时向前移动。

当到达`+`操作符时，它表现为**一元操作符**，因为它没有左操作数。通过[强制](https://www.safaribooksonline.com/library/view/you-dont-know/9781491905159/ch04.html) `[]`翻译成`0`。

# 真+真

这里，`+` 操作符表现为**附加操作符**并将两个操作数相加。然而，在此之前，它需要转换它们`ToNumber`。

[根据定义](https://www.ecma-international.org/ecma-262/6.0/index.html#sec-tonumber)，如果参数为**真**，则`ToNumber`返回 **1** ，如果参数为**假**，则 **0** 。

因为这个原因，我们的表达式`true + true`翻译成`1 + 1`，就是 2。

# 1 < 2 < 3

***剧透预警:*** *是**[***威压***](https://www.safaribooksonline.com/library/view/you-dont-know/9781491905159/ch04.html) *再来*😎*

*在这里，对这两个例子进行逐步讲解可能是有用的。*

*`1 < 2 < 3`*

1.  *因为 JS 从**左到右**解释和执行表达式，所以会先运行表达式`1 < 2`，因此`true < 3`；*
2.  *接下来，**胁迫**，`ToNumber(true) < 3`翻译过来就是【T41*
3.  *返回`true`(因为`1`比`3`低)；*

*`3 > 2 > 1`*

1.  *因为`3 > 2`是`true`那么`true > 1`；*
2.  *`ToNumber(true) > 1`翻译成`1 > 1` ( **再强制**👌);*
3.  *返回`false`(因为`1`不高于`1`)；*

*![](img/4d0bd3ddec5d640315e841b1836ccf7e.png)*

# ***‘5’+3***

*[根据定义](https://www.ecma-international.org/ecma-262/6.0/index.html#sec-addition-operator-plus)****加法运算符**执行**字符串串联**或**数字加法**。***

*****当其中一个操作数是字符串时，JS 将另一个操作数转换为字符串，并返回两者的串联**。在`'5' + 3`的情况下，因为左操作数是一个字符串，所以最终结果是`'5'`和`ToString(3)`的串联，即`'53'`。***

***[**减法运算符**](https://www.ecma-international.org/ecma-262/6.0/index.html#sec-subtraction-operator-minus) 对两个操作数执行**减法**。***

***然而，当操作符不是类型`Number`时，它被转换为`ToNumber`。由于这个原因，字符串`'5'`被转换成减去`3`的数字`5`。最终结果是`2`。***

# ***< your weirdness >***

****如果你发现这里有什么奇怪的地方，请告诉我*👐***

# ***由于🍻***

*   ***Maggie Neterval 为她的[演讲](https://www.youtube.com/watch?v=CRpKE04lkgo)***
*   ***震撼人心的[0.30000000000000004.com](http://0.30000000000000004.com/)***
*   ***[甲骨文](https://twitter.com/Oracle)的— [每个计算机科学家都应该知道的浮点运算](http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html)***
*   ***[凯尔·辛普森](https://twitter.com/getify)为他的[视频](https://www.youtube.com/watch?v=2pL28CcEijU)和[书](https://www.safaribooksonline.com/library/view/you-dont-know/9781491905159/)***
*   ***[MDN 文档](https://developer.mozilla.org/en-US/docs/Web)***
*   ***[克里斯·帕拉特](https://twitter.com/prattprattpratt)为[银河护卫队](https://youtu.be/2LIQ2-PZBC8)和 gif***
*   ***[骇客正午](https://twitter.com/hackernoon)为出版❤️***

****一定要看看我在 JS WTF 上的其他文章****

***[](https://hackernoon.com/oh-my-goodnejs-arrays-f9e9e4f03c97) [## JS WTF🦄使用数组

### 让我们利用一些带有 JavaScript 数组的 WTF。

hackernoon.com](https://hackernoon.com/oh-my-goodnejs-arrays-f9e9e4f03c97) [](https://hackernoon.com/js-wtf-with-number-5cd73514befb) [## JS WTF🦄带编号

### 是时候用数字来驾驭一些 JavaScript WTF 了。

hackernoon.com](https://hackernoon.com/js-wtf-with-number-5cd73514befb)***