# 反应/减少类固醇的发展

> 原文：<https://medium.com/hackernoon/react-redux-development-on-steroids-95dfed7e7a85>

## 在本文中，我将展示使用[](https://github.com/evheniy/redux-lazy)**开发 React / Redux 应用程序有多快。**

**![](img/b2e36ce324002c5b6cb46edbe918b9c1.png)**

**让我分享一下我之前关于**的文章链接:****

**[](https://hackernoon.com/react-redux-for-lazy-developers-b551f16a456f) [## React —为懒惰的开发人员提供 redux

### 每次在 react 应用程序中使用 redux 时，我们都要花费大量时间来创建动作类型、动作创建者、减少者…大多数…

hackernoon.com](https://hackernoon.com/react-redux-for-lazy-developers-b551f16a456f) [](https://hackernoon.com/react-redux-for-lazy-developers-part-2-d0c60123592f) [## React —为懒惰的开发人员提供 redux。第二部分

### 在本文中，我将继续讨论使用 redux-lazy 创建 react redux 应用程序。

hackernoon.com](https://hackernoon.com/react-redux-for-lazy-developers-part-2-d0c60123592f) [](https://hackernoon.com/react-redux-for-lazy-developers-part-3-319b639a22c3) [## React —为懒惰的开发人员提供 redux。第三部分

### 这是关于创建 react redux 应用程序系列的最后一部分。

hackernoon.com](https://hackernoon.com/react-redux-for-lazy-developers-part-3-319b639a22c3) 

[**Redux Lazy**](https://github.com/evheniy/redux-lazy) 是 redux stuff(动作类型、动作创建者、还原者、容器……)的包装器。就像为 redux 重新作曲一样。

如果你正在使用 [**重组**](https://github.com/acdlite/recompose) 你就知道这是一个有用的高阶组件包(HOC)。但是在使用其他开发人员创建的 HOC 之前，您应该知道如何创建一个简单的 React 组件，如何将其与其他组件组合，以及如何创建自己的 HOC。

Redux Lazy 也一样。这不是一个新的商店图书馆。这只是一个包装器，用一点点魔法(是的，我知道在代码中使用魔法不是一个好的做法)你可以在创建 React / Redux 应用程序上节省高达 50%的时间。

在开始描述 Redux Lazy 之前，我想展示使用 Redux 的主要部分。

Redux 是一家商店。您可以阅读大量手册和文章，其中包含文档或良好实践。但是商店永远是商店。这是一个你可以保存数据的地方。

为了让 redux 获得数据，你应该为 react-redux 创建选择器(比如 SQL SELECT ),连接特设和动作类型、动作创建器和还原器，以使用存储调度来设置数据。

因此，我将在这里展示使用 redux 的每个要点:

## 动作类型:

```
**export const** NAME_SPACE_TEXT = '@@nameSpace/TEXT';
```

这里我只创建了一个动作类型。它应该有助于存储文本字段。我使用名称空间将一个大的应用程序分割成具有自己的组件、存储(使用 combineReducers)和逻辑(我使用[**redux-observable**](https://redux-observable.js.org/))的小模块。

## 动作创建者:

```
**import** { NAME_SPACE_TEXT } **from** './types';**export const** textAction = text => ({
  type: NAME_SPACE_TEXT, 
  text,
});
```

## 减速器:

```
**import** { NAME_SPACE_TEXT } **from** './types';

**const** defaultState = {
  text: '',
};

**export default** (state = defaultState, action) => {
  **switch** (action.type) {
    **case** NAME_SPACE_TEXT:
      **return** { ...state, ...action};
    **default**:
      **return** state;
  }
};
```

这是一个简单的减速器。主要思想是检查动作类型，如果它是我们的文本动作，用以前的动作状态创建一个新的状态。

## 容器:

```
**import** { connect } **from** 'react-redux';
**import *** as actions **from** './actions';

**const** mapStateToProps = state => state[NAME_SPACE];
**const** mapDispatchToProps = { ...actions };

**export default** connect(mapStateToProps, mapDispatchToProps);
```

在这里，我使用相同的名称空间在我的模块中获取 store 的一部分。 **mapDispatchToProps** 帮我用商店调度功能包装我的动作创建者。

我需要添加减压器来存储:

```
**import** { combineReducers } **from** 'redux';
**import** nameSpace **from** './reducer';**const** rootReducer = combineReducers({
  ...
  **nameSpace**,
});
```

以及由容器包裹的部件:

```
import Container from './container';
import Component from './component';**export default** Container(Component);
```

仅此而已。每次创建新特性时，你都应该创建类型、创建者、减少者、容器……而不是考虑你的应用程序逻辑。

为了节省时间，我创建了 Redux Lazy。它能为你制造所有这些东西。你需要描述你需要哪些动作并得到它。

所以我来介绍一下 Redux Lazy。要使用它，你应该做一些小步骤。

第一步是安装 redux-lazy:

```
npm i -S redux-lazy
```

或者用纱线:

```
yarn add redux-lazy
```

第二步是创建 Redux 懒惰模型:

```
**import** RL **from** 'redux-lazy';

**const** rl = **new** RL('modelName');const {
  nameSpace,
  types,
  actions,
  defaultState,
  reducer,
  mapStateToProps,
  mapDispatchToProps,
  Container,
} = rl.**flush**();
```

这里我们应该为我们的模型设置一个命名空间。例如，我使用**模型名称**命名空间。

**它为你创建名称空间、类型、动作、缩减器、容器……**

接下来，你应该考虑你需要商店中的哪些领域。假设您正在使用 SQL 表。你需要为它命名(这就是为什么我使用**模型名称**命名空间)和字段。

例如，在前面的例子中，我们只需要一个字段— **文本**。所以你的工作是为这个领域做 getter 和 setter。

## addEventAction(名称)

如果您需要将事件发送到 redux-observable，您需要创建一个只有一个字段的动作类型: **type** 。史诗可以为你运行一些任务。

```
**import** RL **from** 'redux-lazy';

**const** rl = **new** RL('modelName');rl.addEventAction('event');**const** { types, actions } = rl.**flush**();**const** { MODEL_NAME_EVENT } = types;
**const** { eventAction } = actions;**export** { MODEL_NAME_EVENT, eventAction };**export default** rl;eventAction() // => { **type**: MODEL_NAME_EVENT }eventAction(anyData) // => { **type**: MODEL_NAME_EVENT }
```

## addFormAction(名称)；

如果您需要提交带有 redux 动作的表单，您需要处理事件并将函数放入表单组件中。在组件中使用链接代替对象或函数是一个很好的实践。因为它总是创建一个新的实例，React 应该再次渲染组件，即使数据是相同的。

```
<form onSubmit={(event) => {
    event.preventDefault();
    props.submitAction();
}}>
```

使用 Redux Lazy，您可以避免无用的渲染:

```
rl.addFormAction('submit');<form onSubmit={**props.submitAction**}>
```

## addFormElementAction(名称，默认值)

表单元素也是如此。每次都应该从事件(event.target.value)中获取值:

```
<input
  type="text"
  onChange={**event => props.titleAction(event.target.value)**}
  value={props.title}
/>
```

有了 Redux Lazy，你可以让事情变得更简单:

```
rl.addFormElementAction('title', '');<input
  type="text"
  onChange={**props.titleAction**}
  value={props.title}
/>
```

## addParamAction(名称，默认值)

它和 addFormElementAction 一样，只是用简单的数据代替了事件对象。

和任何 redux 动作创建器完全一样:

```
rl.addParamAction('title', 'defaultValue');**const** { actions } = rl.**flush**();
**const** { titleAction } = actions;titleAction('data')// => {type: '@@modelName/TITLE', title: 'data'}titleAction()// => {type: '@@modelName/TITLE', title:'defaultValue'}
```

## addParamsAction(名称，有效负载)

如果需要设置多个字段，可以使用 addParam **s** 动作:

```
rl.addParamsAction('clear', { title: '', body: '' });**const** { actions } = rl.**flush**();
**const** { clearAction } = actions;clearAction('1', '2') // => { title: '1', body: '2' }clearAction('1') // => { title: '1', body: ''}clearAction() // => { title: '', body: '' }
```

Redux Lazy 确实帮助我加快了我的 React / Redux 开发。但是有时很难得到所有的动作、类型…

我知道它是如何工作的，也知道我能从 **rl.flush()** 中得到什么。但是第一次也许你可以运行 **console.log(rl)** 看看里面有什么类型、动作、名称空间……

我认为这对于开发速度来说并不是一个很好的回报。

可以看更多 [**例子**](https://github.com/evheniy/redux-app) 。

请在评论中提出你的问题，别忘了在 [**github**](https://github.com/evheniy/redux-lazy) 上发表。**