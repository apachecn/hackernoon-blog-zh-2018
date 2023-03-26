# 用 Twilio + Haskell 发短信！

> 原文：<https://medium.com/hackernoon/sending-texts-with-twilio-haskell-24960b0ffd05>

![](img/99b12bc6d5d16fcd22d958f6b5c7a4c4.png)

只使用简单的库编写我们自己的 Haskell 代码很有趣。但是我们不能一切从零开始。有各种各样的酷服务可以使用，所以我们不需要这么做。通过使用 API，我们可以与其中的许多接口。通常，最受支持的 API 使用像 Python 和 Javascript 这样的语言。但是富有冒险精神的 Haskell 开发人员也为这些系统开发了绑定！所以在接下来的几周里，我们将探索其中的一些。我们还将看看当没有现成的库可供我们使用时，我们能做些什么。

本周，我们将关注 Twilio API。我们将看到如何使用 [twilio](https://hackage.haskell.org/package/twilio) 库从我们的 Haskell 代码中发送 SMS 消息。我们还将编写一个简单的服务器来使用 Twilio 的回调系统接收文本消息并以编程方式处理它们。您可以在这个系列的 [Github 库](https://james-bowen-c4sg.squarespace.com/blog/2018/3/19/TODO)上跟随代码。

当然，如果您以前从未编写过任何 Haskell，这些都没有用！如果你想从语言基础开始，下载我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)。要了解更多关于高级技术和库的信息，请访问我们的[生产清单](https://www.mmhaskell.com/production-checklist)！

# 设置我们的帐户

自然，您需要一个 Twilio 帐户来使用 Twilio API。设置好之后，您需要添加您的第一个 Twilio 号码。这将是您要发送短信的号码。您还会将其视为系统中其他邮件的发件人。您还应该经历验证您自己的电话号码的过程。这将允许您在手机上发送和接收信息，而无需“发布”您的应用程序。

您还需要您的帐户中的一些其他信息。有帐户 SID 和身份验证令牌。您可以在 Twilio 页面的项目仪表板上找到这些信息。您的代码中需要这些值。但是由于您不想将它们置于版本控制之下，您应该将它们作为环境变量保存在您的机器上。然后当你需要的时候，你可以像这样获取它们:

```
fetchSid :: IO String
fetchSid = getEnv “TWILIO_ACCOUT_SID”fetchToken :: IO String
fetchToken = getEnv “TWILIO_AUTH_TOKEN”
```

# 发送消息

我们要做的第一件事是使用 API 实际发送一条文本消息。我们在`Twilio`单子内执行 Twilio 动作。从`IO`访问这个单子非常简单。我们所需要的就是`runTwilio’`函数:

```
runTwilio’ :: IO String -> IO String -> Twilio a -> IO a
```

这个函数的前两个参数是获取账户 SID 和认证令牌的`IO`动作。我们已经写好了。那么最后一个参数当然就是我们的`Twilio`动作了。

```
sendMessage :: IO ()
sendMessage = runTwilio’ fetchSid fetchToken $ do
  ...
```

为了编写消息，我们将使用`PostMessage`构造函数。这需要三个参数。首先，我们信息的“收件人”号码。把你的手机号码填进去。然后，第二个参数是“发件人”号码，这必须是我们的 Twilio 帐户的电话号码。第三个参数是消息本身。要发送消息，我们所要做的就是使用`post`功能！这就是全部了！

```
sendMessage :: IO ()
sendMessage = runTwilio’ fetchSid fetchToken $ do
  let msg = PostMessage “+15551231234” “+15559879876” “Hello Twilio!”
  _ <- post msg
  return ()
```

就这样，你发出了第一条 Twilio 消息！请注意，通过 Twilio 发送消息确实要花费少量的钱。但是一个试用帐户应该给你足够的免费信用来尝试一点。

# 接收消息

现在，处理传入的消息有点复杂。我们需要做的第一件事是在我们的 Twilio 帐户上创建一个 webhook。为此，请从您的项目仪表板页面转到“管理数字”。然后选择您的 Twilio 号码。现在，您需要滚动到名为“消息”的部分，然后在其中找到“有消息进来”。您需要在下拉列表中选择“Webhook”。然后，您需要指定您的服务器所在的 URL，并选择“HTTP Post”。为了建立一个快速的服务器，我使用 Heroku 结合这个[漂亮的构建包](https://github.com/mfine/heroku-buildpack-stack)与 Stack 一起工作。我将在以后的文章中对此进行更深入的探讨。但主要是要看到我们的终点是`/api/sms`。

![](img/3613ac48a225c8d7a2146031c6ceb28b.png)

有了这个 webhook 之后，每当用户发送我们的号码时，Twilio 就会向端点发送一个 post 请求。该请求将包含消息和发送者的号码。因此，让我们使用 Servant 设置一个服务器来接收该请求。

我们将首先指定一个简单的类型来编码我们将从 Twilio 收到的消息:

```
data IncomingMessage = IncomingMessage
  { fromNumber :: Text
  , body :: Text
  }
```

Twilio 将其 post 请求主体编码为`FormURLEncoded`。为了让 Servant 对此进行反序列化，我们需要为我们的类型定义一个`FromForm`类的实例。这个函数接受一个从键到值列表的散列映射。它将返回一个错误字符串或者我们想要的值。

```
instance FromForm IncomingMessage where
  fromForm :: Form -> Either Text IncomingMessage
  fromForm (From form) = ...
```

所以`form`是一个散列图，我们想要查找消息的“发件人”号以及消息体。然后，只要我们为这些中的每一个找到至少一个结果，我们将返回消息。否则，我们返回一个错误。

```
instance FromForm IncomingMessage where
  fromForm :: Form -> Either Text IncomingMessage
  fromForm (From form) = case lookupResults of
    Just ((fromNumber : _), (body : _)) -> 
      Right $ IncomingMessage fromNumber body
    Just _ -> Left “Found the keys but no values”
    Nothing -> Left “Didn’t find keys”
    where
      lookupResults = do
        fromNumber <- HashMap.lookup “From” form
        body <- HashMap.lookup “Body” form
        return (fromNumber, body)
```

现在我们有了这个实例，我们终于可以定义我们的 API 端点了！它只需要简单的路径组件和请求体。目前，我们实际上不会发布任何回应。

```
type TwilioServerAPI = "api" :> "sms" :> 
  ReqBody '[FormUrlEncoded] IncomingMessage :> Post '[JSON] ()
```

# 编写我们的处理程序

现在让我们为我们的端点写一个处理程序。首先，我们将编写一个自然转换，这样我们就可以在`Twilio`单子中编写我们的处理程序。

```
transformToHandler :: Twilio :~> Handler
transformToHandler = NT $ \action -> 
  liftIO $ runTwilio' fetchSid fetchToken action
```

现在，我们将编写一个简单的处理程序，将用户的消息回显给他们。

```
twilioNum :: Text
twilioNum “+15559879876”smsHandler :: IncomingMessage -> Twilio ()
smsHandler msg = do
  let newMessage = PostMessage (fromNumber msg) twilioNum (body msg)
  _ <- post newMessage
  return ()
```

现在我们总结一下运行服务器的一些服务机制。

```
twilioAPI :: Proxy TwilioServerAPI
twilioAPI = Proxy :: Proxy TwilioServerAPItwilioServer :: Server TwilioServerAPI
twilioServer = enter transformToHandler smsHandlerrunServer :: IO ()
runServer = do
  port <- read <$> getEnv “PORT”
  run port (serve twilioAPI twilioServer)
```

现在，如果我们向我们的 Twilio 号码发送短信，我们将会看到相同的回复消息！

# 结论

在本文中，我们看到了如何使用几行简单的 Haskell 来发送和接收文本消息。使用 Twilio 工具本身需要付出相当大的努力，但是一旦你知道去哪里找，大部分工作就很容易了！下周回来，我们将探索如何使用 [Mailgun](https://www.mailgun.com/) API 发送电子邮件。我们将看到如何将文本和电子邮件结合起来，实现一些非常酷的功能。

让这些应用变得简单的一件重要的事情是知道使用正确的工具！我们在这一部分中使用的工具之一是服务 web API 库。要了解更多，一定要看看我们的 [Haskell 网络技能系列](https://www.mmhaskell.com/haskell-web)。想了解更多关于网络库的想法，请下载我们的[产品清单](https://www.mmhaskell.com/production-checklist)。

如果你以前从未写过 Haskell，希望我已经让你相信用这种语言做一些很酷的事情是可能的！下载我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)以获得陈述！