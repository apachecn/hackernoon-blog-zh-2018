# 神奇链接:一键解决你的大部分漏斗转换问题

> 原文：<https://medium.com/hackernoon/magic-links-d680d410f8f7>

![](img/7e191d9c55e81e51e1fc556d2b478419.png)

> 你是一名开发人员/产品经理，刚刚发布了一项新功能，现在你想推广它。您将会发送有关新功能的短信/电子邮件。你已经创建了跟踪漏斗来跟踪用户在你的网站上的旅程。您现在面临的唯一问题是，用户需要执行太多的点击，直到他们到达目标页面(这包括不可避免的登录屏幕)。你知道软件行业的经验法则，每次点击都是用户数量的下降。您希望尽可能多的用户访问目标页面。你怎么能这样做？

如果用户登录到浏览器，那么你只需将用户重定向到目标屏幕就可以了(只需一次点击就可以了！).但事实并非总是如此，事实上，大多数时候(如果你不是[谷歌](https://hackernoon.com/tagged/google)/脸书)，用户根本不会登录浏览器浏览你的页面。你能做什么来解决这样的问题？

神奇链接就是解决上述问题的一种方法。 [Slack](https://slack.com/) 正在这么做，其他很多创业公司也是如此。如果你不这样做，你就会错过你的软件产品的大量消费者/用户。

# 到底什么是魔法链接？

嗯，Magic Link 是一种经过认证的 URL，你以短信/电子邮件的形式发送给消费者，帮助他们只需点击链接即可登录系统，无需任何人工交互(用户无需输入用户名+密码)。它消除了用户退出系统的所有摩擦点。

令人印象深刻，对吧？是的，我们在 [Practo](https://www.practo.com) 使用过，它给我们带来了奇迹。短信/邮件整体转化率暴涨。

我们使用相同的方法让用户登录到系统，创建他们的会话，设置他们的 cookies，并将他们重定向到目标屏幕。所有这一切只需点击一个网址(没有其他人为干预)。

# 但是它是如何工作的呢？你怎么能做出这样的系统呢？

现在你知道什么是魔法链接了，它能为你做什么。但是你不知道它是如何工作的。在接下来的部分中，我将解释其中的逻辑，并编写伪代码来帮助您理解其内部工作原理。

# 步骤 1:创建一个神奇的链接

我们将调用函数 *createMagicLink* ，传递用户的手机号码和目标屏幕 URL。参考伪代码来掌握逻辑的窍门。

用户将获得一个缩短的 URL—[https://bit.ly/2O2WQ0c](https://bit.ly/2O2WQ0c)，它将扩展为“[https://www.mysite.com/magic_link_signup?token=abcd1234](https://www.mysite.com/magic_link_signup?token=abcd1234)”。我们缩短网址的原因是为了节省短信中的字符。

# 第二步:当用户点击时处理神奇的链接

在这一步中，您需要在您的应用程序中创建一个 API 端点 **"/magic_link_signup"** ，它将在用户点击链接时处理该链接。

简而言之，这个 API 端点正在执行以下操作

1.  从 URL 获取查询参数 token，这与步骤 1 中生成的$randomString 相同。
2.  使用此标记，通过步骤 1 中定义的函数 generateMaskedRandomString 生成$maskedRandomString。
3.  因为这个$maskedRandomString 存储在 DB 中的表“magic_links”下，所以我们将搜索这个表来获得这个令牌所属用户的信息。
4.  我们将从 magic link 表中获取相应的$maskedRandomString 的手机号码。
5.  使用此手机号码从数据库的用户帐户表中获取用户信息。
6.  使用这些用户信息，为用户设置会话和 cookies。用户现在登录到系统中。
7.  一旦用户使用会话集登录到浏览器，就使用从 magic_links 表中获得的$targetURL 将用户重定向到目标屏幕。

> 瞧啊。！！心神=炸？

![](img/98f0eab712b9b9ca034f545724fe7da0.png)

是的，当我知道这个技术的时候，这是我的一些反应！只需点击一下，用户就可以到达你选择的目标屏幕！看似童话，故名 ***【魔法链接】*** 。

总而言之，它只不过是-

![](img/d139e4f053bf79ee5bbfe800a96e0d1e.png)

我已经从伪代码中去掉了很多复杂的东西，这取决于你的实现。我刚刚写了我们今天称之为神奇链接背后的核心逻辑。这段伪代码可能有一些安全问题，一旦你开始用自己的方式实现，这些问题就会迎刃而解。为了使文章简单，我已经离开了这一部分。

我们在实施后看到的转换率令人印象深刻，如果我能与你分享确切的数字就好了。但是你可以从我的产品经理现在绝对爱我这一事实中弄清楚这一点！这些联系使我们的生活变得容易多了。

最后，我想用一个问题来结束我的演讲-

> 什么是工程？

根据维基百科-

> **工程**是科学、数学方法、[经验证据](https://en.wikipedia.org/wiki/Empirical_evidence)对[创新](https://en.wikipedia.org/wiki/Innovation)、[设计](https://en.wikipedia.org/wiki/Design)、[施工](https://en.wikipedia.org/wiki/Construction)、[结构](https://en.wikipedia.org/wiki/Structure)、[机器](https://en.wikipedia.org/wiki/Machine)、[材料](https://en.wikipedia.org/wiki/Material)、设备[系统](https://en.wikipedia.org/wiki/System)、[流程](https://en.wikipedia.org/wiki/Process)、[组织](https://en.wikipedia.org/wiki/Organization)的创造性应用

用我自己的话说-

> 工程无非是为现存的问题寻找解决方案，帮助人们过上更好的生活，优化现有的解决方案。它关乎发明，关乎发现，然后在此基础上进行建设，对我来说，工程就是生活:)

所以下一次，当你面对一个问题时，只要想一想。

PS:希望大家喜欢我的文章，如果我哪里说错了请指正。