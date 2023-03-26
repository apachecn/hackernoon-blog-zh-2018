# 用无服务器的 PubNub 函数构建你自己的简单 Slackbot

> 原文：<https://medium.com/hackernoon/build-your-own-simple-slackbot-with-serverless-pubnub-functions-98549204ec39>

大家都在用[懈怠](https://hackernoon.com/tagged/slack)。从分享代码到在凌晨 2 点给同事发送猫迷因，Slack 已经成为企业的聊天和协作平台。不幸的是，信息传递并不在[生产力](https://hackernoon.com/tagged/productivity)食物链的顶端。

真正震撼数据分析者和思想领袖的是对聊天机器人的普遍需求。也就是休闲裤。这背后的原因是，机器人允许 24 小时进行任务处理或信息查找，而不需要另一个人。你可以这样想，如果没有 Slackbots，我们将被迫安排自己的会议。哦，人类！

# 所以你想造一个懒人机器人？

![](img/79a3536c6e1f874762317010665bc49c.png)

首先，去 [Slack API 资源中心](https://api.slack.com/)创建一个新的 Slack 应用。我们需要设置一个名称，也是一个发展松弛组。在我的例子中，我将它命名为 mined **PNbot** ，并将其分配给我的一个组。

一旦创建了应用程序，我们就可以选择向应用程序添加不同的功能。由于我们的重点是创建一个 Slackbot，点击进入`Bots`选项。

![](img/020b6796e0c8593f7849ebcddf3db2df.png)

在机器人部分，我们将有机会为我们的机器人添加用户名和显示名称。在这两种情况下，我都选择将机器人命名为 **pnbot** 。请记住，在底部附近有一个开关，当被选中时，它会保持机器人在线。这在我们的场景中是有意义的，因为多亏了 PubNub，我们的机器人将在大约 99.999%的时间内可用。

所以我们现在有了机器人，但是我们如何让它在我们的工作空间中工作呢？只需点击侧边栏设置部分的安装应用副标题。就是这样！我们的 bot 现在应该可以在我们的演示工作区中使用了。

就这样，我们的机器人开始运行了！现在下一步是查看 Slack API 站点侧栏中的特性部分。在该部分，我们将找到 [**事件订阅**](https://api.slack.com/events-api) 按钮并导航至该按钮。这将把我们带到一个空白页面，但不用担心，因为如果我们单击切换按钮，我们可以启用该功能。

启用后，请注意有一个用于重定向 URL 的空间。不要太担心这个，因为我们会在构建*无服务器*功能时深入探讨这个问题。我们将看到的是重定向 URL 下面的工作区事件。在这里，我们可以选择一些事件，这些事件已经在文档中进行了适当的概述。在我们的例子中，为了简单起见，我们将选择提及事件。这意味着，当 bot 出现在任何渠道中时，它将向重定向 URL 发送一个包含相关信息的 JSON 有效负载。到目前为止，这就是我们现在所需要的。

# 创建无服务器 Slackbot 函数

首先，你必须[注册一个 PubNub 账户](https://admin.pubnub.com/#/register)。按照这个顺序，我们需要- >创建一个应用- >导航到键集- >点击左边的函数选项卡。在这里，我们将创建一个新的按需模块。这意味着我们的模块将作为一个 web 挂钩，Slackbot 可以重定向到这个挂钩。

现在我们已经配置了我们的函数，我们需要设置一个路径，并复制 URL 以设置在我们之前避免的重定向 URL 字段中。这将使得每次提到机器人时，我们的函数都会收到类似于以下内容的有效载荷:

```
"request"{  
   **"verb"**:"rest",
   **"pubkey"**:"your pub key",
   **"subkey"**:"your sub key",
   **"timetoken"**:null,
   **"timetoken_v1"**:null,
   **"timetoken_v2"**:null,
   **"version"**:"v1",
   **"meta"**:{  
      **"clientip"**:"100.25.152.207",
      **"origin"**:"pubsub.pubnub.com",
      **"useragent"**:"Slackbot 1.0 (+https://api.slack.com/robots)"
   },
   **"params"**:{  

   },
   **"uri"**:"/your-path/bot",
   **"path"**:"/bot",
   **"method"**:"POST",
   **"body"**:"{\"token\":\"token\",\"team_id\":\"T0431NL8C\",\"api_app_id\":\"AEQFW5DFV\",\"event\":{\"client_msg_id\":\"4d282463-9622-4979-ae71-2b073895711c\",\"type\":\"message\",\"text\":\"&lt;@UEQEUM144&gt; &lt;@UEQG0576F&gt;\",\"user\":\"UA69Q4A64\",\"ts\":\"1544556685.000600\",\"channel\":\"DES1K0SGN\",\"event_ts\":\"1544556685.000600\",\"channel_type\":\"im\"},\"type\":\"event_callback\",\"event_id\":\"EvESLB9J4E\",\"event_time\":1544556685,\"authed_users\":[\"UEQG0576F\"]}",
   **"headers"**:{  
      **"user-agent"**:"Slackbot 1.0 (+https://api.slack.com/robots)",
      **"content-length"**:"418",
      **"accept"**:"*/*",
      **"accept-encoding"**:"gzip,deflate",
      **"content-type"**:"application/json",
      **"pfunc-original-uri"**:"/your url/bot",
      **"x-forwarded-host"**:"sub-key_js-on-rest",
      **"x-slack-request-timestamp"**:"1544556686",
      **"x-slack-signature"**:"v0=signature"
   }
}
```

我们的机器人现在连接到我们的函数，现在我们希望我们的机器人开始发送消息。查看 Slack 文档，我们可以看到最好的方法是将我们的消息发送到 Slack web API。

在这种情况下，我们将为我们的机器人启用 [**传入 webhooks**](https://api.slack.com/incoming-webhooks) 。这将允许我们能够发布到 webhook，让我们的消息被发送。我们可以通过导航到“功能”部分中的子标题来启用此功能，在该部分中我们启用了事件订阅。

![](img/e460cc5c2389c7c0e725f2b6cee0b231.png)

授权完成后，我们要发布到的端点将如下所示:

```
[https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX](https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX)
```

为了测试功能性，在我们的无服务器函数内部，发送 [**XHR 请求**](https://www.pubnub.com/docs/blocks/xhr-module) 给传入的 webhook Slack 提供给我们一个消息。

```
export default (request, response) => {
  const pubnub = require("pubnub");
  const kvstore = require("kvstore");
  const xhr = require("xhr");
  let body = JSON.parse(request.body);
  const http_options = {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ text: "cool story bro!!!" })
  };
  const url =
    "[https://hooks.slack.com/services/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](https://hooks.slack.com/services/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)";
  response.status = 200;
  response.headers["X-Custom-Header"] = "CustomHeaderValue";
  return xhr.fetch(url, http_options).then(x => {
    return response.send(body);
  });
};
```

在这种情况下，每当在一个频道中提到我们的 bot 时，它都会回复“酷故事老弟！！!"。

![](img/c7736a8cdeea0cd64b81c96a8e36fd90.png)

# 后续步骤

就是这样！如果我激起了你对机器人或无服务器的兴趣，我强烈推荐你看看这些文章:

*原载于*[*www.pubnub.com*](https://www.pubnub.com/blog/build-your-own-simple-slackbot-serverless-pubnub-functions/)*。*