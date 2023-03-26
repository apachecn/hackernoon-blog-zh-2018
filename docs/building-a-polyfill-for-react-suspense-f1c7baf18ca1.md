# 为 React Suspense 构建聚合填充

> 原文：<https://medium.com/hackernoon/building-a-polyfill-for-react-suspense-f1c7baf18ca1>

![](img/537a97ce6f807ceb2f857b5f24563a2e.png)

I chose this image for no other reason than because it showed up in the [Unsplash](https://unsplash.com/search/photos/suspense) search results for “suspense.” There may or may not be some deeper symbolism discussed later in this article regarding how the polyfill is only halfway faithful much like how this doll has been torn in half, but let’s get back to the subject at hand.

> 理解一个软件概念的最好方法是尝试自己构建它。结核病

如果你喜欢 React，你可能听说过一些关于[即将上映的悬疑 API](https://github.com/sw-yx/fresh-async-react)的事情，但即使在看了一个[演示](https://github.com/acdlite/suspense-ssr-demo)或[两个](https://slides.com/swyx/react-suspense#/)之后，我也很难说出**到底是如何悬疑的。**

因此，我戴上了计算机科学的帽子，决定尝试用 React v16 的当前版本重新创建它。

在我们开始之前，我虚构的法律团队想回避一些免责声明。

React 将发布的悬疑的实际版本比这个聚合填充中的版本更加复杂和高效。本教程&附带模块主要用于学习和实验目的。此外，目前的 polyfill 可能无法很好地与 SSR 配合使用。

[**龙嗝！**](https://en.wikipedia.org/wiki/Here_be_dragons)

> 如果你只关心代码，看看[**react-suspension-poly fill**](https://github.com/transitive-bullshit/react-suspense-polyfill)，否则就来吧！

# 搭建舞台

IMHO，悬念是对核心 React API surface 的一个非常强大的补充，我相信它将对几年后如何编写务实的 React 代码产生深远的影响。

如果您没有从这篇文章中获得任何其他东西，请理解这一点:

> React Suspense 的核心是允许一个异步组件从它的`render`方法抛出一个承诺。

这个 polyfill 通过在 [**超时**](https://github.com/transitive-bullshit/react-suspense-polyfill/blob/master/src/timeout.js) 组件中实现一个[错误边界](https://github.com/transitive-bullshit/react-suspense-polyfill/blob/master/src/timeout.js#L24)来模仿 React 对此行为的内部支持。如果错误边界遇到一个抛出的承诺，它会等待，直到该承诺解决，然后尝试重新呈现其子级。如果承诺需要很长时间来解决，它还处理回退到加载内容。(详细解释如下)

我希望这个模块和附带的演示能让你更容易跟上 React 悬念的发展。😄

# 做出反应。占位符

**占位符**是 React 悬念暴露的主要面向公众的组件。它的界面相对简单，公开了以下属性:

*   `delayMs` -显示回退/加载内容前等待的时间(毫秒)。在显示回退内容之前添加延迟的主要原因是为了防止在主异步内容加载之前加载指示器闪烁过快，这可能会干扰用户界面。
*   `fallback` -一个 React 节点，在 `delayMs`过去后，只有 *有子组件加载*时才会显示。这通常是某种类型的装载旋转器。**
*   `suspense` -在 `delayMs`过去之前 *加载*任何子组件时将显示的 React 节点。注意:这个可选道具是 react-suspension-poly fill 专用的，严格来说是为了演示悬念是如何工作的。**
*   `children` -一个 React 节点，表示这个占位符组件的主要内容，在加载异步资源时可能会也可能不会抛出承诺。参见 [react-async-elements](https://github.com/palmerhq/react-async-elements) 获得一些超级性感的、异步友好的子组件的例子。

**占位符**是你最有可能在代码中使用的组件，但是本着理解它如何工作的精神，大部分复杂性由**超时**处理。

# 做出反应。超时

**超时**组件有点复杂，所以让我们一步一步地分析发生了什么:

1.  `render`方法(第 50 行)将首先调用它的`children`呈现函数，该函数带有一个布尔值，表示自从安装和遇到异步工作负载以来，该组件是否达到了超时`ms`。
2.  如果`children`渲染成功，一切正常，React 继续正常运行。😃
3.  如果`children`子树**中的任何组件从其`render`方法抛出一个承诺**，它将被超时的错误边界`componentDidCatch`捕获(第 24 行)。
4.  错误处理程序首先为这个异步工作启动一个超时(第 28 行)，这样当`ms`超时到期时，超时将返回到显示加载内容。
5.  在这个承诺可能到期之前的`ms`时间内，`Timeout`是**【暂挂】**(第 29 行和第 63 行)，这基本上意味着我们正在等待加载一些资源，但是还没有花费足够长的时间来证明显示回退/加载内容是正确的。
6.  一旦承诺解决(第 43 行)，超时再次调用它的`children`呈现属性(第 39 行),期望这一次，初始异步资源将同步解决**和**,世界将再次变得美好。😃

注意，一个子树完全可能包含多个独立的异步资源，在这种情况下， **Timeout** 组件可能会对每个需要解析的异步资源重复步骤 3-6 一次。或者，**占位符** & **超时**可以像任何 React 组件一样嵌套，因此完全有可能更高级别的**超时**不需要处理在 React 组件树中被抛出的异步请求，如果该请求被更接近其来源的超时捕获。这非常接近 React 错误边界的公共行为。

希望**占位符**和底层**超时**组件现在在预期行为方面更加具体。

99%的时间你将使用一个简单的**占位符**组件，并忽略**超时**中的这些细节，但是我相信拥有这种更深层次的心智模型是非常有益和强大的，因为 React Suspense 支持这种从根本上改变游戏规则的模式。

记住这一点，让我们谈一谈这个基本的心智模型与极具天赋的 React 核心团队正在炮制的官方版本有何不同！

![](img/2de3354dc300a53914b4cb8f092d31a6.png)

Subtrees upon subtrees upon trees. (Image Credit: [Unsplash](https://unsplash.com/))

# 对比官方反应悬念

这个 [polyfill](https://github.com/transitive-bullshit/react-suspense-polyfill) 与即将正式实现的 React suspension 相比，有两个主要的局限性。

## 正确性

好吧，我们可能有一点作弊😉在多填充正确行为方面，我们在实现中忽略了一个重要的细节。

你能猜出它是什么吗？

如果你不确定，那也没关系。在我意识到[丹·阿布拉莫夫](https://medium.com/u/a3a8af6addc1?source=post_page-----f1c7baf18ca1--------------------------------)指出了这种方法的一个潜在缺陷之前，我已经完成了整个编码练习，所以如果你一无所获，不要担心…

这种方法的一个潜在的正确性问题(据我所知)是，一旦抛出错误，React 就会卸载**超时**子树，这有一个意想不到的副作用，即每次抛出或解析异步资源时，都会重置所有子树组件及其状态。

React 的悬念内部实现不会受到这个问题的影响，因为它们完全控制跟踪组件状态，因此可以确保在解析悬念资源后，部分呈现的子树被正确恢复。

然而，这里的好消息是，这在很大程度上是一种边缘情况，根据经验，我认为这不会经常发生。只要你遵循 95%的用例，其中占位符**的直接子元素**是唯一潜在的异步子组件，并且*异步子组件急切地预先加载所有异步状态*，而不是说，响应用户交互，你就不会遇到任何问题。👍

实际上，我很好奇 React core 实施这一限制是否有意义…

## 效率

这是 React 悬念的用户实现无法接近官方核心实现的地方。否则，我肯定 React 团队会考虑在 React 上实现这种模式，而不是扩展核心 React API 表面。

特别是，React 团队在过去一年左右的时间里做了大量工作，以实现更智能地重用部分渲染，以及暂停低优先级更新以支持更高优先级更新的能力，这些更新更接近于影响用户对应用程序响应性的感知。

这项工作被统称为 React Fiber，React 悬念应该被视为 React core 中启用的第一个主要优化之一，这是 React Fiber 建立的惊人基础的直接结果。

感谢[塞巴斯蒂安·马克伯格](https://medium.com/u/62e7de0d6312?source=post_page-----f1c7baf18ca1--------------------------------)、[安德鲁·克拉克](https://medium.com/u/6025bd347b9a?source=post_page-----f1c7baf18ca1--------------------------------)、[丹·阿布拉莫夫](https://medium.com/u/a3a8af6addc1?source=post_page-----f1c7baf18ca1--------------------------------)、[索菲·阿尔珀特](https://medium.com/u/b610e3f3fee2?source=post_page-----f1c7baf18ca1--------------------------------)，以及 React 团队的其他成员和贡献者在这一领域的工作！

## 和睦相处

该聚合填充目前不支持 React `v15`，因为在 React `v16`之前错误边界不被正确支持。如果你有关于如何添加对 React `v15`的支持的想法，请提交一个[问题](https://github.com/transitive-bullshit/react-suspense-polyfill/issues)让我们一起讨论！

请注意，React 将在使用此聚合填充时记录一个关于抛出错误的控制台错误，但是*可以安全地忽略此控制台消息*。不幸的是，对于这些类型的有意用例，没有办法[禁用](https://github.com/facebook/react/issues/11098)该错误报告。:感叹:

# 包扎

如果你已经读到这里，请查看完整的源代码和⭐️回购，以示感谢！

我真的希望这篇文章对你有所帮助。如果你是 React 迷，这里有一些相关链接:

*   [在 v16.2](/@pete_gleeson/creating-suspense-in-react-16-2-dcf4cb1a683f) 中制造 React 悬念——[皮特·格里森](https://medium.com/u/ba66bcf17eeb?source=post_page-----f1c7baf18ca1--------------------------------)的类似实验。
*   [react-suspension-starter](https://github.com/palmerhq/react-suspense-starter)-备选方案，捆绑了一个预建版本的悬疑启用 React，允许您尝试 React 悬疑右喵。由[贾里德·帕尔默](https://medium.com/u/fb7a3c353cc1?source=post_page-----f1c7baf18ca1--------------------------------)。
*   [react-async-elements](https://github.com/palmerhq/react-async-elements) -针对常见情况的悬念友好型异步 react 元素。由[贾里德·帕尔默](https://medium.com/u/fb7a3c353cc1?source=post_page-----f1c7baf18ca1--------------------------------)。
*   [新鲜-异步-反应](https://github.com/sw-yx/fresh-async-react) -更多悬念的东西(代码、演示和讨论)。通过 [Swyx](https://medium.com/u/547f259e265e?source=post_page-----f1c7baf18ca1--------------------------------) 。

有没有我漏掉的想法？请在评论中告诉我！❤️

## 在你走之前…

*如果您喜欢这篇文章，请点击👏下面，并与他人分享，这样他们也可以享受它。*