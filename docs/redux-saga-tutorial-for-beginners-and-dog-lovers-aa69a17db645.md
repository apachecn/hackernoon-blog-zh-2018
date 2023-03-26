# Redux-Saga 新手和爱狗人士教程

> 原文：<https://medium.com/hackernoon/redux-saga-tutorial-for-beginners-and-dog-lovers-aa69a17db645>

## 构建一个简单的应用程序，*使用 React，Redux & Redux-Saga 获取*狗的图像

![](img/f14ded20cd06ea58fa43356b86f15fc3.png)

Michael Gil — [https://www.flickr.com/photos/msvg/](https://www.flickr.com/photos/msvg/)

我开始在一个[相当复杂的样板文件](https://github.com/ruddell/ignite-jhipster/tree/master/boilerplate)中使用`[redux-saga](https://redux-saga.js.org/)`。

变得稍微舒服一点的道路是曲折和次优的，所以我想我应该写**教程，我希望我刚开始写传奇的时候就已经有了。**

> 本教程假设您已经牢固掌握 React 或 React Native 和 Redux。如果你还没有准备好`redux-saga`(还没有)，我强烈建议你看看这个惊人的、巨大的学习 React 和 Redux 的资源宝库。

[](https://github.com/markerikson/react-redux-links) [## markerikson/react-redux-links

### react-redux-links——我在 React、Redux、ES6 等网站上收集的精选教程和资源链接

github.com](https://github.com/markerikson/react-redux-links) 

# 你将建造什么

[](https://ryanjyost.github.io/redux-saga-beginner-tutorial/) [## 狗狗传奇-Redux-传奇入门教程

### 超级简单的应用程序的初学者 redux-saga 教程。

ryanjyost.github.io](https://ryanjyost.github.io/redux-saga-beginner-tutorial/) 

# redux-saga 是什么？我为什么想要或需要它？

来自[官方回购](https://github.com/redux-saga/redux-saga):

> `redux-saga`是一个库，旨在使应用程序副作用(即异步的事情，如数据获取和不纯的事情，如访问浏览器缓存)更容易管理，更有效地执行，简单地测试，更好地处理故障。

我认为这是一种有组织的方式，帮助您的 Redux 应用程序与外界通信并保持同步——主要是外部 API。

许多优秀的人对`redux-saga`的利弊和其他一切发表了意见，比我好得多——所以如果你想真正确定**是什么**和**为什么，这里有一些链接。**

我们将关注**如何**。

 [## 给我读 Redux-Saga

### 心理模型是，一个传奇就像你的应用程序中的一个单独的线程，专门负责侧…

redux-saga.js.org](https://redux-saga.js.org/) [](https://jaysoo.ca/2016/01/03/managing-processes-in-redux-using-sagas/) [## 使用 Sagas 管理 React + Redux 中的副作用

### 对我来说，在 React+Flux 设置中一直有一个棘手的问题，那就是涉及到…

jaysoo.ca](https://jaysoo.ca/2016/01/03/managing-processes-in-redux-using-sagas/) [](https://ohyayanotherblog.ghost.io/redux-saga-clock/) [## Redux Saga:未来是 1975 年

### WTF 是时钟？(这是 Redux Saga 三部分介绍中的第一篇。第二封邮件在这里，还有…

ohyayaanother blog . ghost . io](https://ohyayanotherblog.ghost.io/redux-saga-clock/) [](https://shift.infinite.red/using-redux-saga-to-simplify-your-growing-react-native-codebase-2b8036f650de) [## 使用 redux-saga 简化您不断增长的 React 原生代码库

### 您将编写的一些最脆弱、最尴尬、最难读的代码是流控制。—贝奥武夫

红色](https://shift.infinite.red/using-redux-saga-to-simplify-your-growing-react-native-codebase-2b8036f650de)  [## 什么是 Redux-Saga？

### Redux-saga 是一个 Redux 中间件库，旨在使处理 redux 应用程序中的副作用变得更好…

engineering.universe.com](https://engineering.universe.com/what-is-redux-saga-c1252fc2f4d1)  [## 当今的 Redux:从动作创作者到传奇

### 每当我偶然发现某样东西(某项技术)会改变我的习惯时，我都会说这句话…

riad .博客](https://riad.blog/2015/12/28/redux-nowadays-from-actions-creators-to-sagas/) [](http://konkle.us/master-complex-redux-workflows-with-sagas/) [## 使用 Sagas 掌握复杂的 Redux 工作流程

### 了解如何使用异步监视器例程(sagas)处理复杂的过程，这些例程监视您的 Redux 事件。布兰登…

konkle.us](http://konkle.us/master-complex-redux-workflows-with-sagas/) 

# 我们开始吧

用`[create-react-app](https://github.com/facebookincubator/create-react-app)`新建一个 app。

```
npx create-react-app dog-saga
```

> 被`npx`搞糊涂了？我也是，直到我读到[这个](/@maybekatz/introducing-npx-an-npm-package-runner-55f7d4bd282b)。

进入项目目录，启动应用程序。

```
cd dog-saga
npm start
```

你现在应该看到`create-react-app`的样板文件，带有旋转的 React 标志。你很快就会用可爱的狗来代替它。

# 喜欢这个教程？

[**订阅约斯特的帖子简讯**](https://tinyletter.com/ryanjyost) **获取更多。**

# Redux

安装 redux。

```
npm install --save redux
```

在您的`src`文件夹中创建一个名为`redux.js`的新文件，并向其中添加以下代码。

## 我们有三种动作类型和一个减速器

*   `**API_CALL_REQUEST**`表示我们正在开始从[狗 API](https://dog.ceo/dog-api/) 中获取`fetching`狗的过程。
*   `**API_CALL_SUCCESS**`告诉商店，我们成功取回了一个`dog`，因此不再进行`fetching` one。
*   `**API_CALL_FAILURE**` 告诉商店我们的 API 调用出了问题。我们收到了一个`error`而不是一个新的`dog`。

但是我们应该如何制作`API_CALL_REQUEST`？

商店如何知道 API 调用是成功还是失败？

我们如何得到可爱的 DOGS 该死的照片？？？**带着传奇色彩。**

> 附注:我们不会在这个应用程序中使用动作创作者。为了这么简单的事情，他们可能会把水搅浑。此外，您将看到 redux-saga 如何在没有它们的情况下更清晰地处理和分派动作(在我看来)。

# 还原传奇

我们想使用`redux-saga`创建一个*传奇*，它将启动一个狗图像的 API 调用，然后告诉商店该 API 调用是成功还是失败。

*   如果成功，我们将得到一个新的`dog`，并将`API_CALL_SUCCESS`和`dog`一起发送出去。
*   如果失败，我们将得到一个`error`，并将`API_CALL_FAILURE`和`error`一起发送出去。

安装`redux-saga`。

```
npm install --save redux-saga
```

还要安装`[axios](https://github.com/axios/axios)`，它将帮助我们进行基于`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)`的 API 调用。

```
npm install axios
```

创建一个名为`sagas.js`的新文件，并向其中添加以下代码。

在我们浏览这个新文件之前，请注意`function*`语法。这创建了一种特殊的 ES6 新功能，称为`[generator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)`。

生成器可以暂停和重新启动——退出和重新进入——并且可以随时记住函数的上下文/状态。

生成器中的每个`yield`基本上代表一个更加同步/连续的过程中的一个异步步骤——有点像`[async](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)`函数中的`await`的[](https://www.reddit.com/r/javascript/comments/2ywqif/if_we_have_functions_and_yields_why_do_we_need/)*。*

*`redux-saga`依赖于生成器，但是为我们做了相当多的工作，所以(以我相当有限的经验)对于这个用例来说，深入理解它们是不必要的。*

## *如果你想了解更多关于发电机的知识，这里有一些资源*

*[](https://davidwalsh.name/es6-generators) [## ES6 发电机的基础知识

### ES6 发电机:完整系列 ES6 发电机的基础更深入，ES6 发电机与 ES6 异步运行…

davidwalsh.name](https://davidwalsh.name/es6-generators) [](/javascript-scene/the-hidden-power-of-es6-generators-observable-async-flow-control-cfa4c7f31435) [## ES6 发电机的隐藏能量:可观测的异步流量控制

### 在我用 JavaScript 编写斐波那契生成器时学到的 7 件令人惊讶的事情中，我介绍了 ES6 的一个明显的用例…

medium.com](/javascript-scene/the-hidden-power-of-es6-generators-observable-async-flow-control-cfa4c7f31435) 

**现在我们来看一下** `**sagas.js**`

*   一个`**watcherSaga**`是一个传奇，*观察*等待一个动作被分派到商店，触发一个`workerSaga`。
*   `[takeLatest](https://github.com/redux-saga/redux-saga/tree/master/docs/api#takelatestpattern-saga-args)`是由`redux-saga`提供的一个帮助函数，当它看到一个`API_CALL_REQUEST`时将触发一个新的`workerSaga`，同时取消任何先前触发的仍在进行中的`workerSaga`。
*   `**fetchDog**`简单地使用`axios`从 dog API 请求一个随机的 Dog 图像，并返回一个`[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)`作为响应。
*   `**workerSaga**`使用另一个`redux-saga`辅助函数`[call](https://github.com/redux-saga/redux-saga/tree/master/docs/api#callfn-args)`尝试`fetchDog`，并将结果(已解决或失败的`Promise`)存储在`response`变量中。
*   如果`fetchDog`成功，我们从`response`中提取`dog`图像，并使用另一个`redux-saga`辅助函数`[put](https://github.com/redux-saga/redux-saga/tree/master/docs/api#putaction)`将有效载荷中带有`dog`的`API_CALL_SUCCESS`动作发送到商店。
*   如果`fetchDog`有错误，我们会通过`error`发送一个`API_CALL_FAILURE`动作让商店知道。

唷！开始时有点奇怪，但是在几次实现之后，这种模式/过程及其好处变得更加清晰。

# 连接 React、Redux 和 Redux-Saga

好了，我们有了自己的作品，现在是时候把它们放在一起了。

安装`[react-redux](https://github.com/reactjs/react-redux)`。

```
npm install --save react-redux
```

此外，安装 Redux dev tools——调试和查看 Redux(以及 sagas 的相关操作)的必备工具。[点击这里获取设置 Redux 开发工具的信息。我推荐使用](https://github.com/reduxjs/redux-devtools)[浏览器扩展](https://github.com/zalmoxisus/redux-devtools-extension#redux-devtools-extension)。

打开您的`index.js`文件，使它看起来像下面的文件。

Redux 的东西应该看起来很熟悉。

*   `[createStore](https://github.com/reactjs/redux/blob/master/docs/api/createStore.md)`与我们的`reducer`
*   将 [Redux DevTools](https://github.com/gaearon/redux-devtools) 连接到存储器进行调试和学习
*   并将`<App/>`组件包装在带有`store`的`<Provider/>`组件中，这样我们就可以在 React 中使用 Redux *了。*
*   *我们将实际上*`*connect()*`*`*<App/>*`*的构件简称为。**

*为了让我们的`redux-saga`与 Redux 一起工作…*

*   *`createSagaMiddleware`，并在`[compose](https://github.com/reactjs/redux/blob/master/docs/api/compose.md)`和`[applyMiddleware](https://redux.js.org/docs/api/applyMiddleware.html)`的帮助下将其应用到 Redux `store`*
*   *`run`的`watcherSaga`，这样当有`API_CALL_REQUEST`时就可以触发`workerSaga`*

# *将<app>连接到 Redux</app>*

*打开`App.js`并将以下代码粘贴到其中。*

*   *`mapStateToProps`将`fetching`、`dog`和`error`的最新状态作为`App`组件中的`props`可用。*
*   *使用`mapDispatchToProps`，我们创建了一个名为`onRequestDog`的函数，它向商店发送一个`API_CALL_REQUEST`动作。*
*   *`connect`组件`App`并导出它的“还原”版本以用于`index.js`。*

# *喜欢这个教程？*

*[**通过订阅约斯特的帖子获取更多简讯**](https://tinyletter.com/ryanjyost) **。***

# *在屏幕上显示所有内容*

*现在让我们浏览一下对`App`组件的渲染输出所做的一些更改*(从上到下)*，这些更改允许用户查看应用程序的当前状态并请求狗的图像。*

***所有这些代码片段都来自上面的** `**App.js**` **，所以这里没有新的代码。***

*在下面的代码片段中，我们调整了图像`src`以显示一个`dog`图像，如果商店中存在的话。如果`dog`为`null`，则退回到反应`logo`。*

*如果我们应用程序的当前`state`有一个`dog`图像，我们告诉用户继续点击。如果没有，我们告诉他们用一个`dog`替换 React `logo`。*

*如果当前的`state`有错误，我们显示一些文本让用户知道。*

*在这里，如果我们的英雄目前正在`fetching`一个新的狗图像，这意味着`workerSaga`还没有发送一个`API_CALL_SUCCESS`或`API_CALL_FAILURE`，我们禁用该按钮。*

*否则，我们为用户提供一个按钮，单击并请求一个随机的狗图像。*

# *只是为了好玩，让我们造成一个错误*

*要查看`workerSaga`派遣一个`API_CALL_FAILURE`，进入`sagas.js`并为狗 api 搞乱`url`(比如将“品种”改为“床位”)。*

*现在，当你点击“要求一只狗”按钮时，错误信息显示！*

# *一步一步的回顾*

1.  *事件发生—例如，用户做了一些事情(点击“请求狗”按钮)或更新发生(如`componentDidMount`)*
2.  *基于该事件，可能通过在`mapDispatchToProps`中声明的函数(例如`onRequestDog`)分派一个`action`*
3.  *一个`watcherSaga`看到`action`并触发一个`workerSaga`。*使用* [*传奇助手*](https://github.com/redux-saga/redux-saga/tree/master/docs/api#saga-helpers) *观察不同的动作。**
4.  **当*传奇开始时，`action`也点击`reducer`并更新`state`的某个片段，以指示传奇已经开始并正在进行中(例如`fetching`)。*
5.  *`workerSaga`执行一些副作用操作(如`fetchDog`)。*
6.  *基于`workerSaga`的操作结果，它会分派一个`action`来指示该结果。如果成功(`API_CALL_SUCCESS`)，您可以在操作中包含一个有效负载(例如`dog`)。如果出现错误(`API_CALL_FAILURE`)，您可以发送一个`error`对象来获得关于错误的更多细节。*
7.  *`reducer`处理来自`workerSaga`的成功或失败`action`，并相应地用任何新数据更新存储器，以及将“进行中”指示器(例如`fetching`)设置为假。*

*在整个过程中，您可以使用 Redux `state`到`props`的更新来让您的用户了解过程及其进度。*

# *更多资源*

*[](https://medium.freecodecamp.org/async-operations-using-redux-saga-2ba02ae077b3) [## 使用 redux-saga 的异步操作

### 2017 年 8 月更新:

medium.freecodecamp.org](https://medium.freecodecamp.org/async-operations-using-redux-saga-2ba02ae077b3) [](https://medium.freecodecamp.org/redux-saga-common-patterns-48437892e11c) [## 这种常见的 Redux-saga 模式的集合将使您的生活更加轻松。

### 这是一个由两部分组成的系列，您可以在这里查看第一部分。

medium.freecodecamp.org](https://medium.freecodecamp.org/redux-saga-common-patterns-48437892e11c)**