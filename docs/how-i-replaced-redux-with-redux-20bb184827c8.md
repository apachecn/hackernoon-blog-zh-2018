# 我如何用 Redux 替换 Redux

> 原文：<https://medium.com/hackernoon/how-i-replaced-redux-with-redux-20bb184827c8>

![](img/f4b845ea6e6f2510e0c8f35df4794918.png)

Artwork by RJ Barnes

## 以及如何构建简单、可预测的具有流量标准功能的减压器

# TL；速度三角形定位法(dead reckoning)

我写了`[flux-standard-functions](https://www.npmjs.com/package/flux-standard-functions)`，这是一个[小(2.1kb)](https://bundlephobia.com/result?p=flux-standard-functions) 包，用于为 Redux 构建简单的、可预测的减速器。它有助于让令人敬畏的事情变得简单，比如用清晰、有效的原子突变替换 reducer 样板文件。它适用于现有的和新建的项目。⭐ [给它一颗星！](https://github.com/skonves/flux-standard-functions) ⭐

# Redux 很难

Redux 是大家又爱又恨的库。几乎没有一天我不看到一些文章声称“库 X”正在杀死 Redux。Redux 为什么很“硬”，为什么开发者这么一心想“干掉”它？

![](img/ed5f9a23dee850156283f8a3267d5c4d.png)

从根本上说，Redux 的存在是为了解决“人类思维很难思考的两个概念:突变和异步”之间的紧张关系。这是通过与另一个潜在的困难概念:间接性的权衡来实现的。如果处理得当，这样的权衡会产生非常好的结果。然而，如果你认为 Redux 是一个银弹，你会有一段不好的时间。

> “重复是一种交易”——丹·阿布拉莫夫

但是除了间接的复杂性，Redux 还有另一个困难:它的简单性。Redux 的一个最好的特性是它缺少这些特性。在写这篇文章的时候，这个库在缩小和压缩后只有 2.6kb。它的小尺寸是由于它缺乏特征和观点。这种简单性使得 Redux 非常灵活。

但是最终的灵活性往往会成为生产力的障碍。Andrew Clark 引入了一个“通量动作对象的人性化标准”，他称之为“[通量标准动作](https://github.com/redux-utilities/flux-standard-action)”(或 FSA)，以控制动作的灵活性。他观察到“如果我们能够对它们的形状做出某些假设，那么处理流动行为就会容易得多。”FSAs 引入的观点有助于开发者推理动作的形状，然而，仍然缺乏构建 reducers 的类似标准。

Redux reducers 的大部分样板来自于无处不在的对象定制传播，以创建新的状态。通量标准函数(FSF)是一组特定的函数，可以组合成减速器。通过去除 Redux 的一些终极灵活性，FSF 允许开发人员构建和理解简单的、可预测的 reducers。

当面临像 Redux 这样的挑战时，开发人员有几个选择:改善体验或“杀死它”并使用其他东西。总的来说，我发现 Redux 权衡对于大型项目来说是值得的。因此，在通过大量 Redux reducers 寻找通用模式之后，我认为我找到了一个简单、全面、高效且向后兼容的解决方案。

# 给我看看代码！

下面是一个基于 Redux 文档中提供的[示例](https://redux.js.org/introduction/examples)的“Todo 缩减器”示例:

以下是用通量标准函数实现的相同示例:

首先，让我们来看看最明显的区别。首先，Todo ( `todoDef`)的结构是在 reducer 之外定义的。这样做有助于防止重复，因为我们添加了更多的 Todo 操作类型。接下来，由此产生的还原函数*真的很小！*

第二，我们实际上免费得到了一些在最初的例子中没有的东西。如果`id`未定义，那么原来的缩减器将创建一个无效的新状态。在“FSF”的例子中，如果 Todo `id`未定义，`set`函数确定 Todo 无效，并通过引用返回原始状态。这种验证不仅确保应用程序状态保持有效，还增加了性能优化，即如果没有任何变化，就不返回新的状态对象。

为了进行更多的比较(包括验证)，最初手写的 reducer 看起来更像这样:

上面的例子开始展示了一个非常简单的操作是如何随着时间的推移而膨胀的。我们所做的只是添加验证，突然我们在多个位置定义了相同的 Todo 属性。随着这一缩减函数随着时间的推移而发展，并且随着新的动作类型的添加，将变得难以确保待办事项的定义和验证在整个待办事项缩减器中保持一致。

这只是简化一个相当简单的操作(向 state 添加一个新项)的一个例子。让我们来看一下这三个函数，以理解如何使用它们来构建您能想到的几乎所有的缩减器。

![](img/b4119ba04ad694343dcfb9fc7fca6610.png)

# 三大功能

标准函数使用三个参数的组合:`target`、`payload`和`definition`。`target`是正在被“变异”的数据。(注意:如果`target`改变了，那么就创建了一个浅层克隆。(`payload`)是添加、更新、替换或删除的新数据。`definition`是描述`target`对象结构的对象，用于验证、索引和优化。

![](img/6038e21b58da1e0d43c784e03c47fe4f.png)

## 一组

Set 提供了添加或覆盖数据的能力。这类似于“创建”CRUD 操作。如果正在设置的值已经存在，则它将被覆盖。如果设置的值不存在，则添加该值。任何设置了不包含在`definition`中的值或被定义为不可变的值的操作都将被忽略。

使用`[set](https://github.com/skonves/flux-standard-functions/blob/master/src/functions/set.md)`进行单值操作，使用`[setEach](https://github.com/skonves/flux-standard-functions/blob/master/src/functions/set-each.md)`进行批量操作。

![](img/16c8d82d989696e51b067f2a4d7d9f5a.png)

## 修补

修补程序提供了更新(或“更新”)数据的能力。这类似于“更新”CRUD 操作。如果要修补的值已经存在，那么它将被替换。对于复杂属性，将使用`payload`中的属性进行部分更新。如果该属性尚不存在，并且根据`definition`有效，则将添加该属性。

使用`[patch](https://github.com/skonves/flux-standard-functions/blob/master/src/functions/patch.md)`进行单个补丁操作，使用`[patchEach](https://github.com/skonves/flux-standard-functions/blob/master/src/functions/patch-each.md)`进行批量操作。

![](img/7651d80cc80eb777fd2c688459ce0c4e.png)

## 未设置

Unset 提供了删除数据的能力。这类似于“删除”CRUD 操作。如果存在未设置的值，则将其删除。如果未设置的值不存在，或者被`definition`指定为必需的或不可变的，那么什么都不会发生。

使用`unset`删除单个值，使用`unsetEach`进行批量未设置操作。

# 定义和规则

标准函数使用`definition`参数来验证更改。`[define()](https://github.com/skonves/flux-standard-functions/blob/master/src/define.md)`函数用于创建应用程序状态中对象类型的定义。

以下是定义“用户”类型的示例:

以下规则可用于创建类型定义:

*   `key()`:属性是“索引”或表的“键”
*   `immutable()`:该属性一旦设置就不能更改
*   `required()`:房产必须存在
*   `optional()`:房产可能存在或`undefined`
*   `indexOf(def)`:所提供定义的“索引”中的属性
*   `objectOf(def)`:属性是一个复杂的对象
*   `arrayOf()`:该属性是一个原始数组

注意，Typescript 也允许开发人员定义类型；但是，Typescript 类型只是一种编译时构造。一旦代码被编译成普通的 Javascript，类型定义本身就消失了。这意味着它们不能在运行时用于验证。正因为如此，`define()`函数被设计成独立于 Typescript 或者与 Typescript 协同工作。如果您使用的是 Typescript，那么您可以进行丰富的类型和属性检查。如果您没有使用 Typescript，那么您仍然可以从标准函数提供的运行时验证中受益。

# 先前技术

和大多数新库一样，Flux 标准函数站在巨人的肩膀上。几个项目影响了这个项目的开发(或者至少尝试解决一个类似的问题)。在这里不提他们是我的失职。

## [通量标准动作](https://github.com/redux-utilities/flux-standard-action#readme)

“通量动作对象的人性化标准。如果我们能够对它们的形状做出某些假设，那么处理通量动作就会容易得多。”标准函数基于一个相似的原理，即如果我们可以对它们的组成进行假设，减速器就很容易构建。

## [Redux 数据归一化](https://redux.js.org/recipes/structuringreducers/normalizingstateshape)

Redux 文档中讨论的[数据标准化原则对该项目产生了重大影响。像文档推荐的那样，Flux 标准函数在“平坦”或规范化状态下工作良好。在这个项目中,“表”的概念不严格地解释为“索引”。我也建议保持州政府不变。YMMV。](https://redux.js.org/recipes/structuringreducers/normalizingstateshape)

## [下划线/连字符](https://lodash.com/)

`_.set`和`_.merge`功能大致映射到`set`和`patch`标准功能。在网上可以找到很多使用下划线或连字符来构建减号的例子。这个项目提供了一个类似 lodash 的功能的小子集，为使用 Redux 进行了优化。

## [正常化](https://www.npmjs.com/package/normalizr)

Normalizr 是一个很小但很强大的实用程序，用于获取带有模式定义的 JSON，并返回包含 id 的嵌套实体，这些 id 收集在字典中。

## [沉浸](https://www.npmjs.com/package/immer)

Immer 是在开发 Flux 标准函数期间流行的热门新库，绝对值得一看。它是“一个微小的包，允许你以更方便的方式处理不可变状态。它基于 [*写时复制*](https://en.wikipedia.org/wiki/Copy-on-write) 机制。”

## 角度 1

更多信息请参见本次[会议讲座](https://www.youtube.com/watch?v=dQw4w9WgXcQ)。

# 喜欢你看到的吗？

希望你觉得这个项目很有趣！如果是这样，以下是你可以提供帮助的方式:

1.  怪异的行为？令人困惑的文件？Github 问题超级有帮助！😃
2.  给它一颗星:[https://github.com/skonves/flux-standard-functions](https://github.com/skonves/flux-standard-functions)⭐
3.  给这个帖子几个👏