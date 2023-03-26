# CSS 如何融入 Reactjs？

> 原文：<https://medium.com/hackernoon/how-does-css-fit-into-react-5a4aa4840135>

有许多不同的方式可以让你设计 React 应用程序，我甚至可以说，与传统的 web 应用程序相比，你实际上有更多的*选项来设计 React 应用程序。因此，不再赘述，让我们列出其中一些选项:*

*   *标准 CSS*
*   *内嵌样式*
*   *CSS 模块*
*   *CSS-in-JS*

*现在，让我们仔细看看其中的每一项。*

## ***标准 CSS***

*这是传统的也是最常见的 CSS 编写方式。您创建一个 ***style.css*** 文件，在其中添加您的所有样式，然后导入样式表并通过`className`或`id`属性在您的 JSX 中引用这些样式。*

*请注意，您不必将样式限制在一个样式表中，您也可以将它们分成多个文件，放在它们应该面向的组件旁边。*

*还要注意，你可以继续使用你日常使用的 CSS 库和框架，比如 ***SASS*** ， ***LESS*** ， ***Bootstrap*** ， ***语义 UI*** 等等。同样，React 不会以任何方式限制您。*

## *内嵌样式*

*内嵌样式是指通过`style`属性直接在 HTML 标记中编写 CSS。在 React 中，使用 JSX，看起来像这样:*

*`<p style={{color: "red"}}>Hello World</p>`*

*就设计成熟的应用程序而言，内联样式可能是最受限制的方法，因为它们不能利用所有的 CSS 功能，如 ***伪选择器*** 、 ***动画*** 和 ***媒体查询*** 。*

*在实践中，它们主要用于基本的样式修改，如基于条件的颜色切换，但我也听说过有人甚至公司将此作为他们唯一的样式机制。*

**(请注意，有争议的是，内联样式也可以被认为是 CSS-in-JS 的一部分，因为从技术上讲，你确实是用 React 在 JS 文件内部编写它们。我选择将它们放在自己的类别中的原因是因为 CSS-in-JS 库通常比在 style 属性中编写样式更复杂——相反，它们引入了某种更高级别的抽象，您将在 CSS-in-JS 一节中看到这一点。)**

## *CSS 模块*

*使用 CSS 模块，您可以为每种类型的组件创建一个 CSS 文件，就像您使用*“标准 CSS”*选项一样，但是这里的不同之处在于，CSS 模块的样式是在本地*范围内的，这意味着与*“标准 CSS”*不同，它们**不会泄漏到其他组件**。**

**局部作用域的样式也意味着您可以忘记实现您自己的局部作用域策略或者遵循诸如[***BEM***](http://getbem.com)之类的方法，这些方法需要您手动创建并输入长的`class`或`id`名称。**

*****如何在 React 内使用:*****

**使用`webpack.config`中的*CSS-loader，将`modules`属性设置为`true`，或者下载一个完整的库，如 ***css-modules*** ，安装后使用如下:***

*   **为您想要设置样式的任何组件创建一个 CSS 文件**
*   **编写您的 CSS**
*   **将这个 CSS 文件导入目标组件内部**
*   **通过组件的`className`和`id`属性在组件中引用这些样式**

*****CSS 模块是如何在幕后实现的？*****

**CSS 模块的工作方式是获取你的 CSS 文件并通过 ***CSS 模块编译器*** 运行它们，它做两件事:**

*   **修改 CSS 文件中的`class`和/或`id`名称，使它们唯一**
*   **生成一个 JS 文件，其中包含新生成的名称和旧名称之间的映射**

**因此，您可以对 CSS 模块使用命名导入——您实际上不是导入您的 CSS，而是导入由 CSS 模块编译器生成的 JS。**

## **CSS-in-JS**

**顾名思义，CSS-in-JS 是在 JavaScript 文件中编写 CSS，而不是在 CSS 文件中编写，但是，如何编写和应用它们很大程度上取决于您使用的特定 CSS-in-JS 库。**

**在后台，CSS-in-JS 通常以以下三种方式之一实现*(或组合)*:**

*   **通过内嵌样式**
*   **通过`<style>`元件**
*   **在构建时通过`<link>`元素将样式提取到 CSS 文件中**

**为了不使文章过长，我们将只看一个 CSS-in-JS 实现，即***styled-components***，这也是最流行的实现之一。**

**然而，在我们分析其工作原理之前，让我们先来看一个例子:**

```
**import React, { Component } from "react";
import styled from "styled-components";const Wrapper = styled.div`
  max-width: 600px;
  margin: 0 auto;
`;class HelloWorld extends Component {
  render() {
    return (
      <Wrapper>
        <p>Hello World</p>
      </Wrapper>
    );
  }
}**
```

**现在让我们来分解这个例子。在顶部，我们声明了一个名为`Wrapper`的变量，并要求***styled-components***创建并返回给我们一个`div` **组件**，它由我们在反斜线中定义的样式组成——在本例中，我们要求它提供一个具有以下样式的`div`组件`max-width: 600px; margin: 0 auto;`。**

**然后我们将这个`Wrapper`组件添加到我们的`render`方法中，并像普通的`div`一样使用它。**

**简而言之，这里所发生的就是你告诉样式化组件你想要什么样的内置组件，以及它应该如何被样式化和样式化，组件把那个组件还给你。**

**样式是如何在幕后应用的？**

**样式化组件依赖于`<style>`元素，所以在内部你可以把它想象成创建并附加`<style> *your styles* </style>`到文档头部分。此外，由于它提供了局部风格的作用域，它负责生成和应用唯一的类名。**

**为了更好地形象化，让我们运行上面的代码片段，看看它被添加到 DOM 后会产生什么:**

```
**...
<head>
  ...
  <style>
    /* sc-component-id: sc-bdVaJa */
    .sc-bdVaJa {}
    .brPdfu {
      max-width: 600px;
      margin: 0 auto;
    }
  </style>
</head><body>
  <div id="app">
    <div class="sc-bdVaJa brPdfu">
      <p>Hello World</p>
    </div>
  </div>
</body>
...**
```

**如你所见，它看起来确实和我们预期的一样。唯一令人惊讶的是额外的空样式声明`.sc-bdVaJa {}`，基于它上面的注释`/* sc-component-id: sc-bdVaJa */`，它似乎暗示样式组件跟踪样式属于哪个组件——大概是为了某种内部性能优化或类似的事情。**

**如果你喜欢你所看到的，你可能还想签出另一个非常相似的 CSS-in-JS 库，名为 ***Emotion*** ，它与***styled-components***非常相似，但是在构建时，你可以选择将你所有的样式提取到一个 CSS 文件中，然后通过`<link>`元素链接到文档中。这为什么有用？因为生成的样式表可以被浏览器缓存。**

**关于最流行的 CSS-in-JS 解决方案的完整列表，请参见这个 [github](https://github.com/MicheleBertoli/css-in-js) 资源库。**

## **终点线**

**恭喜你读到最后:)，感谢阅读，希望你学到有用的东西。推特: [linasmnew](https://twitter.com/linasmnew) 。**

**![](img/869c5d452dd1449429e9522e15835546.png)**