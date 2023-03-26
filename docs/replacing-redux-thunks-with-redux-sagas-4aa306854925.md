# 用冗余的传奇代替冗余的思想

> 原文：<https://medium.com/hackernoon/replacing-redux-thunks-with-redux-sagas-4aa306854925>

## React 博客系列:第五部分

![](img/4e66691aa668401a60ef7d108953cb0c.png)

本文是用 React 创建博客的每周系列的第五部分，是前几部分创建的代码的扩展。

## React 博客系列

[**第一部分:**用 React 和布尔玛](/front-end-hacking/building-a-website-with-react-and-bulma-d655214bff2a)
[**建立一个网站第二部分:**用 React 和 Contentful 建立一个博客](/@aaron.klaser/building-a-blog-with-react-and-contentful-fd538f68f6fb)
[**第三部分:**将你的媒体提要导入 React](/@aaron.klaser/import-your-medium-feed-into-react-ceadbaf785c7)
[**第四部分:**向 React 博客添加一个 Redux](/@aaron.klaser/adding-redux-to-a-react-blog-97f5fea606c2)
**第五部分:**用 Redux Sagas 替换 Redux Thunks

> 首先，让我从这个问题开始说起，我绝不是这方面的专家。实际上，我只是在过去的两个星期里才开始使用 Sagas。如果你和我一样，当你读到这里的时候，你会不断地问自己，为什么会有人想用这个，这太过分了，你是绝对正确的。对于我正在做的事情来说，这有点矫枉过正了，但是说到底，传奇更好，更具可伸缩性，也更容易进行单元测试，这一点我们将在第六部分中看到。如果你不喜欢我的解释或例子，我理解。除了我在这里要用到的，还有更多关于传奇的东西。这里是 Redux Saga 文档的链接。祝你好运！— [但是，我从 Mozilla](https://medium.com/u/1ef30430402c#$ing 魔法，那是什么！</p><p id=)[](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)**那里偷来了一个更好的解释。**
> 
> **生成器是可以退出并在以后重新进入的功能。它们的上下文(变量绑定)将在重入时被保存。**
> 
> **调用生成器函数不会立即执行其主体；而是返回函数的一个[迭代器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#iterator)对象。当迭代器的`next()`方法被调用时，生成器函数的主体被执行，直到第一个`[yield](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield)`表达式(用`[yield*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield*)`指定从迭代器返回的值)委托给另一个生成器函数。`next()`方法返回一个对象，该对象的`value`属性包含生成的值，而`done`属性指示生成器是否以布尔值的形式生成了它的最后一个值。用一个参数调用`next()`方法将恢复生成器函数的执行，用来自`next()`的参数替换执行暂停的`yield`表达式。**
> 
> **生成器中的`return`语句，当被执行时，将使生成器`done`。如果一个值被`return` ed，它将作为`value`被传回。已经返回的生成器不会再生成任何值。**
> 
> ## **好吧，回到传说**
> 
> **我说到哪里了？啊，是的……saga 是作为生成器函数实现的，它为 redux-saga 中间件产生对象。产生的对象是一种由中间件解释的指令。当一个承诺让给中间件时，中间件将暂停该传奇，直到该承诺完成。一旦承诺被兑现，中间件将继续这个传奇，执行代码直到下一次让步。**
> 
> **Redux Saga 中间件在幕后为我们做了许多生成器魔术，让位于诸如 put、all、call 和 takeLatest 之类的 Saga 函数。**
> 
> **所发生的是，中间件检查每个产生的效果的类型，然后决定如何实现该效果。如果效果类型是一个`PUT`，那么它将向商店发送一个动作。如果效果是一个`CALL`，那么它将调用给定的函数。效果创建和效果执行的分离使得使用`next()`以一种令人惊讶的简单方式测试我们的生成器成为可能**
> 
> > **简而言之，**发生器**是像音乐轨道一样的暂停功能，而
> > **Sagas** 是按下轨道上暂停播放和停止按钮的 DJ。**
> 
> **聊够了，让我们开始吧！**
> 
> # **安装 Saga**
> 
> ```
> npm install redux-saga
> ```
> 
> **然后在`store/index.js`我们来连线一下佐贺。Saga 是一个 redux 中间件，所以我们将把它注入到我们的 redux applyMuiddleware 函数中，但是我们必须首先创建它。**
> 
> ```
> import { createStore, applyMiddleware } from 'redux'
> import { rootReducer } from './rootReducer'
> import reduxImmutableStateInvariant from 'redux-immutable-state-invariant'
> import thunk from 'redux-thunk'
> import { composeWithDevTools } from 'redux-devtools-extension';
> **import createSagaMiddleware from 'redux-saga'****const sagaMiddleware = createSagaMiddleware()**export function configureStore() {
>   return createStore(
>     rootReducer,
>     composeWithDevTools(
>       applyMiddleware(
>         **sagaMiddleware,**
>         thunk,
>         reduxImmutableStateInvariant()
>       )
>     )
>   )
> }
> ```
> 
> **到目前为止，这没有任何作用。我们将需要初始化的传奇，需要一些设置，然后我们才能这样做。我们将彻底改变目前的 thunk 设置。**
> 
> # **创建我们的 Saga 助手**
> 
> **我们将使用三个助手:**
> 
> 1.  ****createAsyncTypes**helper，它将生成一个带有挂起、成功和错误的类型对象。**
> 2.  ****createActions** helper，它将为我们生成我们的操作。**
> 3.  ****创建一个 Reducer** 助手，我们将使用它来替换我们的 switch 语句。**
> 
> **在`store`文件夹中创建一个名为 **Utilities.js** 的文件**
> 
> ```
> *///////////////////
> // ActionHelpers //
> ///////////////////* const asyncTypes = {
>   PENDING: 'PENDING',
>   SUCCESS: 'SUCCESS',
>   ERROR: 'ERROR'
> }export const **createAsyncTypes** = typeString =>
>   Object.values(asyncTypes).reduce((acc, curr) => {
>     acc[curr] = `${typeString}_${curr}`
>     return acc
>   }, {})export const **createAction** = 
>   (type, payload = {}) => 
>     ({ type, ...payload })*///////////////////
> // createReducer //
> ///////////////////* export const **createReducer** = 
>   (initialState, handlers) =>
>     (state = initialState, action) =>
>       handlers.hasOwnProperty(action.type)
>       ? handlers[action.type](state, action)
>       : state
> ```
> 
> **我可能会在未来重新审视这些，我还不是 100%赞成 **createReducers** 方法，但目前它是有效的，而且非常干净**
> 
> # **从沉思走向传奇**
> 
> ## **更新博客类型**
> 
> **打开`store/blog/types.js`**
> 
> **之前，我们只有两种类型，我们使用 BLOG_LOADING 来设置完成和错误的加载状态，使用 LOAD_BLOG_SUCCESS 来加载成功的数据。**
> 
> ```
> **--- BEFORE ---***/***
> ** Blog Types*
> **/*
> export const BLOG_LOADING = 'BLOG_LOADING'
> export const LOAD_BLOG_SUCCESS = 'LOAD_BLOG_SUCCESS'
> ```
> 
> **但是现在，我们将调用我们的新帮助来创建 AsyncTypes，这将自动创建我们的三个类型；挂起(加载)、成功(加载数据)、错误(加载完成)。**
> 
> ```
> **--- After ---***/***
> ** Blog Types*
> **/* **import** { createAsyncTypes } from './../Utilities'
> 
> *//Using ASYNC as a convention to know that I'll have three types.*
> **export** **const** GET_BLOG_ASYNC **=** createAsyncTypes('GET_BLOG')
> ```
> 
> **过一会儿这就更有意义了。**
> 
> ## **更新博客缩减器**
> 
> **打开`store\blog\reducer.js`**
> 
> **有两种方法可以做到这一点，我们可以简单地更新 switch 用例中的类型，使用我们刚刚创建的新类型名，就像这样。**
> 
> ```
> switch (action.type) {
>   case types.**GET_BLOG_ASYNC.PENDING**:
>     return {
>       ...state,
>       loading: true
>     }
>   case types.**GET_BLOG_ASYNC.SUCCESS**:
>     return {
>       ...state,
>       posts: action.posts,
>       loading: false
>     }
>   case types.**GET_BLOG_ASYNC.ERROR**:
>     return {
>       ...state,
>       loading: false
>     }
>   default:
>     return state
> }
> ```
> 
> **这是我们的 **createAsyncTypes** types 助手所做的一个例子。它生成了一个具有 3 个值的类型对象，使用 convention _ASYNC 我们知道 **GET_BLOG_ASYNC** 将包含挂起、成功和错误。**
> 
> **让我们通过使用 createReducer helper 并向它传递一个对象而不是一个 switch 语句来使它变得更加智能。**
> 
> **删除所有内容并粘贴到这里**
> 
> ```
> */**
> * Blog Reducer
> */* import initialState from './../initialState'
> import { createReducer } from './../Utilities'
> import * as types from './types'export default createReducer(initialState.blog, {
>   [types.GET_BLOG_ASYNC.PENDING](state) {
>     return {
>       ...state,
>       loading: true
>     }
>   },
>   [types.GET_BLOG_ASYNC.SUCCESS](state, action) {
>     return {
>       ...state,
>       posts: action.posts,
>       loading: false
>     }
>   },
>   [types.GET_BLOG_ASYNC.ERROR](state) {
>     return {
>       ...state,
>       loading: false
>     }
>   }
> })
> ```
> 
> **是的，我知道你的想法，我也同意。这看起来确实比 switch 语句更令人困惑，但却同样清晰。实际上，我花了一分钟才明白这个对象到底在做什么，因为我以前从来没有这样写过。它创建了一个每个都等于的对象和一个匿名函数，但是它用方括号来设置键。**
> 
> ```
> obj = {
>   key: fn,
>   [key]fn
> }
> ```
> 
> **或者您可以使用箭头函数，您将需要一个冒号箭头和对象周围的括号。**
> 
> ```
> export default createReducer(initialState.blog, {
>   [types.GET_BLOG_ASYNC.PENDING]**:**(state) **=> (**{
>     ...state,
>     loading: true
>   }**)**,
>   [types.GET_BLOG_ASYNC.SUCCESS]:(state, action) => ({
>     ...state,
>     posts: action.posts,
>     loading: false
>   }),
>   [types.GET_BLOG_ASYNC.ERROR]:(state) => ({
>     ...state,
>     loading: false
>   })
> })
> ```
> 
> **无论你选择哪种方式都是好的，让我说我还没有 100%同意*和*，但我认为这有助于在未来创造一个异步减速器创造者；)**
> 
> ## **创造我们的第一个传奇**
> 
> **在我们的`store\blog`文件夹中创建一个名为 **sagas.js** 的文件**
> 
> ```
> */**
> * Blog Sagas
> */* import * as contentful from 'contentful'
> import { all, call, put, takeLatest } from 'redux-saga/effects'
> import { actions } from './../Blog'
> import * as types from './types'const client = contentful.createClient({
>   space: 'qu10m4oq2u62',
>   accessToken: 'f4a9f68de290d53552b107eb503f3a073bc4c632f5bdd50efacc61498a0c592a'
> })const fetchPosts = () => client.getEntries()function* getBlogPosts() {
>   try {
>     const posts = yield call(fetchPosts)
>     yield put(actions.success(posts.items))
>   } catch (e) {
>     console.log(e)
>     yield put(actions.error(e))
>   }
> }export default function* () {
>   yield all([
>     takeLatest(types.GET_BLOG_ASYNC.PENDING, getBlogposts)
>   ])
> }
> ```
> 
> **那么，这是在做什么？好吧，让我们从显而易见的开始。**
> 
> ****客户**是我与我的心满意足的联系**
> 
> ****fetchPosts** 调用我们的 to Contentful 来检索我的帖子并作为承诺返回它们**
> 
> **getBlogPosts 是我们真实的传奇故事。它首先产生对 fetchPosts 的调用，saga magic 启动并暂停该函数，等待 Contentful 返回数据。然后，一旦数据被成功返回，而不是返回一个承诺，它实际上将数据返回给`const posts`并在后台调用`next()`，后者告诉函数再次播放。然后它调用`yield put`,后者神奇地分派带有 post 项的动作。如果`fetchPosts()`失败，抛出一个异常来触发 catch。**
> 
> ****导出默认函数*** 是连接到 Redux Saga 中间件的动作观察器。这类似于触发动作，除了不是调用动作和将动作分派给 reducer，而是 saga 观察要调用的动作，然后截取并调用 saga。takeLatest 告诉 Redux Saga 停止任何以前正在运行的 Saga 任务，如果还在运行，则运行一个新任务。**
> 
> **`takeLatest(types.GET_BLOG_ASYNC.PENDING, getBlogPosts)`表示当**类型动作时。GET _ 博客 _ 异步。挂起**被调用，停止任何之前调用的可能没有完成的 getBlogPosts 任务，调用一个新的 **getBlogPosts()** 函数。**
> 
> **`yield all`函数是一种容纳博客传奇所有观察者的容器。如果我们有更多的动作，我们可以创造更多的传奇和创造更多的观察者来调用这些传奇，这些额外的观察者将加入到所有的功能中。**
> 
> **然后，我们导出默认函数，这将创建 gets 注入到绑定到 rootSaga 中的 sagaMiddleware.run 函数。**
> 
> ## **建立根传奇**
> 
> **我们需要将所有的 sagas 添加到 sagaMiddleware 中，这样我们就可以在应用加载时启动所有的观察器。**
> 
> **在`store`文件夹中，创建一个名为 **rootSaga.js** 的文件**
> 
> ```
> import blog from './blog/sagas'const sagas = [
>   blog
> ]export const initSagas = (sagaMiddleware) =>
>   sagas.forEach(sagaMiddleware.run.bind(sagaMiddleware))
> ```
> 
> **现在，我们只有一个传奇。如果我们用 Saga 的设置了 Medium store，那么我们将导入 Medium 并将其传递给 sagas 数组。**
> 
> > ****注意:**我们不需要从我们的 **blog/saga.js** 文件中导入每一个函数，只需要 t 个动作观察器，我们将它们封装在一个 all 函数中，就像你的传奇故事的一个减速器。**
> 
> ## **行动，博客的新面貌**
> 
> **现在，我们有了一些可以观察我们行为的传奇，我们需要更新我们现在的数据行为。**
> 
> **如果你一直在阅读，你知道我遵循[分形文件结构](https://hackernoon.com/building-a-website-with-react-and-bulma-d655214bff2a)，在**商店的情况下**意味着`store/Blog.js`是我的公共应用程序面向代码，而`store/blog`文件夹中的所有东西都是商店的私有。应用程序不会调用传奇，而是调用动作。`store/Blog.js`目前包含我们的 thunks，但这些不再是必要的，所以我们可以删除它们，并用应用程序将调用的触发我们的传奇的动作来替换它们。然后我们可以从我们的`store/blog`文件夹中删除`actions.js`文件。**
> 
> **这里是`Blog.js`文件的前后**
> 
> ```
> **--- Before ---**import * as contentful from 'contentful'
> import * as actions from './blog/actions'const client = contentful.createClient({
>   space: 'qu10m4oq2u62',
>   accessToken: 'f4a9f68de290d53552b107eb503f3a073bc4c632f5bdd50efacc61498a0c592a'
> }) export function loadBlog() {
>   return dispatch => {
>     dispatch(actions.blogLoading())
>     return client.getEntries()
>       .then(({items}) => {
>         dispatch(actions.loadBlogSuccess(items))
>       })
>       .catch(error => {
>         console.log(error)
>         dispatch(actions.blogLoading(false))
>       })
>   }
> }
> ```
> 
> **我们去掉了所有的逻辑，因为这是 Saga 现在的工作，用一个我们的应用程序可以调用的简单动作对象来代替它。**
> 
> ```
> **--- After ---**import { createAction } from './Utilities'
> import * as types from './blog/types'export const actions = {
>   pending: () => createAction(types.GET_BLOG_ASYNC.PENDING),
>   success: (posts) => createAction(types.GET_BLOG_ASYNC.SUCCESS, { posts }),
>   error: (error) => createAction(types.GET_BLOG_ASYNC.ERROR, { error })
> }
> ```
> 
> ****createAction** 函数除了通过 make 清理我们的代码之外，并没有做更多的事情，因此不管有多少东西实际上作为有效载荷传入，这个函数总是接受 2 个参数。我认为在未来，我们可以用它来帮助自动化我们的一些异步 CRUD 过程。**
> 
> ## **最后一件事，初始化传奇**
> 
> **在我们的`store/index.js`**
> 
> ```
> ...
> import createSagaMiddleware from 'redux-saga'
> **import { initSagas } from './rootSaga'**const sagaMiddleware = createSagaMiddleware()export function configureStore() {
>  **const store =** createStore(
>     rootReducer,
>     composeWithDevTools(
>       applyMiddleware(
>         sagaMiddleware,
>         thunk,
>         reduxImmutableStateInvariant()
>       )
>     )
>   )
>   **initSagas(sagaMiddleware)
>   
>   return store**
> }
> ```
> 
> **最初，我们返回 createStore，但是 initSagas 需要先初始化 sagaMiddleware。因此，我们需要创建商店，然后将 sagaMiddleware 传递给 initSages，这将启动我们所有的观察器。**
> 
> **相反，在应用程序根索引中做一些复杂的事情来调用 initSagas，我们可以只设置一个 Store 变量来创建 store，然后在返回 store 之前调用 initSagas。**
> 
> ## **就是这样！**
> 
> **当然，我只是刚刚触及了 Sagas 能做什么的表面，这是一个很好的入门书，让他们设置和实际工作。**
> 
> # **让我们回顾一下**
> 
> *   **我们安装了传奇**
> *   **我们为类型和归约器创建了一些助手。**
> *   **更新了我们的类型**
> *   **更新了我们的减速器，学习了一些新技术**
> *   **创造了我们的第一个传奇**
> *   **建立根**
> *   **将我们的行动转移到公共类**
> *   **在加载的应用程序上初始化我们的传奇**
> 
> **是的，这要复杂得多，而且有相当多的额外设置，而且有一个循序渐进的学习曲线，而且…你明白了。但是，这最终是一个更好的解决方案。它更干净，更可伸缩，更可控，它是实时可用的，并且它非常容易测试，我们将在我的文章中看到😉**
> 
> > ****下一个** —用 Redux Sagas 编写 React 博客的单元测试**