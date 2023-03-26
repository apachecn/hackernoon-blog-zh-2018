# 使用 Slack、标准库和 Node.js 在几分钟内构建一个正常运行时间监视器——就在您的浏览器中

> 原文：<https://medium.com/hackernoon/build-an-uptime-monitor-in-minutes-with-slack-standard-library-and-node-js-e7973d659849>

确保你的网站或 API 正常运行是一项全天候的工作。为了帮助这一不懈的努力，我们可以在我们经常使用的[工具](https://hackernoon.com/tagged/tools)中显示正常运行时间报告和状态警报，如 [Slack](https://slack.com) 。有了像[标准库](https://stdlib.com)这样的平台，这样的集成几乎不费吹灰之力。本指南将向您展示如何在[标准库](https://stdlib.com)上设置一个 API，该 API 将 ping 一个 URL，如果 URL 没有返回 2xx 状态代码，则向 [Slack](https://hackernoon.com/tagged/slack) 发送一条消息。它将利用[标准库的调度任务，](https://stdlib.com/blog/code-xyz-09-26-2018-scheduled-tasks)允许您基于几乎任何调度来执行 API。

![](img/adb7d7ec71eef1aaaea40b55be1e7bee.png)

如果你不熟悉[标准库](https://www.stdlib.com)，我们是一个无服务器平台，让每个人都能在几秒钟内构建、发布和集成可扩展的 API。在我们的在线文本编辑器 [Code.xyz](https://code.xyz) 的帮助下，你可以直接在浏览器上完成。

## 步骤 1:创建一个 Slack 应用程序

前往 [Slack app 仪表盘](https://api.slack.com/apps)，点击屏幕右上角的**创建新应用**。过一会儿，你会被重定向到一个标有“**基本信息**的页面。在屏幕左侧，您会看到“**功能**类别下的“ **OAuth and Permissions** ”。

![](img/a7dedd2e888fc807fd89509b80a88e14.png)

在那里，向下滚动到“**范围**”。我们需要机器人能够代表自己向 Slack 发送消息。这意味着我们需要给它" **chat:write:bot** "权限，所以在"**选择权限范围**"下拉列表中搜索。

![](img/6ed273af013151c443c36b7a2093811c.png)

设置好所需的权限后，向上滚动到“ **OAuth 和权限**”页面的顶部，然后单击绿色的“**将应用程序安装到工作区**”按钮。一旦您授权了它，页面将会刷新，安装按钮所在的位置将会是一个 OAuth 令牌。点击复制；你马上就会需要它。

![](img/2f0e522e5cd9deecf9eb376e03111834.png)

## 步骤 2:获取 Ping API 模板

将松弛部分全部整理好，打开 [Code.xyz](https://code.xyz.com/?src=@steve/ping) 。如果您点击该链接，ping API 模板将自动打开。否则，您可以单击“**社区 API 源**”选项卡并搜索“ **steve/ping** ”。

![](img/0313a43cbccae58a3f5035f80a66ba8b.png)

一旦装载完毕，您将到达`__main__.js`终点。它只有一个参数，`url`。该函数向`url`发出 HTTP 请求，并检查状态代码是否在 2xx 范围内。如果不是，它使用 [Slack SDK](https://github.com/slackapi/node-slack-sdk) 向环境变量`SLACK_CHANNEL_ID`指定的通道发送消息。

繁重的工作在`/src/request.js`文件中完成。您可以随意阅读该文件，但本质上，它向一个 URL 发出 GET 请求，并记录关于该请求的一些信息。它将返回状态代码和计时信息，如到达第一个字节的时间和总请求持续时间。

在 ping 之后，我们检查状态代码是小于 200 还是大于 299。如果是，用函数`formatPing`格式化的请求细节的消息 slack。`formatPing`获取由`request`函数返回的对象，对其进行字符串化，并将其包装在三个反斜线中。Slack 会把任何被三个反斜杠包围的东西显示为一个代码块，这样可以让 JSON 看起来干净一点。有关格式化的更多信息，请参见[时差文档](https://api.slack.com/docs/message-formatting)。

## 步骤 2b:设置环境变量

如果你打开`env.json`，你会看到两个环境变量`SLACK_OAUTH_TOKEN`和`SLACK_CHANNEL_ID`。前者是我们刚刚从 Slack 应用仪表板上获得的，后者是你希望机器人发布到的频道的名称，比如`#general`或`#alerts`。确保您填写了“ **dev** ”集合，如下所示。

![](img/b72d18f6d92f2a674d5e2beef5d7d209.png)

现在似乎是测试这个 API 的合适时机。返回`__main__.js`并按下 **cmd/ctrl+b** 打开屏幕左下方的参数窗格。键入一个网址，然后按回车键。如果该 URL 上的任何网站/API 都正常，您应该什么也看不到！

![](img/eed8b5119e2bf0a2ffa309fbfd1a0057.png)

这是因为如果 ping API 可以到达带有 2xx 状态码的 URL，那么它就是一个 noop。要尝试一个失败的案例，使用`[https://bad.stdlib.com/](http://bad.stdlib.com/)`作为 URL。在 Code.xyz results 面板中，您将看到 Slack 的 API 的响应，表示发送了一条消息。如果您导航到您输入的频道`env.json`

![](img/ed7048b1d9ffc774a5a1b971124027f4.png)

您将看到一条消息，提示`https://bad.stdlib.com/`不可达，以及一些关于请求的信息。

## 步骤 3:设置计划任务

ping API 启动后，我们现在可以安排 API 每隔几分钟运行一次。为此，您需要安装[标准库 CLI，](https://github.com/stdlib/lib)这可以通过 NPM 完成。

```
$ npm install lib.cli -g
```

安装后，您可以通过运行以下命令登录:

```
$ lib login
```

现在，要创建计划任务运行:

```
$ lib tasks:create [username]/ping
```

您将被要求输入 URL，然后您必须选择一个库令牌来认证服务(默认的就可以了)。接下来，您可以选择通过“简单”语法或使用 cron 输入时间表。对于我们的目的，简单的语法就可以了。让服务从 0 分钟开始每小时运行 12 次。最后，随便你给它起什么名字。

![](img/60c797ef5c3793494eaa646b7726a819.png)

## 深入挖掘

有许多方法可以扩展这个简单的 ping API。例如，您可能希望在 3xx 范围(重定向)中包含可接受的状态代码。你所要做的就是修改一行代码！改变

```
if (ping.statusCode < 200 || ping.statusCode > 299) {
   ...
}
```

到

```
if (ping.statusCode < 200 || ping.statusCode > 399) {
   ...
}
```

会成功的！现在，也许你因为没有经常检查 Slack 而感到内疚，并且更喜欢旧的短信。有了[标准库](https://stdlib.com)，短信集成只需一行代码！事实上；ping API 中的另一个端点已经设置了 SMS 通知。打开`extra.js`，你会看到—

它和`__main__.js`一样，但是有额外的条件来检查是否提供了电话号码。如果是这样，它会发送一条信息，提示收件人上 Slack 看看发生了什么。

这些小变化的目的是表明，在一天结束时，计划的任务，以及标准库上的任何 API，都只是 JavaScript，完全是根据您的具体用例定制的。

## 仅此而已！

我希望这篇教程有助于向您展示开始使用[标准库](https://stdlib.com)是多么容易。关于如何更好地使用[标准库](https://stdlib.com)的更多灵感，你可以在这里查看由团队[编写的更多指南。如果你有好的想法想要分享，请直接通过电子邮件联系我:**steve@stdlib.com**，或者在 Twitter 上关注](https://medium.com/stdlibhq)[标准库](https://twitter.com/stdlibhq)团队和[我](https://twitter.com/notoriaga)。

*Steve Meyer 是 Oberlin 学院的应届毕业生，也是标准图书馆的软件工程师。当他不编程的时候，你可以发现他在烤面包，或者玩蜘蛛侠。*