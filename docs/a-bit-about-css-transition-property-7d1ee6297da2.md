# 关于 CSS 的过渡属性

> 原文：<https://medium.com/hackernoon/a-bit-about-css-transition-property-7d1ee6297da2>

如果你对网页设计中的文字和颜色感到厌烦，让我给你介绍一种独特的、令人震惊的、值得爆炸的特性。好吧，也许没有那么多爆炸性，但你会喜欢它。CSS 跟随并支持你的意愿来增加趣味，这就是为什么有一个 ***过渡*** *选项*。

# 关于过渡

transition 属性允许平滑地更改 CSS 属性值。
需要**定义一个将要改变的属性**和效果的持续时间。

```
button{
   transition: background 1s;
   -webkit-transition: background 1s; /* Safari */
}
```

这两个值可以用通用属性`transition`或单个属性`transition-property`和`transition-duration`来定义。

我目前能记得的一些`transition-property`值是:

*   宽度，
*   身高，
*   颜色，
*   背景(颜色、图像、位置)，
*   转换(在下一篇文章中)，
*   边框(颜色、宽度)，
*   [位置](https://kolosek.com/css-position-relative-vs-position-absolute/)(上、下、左、右)，
*   文本([大小](https://kolosek.com/css-relative-font-size/)，粗细，阴影，字间距)，
*   保证金，
*   填充，
*   不透明，
*   能见度，
*   z 指数，
*   全部。

如果该值是用属性定义的，则定义一个以逗号分隔的 CSS 属性名称列表，该列表用于过渡效果。 ***值 all*** 为缺省值，定义所有可以转换的属性都将转换。**在这种情况下，所有已更改的属性将具有相同的** `**duration**` **(以及** `**delay**` **和** `**timing**` **如果已定义)。**

`transition-duration`属性非常简单，它定义了完成一个过渡效果需要多长时间，其值可以用秒(s)或毫秒(ms)来定义。

# 更多过渡选项

与过渡相关的其他属性有:

*   `transition-delay`
*   `transition-timing-function`

与 duration 属性非常相似，`transition-delay`是用秒(s)或毫秒(ms)来定义的，它指定了过渡效果开始的时间。与 duration 属性不同，这个属性可以是负数，如果是这样，它将在播放周期的中途开始。

`transition-timing-function`属性由以下函数定义:

*   **缓和** —慢开始，快中间，慢结束，
*   **线性** —恒速，
*   **缓和** —缓慢开始，快速结束，
*   **缓出** —快速启动，慢速结束，
*   **缓入缓出** —更明显的加速/减速曲线，
*   **三次贝塞尔函数(n，n，n，n)** —你可以编写自己的用 4 个坐标定义的函数([三次贝塞尔函数](http://cubic-bezier.com/))。

```
button{
   transition: background 1s ease-in-out 2s;
   -webkit-transition: background 1s ease-in-out 2s; /* Safari */
 }
```

使用通用属性`transition`你可以**定义所有四个属性**，只要记住第一次定义总是`transition-duration`。

可以设置多个转场。使用`,`分割不同的过渡。

```
button{
   transition: background 1s ease-in-out 2s, width 2s linear;
   -webkit-transition: background 1s ease-in-out 2s, width 2s linear; /* Safari */
}
```

# 触发

关于转换，要记住的主要事情是，这个属性是之前定义的，只有当它被触发时才会发生(如果用 transition-property 定义的属性被更改)。

您可以使用伪类直接触发 CSS 转换，如:hover、:focus 和:active，或者通过 JavaScript 添加或删除类。

# 一锤定音

这个属性得到了广泛的支持，所以请使用它！如果你需要一些灵感，看看这个例子。

*原载于 2018 年 4 月 10 日*[*kolosek.com*](https://kolosek.com/css-transition/)*。*