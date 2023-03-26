# 有问题的 React 生命周期方法将在 React 17 中消失

> 原文：<https://medium.com/hackernoon/problematic-react-lifecycle-methods-are-going-away-in-react-17-4216acc7d58b>

![](img/4a06602a19d3fda7aa252e575ddd63b2.png)

您在理解一些 react 生命周期方法以及如何在不出错的情况下实现它们方面有困难吗？

如果你的答案是肯定的，你并不孤单。

我一直在 React 原生应用中使用它们，我必须承认，我已经多次误用它们，并以艰难的方式吸取了教训。

React 团队已经决定在 React 17 中弃用一些生命周期方法。reactjs 团队最近的一篇博客文章揭示了组件生命周期方法的未来。

他们为什么决定除掉他们？最初的生命周期模型并不打算用于即将到来的一些特性，比如异步渲染。随着异步渲染的引入，这些生命周期方法中的一些在使用时会变得不安全。

> *传统的生命周期方法有太多的潜在缺陷，无法安全地用于异步渲染。*

# 那么，什么正在消失？

下面的生命周期方法将很快被弃用。

```
componentWillMountcomponentWillRecievePropscomponentWillUpdate
```

他们将分阶段淘汰这些方法，所以这不会是一夜之间的改变。我相信我们中的许多人都经常使用这三种方法。

上述生命周期方法在 16.3 版本中被归类为 ***不安全*** 。在之后的版本中，弃用警告将被添加到遗留方法中。最后，在 React 17 中，遗留方法将被完全废弃。

有趣的是， ***不安全*** 方法在 React 17 中仍然会被使用。

# 什么是新的？

三种旧的生命周期方法将被重命名，另外两种方法也将被引入。

```
UNSAFE_componentWillMountUNSAFE_componentWillRecievePropsUNSAFE_componentWillUpdategetDerivedStateFromPropsgetSnapshotBeforeUpdate
```

所以方法并没有真的消失，但是它们被标记为不安全。程序员应该意识到不要使用它们，或者小心谨慎地使用它们。

异步渲染将导致 *componentWillMount* 触发组件树的多重渲染。这使得它不安全。

如果你想知道未来的服务器渲染如何只使用 *componentDidMount，*这里有一个来自 [Dan Abramov](https://medium.com/u/a3a8af6addc1) 的回复来解释它。

# 安全的生命周期方法

以下是我从博文中收集到的关于新生命周期方法的信息。

***getDerivedStateFromProps***

```
static getDerivedStateFromProps(nextProps, prevState) {
    // ...
  }
```

这个方法将处理*component will receive props*和 *componentDidUpdate 能够做的事情。*是静态的。它在一个组件被创建后被调用，当它收到一个新的属性时也被调用。这将是比*组件接收弹出窗口更安全的选择。*

它返回一个对象来更新状态以响应属性更改。它将返回 null，表示状态没有变化。

即使道具没有改变，React 也可能调用这个方法。

***getsnapshotbefore update***

```
getSnapshotBeforeUpdate(prevProps, prevState) {
    // ...
  }
```

这将处理 *componentWillUpdate* 和 *componentDidUpdate 能够做的事情。*这是在 DOM 更新之前调用的。从*getsnapshotbeforedupdate*返回的值被传递给 *componentDidUpdate。*

DOM 一更新，componentDidUpdate 就被调用。*快照*值被传递给 *componentDidUpdate。*

当*getsnapshotbefore update*可用时，在异步渲染期间调整窗口大小是一个很好的用例。

# 编辑:当我写这篇文章的时候，React 16.3.0 正式发布了。Wohoo！！！

除了生命周期方法的改变，他们还发布了一个叫做**严格模式的新工具。** It 识别并强调组件中不安全的生命周期方法，以及开发过程中的许多其他副作用。从他们的博客文章中了解更多关于严格模式的信息。

关于新的生命周期方法及其用法的例子，请查看最初的博客文章。

为了更好地理解，请查看 React 团队展示这些很酷的新功能的预览视频。

# 我怎么想？

当最初设计 react 组件生命周期方法时，它们不必担心许多未来的用例，如异步呈现。

虽然这些弃用不是向后兼容的，但这是朝着改进迈出的一步，我很喜欢。

我很高兴 react 团队指出遗留生命周期方法是不安全的。这将禁止反模式和误用这些方法。我很高兴能在 React 16.3.0 中看到为我们开发者准备的所有新特性。

我们去找点乐子吧。

我是致力于 React 本地应用的软件顾问。我也是一名多视作家，热爱教学。你可以查看我在 React Native 上的最新课程，来自下面的 [pluralsight](https://www.pluralsight.com/courses/react-native-big-picture) :

[https://www . plural sight . com/courses/react-native-big-picture](https://www.pluralsight.com/courses/react-native-big-picture)

如需更多信息和我的帖子，请访问[http://adhithiravichandran.com/](http://adhithiravichandran.com/)并在 twitter 上关注我 [@AdhithiRavi](https://twitter.com/AdhithiRavi) 。