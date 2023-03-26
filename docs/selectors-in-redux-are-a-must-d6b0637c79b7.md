# Redux 中的选择器是必须的

> 原文：<https://medium.com/hackernoon/selectors-in-redux-are-a-must-d6b0637c79b7>

![](img/5de61e2e9071e5faa1ded69ae8536d4f.png)

最近，我对 Redux 的选择器可以解决多少问题感到震惊。这就是我成为超级粉丝的原因，看到这种模式被视为高级模式，我很难过。它们易于理解，同时，在 Redux 的状态级别上支持低耦合/高内聚的代码。

几个月前我开始了解选择者。事实上，我开始为一个项目开发一个疯狂的搜索页面:除了普通的文本查询，用户还可以选择许多过滤器。事实上，过滤器有各种各样的形式:有些是一组复选框，有些是复选框的嵌套树，有些树可以用文本框来过滤。不仅如此，它们使用 AND 和 OR 的组合产生非常复杂的查询。不仅如此，它们之间还相互影响。“不仅如此”的清单还要长得多。

开始时，我被所有这些复杂性淹没了。最终，我一步一步走向光明。在这个旅程中，选择者救了我的命，成了我最好的朋友。

接下来是这个项目中的一个故事，讲述了我是如何认识选择者的，以及他们为什么这么棒。

## 保持状态平坦

开始时，我有一个类似这样的商店形状

```
store = {
  filters: {
    locations: []
    ...
  }
}
```

`locations`可能在哪里

```
locations = [{
  id: 1
  value: 'Europe'
  checked: false
  children: [{
    id: 2
    value: 'Central Europe'
    checked: false
    children: [{
      id: 3
      value: 'Italy'
      checked: false
    }],
  }]}, ...]
```

看起来够糟吗？嗯，想象一下进行不可变更新来检查 id 为`3`的节点的痛苦。在普通的 JavaScript 中，看起来像这样

```
newLocations = [ 
  ...locations.slice(1)
  {
    ...locations[0]
    children: [
      ...locations[0].children.slice(1)
      {
        ...locations[0].children[0]
        children: [
          ...locations[0].children[0].children.slice(1)
          {
            ...locations[0].children[0].children[0]
            checked: true
          }
        ]
      }
    ]
  }
]
```

当然，我可以使用像 [dot-prop](https://github.com/debitoor/dot-prop-immutable) 这样的库，但是那就像是把代码的味道扫到了地毯下面。

更好的表述可能是

```
locationsById = {
  1: {
    id: 1
    value: 'Europe'
    checked: true
    children: [2, 3]
  }
  2: {
    id: 2
    value: 'Central Europe'
    checked: true
    children: [3]
  },
  3: {  
    id: 3
    value: 'Italy'
    checked: true
}, ...}
```

还是我最后遇到的那个

```
locationsByPath = {
  '1': {
    id: 1
    value: 'Europe'
    checked: true
  }
  '1/2': {
    id: 2
    value: 'Central Europe'
    checked: true
  }
  '1/2/3': {  
    id: 3
    value: 'Italy'
    checked: true
}, ...}
```

路径中编码了树的层次结构(例如`1/2`是`1`的子节点和`1/2/3`的父节点)。

随着`locationsByPath`的更新，看起来更好

```
newLocationsByPath = {
  ...locations
  '1/2/3': {
    ...locationsByPath['1/2/3']
    checked: false
  }
}
```

## 分离关注点

在制作这个功能时，我注意到一些事情:`locationsByPath`知道得太多了。事实上，它既有领域知识(即`id`、`value`和层次)，也有 UI 数据(即`checked`)。

这是错误的，因为大多数时候域和 UI 数据有不同的需求和生命周期。例如，在我的例子中，一旦过滤器被加载到存储中，它们就不会被更新。相反，每当复选框的状态改变时，`checked`属性就会被切换。因此，我决定将两者分开:

```
locationsByPath = {
  1: {
    id: 1
    value: 'Europe'
  }
  '1/2': {
    id: 2
    value: 'Central Europe'
  }
  '1/2/3': {  
    id: 3
    value: 'Italy'
}, ...}checkedLocationsPaths: ['1', '1/2', '1/2/3']
```

首先，这使得检查节点变得非常容易

```
checkedLocationsPaths.includes('1/2/3') ?
  checkedLocationsPaths :
  checkedLocationsPaths.concat('1/2/3')
```

还有，`checkedLocationsPaths`和`locationsByPath`现在是独立的。这意味着，它们可以遵循不同的生命周期。例如，`checkedLocationsPaths`可以在加载`locationsByPath`之前出现在商店中。

不幸的是，重构到扁平状态让我的工作变得更容易，但是对于组件却更难。事实上，组件处理初始嵌套形状要容易得多:

```
const Component = ({ value, checked, children }) =>
  <div>
    <input type='checkbox' value={value} checked={checked} />{children.map(({ value, checked, children }) =>
      <Component
        value={value}
        checked={checked}
        children={children}/>
    }
  </div>
```

我可以给组件添加一些逻辑来处理扁平状态。但这是合适的地方吗？我不这么认为。事实上，这样做意味着将组件与状态的形状相耦合。此外，我相信真正愚蠢的组件。

## 救援选择器

Reducers 负责将状态写入特定的形状。因此，最好有一个尽可能靠近他们的东西来处理阅读部分。这就是选择器发挥作用的地方。

如果你不想听我的话，听听丹·阿布拉莫夫的。

事实上，将`initialState`、写入面和读取面放在同一个位置是非常正确的:

```
// reducer.jsinitialState = {
  locationsByPath: {}
  checkedLocationsPaths: []
  ...
}// write side
const filters = (state = initialState, action) => {
  if (action.type === 'LOAD_LOCATIONS') {
    return { ...state, locations: action.locations };
  } else if (action.type === 'CHECK_LOCATION') {
    const checkedLocationsPaths =
      state.checkedLocationsPaths.includes('1/2/3') ?
        state.checkedLocationsPaths :
        state.checkedLocationsPaths.concat('1/2/3')
    return { ...state, checkedLocationsPaths }
  }
  ...
}// read side// nests locations
const nest = locationsByPath => ...// adds `checked: true` where appropriate
const withChecks = (locationsByPath, checks) => ...const getNestedLocationsWithChecks = state => {
  const {
    locationsByPath,
    checkedLocationsPaths,
  } = state; return nest(
    withChecks(locationsByPath, checkedLocationsPaths)
  );
}
```

让我们通过一个例子来看看选择器是如何工作的

```
locationsByPath = {
  1: {
    id: 1,
    value: 'Europe',
  }, 
  '1/2': {
    id: 2,
    value: 'Central Europe'
  },
  '1/2/3': {  
    id: 3,
    value: 'Italy', 
}, ...}checkedLocationsPaths: ['1', '1/2', '1/2/3']state = {
  locationsByPath,
  checkedLocationsPaths
}nestedLocationsWithChecks(state)
=> [{
  id: 1,
  value: 'Europe',
  checked: true,
  children: [{
    id: 2,
    value: 'Central Europe'
    checked: true,
    children: [{
      id: 3,
      value: 'Italy', 
      checked: true,
    }]
  }]},
...]
```

其中`nestedLocationsWithChecks(state)`准备好从先前部分传递到`Component`。

换句话说，一方面，应用程序以平面状态工作。这样更便于数据管理。另一方面，应用程序处理嵌套数据。这对于 UI 来说更方便。

更重要的是，它们可以独立发展，因为选择器作为一个[反腐败层](http://www.markhneedham.com/blog/2009/07/07/domain-driven-design-anti-corruption-layer/)工作，并防止泄漏结构耦合。

## 使选择器冒泡

在上一节中，我从上到下，首先重构状态，然后向下重构组件。

在了解选择器之前，让我们通过重构我写的一些代码来看看相反的情况。

在应用程序中有一个页面，一些文章按类别分组显示(即文章只属于一个类别)。除了最后一个类别(如果为空，则应隐藏)之外，所有类别都必须始终呈现。

`categoriesFrom`执行分组并翻译当前区域的标题。`Component`分别渲染每个类别。

```
const categoriesFrom = resources => {
  const byCategory = [
    { id: 1, name: I18n.t('cat1'), resources: resources.cat1 },
    { id: 2, name: I18n.t('cat2'), resources: resources.cat2 },
    { id: 3, name: I18n.t('cat3'), resources: resources.cat3 },
    { id: 4, name: I18n.t('cat4'), resources: resources.cat4 },
  ]; if (resources.cat5.length !== 0) {
    const name = I18n.t('cat5')
    return byCategory
      .concat({ id: 5, name, resources: resources.cat5 })
  } else {
    return byCategory
  }
}const Component = ({ resources }) =>
  categoriesFor(resources).map(category => 
    <Category key={category.id} category={category} />)
  )
```

`Component`太聪明，耦合到状态形状(如`resources.cat1`)。

让我们把这个逻辑上升到一个选择器:

```
// reducer.js// selector
const getResources = state => state.resources;// selector
const getResourcesGroupedByCategory = state => {
  const resources = getResources(state); const byCategory = [
    { id: 1, name: I18n.t('cat1'), resources: resources.cat1 },
    { id: 2, name: I18n.t('cat2'), resources: resources.cat2 },
    { id: 3, name: I18n.t('cat3'), resources: resources.cat3 },
    { id: 4, name: I18n.t('cat4'), resources: resources.cat4 },
  ]; if (resources.cat5.length !== 0) {
    const name = I18n.t('cat5')
    return byCategory
      .concat({ id: 5, name, resources: resources.cat5 })
  } else {
    return byCategory
  }
}// Component.jsxconst Component = ({ resources }) =>
  resources.map(category => 
    <Category key={category.id} category={category} />)
  )connect({ resources: getResources(state) })(Component)
```

听说过哑元件吗？有了选择器，你就有了 dumber components:它们只是析构`props`并呈现它们，没有任何额外的逻辑。

## 结尾部分

一般来说，状态的形状是只有还原者才应该知道的。代码从存储中泄露出来的那一刻起，就变成了结构上的耦合。

因为缩减器决定了形状，因为它们写下了形状。用选择器让“读”发生在它们附近只是常识。

根据经验，做`state.`是一个错误。唯一允许这样做的地方是选择器内部。但前提是选择器位于负责这部分状态的缩减器中。

想看更多很酷的 JavaScript 东西吗？[看看函数式编程如何让你的生活更轻松](/@riccardoodone/fp-in-vanilla-javascript-a-rookies-intro-e32ad13484f4)。

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！