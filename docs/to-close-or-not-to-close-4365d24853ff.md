# 关还是不关

> 原文：<https://medium.com/hackernoon/to-close-or-not-to-close-4365d24853ff>

你有没有想过像`<br />`一样“关闭”一个`br`或`input`标签更好，或者在 HTML5 中只写`<br>`更好？或者为什么写`<script src="script.js" />`不正确？我也是，我在这个问题上的发现比我预期的要有趣得多(如果出于某种奇怪的原因，你觉得这类东西有趣的话)。

如果你对整个故事不感兴趣，直接跳到有效性部分就能得到你的答案。

![](img/8463d22f6615f5f73260d3ee89c97b25.png)

Random Photo © Matias Meno

# 空元素

Void 元素是一种特殊的元素，它不能包含内容。这与其他元素有很大的不同，即*可以为空，但也可以包含其他元素和文本(如`<div>` s)。*

最常见的空隙元素有:

`<br>``<hr>``<img>``<input>``<link>`

鲜为人知的有:

`<area>``<base>``<col>``<command>``<embed>``<keygen>``<param>``<source>``<track>`

就是这样。这些是所有现存的空元素。

编写`<br></br>`不是也从来不是有效的 HTML，因为这意味着`br`元素接受内容(编写`<br>Hello!</br>`完全没有意义)。但是同时看到`<br>`和`<br />`是很常见的。

尽管大多数人都知道在 HTML 中, **X** 是强制的,来编写,`<br />`HTML 的规则并不明显。

# 历史

为了完全理解无效元素的规则，有必要了解一些历史。

[HTML](http://en.wikipedia.org/wiki/HTML) 、 [XML](http://en.wikipedia.org/wiki/XML) 和 [X(HT)ML](http://en.wikipedia.org/wiki/XHTML) 都是基于 [**SGML**](http://en.wikipedia.org/wiki/Standard_Generalized_Markup_Language) 、标准的通用[标记](https://hackernoon.com/tagged/markup)语言，这种语言在 1986 年就已经制作完成。

HTML 和 XML 直接来源于 SGML。XML 是 SGML 的一个限制性更强的子集，这也是 XHTML 的基础。

> XHTML 基本上和 HTML 一样，但是基于 XML。

到目前为止还好吗？然后让我们进入有趣的部分:

SGML 有一个特性叫做 [NET(空结束标签)](http://en.wikipedia.org/wiki/Standard_Generalized_Markup_Language#NET)。这是一个简短的符号，当元素的内容是简单的文本时，它可以避免关闭标签。用*网*可以写`<quote/Quoted text/`而不是`<quote>Quoted text</quote>`。

顺便提一下，不包含任何文本的元素可以写成`<quote//`，称为[short tag NETENABL immed net](http://en.wikipedia.org/wiki/Standard_Generalized_Markup_Language#Other_features)，与`<quote></quote>`相同。

现在，按照这种逻辑，如果一个 void 元素的*没有*的结束标签，`<br/`将被解释为`<br>`，而`<br/>`将被解释为`<br>>`，这显然是不正确的语法。如果你像我一样，你可能会认为这是疯狂的！。不幸的是，HTML4 规范的作者不这么认为，这就是为什么这是规范的一部分。显然，当时的浏览器供应商也没有被说服，这导致了非常差的浏览器支持(在这种情况下，可以说不是一件坏事)。

XML(以及 XHTML)认识到了这种语法的疯狂，并没有包括 *NET* 或 SHORTTAG NETENABL IMMEDNET 特性，而是为 void 元素提供了一种合理的语法，即类似于`<br />`的[空元素标签](http://www.w3.org/TR/xml/#sec-starttags)。这看起来很自然，这也是为什么大多数开发者认为这是正确的编写方式。

幸运的是，HTML 在发展，万维网联盟的人(他们正在起草和制定整个网络的标准)也在从他们过去的错误中学习。这就是为什么 HTML5 让*更有意义。*

就在新 HTML5 语法的介绍中，W3C 说:

> HTML 5 定义了一种 HTML 语法，它与发布在 Web 上的 HTML 4 和 XHTML 1 文档兼容，但与 HTML 4 更深奥的 SGML 特性不兼容，比如 NET 语法(即

*Yay for HTML5!*

*(I think they should have kept the cool *SHORTTAG* 特性(`<strong>Hell yea</>`)但是，嘿…至少 HTML 不再是一团乱麻了)*

# *有效期*

*所以回到有效性的问题上来，void 元素的当前 [HTML5 规范如下:](http://www.w3.org/TR/html-markup/syntax.html#void-element)*

*开始标记由以下部分组成，完全按照以下顺序排列:*

*   *一个`<`人物。*
*   *元素的标记名。*
*   *(可选)一个或多个属性，每个属性前面必须有一个或多个空格字符。*
*   *或者，一个或多个空格字符。*
*   *可选地，一个`/`字符，只有当元素是空元素时才出现。*
*   *一个`>`人物。*

*这意味着`/`字符在 HTML5 中已经被渲染为*可选*，但并没有增加任何意义。`<br>`和`<br />`之间绝对没有的区别。*

# *正确性*

*好吧，对于那些真正沉迷于 X(HT)ML 的人来说，你可能会想，是的，这是可选的，但`<br />`仍然是‘更正确的’，但我必须告诉你:它不是。实际上，有人可能会认为将`/`添加到 void 标签是一个被*忽略的语法错误*。编写它的可能性主要是出于兼容性的原因，每个浏览器和解析器都不应该对`<br>`和`<br />`有任何不同的处理。*

*在这个问题上，谷歌的风格指南也非常明确，你确实应该*而不是*关闭 void 标签。*

# *理论上的缺点*

*当然，不关闭 void 标签也有它的缺点，但是我认为它们并没有超过拥有像`<meta>`这样干净简洁的 void 标签的优点。*

*不关闭 void 标签的第一个缺点是用户需要**了解现有的 void 标签**。例如，如果您不知道什么是`<img>`元素，如果您找不到它的任何结束标签，您可能会感到困惑。但是 void 标签的列表很短，通常很明显哪些标签是 void 标签。*

*第二个缺点是对于**编辑器和解析器**来说，得到正确的结果变得更加复杂。他们需要知道完整的列表来正确地解释代码。如果你在编辑器中写`<input>`，它*必须知道*后面永远不会有`</input>`。*

*但是这更多的是一个与规范相关的问题，而不是关闭或不关闭 void 标签，因为`<input></input>`是*无效*，所以 void 元素在任何情况下都需要特殊处理。*

# *我对空标签的看法*

*我认为，通过使用一些标签的内容，而不是定义额外的属性，可以完全避免 void 标签的整个概念。让我们以`<img>`标签为例。它有一个*强制的* `alt`属性来添加图像的描述，这是有充分理由的:看不到图像的人(要么因为他们身体上没有能力，要么因为他们的设备不能显示图像)应该有办法知道描述的是什么(如果你添加一个`img`标签仅仅是为了设计的目的，那么你无论如何都做错了)。所以我的问题是:为什么图片标签的*内容*不是备选标签？写`<img src="doge.png">Image of doge</img>`在我看来还是比较明显的。同样的道理也适用于`<meta>`标签，它甚至有一个`content` *属性*！为什么不使用实际的元素内容呢？`<input value="Value content">`应该是`<input>Value content</input>`跟`<textarea>`一样，等等…*

*所以实际上应该只有几个 void 标签存在，但是显然 W3C 必须考虑向后兼容性，这使得这种改变更加困难。*

# *最后的想法:标签*

*script 标签真的让我很困扰，因为对于这样一个简单的指令来说，它是一个如此冗长的标签。编写`<script src="my-script.js"></script>`似乎是错误的，因为这个`script`标签的内容与`my-script.js`没有逻辑关联(并且 [html 规范](http://www.w3.org/html/wg/drafts/html/master/scripting-1.html#the-script-element)不允许添加内容和`src`属性)。*

*问题是，`<script>`是**而不是**一个 void 标签，因为你可以在你的页面上内嵌 JavaScript，并且没有“可选的 void 标签”。*

*使用`<link>`标签是完美的，因为它已经用于其他导入，并提供了包含外部文件所需的所有属性。当然，正如在 web 中经常出现的那样，不使用它的原因是向后兼容性，因为您会排除所有不支持该语法的旧浏览器。*