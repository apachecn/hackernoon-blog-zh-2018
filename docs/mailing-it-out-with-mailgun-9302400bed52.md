# 用 Mailgun 寄出去！

> 原文：<https://medium.com/hackernoon/mailing-it-out-with-mailgun-9302400bed52>

![](img/73bb06515d9ef077b51643129452cc2e.png)

[上周](https://mmhaskell.com/blog/2018/3/19/sending-texts-with-twilio-and-haskell)，我们通过集成 Haskell 和 Twilio 开始了对 API 世界的探索。我们能够发送基本的 SMS 消息，然后创建一个能够响应用户消息的服务器。本周，我们将尝试另一种效果:发送电子邮件。我们将使用 [Mailgun](https://www.mailgun.com/) 完成这项任务，同时使用 [Hailgun](https://hackage.haskell.org/package/hailgun) Haskell API。

您可以通过查看我们的 Github 资源库上的`[mailgun](https://github.com/jhb563/HaskellApisSeries/tree/mailgun)`分支来查看本文的完整代码。如果这篇文章激发了你对更多 Haskell 库的好奇心，你应该下载我们的[生产清单](https://www.mmhaskell.com/production-checklist)！

# 做账

首先，我们显然需要一个 mailgun 帐户。注册是免费和简单的。它会问你一个电子邮件域，但你不需要一个开始。只要您处于测试模式，您就可以使用他们提供的沙盒域来托管您的邮件服务器。

对于 Twilio，我们必须指定一个“经过验证”的电话号码，以便在测试模式下发送消息。同样，您还需要指定一个经过验证的电子邮件地址。您的沙盒域将只能向此地址发送邮件。你还需要保存一些关于你的 Mailgun 账户的信息。特别是，您需要您的 API 密钥、沙盒电子邮件域以及您的电子邮件要使用的回复地址。将这些作为环境变量保存在本地系统和远程机器上。

# 基本电子邮件

现在，让我们通过发送一封基本的电子邮件来感受一下 Hailgun 代码。所有这些都发生在简单的`IO`单子上。我们最终想要使用函数`sendEmail`，它需要一个`HailgunContext`和一个`HailgunMessage`:

```
sendEmail
  :: HailgunContext
  -> HailgunMessage
  -> IO (Either HailgunErrorResponse HailgunSendResponse)
```

我们将从检索环境变量开始。利用我们的域和 API 键，我们可以构建需要作为参数传递的`HailgunContext`。

```
import Data.ByteString.Char8 (pack)sendMail :: IO ()
sendMail = do
  domain <- getEnv “MAILGUN_DOMAIN”
  apiKey <- getEnv “MAILGUN_API_KEY”
  replyAddress <- pack <$> getEnv “MAILGUN_REPLY_ADDRESS”
  -- Last argument is an optional proxy
  let context = HailgunContext domain apiKey Nothing
  ...
```

现在，为了构建消息本身，我们将使用一个构建函数`hailgunMessage`。它需要几个不同的参数:

```
hailgunMessage
 :: MessageSubject
 -> MessageContent
 -> UnverifiedEmailAddress -- Reply Address, just a ByteString
 -> MessageRecipients
 -> [Attachment]
 -> Either HailgunErrorMessage HailgunMessage
```

这些都很好填。`MessageSubject`是`Text`，然后我们将从上面传递我们的回复地址。对于内容，我们将从使用纯文本电子邮件的`TextOnly`构造函数开始。稍后我们将看到一个如何在内容中使用 HTML 的例子:

```
sendMail :: IO ()
sendMail = do
  …
  replyAddress <- pack <$> getEnv “MAILGUN_REPLY_ADDRESS”
  let msg = mkMessage replyAddress
  …
  where
    mkMessage replyAddress = hailgunMessage
      “Hello Mailgun!”
      (TextOnly “This is a test message.”)
      replyAddress
      ...
```

`MessageRecipients`类型有三个字段。首先是直接收件人，然后是抄送电子邮件，然后是密件抄送用户。我们目前只发送给一个用户。所以我们可以选择`emptyMessageRecipients`项并修改它。现在，我们将通过提供一个空的附件列表来结束我们的构造:

```
where
  mkMessage replyAddress = hailgunMessage
    “Hello Mailgun!”
    (TextOnly “This is a test message.”)
    replyAddress
    (emptyMessageRecipients { recipientsTo = [“verified@mail.com”] } )
    []
```

如果有问题，`hailgunMessage`函数会抛出一个错误，就像`sendEmail`函数本身一样。但是，只要我们检查这些错误，我们就能很好地发送电子邮件！

```
createAndSendEmail :: IO ()
createAndSendEmail = do
  domain <- getEnv “MAILGUN_DOMAIN”
  apiKey <- getEnv “MAILGUN_API_KEY”
  replyAddress <- pack <$> getEnv “MAILGUN_REPLY_ADDRESS”
  let context = HailgunContext domain apiKey Nothing
  let msg = mkMessage replyAddress
  case msg of
    Left err -> putStrLn (“Making failed: “ ++ show err)
    Right msg’ -> do
      result <- sendEmail context msg
      case result of
        Left err -> putStrLn (“Sending failed: “ ++ show err)
        Right resp -> putStrLn (“Sending succeeded: “ ++ show rep)
```

请注意，当我们从类型定义开始时，构建所有的函数是多么容易。我们可以研究每种类型，找出它需要什么。在这篇关于[编译驱动学习](https://mmhaskell.com/haskell-brain-4)的文章中，我对这个想法进行了更多的思考，这是我们为 Haskell 新手准备的 [Haskell 大脑系列](https://mmhaskell.com/haskell-brain)的一部分！

# 有效的电子邮件

现在，我们想将发送电子邮件整合到我们的服务器中。正如你从查看[源代码](https://github.com/jhb563/HaskellApisSeries/blob/mailgun/src/Server.hs)中注意到的，我修改了服务器以使用免费的单子。在我们的系统中有许多不同的影响，这有助于我们保持他们的直线。查看[这篇文章](https://mmhaskell.com/blog/2017/11/20/eff-to-the-rescue)了解更多关于自由单子和 Eff 库的细节。首先，我们想把发送电子邮件描述成一种效果。我们将从一个只有一个构造函数的简单数据类型开始:

```
data Email a where
  SendSubscribeEmail :: Text -> Email (Either String ())sendSubscribeEmail :: (Member Email r)
  => Text -> Eff r (Either String ())
sendSubscribeEmail email = send (SendSubscribeEmail email)
```

现在我们需要一种方法来剥离堆栈中的`Email`效果，只要我们有`IO`就可以做到这一点。我们将模仿我们已经编写的转换的`sendEmail`函数。现在，我们将接收用户的电子邮件作为输入！

```
runEmail :: (Member IO r) => Eff (Email ': r) a -> Eff r a
runEmail = runNat emailToIO
  where
    emailToIO :: Email a -> IO a
    emailToIO (SendSubscribeEmail subEmail) = do
      domain <- getEnv "MAILGUN_DOMAIN"
      apiKey <- getEnv "MAILGUN_API_KEY"
      replyEmail <- pack <$> getEnv "MAILGUN_REPLY_ADDRESS"
      let context = HailgunContext domain apiKey Nothing
      case mkSubscribeMessage replyEmail (encodeUtf8 subEmail) of
        Left err -> return $ Left err
        Right msg -> do
          result <- sendEmail context msg
          case result of
            Left err -> return $ Left (show err)
            Right resp -> return $ Right ()
```

# 扩展我们的短信处理程序

现在我们已经恰当地描述了发送电子邮件的效果，让我们把它合并到我们的服务器中！我们将从编写另一种数据类型开始，它将代表用户可能发给我们的潜在命令。目前，它只有“订阅”命令。

```
data SMSCommand = SubscribeCommand Text
```

现在，让我们编写一个函数，它将接收他们的消息，并将其解释为一个命令。如果他们发短信，我们会给他们发邮件！

```
messageToCommand :: Text -> Maybe SMSCommand
messageToCommand messageBody = case splitOn " " messageBody of
  ["subscribe", email] -> Just $ SubscribeCommand email
  _ -> Nothing
```

现在我们将扩展我们的服务器处理程序来回复。如果我们正确理解他们的命令，我们将发送电子邮件！否则，我们会给他们回短信说我们听不懂。注意我们的`SMS`效果和`Email`效果是这个处理程序的一部分:

```
smsHandler :: (Member SMS r, Member Email r)
  => IncomingMessage -> Eff r ()
smsHandler msg = 
  case messageToCommand (body msg) of
    Nothing -> sendText (fromNumber msg) 
      "Sorry, we didn't understand that request!"
    Just (SubscribeCommand email) -> do
      _ <- sendSubscribeEmail email
      return ()
```

现在我们的服务器将能够在用户“订阅”时发送电子邮件！

# 附加文件

让我们把我们的电子邮件变得复杂一点。现在我们只发送一封非常简单的电子邮件。让我们修改它，使它有一个附件。我们可以通过提供文件路径和描述文件的字符串来构建附件。为了得到这个文件，我们的消息生成函数需要当前的运行目录。我们还会稍微改变一下身体。

```
mkSubscribeMessage :: ByteString -> ByteString -> FilePath -> Either HailgunErrorMessage HailgunMessage
mkSubscribeMessage replyAddress subscriberAddress currentDir = 
  hailgunMessage
    "Thanks for signing up!"
    content
    replyAddress 
    (emptyMessageRecipients { recipientsTo = [subscriberAddress] })
    -- Notice the attachment!
    [ Attachment 
        (rewardFilepath currentDir)
        (AttachmentBS "Your Reward")
    ]
  where
    content = TextOnly "Here's your reward!”rewardFilepath :: FilePath -> FilePath
rewardFilepath currentDir = currentDir ++ "/attachments/reward.txt"
```

现在，当我们的用户注册时，他们将获得我们指定的任何附件文件！

# HTML 内容

为了展示更多的功能，让我们改变电子邮件的内容，使它包含一些 HTML 而不仅仅是文本！特别是，我们将让他们有机会通过点击我们服务器的链接来确认他们的订阅。这里所有的变化是我们将使用`TextAndHTML`构造函数代替`TextOnly`。我们确实想为我们的电子邮件提供一个纯文本的解释，以防 HTML 由于任何原因无法呈现。注意链接使用了`<a>`标签:

```
content = TextAndHTML 
   textOnly
   ("Here's your reward! To confirm your subscription, click " <> 
     link <> "!")
  where
    textOnly = "Here's your reward! To confirm your subscription, go to "
       <> "https://haskell-apis.herokuapp.com/api/subscribe/"
       <> subscriberAddress
       <> " and we'll sign you up!"
   link = "<a href=\"https://haskell-apis.herokuapp.com/api/subscribe/" 
     <> subscriberAddress <> "\">this link</a>"
```

现在，我们将添加另一个端点，它将捕获电子邮件作为参数，并将其保存到数据库中。`Database`效果非常类似于 [Eff 文章](https://mmhaskell.com/blog/2017/11/20/eff-to-the-rescue)中的效果。它会将电子邮件保存在一个数据库表中。

```
type ServerAPI = "api" :> "ping" :> Get '[JSON] String :<|>
  "api" :> "sms" :> ReqBody '[FormUrlEncoded] IncomingMessage
    :> Post '[JSON] () :<|>
  "api" :> "subscribe" :> Capture "email" Text :> Get '[JSON] ()subscribeHandler :: (Member Database r) => Text -> Eff r ()
subscribeHandler email = registerUser email
```

现在，如果我们想写一个函数，给我们系统中的每个人发邮件，这一点也不难！我们扩展了`Email`和`Database`的效果类型。`Database`函数将检索我们系统中的所有订户。同时`Email`效果会将指定的邮件发送给整个列表。

```
data Database a where
  RegisterUser :: Text -> Database ()
  RetrieveSubscribers :: Database [Text]data Email a where
  SendSubscribeEmail :: Text -> Email (Either String ())
  -- First parameter is (Subject line, Text content, HTML Context)
  SendEmailToList
    :: (Text, ByteString, Maybe ByteString)
    -> [Text]
    -> Email (Either String ())
```

组合这些只需要使用两种效果:

```
sendEmailToList :: (Member Email r, Member Database r) => ByteString -> ByteString -> Eff r ()
sendEmailToList = do
  list <- retrieveSubscribers
  void $ sendEmailToList list
```

注意没有任何`lift`电话！这是`Eff`的酷劲之一。

# 结论

正如我们在本文中看到的，用 Haskell 发送电子邮件并不可怕。API 是非常直观的，当你把事情一件一件的分解，看看涉及的类型。本文汇集了编译驱动开发和 Eff 框架的思想。特别是，我们可以在这个系列中看到用`Eff`来分离我们的效果是多么的方便，这样我们就不会做很多杂乱的提升。

这篇文章中有很多高级材料，所以如果你认为你需要回溯，不要担心，我们已经为你准备好了！我们的 [Haskell Web 技能系列](https://www.mmhaskell.com/haskell-web)将教你如何使用像 Persistent 这样的库进行数据库管理和 Servant 来制作 API。要获得更多可以用来编写增强型 Haskell 的库，请下载我们的[生产清单](https://www.mmhaskell.com/production-checklist)！

如果您从未使用 Haskell 编程，您应该尝试一下！下载我们的 [Haskell 初学者清单](https://www.mmhaskell.com/beginners-checklist)或者阅读我们的[升空系列](https://www.mmhaskell.com/liftoff)！