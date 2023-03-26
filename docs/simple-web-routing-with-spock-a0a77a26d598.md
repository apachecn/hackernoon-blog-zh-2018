# 简单的网络路由与斯波克！

> 原文：<https://medium.com/hackernoon/simple-web-routing-with-spock-a0a77a26d598>

![](img/3b1017c7b3df5f51f9e5579790ff4339.png)

在我们的 [Haskell Web 系列](https://www.mmhaskell.com/haskell-web)中，我们将回顾如何用 Haskell 构建 Web 应用程序的基础知识。这包括对我们的数据库层使用 Persistent，对我们的 HTTP 层使用 Servant。但是在 Haskell 生态系统中，这些并不是那些任务的唯一库。

我们已经了解了如何使用 Beam 作为另一个潜在的数据库库。在接下来的两篇文章中，我们将研究另一个 HTTP 库 [Spock](https://www.spock.li/) 。我们将它与 Servant 进行比较，看看不同的设计决策是什么。本周我们将从路由的基础知识开始。我们还将看到如何使用全局应用程序状态来协调服务器上的信息。下周，我们将看到如何连接数据库和使用会话。

对于一些有用的库，请确保下载我们的[生产清单](https://www.mmhaskell.com/production-checklist)。它会给你更多关于库的想法，你甚至可以超越这些！此外，您可以通过查看我们的 [Github 库](https://github.com/jhb563/SpockExample)来了解这里的代码！

# 入门指南

Spock 为我们提供了一个制作基本服务器的有用起点。我们将从他们主页上的[启动代码](https://www.spock.li/)开始。这是我们最初的改编:

```
data MySession = EmptySession
data MyAppState = DummyAppState (IORef Int)main :: IO ()
main = do
  ref <- newIORef 0
  spockConfig <- defaultSpockCfg EmptySession PCNoDatabase (DummyAppState ref)
  runSpock 8080 (spock spockConfig app)app :: SpockM () MySession MyAppState ()
app = do
  get root $ text "Hello World!"
  get ("hello" <//> var) $ \name -> do
    (DummyAppState ref) <- getState
    visitorNumber <- liftIO $ atomicModifyIORef' ref $ \i -> (i+1, i+1)
    text ("Hello " <> name <> ", you are visitor number " <> T.pack (show visitorNumber))
```

在我们的 main 函数中，我们初始化了一个 IO ref，我们将用它作为应用程序的唯一“状态”。然后，我们将为我们的服务器创建一个配置对象。最后，我们将使用实际路线的`app`规范来运行我们的服务器。

该配置有几个重要的附加字段。目前，我们对所有这些都使用虚拟值。我们的配置需要一个`Session`，我们将其定义为`EmptySession`。它还需要某种数据库，我们稍后会添加。最后，它包括一个应用程序状态，现在我们只提供指向一个整数的指针。稍后我们将看到如何给这些参数添加更多的味道。但是现在，让我们更深入地研究一下定义服务器路由的`app`表达式。

# 斯波克姆单子

我们的路由器生活在`SpockM`单子里。我们可以看到它有三个不同的类型参数。记住`defaultSpockConfig`有三个相似的参数！我们将空会话称为`MySession`，将`IORef`应用状态称为`MyAppState`。最后，有一个额外的`()`参数对应于我们的空数据库。(我们路由器的返回值也是`()`)。

现在这个单子的每个元素都是一个路径组件。如您所料，这些路径组件使用 HTTP 动词。目前，我们的路由器只有几条`get`路由。第一个位于我们路径的`root`，输出`Hello World!`。第二个位于`hello/{name}`。它将打印一条指定输入名称的消息，同时跟踪我们有多少访问者。

# 构成路线

现在，让我们来谈谈我们的路由器代码的结构。`SpockM`单子的工作方式类似于`Writer`单子。我们采取的每一个动作都为应用程序增加了一条新的路径。在这种情况下，我们采取两个动作，每个动作响应`get`请求(下周我们将看到一个`post`请求的例子)。

对于我们的任何 HTTP 动词，第一个参数将是路径的表示。在我们的第一条路线上，我们使用硬编码的`root`表达式来引用`/`路径。对于我们的第二个表达式，我们用`<//>`组合了几个不同的组件。

首先，我们有一个字符串路径组件`hello`。我们也可以组合其他字符串。假设我们想要路线`/api/hello/world`。我们会用这样的表达:

```
"api" <//> "hello" <//> "world"
```

但是在我们的原始代码中，路径的第二部分是一个`var`。这允许我们将信息替换到路径中。当我们访问`/hello/james`时，我们将能够得到路径组件`james`作为一个变量。Spock 将这个参数传递给我们的函数，作为`get`组合子的第二个参数。

这个论点有一个相当复杂的类型`RouteSpec`。这里不需要赘述细节。但是我们可以使用`text`组合子返回的最简单的东西是一些原始文本。(如果我们有自己的模板，我们也可以使用`html`)。我们通过这样做来结束我们的路由定义。

请注意，第一条路由的表达式没有参数，而第二条路由只有一个参数。正如您可能猜到的那样，第二条路径中的参数指的是由于`var`我们可以从路径中取出的变量。路径中的`var`元素数量与函数的参数数量相同。Spock 使用依赖类型来确保这些匹配。

# 使用应用程序状态

现在我们知道了基础知识，让我们开始使用 Spock 的一些更高级的功能。本周，我们将了解如何使用应用程序状态。

目前，我们每次访问有名称的路线时都会增加游客数量，即使名称是相同的。所以第一次访问`/hello/michael`的结果是:

```
Hello michael, you are visitor number 1
```

然后我们再去看看:

```
Hello michael, you are visitor number 2
```

相反，让我们把每个名字分配给一个特定的号码。这样，当用户再次访问相同的路线时，他们将看到他们原来的号码。

做出这种改变相当容易。我们将使用从`Text`到`Int`的映射，而不是在`Int`上使用`IORef`来表示我们的状态:

```
data AppState = AppState (IORef (M.Map Text Int))
```

现在我们将用一个空的 map 初始化我们的 ref，并将其传递给我们的 config:

```
main :: IO ()
main = do
  ref <- newIORef M.empty
  spockConfig <- defaultSpockCfg EmptySession PCNoDatabase (AppState ref)
  runSpock 8080 (spock spockConfig app)
```

对于我们的`hello/{name}`路线，我们将按照以下流程对其进行更新:

1.  获取地图参考
2.  看看我们是否有这个用户的条目。
3.  如果没有，插入地图的长度，并写回我们的`IORef`
4.  返回消息

这个过程非常简单。让我们看看它是什么样子的:

```
app :: SpockM () MySession AppState ()
app = do
  get root $ text "Hello World!"
  get ("hello" <//> var) $ \name -> do
    (AppState mapRef) <- getState
    visitorNumber <- liftIO $ atomicModifyIORef' mapRef $ updateMapWithName name
    text ("Hello " <> name <> ", you are visitor number " <> T.pack (show visitorNumber))updateMapWithName :: T.Text -> M.Map T.Text Int -> (M.Map T.Text Int, Int)
updateMapWithName name nameMap = case M.lookup name nameMap of
  Nothing -> (M.insert name (mapSize + 1) nameMap, mapSize + 1)
  Just i -> (nameMap, i)
  where
    mapSize = M.size nameMap
```

我们创建了一个函数，每当我们的应用程序遇到一个新名称时，它就会更新地图。我们用`atomicModifyIORef`更新我们的`IORef`。现在，如果我们连续访问`/hello/michael`两次，我们将得到相同的输出！

# 结论

这是我们这个星期能去的最远的地方！我们讲述了如何在 Spock 中创建一个基本应用程序的基础知识。我们看到了构成路线的基础。然后我们看到了如何使用应用程序状态来跟踪跨请求的信息。下周，我们将通过在应用程序中添加一个数据库来改进这个过程。我们还将使用会话来跟踪用户。

想要更多的酷库，请阅读我们的 Haskell 网络系列。此外，您可以下载我们的[生产清单](https://www.mmhaskell.com/production-checklist)了解更多创意！