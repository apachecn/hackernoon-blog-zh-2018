# Git State——使用 GraphQL 进行 Git 风格的同构状态管理

> 原文：<https://medium.com/hackernoon/gitstate-git-style-isomorphic-state-management-with-graphql-3b0c9475412d>

没有一天不出现关于国家管理图书馆的新想法。我想谈谈另一个例子。但是要注意，**这不是真正的**图书馆。这是我的一个想法，并希望与社区分享。

![](img/48003f58cb94f559dfda5bc8c8343139.png)

This is supposed to be a mashup of Git and GraphQL logos

我将把这个假想的库称为 GitState。

GitState 可以帮助你在本地管理你的状态，并使它的一部分与你的远程数据库保持同步。所有这些事务都用 git 风格的 api 来处理。它应该与任何 GraphQL 后端工作，可能有一些配置。它负责以下工作:

*   **保持/管理本地状态** —对于本地和被跟踪的远程数据，它就像一个可预测的、不可变的状态容器。
*   **处理数据流** —您可以*跟踪远程数据存储的*片，并扩展/更改这些片(用于加载更多、过滤等。).加载和错误状态由 GitState 处理。
*   **处理请求流** —请求被完全抽象出来。你可以添加或修改音轨，在你执行 *G.pull* 之前，不会有任何东西到达后端。一旦你这样做了，GitState 就会根据音轨和缓存生成一个最优的查询，所以它尽可能少地访问后端，请求最少的负载。
*   **缓存** — GitState 通过与状态分离的`__typename`和`id`来跟踪获取的资源，这些资源被规范化并且独立于它们被拉入的层次结构。它允许为每种类型或每种类型的每个字段单独定义全局缓存策略。优化查询时会考虑这些因素。

下面是对该 API 的一个简要介绍。请注意，所有功能都是按照配置第一数据最后数据的顺序自动执行的。

## G

库的句柄，像这样导入:

```
import G from 'gitstate'
```

## g .初始化

用于配置 GitState 客户端。

```
G.init({ origin: "<server ip>" })
```

## g .跟踪

与`git track …`类似，该方法跟踪远程数据存储的一部分。接受一个**轨迹键**和一个 **GQL 查询**。它可以有选择地带**查询参数**和选项。它或者创建一个新的轨迹，或者覆盖已经存在的轨迹。

```
G.track(key)(query)(params)(options)
```

可以很容易地修改现有轨道的范围，以支持诸如**分页**、**查询**和**过滤**之类的事情。这可以通过覆盖查询参数轻松实现。为此，只需省略查询并提供新值。

```
G.track(key)(params)(options)
```

示例:

```
const q = `
  query Posts($limit: Int, $offset: Int){
    posts(limit: $limit, offset: $offset) {
      id
      title
      contents
    }
  }
`;// Create a new track & start pulling
G.track('posts')(q)({ limit: 10, offset: 0 })()
G.pull('posts')()// ...Later

// Modify the scope of the tracked posts
G.track('posts')({ offset: 10 })({ append: true })
G.pull('posts')()
```

## g .拉

接受两个可选参数，**轨迹关键点**和**选项**。省略任何一个都是可行的。当提供 track key 时，它只尝试获取给定的轨迹，否则它将所有现有轨迹作为目标。

```
G.pull(key)(options)
```

调用时，GitState 只获取需要获取的数据。如果一个音轨的查询参数发生了变化，它就会被获取。如果不是，则根据缓存解析策略或`force`选项做出决定。

示例:

```
G.pull()()
G.pull('posts')()
G.pull()({ force: true })
G.pull('posts')({ force: true })
```

## g .现状

Status 是 GitState 保存**请求信息**和**跟踪元数据**的对象。它由轨迹键组织。该对象可用于处理加载/错误状态，并访问跟踪参数。

示例:

```
// G.status
// {
//  userDetails: {
//   fetched: <boolean>, // has it been fetched
//   fetching: <boolean>, // is it fetching now
//   loading: <boolean>, // see G.exec
//   hasChange: <boolean>, // did the request params change
//   error: <err object>, // error object for the last request
//   params: {...}, // current query parameters
//   options: {...}, // current query options
//  }
// }
```

## g .表格

[Forms](https://en.wikipedia.org/wiki/Theory_of_forms) 是一个内部使用的对象，通过它们的`__typename`和`id`保存所有获取的资源的标准化和扁平化索引。每次拉取后，GitState 都将这个对象与后端保持同步。它保存资源数据和上次获取时间(对于每个单独的字段)。Forms 对象是最近获取的资源版本的唯一来源，应用程序状态是该对象的派生。

## g .国家

应用程序的本地状态。由轨迹键组织。getter 函数从 forms 对象中获取当前状态，因此它总是包含每个资源的最新获取版本。此外，它在根级别保存未跟踪的局部变量。要获得状态，只需调用 getter 函数。

示例:

```
G.track('userDetails')(`...`)
G.track('favoritePosts')(`...`)
G.pull()// G.state()
// {
//  userDetails: {...},
//  favoritePosts: {...},
//  nonTrackedLocalVariable: 'blah'
// }
```

## G.add

创建或更新本地状态树中的节点。它接受一个**对象路径**和一个**节点的新值**。它是自动激活的，这意味着如果它不存在，它会创建一个新节点及其所有路径。

```
G.add(path)(val)
```

如果更新的节点被跟踪，这意味着它实际上只是 forms 对象上的一个资源的派生。在这种情况下，GitState 会更新相关的资源表单，因此从该资源派生的所有状态变量也会得到更新。

示例:

```
G.add('nonTrackedLocalVariable')('blah')
G.add('counter')(G.state().counter + 1)
G.commit()// Below, it actually updates the associated `User` resource on G.forms, so this change gets reflected on all references on the local state object that points to that resource

G.add('userDetails.email')('new@email.com')
G.commit()
```

请注意，这种变化只是局部的。修改数据的 GraphQL 方法是调用突变。如果更改没有发生在后端，对跟踪数据的本地状态的更新将在下一次拉取时被覆盖。参见 *G.exec* 。

## 提交

添加操作只是排队，直到提交后才生效。这确保了开发人员可以准确地决定何时更新状态，并且在此之前，使用该状态的所有内容都将收到相同的版本。

```
G.commit(options)
```

## g .执行

该命令用于执行突变。它接受一个**执行键**和一个 **GQL 变异**。它可以额外带**参数**和一些选项。

```
G.exec(key)(mutation)(params)(options)
```

突变调用的加载/错误状态在`G.status`上处理，就像轨道一样。它是在执行键下组织的:

```
G.exec('modifyUserDetails')(`...`)({...})({...})// G.status
// {
//  modifyUserDetails: {
//   loading: <boolean>,
//   error: <err object>,
//  }
// }
```

突变是在 GQL 中执行写操作的唯一方式，更新必须与本地状态保持同步。有几个选项可以做到这一点。下面是一个变异示例，展示了这些选项是如何工作的:

```
const m = `
  mutation ModifyUserEmail($id: String!){
    modifyUserEmail(id: $id){
      id
      email
    }
  }
`;// Note that since functions are curried, `exec` below is assigned to a function that takes in the options to trigger the G.exec call.const exec = G.exec('modifyUserEmail')(m)({ id: '123' });
```

*   **Merge** —如果您知道变异用修改后的资源或者只是修改后的位来响应，那么您可以使用`merge`选项。这将遍历响应，通过给定的`__typename`和`id`将资源与表单匹配，并更新表单对象。因此，在您的本地状态中，您将拥有修改后的资源的最新版本。

```
exec({ merge: true })
```

*   **手动** —如果您不咨询服务器响应就知道哪个字段发生了变化，或者如果您希望手动更新状态，您可以使用您首选的异步方法，并更新本地状态，如下所示:

```
// Manually update the local state
const updateEmail = res => {
  G.add('userDetails.email')(res.email);
  G.commit()
}// Callback
exec({ callback: updateEmail })// Promise
exec({ promise: true }).then(updateEmail)// Async / await
const res = await exec({ async: true })
updateEmail(res);
```

*   **触发** —有时候你可能不知道资源是如何改变的，后端可能不会返回修改后的版本。或者出于某种原因，您可能需要重新提取一个轨道。在这些情况下，您可以使用触发器选项。它接受一个或多个轨迹关键点，并将变异与给定轨迹相关联。它将 *G.status* 上所有关联轨迹的`loading`标志设置为真，一旦解决，它将触发对它们的强制 *G.pull* 。

```
exec({ trigger: 'userDetails' })// G.status
// {
//  userDetails: {..., loading: true },
//  modifyUserEmail: {..., loading: true},
// }
```

*   **混血儿**——你可以把以上所有的结合起来:

```
exec({
  merge: true,
  trigger: 'userDetails',
  callback: res => /* do something */
})
```

# 最后的话

正如我提到的，这不是一个真正的图书馆。它是不完整的，并且可能有更多的事情要考虑，以使这成为现实。如果你喜欢这个想法，你可能想看看阿波罗客户端。它没有 git 风格的 api，但是处理事情的方式非常相似。

我希望 GitState 能启发一些人，我也很想听听你们的想法！