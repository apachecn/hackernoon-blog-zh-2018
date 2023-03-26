# 用 Node.js 创建 Instagram Bot

> 原文：<https://medium.com/hackernoon/how-to-create-an-instagram-bot-with-node-js-a289185a7d6f>

![](img/cb3781258af046d8cef9b5efc01f296e.png)

Photo by [Elijah O’Donell](https://unsplash.com/photos/7ICU-gxJ1zY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/instagram?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

当 Instagram 的月活跃用户达到 10 亿的消息传出时，我忍不住创建了一个自己的账户，试图为自己赢得一些粉丝。

然而，鉴于我对它的浓厚兴趣，我希望借此机会测试我所学内容的一些现实应用，并使用机器人来这样做(即获得追随者)，而不是投入所有乏味的工作。

我做的第一件事是检查一个 [Instagram API](https://www.instagram.com/developer/) 。然而，无济于事，因为它被证明是一个无用的，过时的软件。

尽管脸书最近发布了[新的 Instagram API](https://developers.facebook.com/docs/instagram-api/) ，但他们只支持商业客户。

但是，嘿，这没问题——我想——我可以自己创造一个。

这正是我们今天要学的。

仔细想想，Instagram 的网站本身就是平台的 API。我们所需要做的就是弄清楚如何与它远程交互，而不是像普通用户那样手动操作。

> 有志者事竟成。

这就是操纵木偶的人进入画面的地方。该库允许我们创建一个无头的 Google Chrome / Chromium 实例，并通过使用 DevTools 协议来控制它。

# 设置项目

您可以继续复制[存储库](https://github.com/maciejcieslar/instagrambot)。

```
git clone [https://github.com/maciejcieslar/instagrambot.git](https://github.com/maciejcieslar/instagrambot.git)
```

我们的结构看起来像这样:

```
|  -- instagrambot/
|  |  -- .env
|  |  -- .eslintrc.js
|  |  -- .gitignore
|  |  -- README.md
|  |  -- package-lock.json
|  |  -- package.json
|  |  -- src/
|  |  |  -- common/
|  |  |  |  -- browser/
|  |  |  |  |  -- api/
|  |  |  |  |  |  -- authenticate.ts
|  |  |  |  |  |  -- comment-post.ts
|  |  |  |  |  |  -- find-posts.ts
|  |  |  |  |  |  -- follow-post.ts
|  |  |  |  |  |  -- get-following.ts
|  |  |  |  |  |  -- get-post-info.ts
|  |  |  |  |  |  -- get-user-info.ts
|  |  |  |  |  |  -- index.ts
|  |  |  |  |  |  -- like-post.ts
|  |  |  |  |  |  -- unfollow-user.ts
|  |  |  |  |  -- index.ts
|  |  |  |  -- interfaces/
|  |  |  |  |  -- index.ts
|  |  |  |  -- scheduler/
|  |  |  |  |  -- index.ts
|  |  |  |  |  -- jobs.ts
|  |  |  |  -- scraper/
|  |  |  |  |  -- scraper.js
|  |  |  |  -- utils/
|  |  |  |  |  -- index.ts
|  |  |  |  -- wit/
|  |  |  |  |  -- index.ts
|  |  |  -- config.ts
|  |  |  -- index.ts
|  |  -- tsconfig.json
|  |  -- tslint.json
```

# 浏览器界面

至此，让我们创建我们的浏览器界面，我们将使用它从 Puppeteer 获得渲染页面。

我们的 getPage 函数为我们创建了一个浏览器的页面，转到提供的 URL 并注入我们的 scraper(后面会提到)。此外，它等待我们的回调返回一个承诺，解析它并关闭页面。

[src/common/browser/index . ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/browser/index.ts)

非常清楚地说，木偶师是一个独立的浏览器界面，我们只是抽象了一些会不断重复的代码。

我们不必担心我们可能不小心打开的页面导致的内存泄漏。

# 刮刀

对于任何与网络抓取相关的事情来说，另一件有帮助的事情是创建你自己的抓取助手。当我们进行更高级的刮削时，我们的刮刀会派上用场。

首先，我们定义一些助手，主要用于设置[数据属性](https://developer.mozilla.org/en-US/docs/Learn/HTML/Howto/Use_data_attributes)。除此之外，我们还有这个元素类，它是对普通 HTMLElement 的抽象。此外，还有一个 find 函数，它为我们提供了一种对开发人员更友好的查询元素的方式。

[src/common/scraper/scraper . js](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/scraper/scraper.js)

# 证明

最后，我们可以创建我们的第一个实际上与 Instagram 相关的功能。当我们第一次打开浏览器时，我们需要验证我们的用户。

![](img/e0262118bd687f20f9fa0a93d58c22af.png)

首先，我们等待页面打开，然后输入在 [config.ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/config.ts) 中提供的凭证。每个字符之间有 100 毫秒的延迟。然后我们使用*登录*按钮，如果它存在，我们点击它。

[src/common/browser/API/authenticate . ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/browser/api/authenticate.ts)

如果你想看到奇迹发生，在木偶师的启动选项中设置 headless 为 false。它会打开一个浏览器，跟踪我们的机器人的每一个动作。

![](img/f444b8c4e7c83b5ccd4372256a542ff4.png)

现在我们登录了，Instagram 会自动在我们的浏览器中设置 cookies，所以我们不必担心必须再次登录。

我们可以关闭页面(我们的界面会处理它)，然后继续创建第一个函数来查找带有#hashtag 的帖子。

Instagram 上带有#标签的最新帖子的网址是[https://www.instagram.com/explore/tags/follow4follow](https://www.instagram.com/explore/tags/follow4follow)。

前 9 个帖子总是*热门帖子*，这意味着他们可能永远不会回复我们的关注或喜欢，因为他们有成千上万个帖子。理想情况下，我们应该跳过它们，只获取最近的。

当我们向下滚动时，会加载更多的帖子。在一次滚动中会出现 12 个帖子，所以我们必须计算我们要滚动多少次才能得到预期的数目。

在第一次装载时，有 9 个*顶部*和 12 个*普通*立柱。我们总共有 21 个。如果我们想找到 36 篇文章并省略前 9 篇，我们必须减去前 12 篇，然后将剩下的除以 12，这样我们就知道我们需要滚动多少次。

36(总数)— 12(第一个)= 24(遗漏的员额)

24 / 12 = 2(我们需要滚动的时间)

此外，我们将添加一个滚动到结果，因为如果一些东西花了太长时间来渲染，这将是我们的安全网。

[src/common/browser/API/find-posts . ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/browser/api/find-posts.ts)

我们可以遍历返回的 URL，并对每个 URL 执行一组给定的操作。

事情是这样的，除了帖子的网址，我们对它一无所知，但是我们可以通过抓取找到所有必要的信息。

# 获取帖子的信息

![](img/f176aec90b7ec45cf597d763f7b6fe58.png)

An Instagram post example

正如我们在上面看到的，网站上有很多关于这篇文章的有用信息，例如:

*   作者被跟踪了吗？
*   跟随按钮选择器
*   帖子有人喜欢吗？
*   相似按钮选择器
*   作者的用户名
*   描述和注释
*   注释选择器
*   喜欢的数量

但是在我们继续之前…

# 为我们的评论添加 NLP

有一件事我们应该考虑，那就是*这个职位的目的是什么？*。是为了炫耀某人的新手表，还是为了悼念逝去的亲人？

理想情况下，我们想知道帖子是关于什么的。我们可以这样做:

[Wit.ai](https://wit.ai/) 是一项来自脸书的服务，它让我们创建一个应用程序，并教它理解句子。

这就是 NLP 的由来，它代表自然语言处理。例如，如果你想制作一个聊天机器人，它也包含在 Messenger API 中。

虽然这可能需要一些时间，但我们可以教我们的应用程序理解帖子的描述，并给我们提供见解。

这很简单，真的，我们所要做的就是告诉它在一个句子中寻找什么。在我们的例子中，句子将是一个帖子的描述，我们将通过 [node-wit](https://github.com/wit-ai/node-wit) 库发送。

首先，你需要在 [wit.ai](https://wit.ai/) 上创建一个账户。您可以使用您的 GitHub 帐户登录。

然后你要么自己创建 app，要么用别人的 app。如果你想用我训练过的 app，这里[这里](https://wit.ai/primosdace_B2MY2/instao/entities)。

我们的应用程序接收一条消息，并返回它是 happy_description 还是 sad_description 以及对它的确信程度。

还有一个[表情库](https://www.npmjs.com/package/node-emoji)让我们的评论更生动。

现在，让我们将令牌放入 [config.ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/config.ts) 中，并创建一个小助手，用于将消息转换为意图，并基于所提供的意图生成注释。

[src/common/wit/index.ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/wit/index.ts)

虽然代码已经准备好，我们可以将表情符号添加到我们的评论中，[木偶师最近在输入它们时遇到了一些问题](https://github.com/GoogleChrome/puppeteer/issues/1096)。一旦问题得到解决，只需取消注释就可以了。

现在我们可以获得文章信息及其意图，使用我们之前在网站上找到的选择器，我们可以获得保存数据的元素。

[src/common/browser/API/get-post-info . ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/browser/api/get-post-info.ts)

# 获取用户信息

我们只能从一个帖子中获取这么多。有时我们会对用户的个人资料感兴趣。

![](img/cab9e024c28a78d3c9a363738bad579f.png)

An Instagram profile example

我们可以收集到许多有用的信息，例如:

*   职位的数量
*   追随者的数量
*   以下的数字
*   有没有跟账？
*   Bio(描述)，但是因为这对我们没有用，所以我们不打算废弃它。

[src/common/browser/API/get-user-info . ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/browser/api/get-user-info.ts)

# 发布操作

现在，我们需要实现 [like-post.ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/browser/api/like-post.ts) 。

我们只需检查帖子是否已经有人喜欢了。如果没有，我们选择 like 选择器并点击它。

[src/common/browser/API/like-post . ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/browser/api/like-post.ts)

对于[跟帖](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/browser/api/follow-post.ts)也是如此。

[src/common/browser/API/follow-post . ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/browser/api/follow-post.ts)

使用 [comment-post.ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/browser/api/comment-post.ts) 我们在文本区输入评论并按回车键。

[src/common/browser/API/comment-post . ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/browser/api/comment-post.ts)

# 用户操作

我们的机器人还必须能够取消关注人，因为否则它可能会超过 7500 关注限制。

首先，我们需要获得我们正在跟踪的人的 URL。

我们点击个人资料中后的*按钮。*

我们跟踪的最后 20 个用户应该会显示一个列表。然后，我们可以对每个 URL 执行 [unfollow-user.ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/browser/api/unfollow-user.ts) 。

[src/common/浏览器/api/get-following.ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/browser/api/get-following.ts)

现在我们有了 URL，我们只需一次取消关注一个用户。我们单击“取消关注”按钮，然后单击确认对话框。

[src/common/browser/API/un follow-user . ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/browser/api/unfollow-user.ts)

# 调度程序

现在一切都准备好了，我们必须考虑我们的机器人将如何工作。

显然，它不能只是无意识地一直跟着人，因为，你可能已经猜到了，Instagram 会很快禁止他。

根据我从[读到的](https://www.androidtipster.com/instagram-limits/)和在我自己的测试中看到的，有不同的限制，取决于账户的大小和年龄。

让我们现在安全地玩它。不过，不要担心，随着帐户的增长，限制会进一步扩大。

我们可以创建一个简单的调度程序，它将每小时执行一次注册的作业，假设该小时在它们的预期时间范围内。

[src/common/scheduler/index . ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/scheduler/index.ts)

[src/common/scheduler/jobs . ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/common/scheduler/jobs.ts)

你可能已经注意到了，我们使用了很多来自配置的变量。有些值是我们用 proccess.env 提供的，这意味着它们是敏感的，我们应该将它们包含在。环境文件。其余的可以手动更改。

[src/config.ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/config.ts)

让我们用一个普通用户会活跃的小时范围来注册我们的作业，剩下的唯一事情就是运行应用程序。

# 放弃

由于 Instagram 的 [robots.txt](http://instagram.com/robots.txt) 政策，这样的应用是不允许运行的。这个帖子仅仅是为了教育目的。

[src/index.ts](https://github.com/maciejcieslar/instagrambot/blob/master/src/index.ts)

```
npm run start
```

非常感谢你的阅读，希望你喜欢！

如果你有任何问题或意见，欢迎在下面的评论区提出，或者给我发[消息](https://www.mcieslar.com/contact)。

在推特上关注我 [@maciejcieslar](https://www.twitter.com/maciejcieslar) 。

[加入我的简讯](http://eepurl.com/dAKhxb)！

*原载于 2018 年 7 月 10 日*[*www.mcieslar.com*](https://www.mcieslar.com/creating-an-instagram-bot-with-node)*。*