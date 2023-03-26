# 新 React 上下文的详细说明

> 原文：<https://medium.com/hackernoon/detailed-explanation-of-the-new-react-context-7d3c71185163>

由 [artalar](https://github.com/artalar)

![](img/b325eaa56fdd02c9a0cb22fa64d7c229.png)

# 什么是语境

除了只能由直接子组件访问的`props`之外，React 组件还可以定义一个上下文，所有嵌套的子组件都可以访问该上下文。这意味着无论组件嵌套有多深，它都可以访问由树中任意数量的节点创建的上下文。Context API 是所有流行库的基础，这些库需要能够从组件树的每一层访问全局值:react-redux、react-mobx、react-router、styled-components(theme provider)。

# 旧环境的问题

在旧版本中，可以接收数据，但无法订阅其更新。确切地说，更新——对于 props 也是如此——发生在父组件重新渲染时。但是由于数据是通过大量(实际上是任意数量)的组件从上下文源传输到上下文消费者的，并且这些组件可以通过它们自己的`shouldComponentUpdate`停止更新，所以这种订阅不能被称为可靠的。因此，在设计库时，应该为消费组件更新数据，开发人员必须手动创建更新机制——实际上这不是一件容易的工作。阅读文章中的[或观看 MobX creator 的演示文稿](/@mweststrate/how-to-safely-use-react-context-b7e343eff076)。

# 新 API `React.createContext`

一些开发人员认为新的上下文(`[React.createContext](https://reactjs.org/docs/context.html)`)是对`redux`(或任何其他状态容器)的替代，但这是在拿苹果和橘子做比较。新环境的主要目标是促进消费者的有效更新(更多细节由 Dan Abramov 提供)，以便开发人员可以专注于他们库的主要特性。新的上下文还拥有一个改进的、更直观的界面。

请注意，`redux`具有管理状态的最小功能:订阅和更新。这些可以用常规`React.Component`中的`state`(和`setState`)代替。更准确地说，在某些应用程序中，可以使用新的上下文来代替`redux`，这意味着可以使用`React.Component`状态和更新，并且可以用 React.createContext 替换`react-redux`。没有用上下文“开箱即用”替换`redux middleware`，但是有[第三方库](https://github.com/didierfranc/react-waterfall#redux-devtools)。

## 渲染道具

如您所见，新的`React.createContext` API 使用 render-prop 方法与其他订阅的组件共享状态或行为。虽然在[官方文档](https://reactjs.org/docs/render-props.html)中概述了实施细节和示例代码，但我想强调一下这种方法的优点和缺点:

*   ( **+** )在多个订阅的情况下防止命名冲突(`Consumer`)。因为经典的 HOC 组合了`props`，如果我们在一行中有多个 HOC，并且任何传递的属性具有相同的名称，则值将被覆盖，并且到达组件的最后的`props`对象将具有最后一个 HOC 的值。使用 render-prop 解决了这个问题，因为 prop 没有组合在一起，订阅的每个传递参数都是在传递的函数中单独访问的。
*   (–)“сall back hell”和新函数的生成，或者将呈现部分重新定义为单独的方法(这会干扰设计模式的一致性)。更多细节在[官方文档](https://reactjs.org/docs/render-props.html#be-careful-when-using-render-props-with-reactpurecomponent)中。

如果你不喜欢渲染道具方法，而宁愿使用“古老的”HOCs，这里有一个简单的例子，说明如何通过记忆化来实现它:

# 不稳定 _ 观察位

> *信息见于* [*源代码*](https://github.com/facebook/react/blob/4ccf58a94dce323718540b8185a32070ded6094b/packages/react/src/ReactContext.js#L18)*[*测试*](https://github.com/facebook/react/blob/4ccf58a94dce323718540b8185a32070ded6094b/packages/react-reconciler/src/__tests__/ReactNewContext-test.internal.js#L498-L526) *中的 React，以及* [*这篇文章*](/@koba04/a-secret-parts-of-react-new-context-api-e9506a4578aa)*

*虽然还没有公开宣布或添加到官方文档中(最有可能的是，[不会是](https://github.com/facebook/react/issues/12732#issuecomment-395979669))，除了前面提到的功能，`React.createContext`还有第二个接受函数的参数。组件也接受一个位掩码作为 T2 属性。该功能类似于`React.Component`中的`shouldComponentUpdate`。让我们仔细看看。*

## *位掩码*

*[位掩码](https://en.wikipedia.org/wiki/Mask_(computing))是一项古老的技术，特别是在 Linux 权限设置中使用。位屏蔽的概念涉及每个位，以特定的顺序，为特定设置设置`true`或`false`指示器。位掩码的优点是，要更新值，只需使用规则掩码对原始位掩码执行一次逐位运算。为了将目标位的值设置为`true`，使用“或”— `|` —位运算符，目标位=== `1`，掩码中的其他位设置为`0`。为了将值设置为`false`，使用“与”— `&` —位运算符，目标位=== `0`，掩码中的其他位设置为`1`。这在开始时可能会令人困惑，但在实践中，这是一种简单、直观、快速的存储和修改`true` / `false`值的方法。*

## *使用*

*以下描述和示例中的位掩码用于跟踪状态的变化。位屏蔽中的每一位应该对应于状态中的一个值。*

*React.createContext 的第二个参数接受一个接受以前和新状态的函数，并返回更新后的位掩码。反过来，消费者接受一个位掩码，它包含负责我们想要观察的状态值的“真”值的位，作为 unstable_observedBits。当消费者收到位掩码时，它[将新的位掩码与不稳定的观察位进行比较，并对它们执行按位“与”运算。仅当操作结果不为 0 时，才会重新呈现使用者。如果未提供 React.createContext 的第二个参数和 unstable_observedBits 使用者参数，则只要上下文发生变化，就会调用 render-prop。](https://github.com/facebook/react/blob/4ccf58a94dce323718540b8185a32070ded6094b/packages/react-reconciler/src/ReactFiberBeginWork.js#L988)*

## *例子*

*正如参数名所示，这个 API 不稳定，不应该在生产中使用。*

# *创建-订阅*

*React [源代码](https://github.com/facebook/react/tree/master/packages/create-subscription)中添加的另一个实用程序是`[create-subscription](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#adding-event-listeners-or-subscriptions)`。以前，为了管理订阅、响应属性更改和重新呈现组件，开发人员必须使用`React.Component`创建一个包装器，这会触发`setState`或`forceUpdate`。为了简化订阅，开发人员现在可以使用来自官方 React 存储库中`create-subscription`包的更透明的`createSubscription` API。*

## *摘要*

*React 16.3 带来了许多有趣的变化，毫无疑问，将提高 React 框架的使用质量，并简化 React 支持库的开发。*

*在这个交互式演示中可以看到新环境的使用:*

*在这个交互式演示中，可以看到上述所有功能:*