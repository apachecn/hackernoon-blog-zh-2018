# 在适当的地方使用 CSS 网格(重温)

> 原文：<https://medium.com/hackernoon/using-css-grid-where-appropriate-revisited-53df7dbcec6a>

![](img/35a4416e82fe2f9dca499f724a76902c.png)

这个解决方案是我去年的文章[“在适当的地方使用 CSS 网格”](https://www.silvestarbistrovic.from.hr/articles/using-css-grid-where-appropriate/)的后续文章。目标是找到一个带有未知数量项目的导航解决方案。

# 概述

用 CSS Grid 创建导航可能不是最好的解决方案。然而，如果你想使用 CSS 网格，有两种选择:

*   使用`grid-auto-flow: row;`并将每个项目放入网格中，如下所示:

```
.nav__item:nth-child(1) { grid-area: 1 / 1 / 2 / 2;}
```

*   使用关键字 auto 定义明确的网格以设置行和列的宽度:

```
.nav { display: grid; grid-auto-flow: row;} @media screen and (min-width: 320px) { .nav { grid-template-columns: repeat(4, auto); grid-template-rows: repeat(2, auto); }}
```

在这两个例子中，我们都定义了一个严格的网格——一行中的许多列都是严格定义的。

Solution 2017

# 新的解决方案

我使用 CSS Grid 已经一年多了，在这个过程中，我学会了如何正确地使用它的特性:

*   `[minmax()](https://www.w3.org/TR/css-grid-1/#valdef-grid-template-columns-minmax)` [功能](https://www.w3.org/TR/css-grid-1/#valdef-grid-template-columns-minmax)、
*   `[auto-fit](https://www.w3.org/TR/css-grid-1/#valdef-repeat-auto-fit)` [关键词](https://www.w3.org/TR/css-grid-1/#valdef-repeat-auto-fit)，
*   `[grid-auto-flow](https://www.w3.org/TR/css-grid-1/#propdef-grid-auto-flow)` [属性](https://www.w3.org/TR/css-grid-1/#propdef-grid-auto-flow)，以及
*   如何避免媒体的质疑🎊。

# 代码

我已经分叉以前的解决方案，并更新了上面提到的功能。这是最终的解决方案。

```
.nav — grid2 { display: grid; grid-auto-flow: dense; grid-template-columns: repeat(auto-fit, minmax(60px, auto)); justify-content: center;
}
```

Solution 2018

让我们分解这段代码。

# 最小最大值()

`minmax()`函数将尺寸定义为最小值和最大值之间的范围。它允许定义列和行的动态大小。

我们可以使用这个属性来定义导航项目的最小和最大宽度。在我们的例子中，我们使用下面的最小最大值定义:

`minmax(60px, auto)`

我们说该列至少应该有 60px 宽，并且应该和最大内容宽度一样宽。详见`[auto](https://www.w3.org/TR/css-grid-1/#valdef-grid-template-columns-auto)` [关键词](https://www.w3.org/TR/css-grid-1/#valdef-grid-template-columns-auto)。

# 自动适应

`auto-fit`应被用作重复数——在`[repeat()](https://www.w3.org/TR/css-grid-1/#funcdef-repeat)` [功能](https://www.w3.org/TR/css-grid-1/#funcdef-repeat)中使用的数字。它说网格应该放置尽可能多的项目，就像项目为空时一样(我认为🤔).

# 网格-自动流动

`grid-auto-flow`是控制放置项目的网格算法如何工作的属性。在我们的例子中，我们使用`[dense](https://www.w3.org/TR/css-grid-1/#valdef-grid-auto-flow-dense)` [关键字](https://www.w3.org/TR/css-grid-1/#valdef-grid-auto-flow-dense)。它说，网格应该填补更大的网格项目出现时可能留下的漏洞。

# 调整内容

`[justify-content](https://www.w3.org/TR/css-align-3/#propdef-justify-content)`属性[对齐框内内容。我们使用`justify-content: center`将项目内容居中对齐。](https://www.w3.org/TR/css-align-3/#propdef-justify-content)

如您所见，我们没有使用媒体查询。媒体查询是有用的，没有它们，就不会有一个响应式的网站设计，但是当我们能够不用媒体查询就能构建出响应式的行为时，这种感觉是如此令人满意。

# 最后的想法

CSS 网格可能仍然不是导航元素的最佳方法，但它是有效的。总是在适当的地方尝试使用 CSS Grid，即使它解决了你的问题。如果你是一个叛逆者，忽略这种想法并使用它——只要你的用户满意，构建 web 解决方案就没有规则。😎

*最初发布于*[*www . silvestarbitrovic . from . HR*](https://www.silvestarbistrovic.from.hr/articles/using-css-grid-where-appropriate-revisited/)*。*