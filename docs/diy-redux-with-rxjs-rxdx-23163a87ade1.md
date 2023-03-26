# 带 RxJS 的 DIY Redux with RxJS

> 原文：<https://medium.com/hackernoon/diy-redux-with-rxjs-rxdx-23163a87ade1>

![](img/e2035650f8cb1e060e2f23b3e69ba90e.png)

Photo by [Steve Halama](https://unsplash.com/photos/iVGevPcaJzk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

如果你正在开发一个巨大的应用程序，状态管理是必须的，使用适当的状态管理可以是一个真正的救命稻草。在 React 上，Redux 是我们的救生员，在 Angular 上，我们有 NgRx 来担当这个角色。

我认为，在架构层面上，两者都有相似的样板，如果你已经掌握了其中一个，那么你对另一个的学习曲线将会更加平滑。我在 NgRx 上真正喜欢的不是库本身，而是 RxJS，它确实是处理流事件的最令人惊叹的库。

当我在研究 Redux 和 NgRx 时，我意识到几乎没有人在没有大量助手库的情况下单独使用 Redux，但另一方面，NgRx 的情况并不相似，因为它在大多数情况下提供了一个完整的解决方案。所以我决定做一个实验，将 RxJS 和所有 Redux helper 库结合起来，创建一个超级 duper 状态管理库，用于像 Redux 这样具有相似指纹的 React。

在这一点上，诀窍是显而易见的:首先，我用 RxJS 重新实现了 Redux，并在其上添加了一些中间件 salt，它已经准备好了！所以在这篇文章中，我将试着解释我是如何做到的。

**提示:我已经发布了最终库的 alpha 版本，你可以在这里查看**[](https://github.com/onerzafer/rxdx)****。****

# **步骤 0:解构 Redux 和相关库**

**Redux 主要提供了一个 **createStore** 函数，它显然创建了一个商店。这个 createStore 带有一个**缩减器、** **初始状态、**和一个**增强器**。**

****Reducer** 是取一个状态返回一个状态的函数。很简单，对吧？但是等一下，我将需要不止一个减压器，这意味着我将需要一个助手函数来将所有减压器组合成一个大减肥器。**

**出于类似的目的，Redux 有**组合减少器**功能，非常方便。**初始状态**是一个对象，它可能是一个空的或未定义的状态。甚至更简单！**增强器**有点奇怪，有以下特征:**

```
(createStore) => (reducer, initialState) => store
```

**我将再次需要不止一个增强器，我将再次需要一个助手函数来将所有增强器组合成一个名为 **applyMiddleware 的增强器。****

**当我们运行 createStore 函数时，它返回一个类似于下面简化对象的对象:**

```
{
   getState: () => state,
   subscribe: () => unsubscribe,
   dispatch: (action) => void
}
```

****getState** 返回当前状态。**订阅**返回一个**取消订阅**对象，但是如果我打算使用 RxJS，我可以使用它自己的订阅和取消订阅。因此，我将有一个更简单的实现。**分派**功能实际上将动作传递给减速器。RxJS 在这里也很方便，它有自己的调度机制。**

**因此，我创建了一个待办事项列表来实现我的目标:**

*   **实现` createStore(reducer，initialState，enhancer) => store `功能**
*   **实现“combine reducer(structuredReducersObject)= > reducer”**
*   **实现` applymiddleware(createStore)= >(reducer，initialState) => store `功能**

**开始吧！**

# **第一步:在 RxJS 行为主体和操作者的帮助下创建 Store**

**任何时候**商店**的消费者调用 **getState，**商店都应该返回当前状态。如果我使用**behavior subject**作为状态源，内部存储状态的实现将更容易，因为通过调用**behavior subject . value**来自 RxJS observables 的类似行为属于**behavior subject**。**

**事情是这样的:**

**我将用人类的语言重新表述我在这里所做的事情。在 **createStore** 函数中，我用给定的**减速器**和**初始状态**创建了一个**商店**。如果有一个**增强器**我传递一个假的 **createStore** 函数，它将返回已经创建的**存储**，结果它将返回增强的**存储**。然后我分派第一个动作并返回创建的或增强的存储。所以当调用 **createStore** 函数时，我们可以在我们的 app 中使用 **store 引用**。也许这个引用可以传递给某个 react 上下文提供者。**

**如你所见，最复杂的部分是**选择**功能，它根本不是一个必须具备的功能。我添加这个部分是为了有一个接口，比如**重选**。据我目前所知，Redux 的 **subscribe** 功能并不是一个广泛使用的特性，但是 react-redux 库使用它来获得更新和重新呈现组件(如果有人直接使用它，请在下面评论它，因为在我的实验中我找不到任何 subscribe 功能的用例)。**

****因此，createStore 函数到目前为止应该能够为一个 reducer 和一个 enhancer 工作，这意味着我的基于 RxJS 的 Redux 库的主干已经准备好被命名:从现在开始我将称它为 RxJs + Redux = RxDx。****

# **第二步:如何创建一个 combineReducers 函数？**

**当我检查 Redux 的 **combineReducers** 源代码时，我意识到它接受一个对象(可以嵌套),该对象的键有一个 reducer，结果返回一个更大的 reducer。**

**让我们从下面的初始代码开始:**

**现在，我将避免错误预防和错误处理(我将信任使用该函数的任何人)。这里的想法是循环 reducers 对象的键，并调用带有相关状态部分和动作的附加 reducer。**

**因此，所有的 reducers 将只更新状态的相关部分，我将在相同的结构中组合结果并返回它们。当我试图编写这个函数时，我注意到这个函数的名字本身就有解: **Array.reduce.****

# **步骤 3:神奇的 applyMiddleware 函数**

**如果你仔细观察 Redux 的实现，最神奇的事情发生在一个名为 **compose** 的函数上。撰写功能是做什么的？实际上，它非常简单，因为它接受一个函数列表，通过将第二个包装的函数传递给第一个函数，将第三个传递给第二个函数，以此类推，来包装每个函数，最后返回这个奇怪的嵌套函数:**

**从上面可以看到，函数 A，B，C，D，E 变成了 A(B(C(D(E(…args))))。这个行为是至关重要的，因为所有的增强器都应该能够在每个动作上被调用，并且所有的增强器都应该能够改变动作或者调度新的动作，甚至延迟原来的动作。对增强器还有一个限制，因为它们应该遵循下面的签名:**

```
// sample enhancer signature
const enhancer => (createStore) => (next) => (action) => state
```

**最后，通过省略错误处理和错误预防部分，我得到了下面的函数:**

# **最后的话**

**事实上，RxDx 目前包括其他功能的损失，如果你愿意检查它们和所有的示例用法，你可以检查 [repo](https://github.com/onerzafer/rxdx) 和 [sample todo app repo](https://github.com/onerzafer/rxdx-sample-app) 。**

**这是 RxDx 的核心部分，我计划在这篇文章的后续文章中进一步解释如何将 RxDx 连接到 React 组件。**

**在接下来的文章中，我希望能阐明 react-redux 的内部结构，然后我将继续讨论 react-observable，然后重新选择库。**

**如果你对 RxDx 感兴趣，并且喜欢到目前为止的文章，请不要忘记点击拍手(你最多可以拍手 50 次)。如果你在下面分享你的想法和反馈，我会很高兴的！感谢阅读。**

****对于第 2 部分，请点击链接:****

**[](https://hackernoon.com/diy-redux-with-rxjs-part-2-f9d4c53fa230) [## 使用 RxJS 的 DIY Redux:第 2 部分

### 将 RxJS 和所有广泛使用的 Redux 中间件合并为一个库的尝试

hackernoon.com](https://hackernoon.com/diy-redux-with-rxjs-part-2-f9d4c53fa230)**