# React 应用程序中的状态管理—第一部分

> 原文：<https://medium.com/hackernoon/state-management-in-react-apps-part-i-9f46ebed2f6c>

如果你知道如何[正确使用 JSX](https://kolosek.com/react-jsx-loops/) 以及[反应组件如何相互通信](https://kolosek.com/react-props-basic/)你就是一个真正的英雄。但是，您应该问自己一个重要的问题:*在哪里存储您的应用程序关心的信息？*

我们来谈谈 React 中的应用程序状态管理。

有很多方法可以处理应用程序状态，但是首先，让我们来看看最简单的一种——组件的状态。

![](img/c588302483f4925325e54e3982d2b39d.png)

# 对组件状态做出反应

你可能以前看过`this.state`。一个组件有它的本地状态，一个目的是保存对组件重要的信息的对象，比如你应该呈现的电影列表。这个本地状态首先在构造函数中初始化，如下所示:

```
constructor() {
    super();
    this.state = {
        itemKey: defaultItemValue,
        ...
    };
}
```

请记住，您应该为您将在组件中使用的每个状态属性定义一个默认值，即使初始值是`undefined`。

*您应该在组件状态中存储什么？*关于什么时候使用组件的状态，以及什么时候转向更高级的方法，有一个争论:过去许多开发人员强迫你根本不应该使用`this.state`，但是你应该保持所有东西在全局可访问的状态(即 redux 状态——稍后会提到)，即使它只是一个用于[条件渲染](https://kolosek.com/react-jsx-conditions/)的简单开关。

然而，如果一个值只在这一个组件中使用，那么在 redux 状态中保持该值是没有意义的；如果正在开发的应用程序非常简单，就没有必要引入另一个依赖项，比如一个单独的状态管理库。一开始，当学习 React 时，最好坚持使用`this.state`，因为它不需要深入另一个库。

React 组件的状态被异步更新。您将使用`this.setState`功能来更新您的状态。然而，有一件事要永远记住:当一个 setState 依赖于前一个 setState 时，你不应该连续使用`setState`两次，因为它的异步特性。

```
// assuming this.state.count === 0
this.setState({
    count: this.state.count + 1
});
this.setState({
    count: this.state.count + 1
});
// it doesn't necessarily mean that this.state.count will be 2 now
```

然而，你可以使用所谓的*功能设置状态*来设置依赖于先前状态或依赖于某些[组件的属性](https://kolosek.com/react-props-basic/)的状态:

```
this.setState((previousState, currentProps) => {
    return {
        count: previousState.count + 1,
    };
});
```

使用函数 setState 是使用 setState 的最佳方式。

如果您想要更改保存在状态中的多个值，您不需要多次调用 setState:

```
// "standard" way
this.setState({
    item1: value1,
    item2: value2,
});
// "functional" way
this.setState((previousState, currentProps) => {
     return {
         item1: value1,
         item2: value2,
     };
});
```

状态不应该变异。这既适用于简单组件的状态，也适用于一些更高级的组件，比如第三方 redux 状态。组件的状态只能通过使用`setState`功能来改变。虽然您可以只使用`this.state.disabled = true`，但它不会触发组件的重新呈现。没有防止状态下保存的变异物品，所以你要关心自己不要变异它们。

到目前为止，我已经介绍了 React 组件的状态和它的三个基本概念:它是**不可变的**、**异步的**和**局部的**。这是在简单的应用程序中处理存储信息的一种快捷方式，对于应该从组件实例中的多个位置访问的局部变量也是如此，甚至在非常大和复杂的应用程序中也是如此。

然而，对于更复杂的应用程序，对于过于依赖彼此状态的[组件](https://kolosek.com/react-props-communication/)，以及为了更好地处理应用程序使用的数据，有第三方库，如 Redux、Flux、CerebralJS 和许多其他库，它们提供了更高级的方法来处理应用程序状态。下次我一定会写更多关于他们的东西。

*敬请关注更多 React 内容！*

*原载于 2018 年 6 月 4 日 kolosek.com**T21*[。](https://kolosek.com/react-state/?utm_source=me)