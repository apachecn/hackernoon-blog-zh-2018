# 反应中的思考第一部分:对 JSX 的爱

> 原文：<https://medium.com/hackernoon/thinking-in-react-part-1-some-love-for-jsx-5e15a9f599b2>

每个框架都使解决某些问题变得容易，而其他问题则不在它的范围之内。React 也不例外。当我们理解了 React 旨在解决的问题时，它的效果最好。这意味着弄清楚框架的边界和限制，并以这样一种方式设计我们的解决方案，它导致最小的认知失调。在这个系列中，我将解释我如何解释 React 框架的特性，并试图让 React 对每个人都很直观。这样，本系列将不会讨论 React 相对于其他前端框架的优缺点。

第一个让开发人员(尤其是来自其他框架的开发人员)却步的是 JSX。现在，JSX 或 [JavaScript](https://hackernoon.com/tagged/javascript) + XML 是 React 定义 UI 元素的首选方式。将 [HTML](https://hackernoon.com/tagged/html) 和 JavaScript 混合在一起给开发者敲响了警钟。他们虔诚地实践关注点分离，相信表示和渲染逻辑应该分开。

有许多经验丰富的开发者并不相信 JSX。对他们来说，我认为 JSX 是建立在 JavaScript 之上的领域特定语言(DSL)。JSX 不是将 HTML 放在 JavaScript 中，也不是违反关注点分离。这是一个抽象概念，使用户界面更容易被概念化为由几个独立的、共存的、协同工作的部分组成。JSX DSL 的目标是将用户界面分解成可重用的小块，这些小块可以用来组合大型复杂的交互。最后，我想说的是，JSX 让我们更容易谈论现代的反应式用户界面。

让我们一劳永逸地解决这场争论。React 中的每个语句都计算为 JavaScript。类似 XML 的语法只是将文件转换成 JavaScript 函数调用。我们编写的每一点 React 代码都被添加到 body 元素内的一个空 HTML 标记的尾部。因此，整个 React DOM 树存在于一个最初为空的容器中。可以把这个过程看作是用 document.createElement 函数创建子节点，并用 appendChild 函数将它们附加到父节点的一种更优雅的方式。

有两类 JSX 元素——本地 HTML 标签和用户创建的 React 组件。原生 HTML 标签以小写字母命名，而 React 组件的名称以大写字母开头。这种区分是为了方便 React 对原生 HTML 标签的内部处理。React 维护一个包含所有有效 HTML 标签的白名单，如果 JSX 元素的名字是小写的，它会检查该元素是否是本地 HTML 标签。否则，它会将 JSX 视为反应组件。

React 组件是 React.createElement 函数的语法糖。该函数接受一个强制参数，即要创建的 React 元素的类型 HTML 标记名或 React 组件类型。此外，它可以有两个可选参数 props 对象和子组件。这应该消除了开发人员心中的疑虑，即我们正在合并 HTML 和 JavaScript。我们只是编写 JavaScript 代码来创建 HTML 节点，并将它们附加到适当的位置。

为了证明 JSX 是前端开发者的福分，这里有一个练习。用三种不同的方法创建一个三层深度 HTML 结构:

1.  普通 JavaScript —使用 createElement、createTextNode 和 appendChild 方法
2.  没有 JSX 的 React 使用重复调用 React.createElement 方法
3.  与 JSX 反应

要开发第一个和第二个解决方案，您需要首先创建一个最终 HTML 的外观模型。然后，您需要将每个元素映射到适当的 JavaScript 函数调用。这包括在头脑中同时保持两个心智模型。我不需要告诉你大脑在两个任务之间不断切换的代价有多大。因此，从纯效率的角度来看，开始使用 JSX 是有意义的。

JSX 是一个很好的视觉教具。有了 JSX，我们可以准确地告诉 React 我们希望我们的内容是什么样子。声明性语法隐藏了无谓的复杂性。没有必要纠结于不必要的细节，这些细节会让开发人员偏离他们真正的目标。

读完这篇文章后，我希望你会更喜欢 JSX。然而，万一我的尝试失败了，我会感谢建设性的反馈。在本系列的第二篇文章中，我将讨论无状态功能组件，以及它们如何在构建复杂的用户界面中发挥重要作用。

*原载于*[*blog . adityadixit . me*](http://blog.adityadixit.me/thinking-in-react-part-1-jsx.html)*。*