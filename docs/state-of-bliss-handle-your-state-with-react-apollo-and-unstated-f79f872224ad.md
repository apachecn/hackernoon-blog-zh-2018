# 极乐状态:用反应、阿波罗和未陈述来处理你的状态

> 原文：<https://medium.com/hackernoon/state-of-bliss-handle-your-state-with-react-apollo-and-unstated-f79f872224ad>

![](img/22125a407784d5527fd303a9fe7fc5d8.png)

Apollo 是使用客户端缓存管理远程数据的最佳解决方案。但是，如果您已经在使用 Apollo(或另一个客户端缓存，如 Relay ),问题是:您应该如何处理客户端状态的其余部分？React 的新上下文 API(在 16.3 中可用或作为 polyfill)为处理应用程序状态开辟了新的可能性。在 [OK 成长！](https://www.okgrow.com/posts/www.okgrow.com)，我们一直试图找到一种幸福的状态，将来自服务器的数据、组件状态和应用程序状态结合起来，这种状态并不总是平稳的。

# 应用程序状态很小

在看了丹·阿布拉莫夫在 ReactEurope 的开创性演讲后，我对 Redux 产生了兴趣。在我学习了 Redux 的基础知识之后，我开始寻找关于“高级 Redux”的信息但这让我直面 Redux 给 [Apollo](https://hackernoon.com/tagged/apollo) 用户带来的问题:我发现的每一个教程都是在实现客户端缓存。您将学习用 Redux 实现异步的三种方法，然后实现这些异步操作来处理您的 Redux 存储。但是 Apollo 的客户端缓存已经做到了这一切。它处理我所有的加载和错误状态，它处理乐观的更新，你能想到的。那么还剩下什么需要管理呢？

事实证明，对于大多数应用程序来说，答案是非常少的。它的大部分应该只是生活在[里起反应的](https://hackernoon.com/tagged/react)组件。

# 为了更好的可测试性，集中到 Redux 怎么样？

我同意这使得测试非常容易，但是我有两个问题:

1.  它打破了封装。拥有一个组件系统的全部意义在于封装。React 允许您将状态放入组件中，这样当您在其他地方工作时，就不必考虑该组件中发生了什么。一旦您试图分离功能，您头脑中就记住了两件事，组件和状态(现在可能在一个单独的文件中，所以您需要打开更多的缓冲区)。不仅如此，还有风险是人家(绝对不是你！)将试图从不同的组件获取该状态。
2.  **从组件中移除状态感觉不太好。**我听说过开发人员用功能组件制作整个应用程序，所有状态都来自 Redux。嘿，我有一个“让我们使用所有功能组件！”相也。但拥有一个全球商店绕过了自上而下的数据流，让你的应用程序更难推理。此外，这需要更多的代码！

这种变化部分源于这样一个事实，即测试组件长期以来一直相当尴尬，但我认为我们可以使用像 [React Test Renderer](https://reactjs.org/docs/test-renderer.html) 这样的工具来解决这个问题(在未来的帖子中会有更多相关内容)，或者可能使用像 [Cosmos](https://github.com/react-cosmos/react-cosmos) 这样的新工具。

有一些应用程序将真正受益于 Redux，所以我不是说不使用它。但是在我自己的工作中，一旦 Apollo 接管了远程数据同步和缓存，我还不能证明开销的合理性。

# 剩下什么状态要管理？

如果您将大部分状态放入组件中，并且有一个客户端缓存，那么还剩下什么呢？几乎没有。在一个应用程序中，我只需要管理一个全局状态变量。但是我尝试用旧的上下文 API 直接管理它，这使我确信应用程序范围的状态仍然是一个值得解决的问题！以下是我认为属于应用程序范围状态的一些合法内容:

*   **表示需要在路线之间共享。**当您遇到路由边界时，自顶向下的数据传播遇到了障碍。当然，您会将一些参数传递给相关的路由，但是传递共享状态会导致灾难。用应用状态代替就行了。
*   **到处都在使用的状态。当然，你可以把它放在你的根组件中，并把它传递下去，但是为什么呢？顺便说一下，我认为可以将当前会话信息(例如，用户是否登录)存储在 state 中，即使您也可以使用 Apollo 做到这一点。**

在我工作的最后一个应用程序中，我们尝试了[阿波罗链接状态](https://www.apollographql.com/docs/link/links/state.html)。这种逻辑很有说服力:既然您已经有了一个很好的库来查询和更新数据库中的值，而且既然您的大部分数据已经是这种格式了，为什么不用同样的方式存储您的应用程序状态呢？这样就有了一种方法，可以使用现有的反应式查询组件来更新 UI 元素，而不管数据是本地的还是远程的。

公平地说，Apollo Link State 完全可以工作，尽管我在 Apollo 的新增功能上遇到了一些尖锐的问题。真正的问题是，你生产和消费本地应用数据的方式与远程数据的模式不匹配。对远程数据的查询往往只针对应用程序的一部分，不会重复太多。这些查询故意是冗长的，以阐明客户机和服务器之间的关系，并且它们可以驻留在它们自己的文件中，并且可以具有乐观更新、基于突变的更新等选项。它们还广泛使用加载/错误状态、选择所需字段的能力等。但根据定义，应用程序数据无处不在且简单——通常是键值对，或者有时是在应用程序的多个位置读取和更新的各种组合中需要的对象。您不需要选择字段、处理加载状态等。为这些事情编写查询非常笨拙而且过于冗长，不要忘记查询和变异需要单独的代码。

这里有一个例子:我们有一个从餐馆点菜的应用程序。如果你离开你的桌子，它应该清除未完成的订单，并删除你坐的桌号。但是如果你有一张未支付的标签(票)，你不应该能做那件事。我们在状态查询和突变中使用了带有渲染道具的组件，这是应用程序中出现的一段代码:

```
<ClearOrder
    render={clearOrder => (
      <SetCurrentTable
        render={setCurrentTable => (
          <GetOrderStatus
            render={({ ticketId }) => (
              <HeaderButton
                label="Leave Table"
                disabled={!!ticketId}
                onPress={() => {
                  clearOrder();
                  setCurrentTable({ qrCode: '' });
                }}
              />
            )}
          />
        )}
      />
    )}
  />
```

这几乎足以让你回到更高阶的组件！😱实际上，我认为 HOCs 在这里是一个很好的解决方案，但是组合单个的操作仍然很繁重。

# 输入未声明

在我们看无状态之前，我应该说你根本不需要使用任何其他的状态库！新的上下文 API 非常好，可能就是你所需要的，允许你在根组件中保持状态。也就是说，这是用[编写的相同组件，没有声明](https://github.com/jamiebuilds/unstated)

```
<Subscribe to={[Table, Order]}>
  {(currentTable, order) => (
    <HeaderButton
      label="Leave Table"
      disabled={!!order.state.ticketId}
      onPress={() => {
        order.clear();
        currentTable.set({ qrCode: '' });
      }}
    />
  )}
</Subscribe>
```

我发现它可读性更好，缩进层次更少，代码行更少。它以两种方式避开了末日金字塔:

*   Get 和 set 功能可以在一个状态对象中共享，减少了导入。如果我们愿意的话，订单和表状态可以合并到一个对象中。我们正在处理类，所以我们可以在那个容器上组合尽可能多的东西。
*   在一个`Subscribe`组件中有一个内置的方法来订阅多个状态容器——`to`prop 接受一个状态对象数组。

最后，如果我们比较执行`clearOrder`的代码，其中一个动作，差别变得稍微明显一些:

GraphQL 非常简单，但并没有告诉我们太多信息:

```
mutation clearOrder {
  clearOrder @client
}
```

解析器最终看起来很像 Redux reducer。

```
clearOrder: (_, __, { cache }) => {
  const data = cache.readQuery({ query: GET_ORDER });

  *// make sure you mutate your object correctly*
  *// or other state could be affected*
  data.items = [];

  cache.writeQuery({ query: GET_ORDER, data });
  return null; *// if you forget to return null, you'll get errors*
},
```

未声明类中的等效方法是一行代码:

```
*// make sure you bind your method if you might pass it*
*// as a prop*
clear = () => this.setState({ items: [] });
```

啊，感觉好多了！

# 未声明的测试

因为 Unstated 创建了一个不是 react 组件的对象，所以您可以在不呈现组件的情况下测试它，就像使用 Redux:

```
test('clear order', () => {
  *// instantiate and set up our state object for the test*
  const orderState = new OrderState();
  orderState.setState({ items: ['hamburger'] });

  orderState.clear();

  expect(orderState.state.items).toEqual({ items: [] });
});
```

或者您可以通过使用`inject` prop 将它注入到组件树中来测试它:

```
test('clear rendered Orders', () => {
  *// render an Orders component with the same orderState as above*
  *// (here with react-test-renderer)*
  const tree = TestRenderer.create(
    <Provider inject={[orderState]}>
      <Orders />
    </Provider>,
  );
  *// make sure our order rendered (1 item)*
  expect(tree.root.findAllByType(OrderItem).length).toBe(1);

  *// "press" the leave-table button by calling its onPress, which will*
  *// call our `clear()` method*
  const leaveTableButton = tree.root.findByProps({
    testId: 'leave-table-button',
  });
  button.props.onPress();

  expect(tree.root.findAllByType(OrderItem).length).toBe(0);
});
```

# 追随你的幸福

事实上，Unstated 非常容易使用，它可以很容易地用于任何事情，但这并不是本库的真正意图。不过，明智地使用它，它会填补您的状态管理中一个很小但非常重要的漏洞。在我目前的项目中使用 Unstated 感觉就像我终于得到了一个完整的数据解决方案，我不再希望有什么不同。

*原载于【www.okgrow.com】[](https://www.okgrow.com/posts/state-of-bliss)**。***