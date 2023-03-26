# 高级 Github: Webhooks 和自动化

> 原文：<https://medium.com/hackernoon/advanced-github-webhooks-and-automation-952ee091778c>

![](img/26fa3a6530156575e36a538e5d348fb3.png)

几周前，我们看到了如何结合使用 Docker 和 Heroku 来部署我们的 Haskell 应用程序。这个过程使 Circle CI 配置比以前更简单，因为我们让 Docker 做了大部分繁重的工作。特别是，我们不再需要自己下载和构建 stack。我们在 docker 文件中指定了构建过程，然后调用`docker build`。我们还看到了从 Circle CI 框登录这些服务的几种不同方式。

在未来，我们将着眼于使用比 Heroku 更多样化的部署平台。我们将特别关注 AWS。但这是一个棘手的问题，所以它可能值得自己的系列！现在，我们将通过查看 Github 开发人员 API 来结束我们的部署系列。您将从事的大多数项目都使用 Github 进行版本控制。但是使用 API，有很多有趣的技巧可以让你的体验更酷！本周，我们将看到如何设置一个服务器来响应我们存储库中发生的事件。然后我们将看看如何从服务器发送我们自己的事件！你可以通过查看[这个 Github 库](https://github.com/jhb563/GithubServer)来理解这段代码！

这篇文章基于我们对 Servant library 的了解。如果你以前从未用过，我强烈推荐你阅读我们的 [Haskell Web Skills](https://www.mmhaskell.com/haskell-web) 系列。你将了解仆人和更多！您还可以下载我们的[生产清单](https://www.mmhaskell.com/production-checklist)，了解更多用于您应用的工具。

# Github Webhooks 入门

首先让我们理解 webhooks 的概念。除了 Github 很多服务也用。webhook 是一个集成，每当发生一些事件时，服务将向您选择的端点发送 HTTP 请求。Webhooks 通常是你从系统中获得一些更高级功能的一种方式。他们可以让你自动化很多流程。有了 Github，我们可以定制发生这种情况的事件。例如，我们可以在创建一个拉请求时触发一个请求。

在本文中，我们将设置一个非常简单的服务器来完成这项工作。当他们打开一个新的 PR 时，我们将添加一个评论，说我们将很快查看拉请求。我们还会让评论标记我们的帐户，这样我们就会收到通知。

Github 部分很简单。我们转到存储库的设置，然后找到“Webhooks”部分。我们将为自定义事件添加一个 webhook，我们将只选中“Pull Requests”旁边的框。我们将把它分配给一个服务器的 URL，我们将把它放在 Heroku 服务器上，点击`/api/hook`端点。

# 构建我们的服务器

首先让我们为 Github 请求创建一个数据类型。这将是一个简单的双构造函数类型。我们的第一个构造函数将包含关于一个打开的拉请求的信息。我们希望从请求对象中获取用户名，以及我们用来发送评论的 URL。我们还将有一个`Other`构造函数，用于当请求不是关于一个开放的拉请求时。

```
data GithubRequest =
  GithubOpenPRRequest Text Text | -- User’s name, comments URL
  GithubOtherRequest
  deriving (Show)
```

因此，我们需要一个简单的服务器来监听特定端点上的请求。和过去一样，我们将在这个过程中使用 Servant。我们的端点类型将使用我们想要的路径。然后它还会用我们的 GithubRequest 接收一个请求体。我们将监听 post 请求，然后返回一个`Text`作为结果，以帮助调试。

```
type ServerAPI = “api” :> “hook” :> 
  ReqBody ‘[JSON] GithubRequest :> Post ‘[JSON] Text
```

现在我们需要为我们的请求类型指定一个`FromJSON`实例。使用[文档](https://developer.github.com/v3/)，我们将找到一些需要阅读的字段来实现这一点。首先，我们将检查这个请求是否确实有一个 pull 请求部分，并且它的操作是“opened”。如果这些不存在，我们将返回`Other`:

```
instance FromJSON GithubRequest where
  parseJSON = withObject “GithubRequest” $ \o -> do
    (action :: Maybe Text) <- o .:? “action”
    prSectionMaybe <- o .:? “Pull_request”
    case (action, prSectionMaybe) of
      (Just “opened”, Just pr_section :: Maybe Value) -> do
        …
      _ -> return GithubOtherRequest
```

现在我们可以从`pull_request`部分获取用户部分和评论 URL。我们用一个`Data.Aeson`对象上的函数这样做:

```
where
  fetchUserAndComments o’ = do
    uSection <- o’ .: “user”
    commentsURL <- o’ .: “comments_url”
    return (uSection, commentsURL)
```

注意我们要的是`comments_url`，不是`review_comments_url`！我们想留下一个单一的评论，而不是执行一个完整的审查这一公关。发现文档在[问题](https://developer.github.com/v3/issues/comments/)部分中包含了这一点，而不是关于拉取请求的部分，这非常令人恼火！一旦我们得到了用户部分和评论，URL，我们还需要一个步骤。我们将从该部分中获取用户名，然后返回我们的最终请求！

```
instance FromJSON GithubRequest where
  parseJSON = withObject “GithubRequest” $ \o -> do
    (action :: Maybe Text) <- o .:? “action”
    prSectionMaybe <- o .:? “Pull_request”
    case (action, prSectionMaybe) of
      (Just “opened”, Just pr_section :: Maybe Value) -> do
        (userSection :: Value, commentsURL :: Text) <-
          withObject “PR Section” fetchUserAndComments prSection
        userName <-
          withObject “User Section” (\o’ -> o’ .: “login”) userSection
        return $ GithubOpenPRRequest userName commentsURL
      _ -> return GithubOtherRequest
```

# 处理端点

现在我们需要一个端点处理函数。该处理程序将对请求类型进行模式匹配，并返回一个调试字符串。如果我们确实发现了打开 PR 的请求，我们还想调用另一个`IO`函数来添加我们的注释:

```
hookHandler :: GithubRequest -> Handler Text
hookHandler GithubOtherRequest =
  return “Found a non-PR opening request.”
hookHandler (GithubOpenPRRequest userName commentsURL) = do
  liftIO $ addComment userName commentsURL
  return $ “User: “ <> userName <> 
    “ opened a pull request with comments at: “ <> commentsURLaddComment :: Text -> Text -> IO ()
...
```

# 添加评论

为了给这个 pull 请求添加注释，我们需要用我们自己的请求访问 Github API。同样，我们将使用仆人的魔法！首先，我们再做一个 API 类型来表示 Github 自己的开发者 API。由于我们在请求中获得了完整的评论 URL，所以这里不需要任何路径组件。但是我们需要使用`BasicAuth`进行认证:

```
type GithubAPI = BasicAuth “GithubUser” () :>
  ReqBody GitPRComment :> Post ‘[JSON] ()
```

我们的`GitPRComment`只需要一个`Text`作为评论的主体。因此，让我们制作一个简单的`newtype`包装器，并为其添加一个`ToJSON`实例:

```
newtype GitPRComment = GitPRComment Textinstance ToJSON GitPRComment where
  toJSON (GitPRComment body) = object [ “body” .= body ]
```

我们现在可以使用来自`Servant.Client`的神奇的`client`函数为这个 API 创建一个客户端函数:

```
sendCommentClient :: BasicAuthData -> GitPRComment -> ClientM ()
sendCommentClient = client (Proxy :: Proxy GithubAPI)
```

现在，为了构建我们的注释函数，我们将从构建 auth 数据开始。

```
import qualified Data.ByteString.Char8 as BSC...
addComment :: Text -> Text -> IO ()
addComment userName commentsURL = do
  gitUsername <- getEnv “GITHUB_USERNAME”
  gitPassword <- getEnv “GITHUB_PASSWORD”
  let authData = BasicAuthData (BSC.pack gitUsername)
                               (BSC.pack gitPassword)
  ...
```

现在，我们将使用注释 URL 设置我们的客户端环境:

```
addComment :: Text -> Text -> IO ()
addComment userName commentsURL = do
  ...
  manager <- newManager tlsManagerSettings
  baseUrl <- parseBaseUrl (Data.Text.unpack commentsURL)
  let clientEnv = clientEnv maanger baseUrl
  ...
```

我们将添加一个简单的函数，以我们的管理员的用户名和组成评论的主体。我们将标记自己以及打开 PR 的用户:

```
addComment :: Text -> Text -> IO ()
addComment userName commentsURL = do
  …
  where
     commentBody adminName = GitPRComment $
       “Thanks for posting this @” <> userName <>
       “! I’ll take a look soon! - @” <> adminName
```

现在，我们通过给客户打电话来把所有的事情打包在一起。就是这样！

```
addComment :: Text -> Text -> IO ()
addComment userName commentsURL = do
  gitUsername <- getEnv “GITHUB_USERNAME”
  gitPassword <- getEnv “GITHUB_PASSWORD”
  let authData = BasicAuthData (BSC.pack gitUsername)
                               (BSC.pack gitPassword)
  manager <- newManager tlsManagerSettings
  baseUrl <- parseBaseUrl (Data.Text.unpack commentsURL)
  let clientEnv = clientEnv maanger baseUrl
  runClientM (sendCommentClient
                authData
               (commentBody gitUsername))
             clientEnv
  return ()
  where
    commentBody = ...
```

# 结论

像 Github 这样的服务尽最大努力为所有普通用户提供良好的用户体验。但是如果你有一点点进步，你经常可以在很大程度上定制他们的行为！注意了解如何设置一个简单的服务器是多么重要。这给了你无限的自由来操纵系统和添加你自己的行为。学习这些特定的网络技能是一个很酷的额外津贴。如果你想看我为这篇文章写的完整代码，可以在[这个 Github repo](https://github.com/jhb563/GithubServer) 上查看！

要了解更多可以增强你编程能力的网络技能，请查看我们的 [Haskell 网络技能系列](https://www.mmhaskell.com/haskell-web)。它将带您了解一些不同的 Haskell 库，比如数据库的 Persistent 和 web 服务器的 Servant。您也可以下载我们的[生产清单](https://www.mmhaskell.com/production-checklist)。它会给你更多的库的想法来增强你的 Haskell 体验！