# 教程:使用 React、Redux 和 Next.js 进行 SSR 分割测试和分析

> 原文：<https://medium.com/hackernoon/tutorial-ssr-split-testing-and-analytics-with-react-redux-and-next-js-5392799c15c7>

![](img/b7e4017738770a20ac36b5d7a4ac4ca1.png)

Eric Reis 的《精益创业》为我们企业的成功开出了药方:建立、**测量**，学习，重复。这篇文章主要关注“测量”部分。

**分割测试和分析不是特性，而是需求。**

如果你连用户在做什么都不知道，你怎么能知道任何东西在工作呢？如果不进行测量，您如何知道一种方法是成功还是失败？

> “当推出一个新的漏斗时，你不一定期望它马上起作用——你是在购买数据。”—鲁达·克里希纳

不仅是分割测试和分析需求；他们也是均衡器。他们可以帮助你的团队消除“河马”或高薪人士的看法。

这就是说，尽管有证据表明一个解决方案是强有力的，但薪酬最高的人的意见仍然是最终得到实施的东西……因为他们是这么说的，而且他们赚的比你多。显然，更高的“波段”意味着更高的智力…😜

**我更喜欢更科学的方法:*一切都是假设，除非数据显示并非如此。***

为此，我已经写了很多分测试，而且，我认为很多人真的把事情过分复杂化了。

我想展示使用 Redux 和 Next.js 添加自定义 SSR 分割测试是多么简单。

**在本教程中，我们将从一个空的 Node.js 项目开始，并通过创建自定义 Redux 分析中间件，使用 Next.js、Redux 和无缝分析构建简单的分割测试功能。**

**这是一举两得。作为对 Redux 和 Next.js 的介绍，以及如何使用它们构建一个实际有用的特性。**

对于那些不熟悉这些技术的人来说:

**Redux:** Redux 是 JavaScript 应用的可预测状态容器。它帮助您编写行为一致、在不同环境(客户机、服务器和本机)中运行、易于测试的应用程序。
**React** :用于构建用户界面的 JavaScript 库
**Next.js:** 服务器渲染或静态导出 React 应用的框架

**注:**这是用 next@5 写的——用 next@6 破教程。教程中的概念是重要的部分，可以应用于任何框架或语言。我已经在安装命令中添加了版本号，所以您仍然可以遵循。

# **1。我们将从一个全新的 Node.js 项目**开始

```
mkdir split-test
cd split-test
echo "node_modules\n.next" | tee .gitignore
npm init
```

# 2.**安装并配置 Next.js**

```
npm install --save next@5 react@16 react-dom@16# and open up your code editor. I'm using VSCode.
code .
```

为了完成下一步的设置，我们需要将以下内容添加到`package.json`

```
"scripts": {
 "dev": "next",
 "build": "next build",
 "start": "next start"
}
```

接下来，我们需要一个索引页面。从根目录创建一个名为`pages`的文件夹，并在其中创建一个文件`index.js`

**。/pages/index.js**

```
export default () => <div>Welcome to next.js!</div>
```

如果你不熟悉 Next.js，它是一个围绕 Webpack 构建的固执己见的框架。`pages`目录中的一切都是新的`entrypoint`，也就是`code-split`。

您现在可以使用`npm run dev`运行您的应用程序。默认情况下启用热代码重载。请访问 localhost:3000 查看您的进度！

但是如果这个页面写着“欢迎来到我的 next.js”会转换得更好呢？我们继续吧。

# **3。配置冗余**

Redux 将管理应用程序的状态，包括哪些实验当前是活动的。

## **安装 Redux**

```
npm i --save redux@3 react-redux@5 next-redux-wrapper@1
```

接下来，让我们创建一个初始化 Redux 的模块。姑且称之为`initRedux.js`，存放在一个新文件夹`lib`。

**。/lib/initRedux.js**

```
import {
  createStore,
  combineReducers,
  applyMiddleware,
  compose
} from 'redux'let reducers = {}
let reduxStore = null// The following checks if the Redux DevTools extension 
// is available in your browser, and activates it if so.
// Otherwise, it executes a no-op function
let devtools = f => f
if (process.browser && window.__REDUX_DEVTOOLS_EXTENSION__) {
  devtools = window.__REDUX_DEVTOOLS_EXTENSION__()
}function create (initialState = {}) {
  return createStore(
    combineReducers({ // Setup reducers
      ...reducers
    }),
    initialState, // Hydrate the store with server-side data
    devtools
  )
}export default function initStore (initialState) {
  // Make sure to create a new store for every server-side request so that data
  // isn't shared between connections (which would be bad)
  if (!process.browser) {
    return create(initialState)
  }

  // Reuse store on the client-side
  if (!reduxStore) {
    reduxStore = create(initialState)
  } return reduxStore
}
```

在上面的文件中，我们导出了一个默认函数`initStore`，它将使用提供的`initialState`创建商店。因为 Next.js 是**同构的**，这意味着代码将在客户端和服务器端运行。在`initStore`中，我们还检查该进程是否正在浏览器中运行。如果是，我们可以重用在服务器上创建的`reduxStore`。

无论哪种情况，接下来都会调用`create`。这用我们的 reducers、initialState 和 Redux DevTools 创建了一个新的 redux store，用于更好的开发实验。

## 接下来，让我们将 Redux“连接”到我们的索引页面。

**。/pages/index.js**

```
import initStore from '../lib/initRedux'
import withRedux from 'next-redux-wrapper'const Index = () => <div>Welcome to next.js!</div>const mapStateToProps = () => ({})
const mapDispatchToProps = () => ({})export default withRedux(initStore, mapStateToProps, mapDispatchToProps)(Index)
```

我们通过用`withRedux`包装组件来修改`./pages/index.js`。`withRedux`接受一个返回 redux store 的函数作为它的第一个参数，并接受两个额外的函数返回一个对组件可用的`props`对象。我们将很快填写这些内容。目前，我们已经成功连接了 Redux。访问 localhost:3000，将显示 [Redux DevTools chrome 扩展](http://extension.remotedev.io/)已启用。如果您还没有安装它，请安装它。

# 4.实验缩减器

Reducers 是存储应用程序逻辑的地方。动作进来，reducer 决定这个动作如何改变状态。

我们先在 lib 中新建一些文件夹，分别叫做`redux`和`redux/reducers`，在新建的`reducers`文件夹中定义一个文件`experiments.js`。

**。/lib/redux/reducers/experiments . js**

```
export default (state = {
  active: {}
}, { type, payload }) => {
  switch (type) {
    case 'START_EXPERIMENT':
      let { name, variant } = payload
      let active = Object.assign({}, state.active)
      active[name] = variant
      return {
        active
      }
    default:
      return state
  }
}export function startExperiment ({name, variant}) {
  return {
    type: 'START_EXPERIMENT',
    payload: {
      name,
      variant
    }
  }
}
```

我们的整个缩减器非常简单——它导出一个初始状态为`{ active: {} }`的缩减器。我们的应用程序真正需要知道的是哪些实验是活动的，所以这就足够了。

接下来，我们为缩减器的`switch`语句定义了一个`case 'START_EXPERIMENT'`。这将为我们用实验的**名称**定义的`active`状态添加一个新的键，该值将是活动的**变量**。

最后，我们导出一个*动作* `startExperiment`。这需要一个配置对象，并期望一个实验`name`，以及哪个`variant`是活动的。

**。/lib/redux/reducers/index . js**

```
import experiments from './experiments'export default {
  experiments
}
```

每当我们有新的减速器时，我们都可以将其添加到此导出中。我们只需要把它连接起来。

**。/lib/initRedux.js**

在`./lib/initRedux.js`中更换管路:

```
let reducers = {}
```

使用:

```
import reducers from './redux/reducers'
```

**Redux 现在与我们的减速器“连接”在一起。**

接下来，我们需要利用 Next.js 的`getInitialProps`在服务器端开始实验。

# 5.`dispatch`来自`getInitialProps`

在`const Index = ...`行下面，我们来添加一个`getInitialProps`函数。当我们使用`withRedux`包装我们的组件时，这使得 Redux `store`在传递给`getInitialProps`的上下文对象中是可访问的。我们需要调用我们在 reducer 中定义的动作，所以也在顶部导入它。

**。/pages/index.js**

```
import { startExperiment } from '../lib/redux/reducers/experiments'// .../* context: {req, res, query, isServer, store} */
Index.getInitialProps = ({store}) => {
  const dispatchStartExperiment = ({name, variant}) => {
    store.dispatch(startExperiment({
      name,
      variant
    }))
  }
}// ...
```

好了，我们已经定义了一个函数来分派一个`startExperiment`动作，并接受实验**名称**和**变量**作为参数。

接下来我们需要实验。我们自然会用 JavaScript 来实现。

# **6。创建新实验()**

我们将创建一个实验类，扩展`EventEmitter`作为一种简单的方法来挂钩每个实验的状态变化。

首先是实验班。

在其中，一个给定变量列表和 id 的`selectVariant`算法将总是选择相同的变量。也就是说，要确保用户总是得到相同的实验，你需要做的就是传入相同的 id。您可以通过在 cookie 中存储他们的 userId 或其散列来做到这一点。这个算法是我从 [react-ab-test](https://github.com/pushtell/react-ab-test/blob/master/src/Experiment.jsx#L51) 借来的。

我认为使用 React 组件来维护实验的状态过于复杂，redux 更适合这项任务，但“支持”算法 [John Wehr](https://medium.com/u/1b78fc47a4ad?source=post_page-----5392799c15c7--------------------------------) 。👏

**。/lib/experience . js**

```
import { EventEmitter } from 'events'
import crc32 from 'fbjs/lib/crc32'export default class Experiment extends EventEmitter {
  constructor ({
    name,
    variants,
    userId
  }) {
    super()
    this.name = name
    this.variants = variants
    this.userId = userId
  }selectVariant (userId) { /*
    Choosing a weighted variant:
      For C, A, B with weights 2, 4, 8
      variants = A, B, C
      weights = 4, 8, 2
      weightSum = 14
      weightedIndex = 9
      AAAABBBBBBBBCC
      ========^
      Select B
    */ // Sorted array of the variant names, example: ["A", "B", "C"]
    const variants = Object.keys(this.variants).sort() // Array of the variant weights, also sorted by variant name. For example, if
    // variant C had weight 2, variant A had weight 4, and variant B had weight 8
    // return [4, 8, 2] to correspond with ["A", "B", "C"]
    const weights = variants.reduce((weights, variant) => {
      weights.push(this.variants[variant].weight)
      return weights
    }, []) // Sum the weights
    const weightSum = weights.reduce((a, b) => {
      return a + b
    }, 0) // A random number between 0 and weightSum
    let weightedIndex = typeof userId === 'string' ? Math.abs(crc32(userId) % weightSum) : Math.floor(Math.random() * weightSum) // Iterate through the sorted weights, and deduct each from the weightedIndex.
    // If weightedIndex drops < 0, select the variant. If weightedIndex does not
    // drop < 0, default to the last variant in the array that is initially assigned.
    let selectedVariant = variants[variants.length - 1]
    for (let index = 0; index < weights.length; index++) {
      weightedIndex -= weights[index]
      if (weightedIndex < 0) {
        selectedVariant = variants[index]
        break
      }
    } return selectedVariant
  } start ({ userId }) {
    userId = userId || this.userId
    let variant = this.selectVariant(userId)
    this.emit('variant.selected', { name: this.name, variant })
  }
}
```

让我们用它来做一个实验！

**。/experiments/headerText.js**

```
import Experiment from '../lib/Experiment'const headerTextExperiment = new Experiment({
  name: 'Header Text',
  variants: {
    control: {
      weight: 50,
      displayName: 'control'
    },
    mine: {
      weight: 50,
      displayName: 'mine'
    }
  }
})export default headerTextExperiment
```

在我们定义的实验类中，我们只需要传入一个带有一些初始化选项的对象:1)实验的名称，以及 2)变量及其各自的权重。在本例中，我们有两种变体，对半分割。

# 7.激活实验

我们想激活**上的实验。/pages/index.js，**所以让我们导入它，并在 getInitialProps 中激活它。正如我前面提到的，如果 userId 存在，我们将希望传入一个 userId，所以为了演示起见，我们假设它在 cookies 中是可用的。

```
npm install --save next-cookies@1
```

**。/pages/index.js**

```
import initStore from '../lib/initRedux'
import withRedux from 'next-redux-wrapper'
import { startExperiment } from '../lib/redux/reducers/experiments'
**import headerTextExperiment from '../experiments/headerText'
import cookies from 'next-cookies'****const Index = ({experiments}) => (
  <div>
    { experiments.active[headerTextExperiment.name] === 'control' ? "Welcome to Next.js!" : null }
    { experiments.active[headerTextExperiment.name] === 'mine' ? "Welcome to MY Next.js!" : null }
  </div>
)**/* context: {req, res, query, isServer, store} */
**Index.getInitialProps = (ctx) => {
  const { store } = ctx**
  const dispatchStartExperiment = ({name, variant}) => {
    console.log('starting experiment')
    store.dispatch(startExperiment({
      name,
      variant
    }))
  } **const activeExperiments = [
    headerTextExperiment
  ]** **headerTextExperiment.once('variant.selected', dispatchStartExperiment)** **let { userId } = cookies(ctx)
  activeExperiments.forEach((experiment) => {
    experiment.start({userId})
  })**
}**const mapStateToProps = ({ experiments }) => ({
  experiments
})**
const mapDispatchToProps = () => ({})export default withRedux(initStore, mapStateToProps, mapDispatchToProps)(Index)
```

为了清楚起见，我用粗体字标出了变化。首先，我们导入`headerTextExperiment`和`cookies`。`cookies`需要 next 的上下文对象，所以快速重构 getInitialProps 以在顶部暴露它。然后，我们创建一个要激活的实验数组，并使用 forEach 来启动每个实验，如果可用的话，传入一个`userId`。

实验启动后，会选择一个变量，并发出一个事件:`'variant.selected'`。当发生这种情况时，我们将向 redux 发送一个动作，该动作将更新全局状态。当状态改变时，`mapStateToProps`将被触发，让我们的组件通过它的`props`访问正在进行的实验。

如果实验的变体“control”是活动的，我们显示原始文本，如果变体“mine”是活动的，我们显示替代文本。

# 8.使用分析中间件跟踪事件

因为我们使用 redux 来改变我们的应用程序的状态，所以我们可以挂钩到它，并在 redux 动作发生时发出分析事件。为此，我们将创建一个定制的分析中间件。我将展示如何与 google analytics 以及 facebook analytics 集成。你可以用你的想象力进行其他的整合。我特别喜欢 Mixpanel 的这种类型的分析。

**。/lib/analytics.js**

```
export const track = ({event, value}) => {
  console.log('track', event, {
    value
  }) if (process.browser) {
    window.ga && window.ga('send', 'event', {
      eventCategory: event,
      eventLabel: value
    })
    window.fbq && window.fbq('track', event, {
      value
    })
  }
}
```

**。/lib/redux/middleware/analytics . js**

```
import { track } from '../../analytics'export default ({ dispatch, getState }) => next => action => {
  const {
    analytics
  } = action.meta || {}next(action)if (analytics) {
    track(analytics)
  }
}
```

**。/lib/initRedux.js**

```
**import analyticsMiddleware from './redux/middleware/analytics'**// ...function create (initialState = {}) {
  return createStore(
    combineReducers({ // Setup reducers
      ...reducers
    }),
    initialState, // Hydrate the store with server-side data
    **compose(
      applyMiddleware(
        analyticsMiddleware
      ),
      devtools
    )**
  )
}
```

我们使用`compose`和`applyMiddleware`是为了使用多个中间件以及 Redux DevTools。

现在我们可以简单地在 redux 动作中添加一个`meta`键，以便在它们发生时跟踪它们。

**。/lib/redux/reducers/experiments . js**

```
// ...export function startExperiment ({name, variant}) {
  return {
    type: 'START_EXPERIMENT',
    payload: {
      name,
      variant
    }**,**
    **meta: {
      analytics: {
        event: `${name} Experiment Played`,
        value: variant
      }
    }**
  }
}
```

现在，每次调度 startExperiment 时，一个事件“NAME Experiment Played”将被发送到您的具有所选变体的分析端点。

# 9.跟踪其他事件

但是，按照设计，此操作不会在客户端发生。只有服务器。

让我们添加一个特殊的异常，以便在组件装载到客户机上时也跟踪这个事件，因为我们需要这个数据。有很多很好的理由需要在服务器和客户端进行分析。例如，查看有多少人请求了一个页面，但是在它加载之前因为某种原因而放弃了。

我们需要一些生命周期事件，所以这导致了一些重构，以使 Index extend React 的组件，以及将 getInitialProps 移入其中。

**。/pages/index.js**

```
// ...
**import { Component } from 'react'
import { track } from '../lib/analytics'****class Index extends Component {
  static getInitialProps (ctx) {**
    /* ctx: {req, res, query, isServer, store} */
    const { store } = ctx
    const dispatchStartExperiment = ({name, variant}) => {
      store.dispatch(startExperiment({
        name,
        variant
      }))
    } const activeExperiments = [
      headerTextExperiment
    ]

    headerTextExperiment.once('variant.selected', dispatchStartExperiment)

    let { userId } = cookies(ctx)
    activeExperiments.forEach((experiment) => {
      experiment.start({userId})
    })
  **}** **componentDidMount () {
    const { experiments } = this.props** **if (experiments.active[headerTextExperiment.name]) {
      // startExperiment just returns the redux action object
      // we can make use of this to look up the analytics
      // event name and value
      let analytics = startExperiment({
        name: headerTextExperiment.name,
        variant: experiments.active[headerTextExperiment.name]
      }).meta.analytics
      track({
        event: analytics.event, 
        value: analytics.value
      })
    }
  }** **render () {
    const { experiments } = this.props
    return** (
      <div>
        { experiments.active[headerTextExperiment.name] === 'control' ? "Welcome to Next.js!" : null }
        { experiments.active[headerTextExperiment.name] === 'mine' ? "Welcome to MY Next.js!" : null }
      </div>
    )
  **}**
**}**// ...
```

新的`componentDidMount`功能只在客户端运行。如果一个实验是活动的，我们只是从 redux 动作对象中用相同的值调用`track`。

# 10.包括第三方分析库

我们可以简单地从任何提供的 snippet 的`<script>`标签中提取 javascript，并将它们存储为纯 js。接下来，我们将利用 React 的`dangerouslySetInnerHTML` API 使用`next/head`来插入它们。

**。/pages/index.js**

```
**import ga from '../lib/analytics/ga'
import fb from '../lib/analytics/fb'
import Head from 'next/head'**class Index extends Component {
  // ... render () {
    const { experiments } = this.props
    return (
      <div>
        **<Head>
          <title>SSR Split Tests</title>
          <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
          <script dangerouslySetInnerHTML={{__html: ga}} />
          <script dangerouslySetInnerHTML={{__html: fb}} />
        </Head>**
        { experiments.active[headerTextExperiment.name] === 'control' ? "Welcome to Next.js!" : null }
        { experiments.active[headerTextExperiment.name] === 'mine' ? "Welcome to MY Next.js!" : null }
      </div>
    )
  }
}// ...
```

**。/lib/analytics/fb.js**

```
export default `
if(typeof fbq === 'undefined') {
  !function(f,b,e,v,n,t,s){if(f.fbq)return;n=f.fbq=function(){n.callMethod?
  n.callMethod.apply(n,arguments):n.queue.push(arguments)};if(!f._fbq)f._fbq=n;
  n.push=n;n.loaded=!0;n.version='2.0';n.queue=[];t=b.createElement(e);t.async=!0;
  t.src=v;s=b.getElementsByTagName(e)[0];s.parentNode.insertBefore(t,s)}(window,
  document,'script','[https://connect.facebook.net/en_US/fbevents.js'](https://connect.facebook.net/en_US/fbevents.js'));
  fbq('init', '**YOUR FB ID GOES HERE**');
  fbq('track', 'PageView');
} else {
  fbq('track', 'PageView');
}
`
```

**。/lib/analytics/ga.js**

```
export default `
  (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
  (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
  m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
  })(window,document,'script','[https://www.google-analytics.com/analytics.js','ga'](https://www.google-analytics.com/analytics.js','ga'));
  ga('create', '**YOUR GA ID GOES HERE**', 'auto');
  ga('send', 'pageview');
`
```

# 11.如果有目标，会有帮助

此外，我们需要一个目标。现在，我们只知道一个实验何时被观看。

我最初为一个登录页面做了所有这些，所以让我们继续这个例子。所以我们这个页面的目标是让用户输入他们的电子邮件地址并提交它！

当这种情况发生时，我们希望将他们的电子邮件存储在我们的 redux store 中，并说声谢谢。这也展示了未接触的`mapDispatchToProps`和使用多个减速器。

似乎我们是如何跟踪**线索**的，让我们做一个`lead`减速器。

**。/lib/redux/reducers/lead . js**

```
export default (state = {
  email: null
}, { type, payload }) => {
  switch (type) {
    case 'SIGNUP_LEAD':
      let { email } = payload
      return {
        ...state,
        email
      }
    default:
      return state
  }
}export function signupLead ({email}) {
  return {
    type: 'SIGNUP_LEAD',
    payload: {
      email
    },
    meta: {
      analytics: {
        event: `Signed Up`,
        value: email
      }
    }
  }
}
```

**。/lib/redux/reducers/index . js**

```
import experiments from './experiments'
import lead from './lead'export default {
  experiments,
  lead
}
```

接下来，我们需要一个新的组件来包含注册表单，并显示在索引页面上。

**。/components/SignUpForm.js**

```
export default function SignUpForm () {
  function submit(e) {
    e.preventDefault()
    let email = e.target.elements.email.value
    if (email) {
      alert(email)
    } else {
      alert("Email is Required")
    }
  }
  return (
    <form onSubmit={submit}>
      <input name="email" type="email" placeholder="Enter your email..." />
      <button>Submit</button>
    </form>
  )
}
```

**。/pages/index.js**

```
**import SignUpForm from '../components/SignUpForm'**// ...  
  render () {
    const { experiments } = this.props
    return (
      <div>
        <Head>
          <title>SSR Split Tests</title>
          <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
          <script dangerouslySetInnerHTML={{__html: ga}} />
          <script dangerouslySetInnerHTML={{__html: fb}} />
        </Head>
        { experiments.active[headerTextExperiment.name] === 'control' ? <h1>Welcome to Next.js!</h1> : null }
        { experiments.active[headerTextExperiment.name] === 'mine' ? <h1>Welcome to MY Next.js!</h1> : null }
        **<SignUpForm/>**
      </div>
    )
  }
// ...
```

# 12.将 redux 连接到表单

我们的表单需要访问我们定义的 redux 动作，以及来自我们存储的`lead`状态。我们可以使用`mapDispatchToProps`和`mapStateToProps`来提供访问。

**。/pages/index.js**

```
// ...
**import { signupLead}  from '../lib/redux/reducers/lead'**class Index extends Component {
  // ... render () {
    const { experiments**, lead, signupLead** } = this.props
    return (
      <div>
        <Head>
          <title>SSR Split Tests</title>
          <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
          <script dangerouslySetInnerHTML={{__html: ga}} />
          <script dangerouslySetInnerHTML={{__html: fb}} />
        </Head>
        { experiments.active[headerTextExperiment.name] === 'control' ? <h1>Welcome to Next.js!</h1> : null }
        { experiments.active[headerTextExperiment.name] === 'mine' ? <h1>Welcome to MY Next.js!</h1> : null }
 **<SignUpForm lead={lead} signup={signupLead}/>**      </div>
    )
  }
}const mapStateToProps = ({ experiments**, lead** }) => ({
  experiments,
  **lead**
})
**const mapDispatchToProps = (dispatch, ownProps) => ({
  signupLead: ({email}) => {
    dispatch(signupLead({email}))
  }
})**// ...
```

在上面的例子中，我们只是简单地导入新动作，然后用`mapDispatchToProps`中的`dispatch`将它映射到道具。我们还修改了`mapStateToProps`来访问`lead`状态。

最后，让我们使用新的道具来完成表格！

**。/components/SignUpForm.js**

```
export default function SignUpForm (**{lead, signup}**) {
  function submit(e) {
    e.preventDefault()
    let email = e.target.elements.email.value
    if (email) {
      **signup({email})**
    } else {
      alert("Email is Required")
    }
  }
  return (
    <div>
      **{typeof lead.email === 'string' && lead.email.length > 0 ? 
      <p>Hello {lead.email}</p>
      :
      <form onSubmit={submit}>
        <input name="email" type="email" placeholder="Enter your email..." />
        <button>Submit</button>
      </form>
      }**
    </div>
  )
}
```

现在，当按下 submit 时，输入框将切换到 say `Hello {lead.email}`并以 email 为值跟踪事件“已注册”。

现在，在您的漏斗分析中，您可以进行两个查询:

1.  从值为“控制”的“标题文本试验播放”开始
2.  从值为“mine”的“标题文本试验播放”开始

您将能够很容易地看到通过每个实验的每个步骤的用户的百分比。

# 结论🎉

现在你有了，SSR 分裂测试(大部分)自动分析(只要你继续使用 redux)！

感谢阅读！下次见！如果你觉得这有用，请鼓掌并分享，因为它将帮助我接触到更多的人！:)

你可以在 GitHub 库中找到所有的代码。请随意使用它作为样板！

帕特里克·斯科特

—

> 没有无用的 AWS 认证，有兴趣听听我的 DevOps 之旅吗？ [*现在就在 HackerNoon*](https://hackernoon.com/my-journey-to-achieving-devops-bliss-without-useless-aws-certifications-a7cbf7c539d1) *上看。*

**我有空咨询——在** [**Twitter**](https://twitter.com/pat_scott) **或**[**LinkedIn**](https://www.linkedin.com/in/patricklscott/)**上给我发消息。请提及你看到了我的文章！不要害羞！**

*想了解如何使用微服务构建自定义分析后端吗？在我即将开始的课程“* [*微服务驱动*](http://www.microservicedriven.com/get-access) *”中，我就是这么做的。从* ***这个*** *例子开始，你将学习如何构建一个用于跟踪分析和线索的微服务后端，并使用 Docker Swarm 在生产中运行它。* [*现在就报名吧！*](http://www.microservicedriven.com/get-access)