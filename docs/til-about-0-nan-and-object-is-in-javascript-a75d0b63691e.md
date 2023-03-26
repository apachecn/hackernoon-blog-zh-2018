# 今天我学习了 JavaScript 中的+-0，NaN 和 Object.is

> 原文：<https://medium.com/hackernoon/til-about-0-nan-and-object-is-in-javascript-a75d0b63691e>

过去几天我在[克卢日纳波卡](https://en.wikipedia.org/wiki/Cluj-Napoca)，在那里我在[优秀 JSHeroes 大会](https://jsheroes.io/)上发言。在 Mathias Bynens 的演讲[“JS 开发人员的 V8 内幕”](https://www.youtube.com/watch?v=HULaOA5oJMY)(录音来自另一个事件)中，我看到了一个非常有趣的代码片段。

```
Object.is(-0, +0);
```

这一行在两个方面很吸引人——让我们来看看。

# JavaScript 中存在的两个零

第一个事实是，JavaScript 中的数字遵循 IEEE 浮点运算标准。这个标准有几种变体，JavaScript 使用基于 64 位的“双精度”,也称为“binary64”。

IEEE 754 定义[一个符号、一个有效数字和一个指数来描述每个有限数字](https://en.wikipedia.org/wiki/IEEE_754#Formats)。理解这是如何工作的可能需要一些时间，但重要的事实是 JavaScript 数字中有一位(符号位)定义了一个数字是正还是负，这意味着`0`也可以是负的。

```
const posNumber = 1;
const negNumber = -1;
const posZero = +0;
const negZero = -0;
```

我对发现负零的第一反应是，我的代码中肯定没有这些，但是，当我对`-0.23`进行舍入时，我也会以负零结束，这使得负零更有可能也出现在我的 JavaScript 中。

```
Math.round(-0.23); // -0
```

当你想比较正负 0 时，这变得很有趣，因为它们被同等对待。

```
-0 === +0 // true
```

[AbdulFattah Popoola](https://twitter.com/abdulapopoola) 写了[一篇很好的文章](https://abdulapopoola.com/2016/12/19/why-javascript-has-two-zeros-0-and-0/)，如果你感兴趣的话,[在“你不知道的 JavaScript”系列文章](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch2.md#zeros)中甚至有一个关于正负零的章节，会有更多的细节。

*旁注:可以用除法和由此产生的* `*Infinity*` *来区分* `*-0*` *和* `*0*` *。*

```
1 / -0 === -Infinity    // true 
1 / 0 === Infinity      // true
-Infinity !== Infinity  // true
```

# Object.is —比较没有怪癖？

所以，和`===`的严格对比，没有抓住两个零不一样的事实。你可能知道`NaN`也不等于`NaN`。

```
NaN === NaN // false // you can use Number.isNaN as an alternative
Number.isNaN(NaN) // true
```

这些机会就是`Object.is`开始发挥作用的时候。在大多数情况下，它的行为与`===`相同，但它包括一些小的“改进”,使事情变得更有逻辑。

```
Object.is(-0, 0); // false 
Object.is(NaN, NaN); // true
```

这样做的缺点是，不是每个人都知道`-0`的存在，这意味着对于`-0.23`的舍入来说，`0`和`-0`之间的差异可能会导致难以发现的错误。这大概就是它在 JavaScript 中通常被忽略的原因。

我第一次看到`Object.is`是在 Mathias 的幻灯片中，它似乎不经常被使用。

我马上想到的一个问题是`Object.is`是否和`===`一样快。我创建了一个快速 JSPerf 来看看`Object.is`和`===`相比表现如何。在 Safari 和 Firefox 中`Object.is`似乎比`===`慢很多，而在 Chrome 中差不多。那太有意思了！

如果有人对性能测试有意见，请告诉我。浏览器内部极其复杂，有时测试中发生的优化会使整个事情无效。

如果你在你的源代码中使用`Object.is`，我也很乐意在这里！:)

*原载于*[*www.stefanjudis.com*](https://www.stefanjudis.com/today-i-learned/0-nan-and-object-is-in-javascript/)*。*