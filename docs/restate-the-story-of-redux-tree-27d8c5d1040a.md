# 重述——红杉树的故事

> 原文：<https://medium.com/hackernoon/restate-the-story-of-redux-tree-27d8c5d1040a>

redux——现代 React 应用的圣牛。但是这足够了吗？说实话——没有。

![](img/35a9c0667322df82116d16aa95c50976.png)

[https://blog.codecentric.de/en/2017/12/developing-modern-offline-apps-reactjs-redux-electron-part-3-reactjs-redux-basics/](https://blog.codecentric.de/en/2017/12/developing-modern-offline-apps-reactjs-redux-electron-part-3-reactjs-redux-basics/)

# “反应式”待办事项列表

让我们尝试只使用 React 来构建图标式待办事项应用程序。

> 代码以相反顺序被监听

```
// first - create a underlying data structure
const TODOs = [todo1, todo2, todo3];// second pass into Application
const Application = <TodoList todos={TODOs} />// third define todo list
const TodoList = ({todos}) => (
  <ol>
    {todos.map( todo => <Todo key={todo.id} {...todo} />
  </ol>
);
// last define Todo
const Todo = (props) => <div>......</div>
```

这里的关键特性是— **子节点的所有数据都是由其父节点**传递的。

# “Redux”待办事项列表

让我们试着把它改造成 redux 变体

> 这是[原始 Redux 的待办事项列表](https://github.com/reactjs/redux/tree/master/examples/todos)的简化版本

```
// first - create a underlying data structure
const store = createStore({
   todos: [todo1, todo2, todo3]
});// second pass into Application
const Application = 
<Provider store={store}>
 <ConnectedTodoList/>
</Provider>// third connect todo list
const ConnectedTodoList = connect(
   state => ({todos: state.todos})  // <- here we inject the data
)(TodoList)// forth define todo list
const TodoList = ({todos}) => (
  <ol>
    {todos.map( todo => <Todo key={todo.id} {...todo} />
  </ol>
);
// last define Todo
const Todo = (props) => <div>......</div>
```

这里的关键特性是，您可以随时连接到全球商店。获取数据并分派事件。这将整个应用程序解耦，但是，实际上，使得应用程序的各个部分更不可组合。

> 更有甚者——**组件构图**(React 的另一个圣牛)光是**就不行**，因为 long redux 忽略了它在时空中的位置。

# 多一点“冗余”的待办事项

所以。我可以在这里提到一个可以改进的时刻。TodoList 太`reactish`。而托多绝对不是`reduxish`。让我们修理它

```
....
// third connect todo list
const ConnectedTodoList = connect(
   state => ({todos: idsFrom(state[todos])})// only ID, not all data
)(TodoList)
...
// forth define todo list
const TodoList = ({todos}) => (
  <ol>
    {todos.map( todo => <ConnectedTodo key={todo.id} id={todo.id} />
  </ol>
);// use props to get the desired data
const ConnectedTodoList = connect(
   (state, props) => ({...state.todos[props.id]}) // get the TODO
)(Todo)// last define Todo
const Todo = (props) => <div>......</div>
```

这样更好。TODOList 对 Todo 数据的任何更改都是不变的，Todo 元素直接连接到存储。

这也很酷，因为它为事件传播创建了边界。

> Redux 的 connect **是任何更新的** **start** ，只要它直接连接到 store，就直接触发。
> 
> Redux 的 connect **是任何更新的** **end** ，只要它是一个 PureComponent，将只传递由 Redux Store 引入的更改，而不传递 React 更新。

它就像一只章鱼，处理并淹没你的应用程序。

> 想想事物的起源。开始和结束。

![](img/d7db77213b50c39214392ff76ed1a7cd.png)

# 最后的战斗

让我们再举一个例子。几乎待办事项列表，但有嵌套。这是资源的链接。

[](https://github.com/reactjs/redux/blob/master/examples/tree-view/src/containers/Node.js) [## 反应/还原

### JavaScript 应用程序的 redux -可预测状态容器

github.com](https://github.com/reactjs/redux/blob/master/examples/tree-view/src/containers/Node.js) 

这里的关键特性是——你有一个节点，这个节点里面有一个列表。为了更新/编辑列表，您必须指定`nodeId`，而不仅仅是`listId`。

而 redux 没能`purely`解决这个问题。他们不能“正常地”提供预先填充了 nodeId 的调度，也不能为 List 提供 nodeId，让 List 从存储中选择数据。

他们能做什么？他们使用 **React** 处理程序来做 **Redux** 的工作。

```
handleAddChildClick = e => {   
    // get nodeId and dispatch function from a parent
    const { addChild, createNode, id } = this.props;  
    const childId = createNode().nodeId;
    // dispatch event with both variables known
    addChild(id, childId)  
}
```

> 这打破了组件方法。这是失败的。

# 重申救援

> Redux-restate，Redux-tree，re-store，Redux-Lenses，Transformation……Redux 缺的都是这些东西， **Restate** 就是这些东西。

Restate 是一个全新的库，它使你的应用程序变得更具反应性和冗余性。并且解决了最后一个例子的问题。

怎么会？让我们创建一个示例:

```
....
// third connect todo list
const ConnectedTodoList = connect(
   state => ({todos: idsFrom(state[todos])})// only ID, not all data
)(TodoList)
...
// forth define todo list Mapper
const TodoList = ({todos}) => (
  <ol>
    {todos.map( todo => <ReconnectedTodo key={todo.id} id={todo.id} />
  </ol>
);// create a new redux connection point (**HERE IS THE MAGIC**)
// "focus" into the derived stateconst ReconnectedTodo = **reduxFocus**( 
    // keep only selected TODO in the store
    **(state, pros) => { todo: state.todos[id] },** 
    // `restore` TODO id is on dispatch
   (dispatch, event, props) => dispatch({...event, id: props.id})
)(ConnectedTodo);// use props to get the desired data
const ConnectedTodoList = connect(
   (state) => ({...state.todo}) // a single todo HERE, the right one
)(Todo)// last define Todo
const Todo = (props) => <div>......</div>
```

*PS: reduxFocus 是 Redux Restate 的一部分。*

这个例子使用了`react-redux-focus`——这个更简单的`react-redux-restate`版本，它是`redux-restate`的“反应”版本。

> 这解决了主要的 redux(全局状态)问题:可重用性。

获得控制，形成子状态，子存储。映射并减少。作曲。

也许你已经有了一个`connected` 组件，仅仅通过从状态中获取一些键，就紧紧地绑定到了某个状态结构上？你知道，那个组件是不可重复使用的。扔掉它

```
const ConnectedXComponent = connect(mapStateToProps)(MyXComponent);....
// just wrap it with redux state "adapter". Why not?const ReusableXConnectedComponent = reduxFocus( 
  (state,props) => createXState(state, props)
)(ConnectedXComponent)
```

## 它有什么作用？

`reduxFocus`在现有商店的基础上创建新店，**关注**儿童的“更大”状态。ConnectedTodo 将只接受一个 Todo，它将与任何其他不需要的数据隔离开来。*(即将聚焦)*

同时，只要 long reduxFocus 限定了数据的范围，就不需要传递“id ”,在 dispatch 中使用它，只要你可以用你知道的`todoId`来**增强**所有**事件传递回**到原始存储。

这也意味着，你可以将待办事项列表放在一个类似 Trello 的树中，只需定义*透镜*从一个“大”存储中获取一个待办事项列表，并将“nodeid”放在所有事件中，一个嵌套的组件将在将来被分派。

> 这将启用组件模型，因此深受大家喜爱。

# 更复杂的例子？

React-redux-focus 是一个简单的 HOC，设计用于单一商店。最初的`React-redux-restate`旨在与多个 one store 合作。但是为什么呢？？

```
const mapStateToProps = state => {
   something: memoizedWithReselect(someComplexOperation(state))
}
connect(mapStateToProps)(Component)
```

组件的**渲染**方法多久被调用一次？每次某个`real`更新都会经过记忆，并触发更新。所以——不经常。

多久会打一次`someComplexOperation`？每次状态更新时。即使你也会记忆它，你会调用记忆函数…很多次。从 ReactRedux `render`方法。

> 如果您有 100500 个到商店的连接—所有这些连接都会在每次商店更新时调用 mapStateToProps。除非你指定一个状态序列，但你不会。

**重申一下**就是**是 state sequal**——全球儿童商店的一个“透镜化”的小部分，将他们与其他部分隔离开来。

```
// default store will be accessible as `default`.
const NestedStore = restate({}, ({ default:state }) => ({ state.only, state.data, state.i, state.need }))(RenderChildren);
```

但是接下来，您可能会再次需要原始数据。但是也可能需要来自`current`sin titic store 的计算数据。

```
const Reprovider = reprovider('superStore'); // copy store to the superStoreconst NestedNestedStore = restate(
   { base: 'superStore' }, // get `superStore` as `base`
   ({ default: state, base }) => ({ ...state, base.dataFromBase})
   (dispatchers, event, props) => {
      dispatch dispatches.default or dispatcher.base?
   })...
```

这不是最好的例子，但有时可能必须存储在单个应用程序中，例如“应用程序”和“页面”,工作重述将使您能够组合来自这两个应用程序的数据，并将派单发送回正确的存储。

> 更重要的是——两个商店是使用 MobX 的常见方式

# 还是 redux？

这个问题很好，因为很少有事情会打破核心概念。

1.  可以从商店创建派生商店吗？

在数据库世界里，它被称为视图或矩阵视图(只要它们被记忆)。对 mapStateToProps 也是如此。从理论上来说是可以的。

2.回`route`急件可以吗？又冒泡了？

*首先——没有别的办法。第二—由于 long routeDispatch 是一个纯函数(与 mapStateToProps 或 reducers 相同)—它是* `*predictable*` *。因为长期存储行为是可预测的——这是可以的。*

3.可以让容器对树中的位置敏感吗？

这部分不见了。使用 restate 应用程序可以更好地使用 redux 更频繁、更容易、更正确。

4.这是一家真正的`redux`商店吗？

*没有。这只是一个具有类似 redux 的公共接口的视图，可由 redux 的 connect down the tree 重用。是假的。*

当前解决某些任务的方式是不正确的。回想一下树的例子——没有反应，没有重复，只是到处乱砍。

# 不需要 Redux？

这听起来可能有点奇怪，但是**重述没有 Redux 是可以的。**可以将一个或多个状态合并在一起，也可以**从 props 形成一个新的状态，**映射分派到 React 的组件方法。

```
**return** (
    /* <ReduxFocus focus={**bigState => smallState**}> */
    <ReduxFocus focus={**this**.getState}>      
       <ReduxDelay timeout={500}>
          counter: <Counter /> /* will connect to "redux" */ 
       </ReduxDelay>
    </ReduxFocus>
);
```

重申一下，是一个强大的状态管理库。充分反应和 Redux 思想兼容。

# 是时候试试了？

重述是昨天发布的，但它已经准备好了。原始回购包含更多的例子，文件，和更多的理论。

[](https://github.com/theKashey/restate) [## 凯西/重述

### 再次声明-使 redux 可组合！

github.com](https://github.com/theKashey/restate) 

让 redux 重新可组合！让组件架构开花结果！

我有一个 3(已经 5)包给你。选择您需要的一个:

```
// to low-level redux manupulations
import reduxRestate from 'redux-restate'; // to work with multiple stores
import reactReduxRestate from 'react-redux-restate'; // to focus a lens on a single store
import reactReduxFocus from 'react-redux-focus';// to freeze the time
import reactReduxSemaphore from 'react-redux-semaphore';// to optimize state changes
import reactReduxDelay from 'react-redux-delay';// to optimize update propagation
import reactReduxUnbranch from 'react-redux-unbranch';
```

PS:每个组件的实际代码只有 10-50 LoC。如果你读一下代码，事情可能会变得更清楚。

## 后续文章

[](https://blog.cloudboost.io/the-state-of-the-state-of-the-state-f93c8bdc6b1b) [## 国家的国家

### 换句话说，分形状态。更具体地说——还原分形状态。这是有可能创造分形使用…

blog.cloudboost.io](https://blog.cloudboost.io/the-state-of-the-state-of-the-state-f93c8bdc6b1b)