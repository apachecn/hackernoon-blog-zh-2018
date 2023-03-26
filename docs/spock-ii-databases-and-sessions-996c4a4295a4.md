# Spock II:数据库和会话！

> 原文：<https://medium.com/hackernoon/spock-ii-databases-and-sessions-996c4a4295a4>

![](img/9d68ae7c9ed592e253058fd59fa689af.png)

上周我们学习了 [Spock 库](https://www.spock.li/)的基础知识。我们看到了如何设置一些简单的路线。像 Servant 一样，有一点依赖型的路由机制。但是我们不需要学习任何复杂的操作符。我们只需要将参数的数量与我们的路线相匹配。我们还看到了如何使用应用程序状态在请求之间保存一些数据。

本周，我们将在 Spock 应用程序中添加一些更复杂的特性。首先，我们将连接到一个数据库。其次，我们将使用会话来跟踪用户。

更多有用的 Haskell 库的例子，请查看我们的[产品清单](https://www.mmhaskell.com/production-checklist)！

# 添加数据库

上周，我们添加了一些全局应用程序状态。即使有了这样的进步，我们的访客数量也没有持续增长。当我们重置服务器时，一切都消失了，我们的用户将看到一个不同的数字。我们可以通过向我们的服务器添加一个数据库连接来改变这一点。我们将按照 [Spock 教程](https://www.spock.li/tutorials/rest-api#adding-a-database)的例子，通过使用 Persistent 连接到 SQLite 数据库。

如果你以前没有用过 Persistent，看看我们 Haskell Web 系列中的[这个教程](https://www.mmhaskell.com/web-skills-1)！您还可以在 Github 上查看我们的[示例代码](https://github.com/jhb563/SpockExample)，寻找您可能遗漏的任何样板文件。下面是我们将使用的超级简单的模式。记住持久化会给我们一个自动递增的主键。

```
share [mkPersist sqlSettings, mkMigrate "migrateAll"] [persistLowerCase|
  NameEntry json
    name Text
    deriving Show
|]
```

Spock 希望我们在使用数据库时使用连接池。所以让我们使用`createSqlitePool`创建一个 SQLite 文件。我们需要从一个日志单子上运行这个。此时，我们可以从`main`启动函数中迁移我们的数据库。这确保了我们使用最新的模式:

```
import Database.Persist.Sqlite (createSqlitePool)...main :: IO ()
main = do
  ref <- newIORef M.empty
  pool <- runStdoutLoggingT $ createSqlitePool "spock_example.db" 5
  runStdoutLoggingT $ runSqlPool (runMigration migrateAll) pool
  ...
```

既然我们已经创建了这个池，我们可以将它传递给我们的配置。我们将使用`PCPool`构造函数。我们现在使用一个`SQLBackend`作为我们的服务器，所以我们也必须改变路由器的类型来反映这一点:

```
main :: IO ()
main = do
  …
  spockConfig <-
    defaultSpockCfg EmptySession (PCPool pool) (AppState ref)
  runSpock 8080 (spock spockConfig app)app :: SpockM SqlBackend MySession AppState ()
app = ...
```

现在我们想更新我们的 route 操作来访问数据库而不是地图。但是首先，我们将编写一个助手函数，它将允许我们从我们的`SpockM`单子中调用任何 SQL 动作。看起来是这样的:

```
runSQL :: (HasSpock m, SpockConn m ~ SqlBackend)
  => SqlPersistT (LoggingT IO) a -> m a
runSQL action = runQuery $ \conn -> 
  runStdoutLoggingT $ runSqlConn action conn
```

其核心是 Spock 库中的`runQuery`函数。因为我们的路由器现在使用`SpockM SqlBackend`而不是`SpockM ()`，所以它工作了。现在让我们写几个可以使用的 SQL 操作。我们将让一个按名称执行查找，并返回第一个匹配的条目的`Key`，如果存在的话。然后我们还会有一个插入新名字并返回它的键的函数。

```
fetchByName
  :: T.Text
  -> SqlPersistT (LoggingT IO) (Maybe Int64)
fetchByName name = (fmap (fromSqlKey . entityKey)) <$> 
  (listToMaybe <$> selectList [NameEntryName ==. name] [])insertAndReturnKey
  :: T.Text
  -> SqlPersistT (LoggingT IO) Int64
insertAndReturnKey name = fromSqlKey <$> insert (NameEntry name)
```

现在我们可以用这些功能代替我们的地图了！

```
app :: SpockM SqlBackend MySession AppState ()
app = do
  get root $ text "Hello World!"
  get ("hello" <//> var) $ \name -> do
    existingKeyMaybe <- runSQL $ fetchByName name
    visitorNumber <- case existingKeyMaybe of
      Nothing -> runSQL $ insertAndReturnKey name
      Just i -> return i
    text ("Hello " <> name <> ", you are visitor number " <> 
      T.pack (show visitorNumber))
```

瞧啊。我们可以在运行之间关闭我们的服务器，我们将保留我们已经看到的访问者！

# 跟踪用户

现在，使用一条路线来识别我们的用户并不是我们想要做的。毕竟任何人都可以参观任何路线！因此，对于服务器的最后一次修改，我们将添加一个小的“登录”功能。我们将使用应用程序的会话来跟踪用户当前正在访问什么。我们新流程将如下所示:

1.  我们将改变进入路线到`/hello`。
2.  如果用户访问这个，我们将显示一个字段，允许他们输入自己的名字并登录。
3.  按下登录按钮将发送一个 post 请求到我们的服务器。这将更新会话，使会话 ID 与用户名相匹配。
4.  然后，它会将用户发送到`/home`页面，该页面会问候用户并显示一个注销按钮。
5.  如果他们退出，我们就取消这个会话。

请注意，使用会话不同于我们在第一部分中使用的应用程序状态图。我们与使用我们服务器的每个人分享应用状态。但是会话将包含用户特定的引用。

# 添加会话

第一步是改变我们的会话类型。同样，我们将在地图周围使用一个`IORef`包装器。不过这一次，我们将使用简单的类型同义词来简化事情。这是我们的类型定义和更新后的 main 函数。

```
type MySession = IORef (M.Map T.Text T.Text)main :: IO ()
main = do
  ref <- newIORef M.empty
  -- Initialize a reference for the session
  sessionRef <- newIORef M.empty
  pool <- runStdoutLoggingT $ createSqlitePool "spock_example.db" 5
  runStdoutLoggingT $ runSqlPool (runMigration migrateAll) pool
  -- Pass that reference!
  spockConfig <-
    defaultSpockCfg sessionRef (PCPool pool) (AppState ref)
  runSpock 8080 (spock spockConfig app)
```

# 更新 Hello 页面

现在让我们更新我们的“Hello”页面。看看下面的附录，看看我们的`helloHTML`长什么样。这是一个带有用户名字段和提交按钮的“登录”表单。

```
-- Notice we use MySession!
app :: SpockM SqlBackend MySession AppState ()
app = do
  get root $ text "Hello World!"
  get "hello" $ html helloHTML
  ...
```

现在我们需要为`/hello`的 post 请求添加一个处理程序。我们将使用`post`函数来代替`get`。现在，我们将使用`body`函数提取 post 主体，而不是采用参数的操作。如果我们的应用程序更复杂，我们会希望使用一个合适的库来进行表单 URL 编码和解码。但是对于这个小例子，我们将使用一个简单的助手`decodeUsername`。您可以在附录中查看该助手。

```
app :: SpockM SqlBackend MySession AppState ()
app = do
  …
  post "hello" $ do
    nameEntry <- decodeUsername <$> body
    ...
```

现在，我们希望使用我们的会话保存该用户，然后将他们重定向到主页。首先，我们需要获得会话 ID 和会话本身。为此，我们使用函数`getSessionId`和`readSession`。然后，我们希望通过将名称与会话 ID 相关联来更新我们的会话。最后，我们将重定向到`home`。

```
post "hello" $ do
  nameEntry <- decodeUsername <$> body
  sessId <- getSessionId 
  currentSessionRef <- readSession
  liftIO $ modifyIORef' currentSessionRef $
    M.insert sessId (nameEntryName nameEntry)
  redirect "home"
```

# 主页

现在，在主页上，我们要检查是否有一个用户与会话 ID 相关联。如果我们这样做了，我们将向用户显示一些问候文本(并显示一个注销按钮)。同样，我们需要调用`getSessionId`和`readSession`。如果我们没有与会话相关联的用户，我们将把他们弹回`hello`页面。

```
get "home" $ do
  sessId <- getSessionId 
  currentSessionRef <- readSession
  currentSession <- liftIO $ readIORef currentSessionRef
  case M.lookup sessId currentSession of
    Nothing -> redirect "hello"
    Just name -> html $ homeHTML name
```

我们需要的最后一项功能是“注销”。我们将按照熟悉的模式获取会话 ID 和会话。这一次，我们将通过清除会话密钥来更改会话。然后我们将用户重定向回`hello`页面。

```
post "logout" $ do
  sessId <- getSessionId 
  currentSessionRef <- readSession
  liftIO $ modifyIORef' currentSessionRef $ M.delete sessId
  redirect "hello"
```

现在我们的网站跟踪我们用户的会话！我们可以在不同的会话中以不同用户的身份访问同一个页面！

# 结论

这就结束了我们对斯波克图书馆的探索！我们已经对 Spock 提供的一些不同特性进行了浅显而广泛的研究。我们看到了在服务器上跨请求保存信息的几种不同方法！连接到数据库是最重要的。但是使用会话是一个非常高级的功能，在 Spock 中非常简单！

想了解更多 Haskell web 库的例子，请看我们的 [Web 技能系列](https://www.mmhaskell.com/haskell-web)！您还可以下载我们的[生产清单](https://www.mmhaskell.com/production-checklist)来获得更多创意！

# 附录— HTML 片段和助手

```
helloHTML :: T.Text
helloHTML =
  "<html>\
    \<body>\
      \<p>Hello! Please enter your username!\
      \<form action=\"/hello\" method=\"post\">\
        \Username: <input type=\"text\" name=\"username\"><br>\
        \<input type=\"submit\"><br>\
      \</form>\
    \</body>\
  \</html>"homeHTML :: T.Text -> T.Text
homeHTML name =
  "<html><body><p>Hello " <> name <> 
    "</p>\
    \<form action=\"logout\" method=\"post\">\
      \<input type=\"submit\" name=\"logout_button\"<br>\
    \</form>\
  \</body>\
  \</html>" -- Note: 61 -> '=' in ASCII
-- We expect input like "username=christopher"
parseUsername :: B.ByteString -> T.Text
parseUsername input = decodeUtf8 $ B.drop 1 tail_
  where
    tail_ = B.dropWhile (/= 61) input
```