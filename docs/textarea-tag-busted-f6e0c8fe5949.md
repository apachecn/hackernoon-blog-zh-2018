# Textarea 标签已损坏

> 原文：<https://medium.com/hackernoon/textarea-tag-busted-f6e0c8fe5949>

textarea 是 HTML 标记，主要用于消息和评论。它与 inputs 非常相似，但是因为它提供了一个拥有多行文本的选项，所以它不作为 input [type="textarea"]呈现。

在这篇文章中，这个标签的所有选项将会和经常被问到的问题一起出现，我将会试着解释和文本区域相关的一切。

请记住，此文本位于第二行，在[输入[type="text"]](https://kolosek.com/input-text/) 之后。

# 关于 Textarea

默认的 textarea 标签在右下角带有边框和图形。就像在每个输入字段中一样，添加一个占位符是一个很好的做法。

```
<textarea> </textarea>
```

![](img/6a075def990280b66c98c41a257ea2a2.png)

textarea 的默认大小是用它里面的文本的`font-size`定义的。因此，如果没有不同的定义，文本区域的高度由 2 行文本定义，宽度由 20 个文本字符定义。

# 属性

textarea 元素的一些最有用的选项可以用它的属性来定义。这里有一个最常见的列表:

*   **自动对焦**，
*   **只读**，
*   **必填**，
*   **禁用，**

*所有这 4 个属性都是不言自明的:* `*autofocus*` *使 textarea 在页面加载时聚焦，* `*readonly*` *，* `*required*` *和* `*disabled*` *属性只是将那个状态添加到 textarea 中。*

*   **占位符，**
*   **maxlength，**
*   **列，**
*   **行，**
*   **裹。**

*这 5 个属性用于更接近地样式化和定义 textarea。*

使用`placeholder`属性，您可以像在所有输入字段中一样添加一个占位符。

`maxlenght`指定了允许的最大字符数。`cols`根据字符的平均宽度定义文本区的“宽度”(默认值为 20)，而`rows`根据文本行数定义文本区的高度(默认值为 2)。

属性定义了提交文本的换行符。这些属性的值为**软**(默认)、**关**和**硬**。

通过选择关闭换行**的**，文本区域中的文本不会被打断，直到你按下回车键。

选择**硬**，提交的文本包含换行符，选择**软**则不包含换行符。为了更好地理解这一属性，请查看[html.com 邮报](https://html.com/attributes/textarea-wrap/)。

```
<textarea autofocus required placeholder="type your message" maxlength="180" cols="50" rows="6" wrap="hard"> </textarea>
```

![](img/01bc8e282a19794781638d820b2f37aa.png)

为了进一步的样式化，你应该使用 CSS，只是要记住选项`white-space:nowrap;`不起作用。

# 调整大小问题

调整文本区域的大小有一些问题。要定义 textarea 的高度和宽度，可以使用 cols 和 rows 属性，或者 CSS 标准样式(更好)。

下一个问题包括移除调整大小手柄。因此，你创建了一个非常好的设计，每个人都可以选择在上面拉伸 textarea。不要啊。这个非常容易修理:

```
textarea{
    resize:none;
}
```

此外，关于我们讨论的这个标签，一个很小但很烦人的事情是所有 IE 版本中文本区的默认滚动条。幸运的是，解决方案只有几行代码。

```
textarea{
    overflow:auto;
}
```

最常见的与调整大小相关的问题是自动调整大小。不幸的是，textarea autoresize 仍然没有选项，但是这里有一个你可以使用的技巧。

# 重置设计

textarea 的特定样式选项与输入的样式选项相同。因此，您可以设置边框、背景、文本、占位符文本和状态的样式。我发现了几个非标准 textarea 的例子，这很困难，所以这证明了 textarea 是一个可以成为标准元素的元素。

![](img/290a51e7c1306420821d2c35d84a8b03.png)

# 封闭线

textarea 标签是常用的，它的选项非常简单。一旦你读完这篇课文，我相信你会掌握它的！

**感谢您的阅读！**

*原载于 2018 年 7 月 17 日*[*kolosek.com*](https://kolosek.com/textarea-tag-busted/?utm_source=me)*。*