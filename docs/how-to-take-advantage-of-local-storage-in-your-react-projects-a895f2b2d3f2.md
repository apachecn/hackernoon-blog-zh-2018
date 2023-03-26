# 如何在 React 项目中利用本地存储

> 原文：<https://medium.com/hackernoon/how-to-take-advantage-of-local-storage-in-your-react-projects-a895f2b2d3f2>

## 以及为什么你应该这么做。

![](img/b8c43fc25ac8e79ac2a3074fab13cc13.png)

[本地存储](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)是现代网络浏览器自带的网络 API。它允许网站/应用程序在浏览器中存储数据(简单和有限)，使这些数据在未来的浏览器会话中可用。

在深入本教程之前，可能不清楚为什么要在 React 应用程序中使用本地存储。

有很多原因和用例，超出了我的想象，但这里是我发现的一些。

*   **为你的前端 React 项目提供一个简单的、虚假的后端—** 给你的前端组合项目添加一个后端/数据库的外观通常是很好的。额外的功能将使你的应用更上一层楼，改善用户体验，给潜在雇主留下深刻印象。
*   **在开发过程中尝试不同的状态—** 在开发应用程序时，应用程序拥有特定的`state`来处理特定的样式和功能通常是有用或必要的，(例如，样式化项目列表和移除项目*需要项目*)。本地存储可以保存应用程序的状态，而不是在每次刷新时重新创建应用程序的状态，这使得开发*更加高效和愉快。*
*   ***跨会话保存表单数据—** 比起填写表单，人们更讨厌什么？**填两次表！***

***快速侧边栏:**我正在考虑做一个 React dev 工具(npm 包和/或 chrome 扩展),通过保存旧的状态和一次点击就能恢复组件的状态，这很容易。目标是在开发时最大限度地减少手动重新创建应用程序状态所花费的时间。**如果你也有同样的痛点，希望我构建解决方案，请留下评论或** [**邮件我**](https://ryanjyost.com) **！***

# *入门指南*

*使用 [create-react-app](https://github.com/facebook/create-react-app) 创建一个新的 React 项目。*

```
*npx create-react-app local-storage*
```

*`cd`进入新目录，启动应用程序。[如果还没有安装纱线](https://www.npmjs.com/package/yarn)。*

```
*yarn start*
```

*用下面的代码更新你的`App.js`。在这里，我们设置了一个简单的待办事项应用程序。绝对没什么特别的，但是玩`localStorage`会很有趣。*

*复制完这段代码后，您应该能够将待办事项添加到列表中并删除它们。*

# *开始保存东西到本地存储*

*将我们的`newItem`输入的值保存到`localStorage`是小菜一碟。*

*在`updateInput()`方法中，我们将调用`[localStorage.setItem()](https://developer.mozilla.org/en-US/docs/Web/API/Storage/setItem)`方法，它有两个参数:*

*   *`key: string` —本地存储项目的名称*
*   *`value: string` —要为给定的本地存储器`key`保存的值。**注意:**即使是数组和对象也需要保存为字符串。*更详细一点。**

*这是我们新的`updateInput()`方法。*

*如你所见，和更新 React `state`没太大区别。*

*在你选择的浏览器中打开 Web Developer tools，找到 Web 存储部分(Chrome 中的“应用程序”选项卡)，选择当前域`localhost:3000`，观察`newItem`键的值与应用程序的输入保持同步。*

## *现在，让我们保存待办事项列表*

*当添加一个条目时，我们将新的、更新的`list`保存到`localStorage`，并将`newItem`输入重置为空字符串。*

*这里没有惊喜，除了`[JSON.stringify()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify)`的使用。该方法将 JavaScript 值转换成 JSON 字符串。*

*因为`localStorage`只能存储字符串，**数组和对象需要先传入** `**JSON.stringify()**` **，再传入** `**setItem()**` **。***

*在继续之前，我们还想在删除一个项目时更新`localStorage`中的列表。*

# *喜欢这个教程？*

*[**订阅约斯特的帖子简讯**](https://tinyletter.com/ryanjyost) **获取更多。***

# *好的，我们在节约。但是请注意刷新页面时会发生什么…*

*……`App`恢复到初始状态！我们还没有使用存储的项目，只是在后台保存它们。不是很有帮助…*

*为了即使在刷新页面后也能保持应用程序的`state`，我们需要借助几个新方法，用`localStorage`中的值来*融合*T2 的状态:*

*   *`[localStorage.getItem()](https://developer.mozilla.org/en-US/docs/Web/API/Storage/getItem)` —获取一个存储`key`并返回保存在该键下的值。*
*   *`[JSON.parse()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse)` —将 JSON 字符串转换为 JavaScript 值。您需要它来正确地检索作为字符串保存到`localStorage`的对象和数组。*

*下面的方法用保存到`localStorage`的值来混合应用程序的状态。将这个新方法添加到您的`App`组件中。*

*当页面加载时，即在[组件生命周期](https://reactjs.org/docs/state-and-lifecycle.html)的早期，水合`state`是有意义的。所以让我们在`componentDidMount()`中调用这个函数。*

*一旦你将上面的代码添加到你的`App`组件中，刷新页面不再重置应用程序，而是保持它与`localStorage`同步！*

# *持续储蓄是不必要的——有更好的方法。*

*虽然我们的应用程序通过在用户每次更新时保存 React `state`到`localStorage`来工作，但我们并不需要如此频繁地保存。*

*为什么？因为 React 会在用户的整个会话过程中跟踪应用程序的`state`——这就是它的用途！此外，对于更复杂的组件和状态，在更新`state`时使用`localStorage.setItem()`会非常麻烦和重复。*

*因此，我们不要继续保持`localStorage`与 React `state`同步，只要用户结束他们的会话，无论是通过离开应用程序(“卸载”组件)还是刷新页面，我们都可以简单地将`state`保存到`localStorage`。*

## *事件/操作的新顺序将是…*

1.  *用户访问应用程序(在我们的例子中是 localhost:3000)*
2.  *组件`App`使用任何适用的`localStorage`值安装并水合其`state`。*
3.  *React 将在整个用户会话期间更新`state`。`*localStorage*` *不会改变。**
4.  *当用户结束他们的会话时，将当时的`state`保存到`localStorage`，使其可用于下一个会话的补水。*

*好的，这里有一个新的方法可以一次保存所有的`state`到`localStorage`。将它添加到您的`App`组件中。*

*为了在用户离开 app 时保存`state`到`localStorage`，我们需要调用`componentWillUnmount`中的`saveStateToLocalStorage`方法。*

***警告—** `**componentWillUnmount**` **在用户刷新或离开页面时不会触发，所以我们需要利用** `[**window.onbeforeunload**](https://developer.mozilla.org/en-US/docs/Web/API/WindowEventHandlers/onbeforeunload)` **事件来保存到** `**localStorage**` **。** [**在此了解更多。**](https://stackoverflow.com/questions/39084924/componentwillunmount-not-being-called-when-refreshing-the-current-page)*

*这里有一些更新的代码，我们将事件监听器添加到`componentDidMount`，并将我们需要的添加到`componentWillUnmount`。*

*当更新 React `state`时，我们不再需要`setItem`，所以你需要删除它们。*

*自从本教程开始以来，已经发生了很多变化，所以这里是`App.js`文件。**`*render()*`*中的方法没有任何改变。****

**就是这样！现在，您已经拥有了在 React 项目中使用本地存储的工具。**

# **反应简单存储——一个几乎无耻的插头**

**[](https://www.npmjs.com/package/react-simple-storage) [## 反应-简单-存储

### 将 localStorage 与 React 一起使用的简单组件和助手函数。

www.npmjs.com](https://www.npmjs.com/package/react-simple-storage) 

我发现自己在工作中想要利用大量不同组件的本地存储，所以我创建了一个组件，[**react-simple-Storage**](https://github.com/ryanjyost/react-simple-storage)，它可以处理我们刚刚实现的所有内容以及更多内容。请继续关注我们的小应用程序，看看它有多简单。

## 1.安装它

```
yarn add react-simple-storage
```

## 2.将其导入 App.js

## 3.将它包含在您的<app>组件中，就像这样…</app>

**就是这样！**你不需要教程中所有的额外方法和事件监听器，所以最后的`App.js`使用 [react-simple-storage](https://github.com/ryanjyost/react-simple-storage) 看起来像这样…

[](https://github.com/ryanjyost/react-simple-storage) [## ryanjyost/react-简单-存储

### React-Simple-storage——React 中使用 localStorage 的简单组件和辅助函数。

github.com](https://github.com/ryanjyost/react-simple-storage)**