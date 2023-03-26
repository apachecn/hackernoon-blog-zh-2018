# 提高性能的 JavaScript 地图

> 原文：<https://medium.com/hackernoon/javascripts-maps-for-better-performance-bbebb987c997>

## 展示 JavaScript 地图性能的具体例子。

> 本文不打算详细探究地图对象的 API。如果你正在寻找这样的资源，请查看 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 。

*本文原载于*[*Tinloof*](https://tinloof.com/blog/java-scripts-maps-for-better-performance/)*。*

通常，当我们想要通过一组唯一的键来检索/添加/删除值时，Map 数据结构是有用的。

与其他语言中的实现相比，JavaScript Map 数据结构的一个独特之处在于它记住了键的插入顺序。

在本文中，我们将仔细研究是什么使得这个特性非常有用，以及为什么在某些情况下应该使用 map 而不是其他数据结构。为此，我们将通过构建购物篮来看一个具体的例子。

# 问题:构建购物篮

最初，我们有一个购物清单和一个空购物篮，如下所示:

```
// initial shop_itemsshop_items: [ { *value*:"👕", *stock*:10 }, { *value*:"👖", *stock*:10 }, { *value*:"👞", *stock*:10}]// initial empty shopping_basketshopping_basket: empty
```

我们希望能够将商品添加到我们的购物篮中，并将其放回原处。

将商品添加到购物篮应该:

*   减少该特定项目的库存。
*   如果该项目从未被添加到购物篮中，它应该将它添加到计数为 1 的购物篮中
*   如果该项目已经存在于购物篮中，它应该增加它在购物篮中的计数。

将商品退回商店商品应:

*   增加该特定项目的库存。
*   如果项目在购物篮中的计数大于 1，它应该减少它的计数。
*   如果该项目在购物篮中的计数为 1，则应该将其从购物篮中移除。

额外要求:我们需要确保购物篮中的项目按照它们被插入的顺序保存。

# 使用数组的解决方案

首先，让我们看看如何使用购物篮作为数组来解决它。

最初，我们有以下内容:

```
// *initial shop_items*letshop_items=[ { *value*:"👕", *stock*:10}, { *value*:"👖", *stock*:10}, { *value*:"👞", *stock*:10}];// *initial empty shopping_basket*letshopping_basket=[];
```

然后，让我们创建一个函数`addToBasket`,将商店商品中的一个特定商品添加到购物篮中:

让我们也添加一个`removeFromBasket`函数来移除将商品从购物篮放回商店商品列表:

测试我们的实现给出了以下结果:

```
// *(1)*addToBasket("👕");
addToBasket("👕");// Output:shop_items: [ { *value*:"👕", *stock*: *8*}, { *value*:"👖", *stock*:10 }, { *value*:"👞", *stock*:10}]shopping_basket: [ { *value*:"👕", *count*: *2 }* ]// *(2)*addToBasket("👞");// Output:shop_items: [{ *value*:"👕", *stock*: *8*},{ *value*:"👖", *stock*:10 },{ *value*:"👞", *stock*:}]shopping_basket: [ { *value*:"👕", *count*: *2*}, { value: "👞", count: 1}]// ------------------------------------ //// *(3)*removeFromBasket("👕");// Output:shop_items: [ { *value*:"👕", *stock*: *9*}, { *value*:"👖", *stock*:10 }, { *value*:"👞", *stock*: *9*}]shopping_basket: [ { *value*:"👕", *count*: *1*}, { value: "👞", count: 1}]// ------------------------------------ //// *(4)*removeFromBasket("👞");// Output:shop_items: [ { *value*:"👕", *stock*: *9*}, { *value*:"👖", *stock*:10 }, { *value*:"👞", *stock*: *10*}]// initial empty shopping_basketshopping_basket: [ { *value*:"👕", *count*: *1*} ]
```

那么，我们的实现起作用了吗？！那还有什么问题呢？🤔

如果我们仔细看看我们的两个更新函数，我们会注意到我们在搜索购物篮中的商品时使用了`findIndex`。这导致 O(N)的线性时间复杂度。换句话说，在最坏的情况下，我们将在同一个函数中遍历整个商店商品列表和购物篮两次。

在当前的上下文中，两者的大小都非常小，并且两个函数的使用都非常简单，这实际上不会导致任何性能问题。然而，这并不总是成比例的。如果我们将函数嵌套在另一个循环中，复杂度很容易增长到 O(n)。

这就是为什么地图可以解决我们的问题:

*   添加/检索/删除项目的复杂度是常数 O(1)。
*   它有更多的可读和合适的功能来添加/检索/删除项目。

最初，我们有以下内容:

```
// *initial shop_items*letshop_items=[ { *value*:"👕", *stock*:10}, { *value*:"👖", *stock*:10}, { *value*:"👞", *stock*:10}];// *initial empty shopping_basket*letshopping_basket= *new Map()*;
```

然后让我们重构`addToBasket`函数:

让我们也重构一下`removeFromBasket`函数:

如果我们再做一次测试，我们会看到同样的结果。

# 为什么不直接用对象呢？

有人可能会认为，如果我们使用对象而不是地图，也可以实现前面的性能。

`addToBasket`的实现如下所示:

`removeFromBasket`的实现如下所示:

尽管这种实现也可以工作，但是它有以下问题:

*   根据 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) ，对象键不一定是有序的。
*   删除操作不具有常数复杂度 O(1)！

# 把它包起来🙌

我希望这篇文章对理解 JavaScript Maps 的性能优势非常有用。希望这能激励你在类似的用例中使用它们。

如果你有任何反馈或问题，我也很乐意听到。

*可以找到更多关于*[*Tinloof*](https://tinloof.com/blog)*的文章。*

> ***附言:*** *你可以在推特上找到我*[*@ seif _ ghe zala*](http://twitter.com/seif_ghezala)*:)*

![](img/c5be5636d30577bee9d6483580dc1bff.png)