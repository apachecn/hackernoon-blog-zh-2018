# 将“componentWillReceiveProps”替换为“getDerivedStateFromProps”

> 原文：<https://medium.com/hackernoon/replacing-componentwillreceiveprops-with-getderivedstatefromprops-c3956f7ce607>

![](img/0cd673843e8cccbd6d850602efc6661b.png)

随着 [React](https://hackernoon.com/tagged/react) 16.3 的发布，引入了一些新的[生命周期](https://hackernoon.com/tagged/lifecycle)方法，React 17 的发布将弃用一些生命周期方法。

`getDerivedStateFromProps`是取代`componentWillReceiveProps`的新引入的生命周期方法之一，T1 现已成为`UNSAFE_componentWillReceiveProps`。

`getDerivedStateFromProps`是一个静态方法，在组件被实例化后以及当它接收到新的 props 时被调用。因为它是一个静态方法，你不能在这个方法中访问`this`,也不能访问任何其他的类方法。不像`componentWillReceiveProps`你不能在这个方法中设置状态，所以更新状态的唯一方法是返回一个对象。如果不想更新任何状态，只需返回`null`。

## 让我们深入一些代码

这就是`componentWillReceiveProps`的工作方式。

我们比较`nextProps.someValue`和`this.props.someValue`，如果两者不同，那么我们执行一些操作，`setState`并调用`this.classMethod();`。

现在让我们看看`getDerivedStateFromProps`是如何工作的。

它接收两个参数`nextProps`和`prevState`。如前所述，你不能在这个方法中访问`this`,所以你必须将道具存储在状态中，以便将`nextProps`与之前的道具进行比较。在上面的代码中，比较了`nextProps`和`prevState`，如果两者不同，那么将返回一个对象来更新状态，否则将返回`null`来指示不需要状态更新。如果状态改变，那么`componentDidUpdate`被调用，我们可以像在`componentWillReceiveProps`中那样执行所需的操作。

## 让我们用一个例子来说明这一点

假设我们从 firebase 获得一些数据，并以 stats 的形式显示出来。这是相同的代码。

上面的例子用的是`componentWillReceiveProps`。最初，`displayStat.js`组件将监听`path-1`上的 firebase，当用户点击改变路径按钮时，状态将在`App.js`文件中改变，而`componentWillReceiveProps`将在`displayStat.js`文件中被调用。以前到 firebase path 的连接将被关闭，并将创建一个新的。注意，我们将 firebase 引用作为参数传递给`getDate()`来监听 firebase。

现在让我们用`getDerivedStateFromProps`做同样的事情。

注意在`getDerivedStateFromProps`中返回一个对象来更新状态，并且没有调用任何类方法。我们使用`componentDidUpdate`来检查路径是否被更改，并相应地创建一个新的 firebase 连接并监听新路径。

**感谢阅读本文。如果你喜欢，请点击*拍手*按钮。** 在 [LinkedIn](https://www.linkedin.com/in/amanshu-kataria/) 上联系我。
你也可以在 [Twitter](http://twitter.com/amanshu_kataria) 、 [Quora](https://www.quora.com/profile/Amanshu-Kataria) 和 [GitHub](https://github.com/amanshu-kataria) 上关注我。