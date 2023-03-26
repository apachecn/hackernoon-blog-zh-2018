# 帮助您入门的第一批 HTML 元素

> 原文：<https://medium.com/hackernoon/html-structural-elements-10f3cc9127fa>

## 指南第 1 部分:HTML 结构元素

![](img/fe6146fdccbcccfab23bedf9a374f8fb.png)

Photo by [Rick Mason](https://unsplash.com/@egnaro?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

最近几天，我一直在阅读一些关于 HTML5 中创建的元素的有趣和不同的观点。有许多文章支持并解释了清晰语义结构的好处。不过我也发现了一些对*阴暗面的看法。*探讨问题的文本，规格文档中缺乏清晰度，以及它给开发人员带来的困惑。有些偏激的意见甚至说不要用。嗯，也许最常见的情况是，当生活呈现给你一个想法的两面时，你会爱上其中的一面。与你的思维方式有更多相似之处的人，或者你认为*能更好地合理化他们的论点的人*。但是如果你是一个新手，就像我在谈论 HTML 实践时一样，最好确保你理解两种观点。

HTML5 新元素有一些问题，在使用上有些不清晰:何时使用，如何使用，为什么使用。文档不会让整个世界变得明亮整洁。但是我会站在最边缘，说你根本不需要使用它们。我说过，我不是专家。我真的真的离那很远。如果你允许我给你一个建议，那就是:去了解他们。明确确保您在使用或不使用可用工具方面做出正确决策的唯一方法是尝试一下。

阅读它为什么被制造出来，付诸实践，用一种方法尝试，再用另一种方法尝试。看实际用什么回复你。它是否改善了你在搜索引擎上的网页行为？它是否提高了可访问性，给了屏幕阅读器更多的洞察力？你的团队可以用清晰的语义更快地生产吗？如果你不跟踪所有这些和其他答案，从你的网页数据反馈给你，这是不可能有一个在这个主题的立场。对于像我这样的初学者，尽可能多的阅读和练习。了解文档是开始忽略它的第一步。如果你选择这么做。

说得够多了，让我们来了解一下哪些是可以用来构建 HTML 页面的结构元素。这些产品的规格说明以及我在路上发现的一些额外选项。

# <[html](https://www.w3.org/TR/html53/semantics.html#the-html-element)

`<html>`被称为*根元素*，因为它是所有其他元素的外部容器。如果`<!DOCTYPE>`出现异常。它不是 HTML5 版本中创建的元素。它可能与指定文档主要语言的`[lang](https://www.w3.org/TR/html53/dom.html#element-attrdef-global-lang)` 属性一起使用。

`<html lang="en">`

# <[头](https://www.w3.org/TR/html53/document-metadata.html#the-head-element)头>

`<head>`元素提供了关于文档【元数据】的信息，它对于每个 HTML 文件都是唯一的。它是`<html>`标签中的第一个元素。在`<head>`中，我们通常可以找到一个或多个缩进的元数据内容，唯一需要的是必须只使用一次的`<title>`元素。

**父:** `<html>` **内容:** `<title>`【必选】`<link>``<meta>``<style>``<script>``<noscript>``<base>``<object>``<template>`
**版本:***HTML 2/3.2/4/4.01/5*

# <[正文](https://www.w3.org/TR/html53/sections.html#the-body-element)正文>

`<body>`代表文档的内容，即屏幕上将显示的所有内容。作为`<head>`标签，它应该只使用一次。它是`<html>`标签中的第二个缩进元素。

**父:** `<html>`
**内容:**块、内联和文本元素
**版本:***HTML 2/3.2/4/4.01/5*

# <[标题](https://www.w3.org/TR/html53/sections.html#the-header-element)标题>

`<header>`标签可以用在文档的标题中[缩进为`<body>`中的第一个元素]，或者用在 HTML 文档的任何其他部分。这是事情变得有点混乱的时候。`<header>`的定义是引入其元素最近祖先的内容。但是在里面使用时，`<body>`代表整个文档的介绍性内容。

**警惕！在我们文档的许多地方使用`<header>`时要小心。还有其他标签，如`<h2>`和`<h3>`，它们可以在不需要缩进标签的情况下给一个部分加标题。所以，想一想你需要什么，什么能让你的代码对其他人阅读语义清晰。**

**放置:**块
**内容:**块、内联、文本元素
**除了* `*<footer>*` *和本身。* **版本:***html 5 中新增*

# `<[footer](https://www.w3.org/TR/html53/sections.html#the-footer-element)>`

`<footer>`与`<header>`具有相同的多功能性。它可以用作整个文档的页脚，包含关于文档作者、版权数据或相关文档链接的信息。然而，它可以用在任何 section 标记中，表示其最近祖先的页脚信息。

**警惕！**对`<header>`暗示的相同注意对`<footer>`有效。不要过度使用它，回顾我们的 HTML 文档中的语义，使它简单明了。这不仅仅是为了其他将要使用相同文档的人，而是问问你自己:*“如果我在未来几年遇到它，我能理解它吗？”*

**放置:**块
**内容:**块、内联、文本元素
**除了* `*<header>*` *和本身。* **版本:***html 5 中新增*

# <[章节](https://www.w3.org/TR/html53/sections.html#elementdef-section)章节>

它是文档或应用程序的通用部分。你应该记住,`<section>`标签的作用是对一些特定的内容进行分组。这对于`<header>`和`<footer>`来说是一样的，但是语义理解更少。所以，如果你需要聚合一些内容，而你没有找到一个特定的语义元素来表示它，那么`<section>`可能是一个不错的选择。

**放置:**块
**内容:**块、内联和文本元素 **版本:***html 5 中的新内容*

# <[条](https://www.w3.org/TR/html53/sections.html#the-article-element)条>

`<article>`表示内容的一部分，它本身是 HTML 文档的一个独立部分。它可以是一篇文章、一篇文章或另一个独立的单元，代表可能链接到或包含在其他内容主体中的一些信息。

**放置:**块
**内容:**块、内联和文本元素 **版本:***html 5 中新增*

# <[抛开](https://www.w3.org/TR/html53/sections.html#the-aside-element)不谈>

元素用于表示与页面内容不完全相关的内容，是页面内容的一部分。它经常被用于侧边栏、呼叫框和广告块。主要思想是明确标记内的内容与页面内容是分开的。

**放置:**块
**内容:**块、内联和文本元素 **版本:***html 5 中新增*

# <[导航](https://www.w3.org/TR/html53/sections.html#the-nav-element)

`<nav>`表示导航链接的一部分，它可能在当前文档或其他文档中。当宣布与主要内容或相关内容相关的导航链接时，元素的语义非常有用。

**警惕！**即使没有规定说不能使用多个`<nav>`标签，也不一定每次在某个部分包含导航链接时都要使用这个元素。

**放置:**块
**内容:**块、内联和文本元素 **版本:***html 5 中的新内容*

# <[div](https://www.w3.org/TR/html53/grouping-content.html#the-div-element)

`<div>`是*分部的简称。*元素代表一个通用容器。当您需要定义文档的结构部分时，例如，使用 CSS 将不同的布局应用于该部分时，会用到它。因为除了容器本身，标签在语义上不代表任何东西。当没有其他更好的语义元素来表示内容块时，应该使用它。

**放置:**块
**内容:**块、内联和文本元素 **版本:***HTML 2/3.2/4/4.01/5*

# <[主](https://www.w3.org/TR/html53/grouping-content.html#the-main-element)主>

`<main>`代表主要内容，在文档中只能使用一次。在一组文档中重复的所有内容，比如侧栏、导航链接、版权信息、网站徽标和搜索表单，都不应该包含在`<main>`标签中。

**父:** `<html>`，`<body>`，`<div>`
**内容:**块，内联，和文本元素**，
版本:***html 5 中新增*

# < [h1 >到< h6](https://www.w3.org/TR/html53/sections.html#the-h1-h2-h3-h4-h5-and-h6-elements) 到>

标题标签有六个级别，从`<h1>`【最重要】到`<h6>`【最不重要】。它们应该用于排列标题元素以创建文档轮廓，而不是用于设计文档样式。妮可·沙利文给出了一个使用标题标签的好建议，就像你为高中写文章一样:

```
THE TITLE IS THE H1
I. Big roman numerals are the H2s
   A. This is an h3
   B. This is also and h3
      i. Now we have an h4
      ii. And another h4
II. Big roman numerals are the H2s
III. Big roman numerals are the H2s
IV. Big roman numerals are the H2s
```

**警报！**这些都是导航用的最多的元素说到可访问性，可以查看 2017 年 10 月 的 [*WebAIM 报告。所以，不要在你的文档中填充太多的`<h1>`或者使用较低的级别来减小标题字体大小【CSS 属性*](https://webaim.org/projects/screenreadersurvey7/#finding)*`[font-size](https://developer.mozilla.org/en-US/docs/Web/CSS/font-size)`可以做得更好】。*

**放置:**块
**内容:**行内和文本元素 **版本:***HTML 2/3.2/4/4.01/5*

# <[p](https://www.w3.org/TR/html53/grouping-content.html#the-p-element)

标签`<p>`用于定义一个段落，作为一个内容块。当一个更具体的元素，比如一个列表，不是更合适的时候，不应该使用它。

**警惕！** `<p>`不能包含块状元素，如同`<p>`本身。记住空段落会被浏览器忽略，不要使用空的`<p>`来创建空行。

**放置:**块
**内容:**行内和文本元素 **版本:***HTML 2/3.2/4/4.01/5*

# <[跨度](https://www.w3.org/TR/html53/textlevel-semantics.html#the-span-element)跨度>

`<span>`是一个内联元素，它本身没有任何意义。定义文本的某个特定部分需要在 CSS 中使用不同的样式，或者共享属性值，比如`lang`，这很有用。它在可用性上与`<div>`元素非常相似，但与`<div>`不同的是，它是一个内联元素。

**放置:**内联
**内容:**内联和文本元素 **版本:***HTML 2/3.2/4/4.01/5*

![](img/863c2e0413d8182a570d50ca32b914b9.png)

Photo by [Daniel Cheung](https://unsplash.com/@danielkcheung?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

我希望我在某些方面帮助了你。如果你想了解更多关于这个话题的内容，这里有一个我用来摘录这个内容的相关网站列表:

 [## HTML 5.3 规范

### 这个规范定义了第五个主要版本，这是万维网核心语言的第三个次要修订版本

www.w3.org](https://www.w3.org/TR/html53/) [](https://www.w3schools.com/html/html_elements.asp) [## HTML 元素

### 组织良好，易于理解的网站建设教程，有很多如何使用 HTML，CSS，JavaScript 的例子…

www.w3schools.com](https://www.w3schools.com/html/html_elements.asp) [](https://developer.mozilla.org/en-US/docs/Web/HTML/Element) [## HTML 元素参考

### 这个页面列出了所有使用标签创建的 HTML 元素。

developer.mozilla.org](https://developer.mozilla.org/en-US/docs/Web/HTML/Element) [](https://www.tutorialrepublic.com/html-tutorial/html-elements.php) [## HTML 标签与元素-教程共和国

### HTML 元素代表 HTML 文档的一个单独的组件——例如，title 元素代表…

www.tutorialrepublic.com](https://www.tutorialrepublic.com/html-tutorial/html-elements.php) 

我是苏，学技术的女孩。如果你想知道更多关于我的事情，以及为什么我一直在媒体上写作，请阅读这里的。

## 对于我写的与这个主题相关的一些其他内容:

[*html 5 的基本结构*](/p/7258abb2789f?source=your_stories_page---------------------------)[*前端、后端、全栈，有什么区别？*](/p/fbd5d3fd4939?source=your_stories_page---------------------------)[这是我的故事。一个带着梦想和恐惧的梗妹。](/@girllearningtech/a-stem-girl-with-dreams-and-fear-1a3c783f3b34)