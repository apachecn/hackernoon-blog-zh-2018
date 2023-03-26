# 如何用 0 美元建造一个 SaaS

> 原文：<https://medium.com/hackernoon/how-to-build-a-saas-with-0-fed2341078c8>

![](img/6f24d832679e2eda32c4e7d3823d0531.png)

How would you build a SaaS with $0?

> 不用花上几千美元就能造出产品。

去年 10 月，我发布了 ipdata.co 和我的第一个 SaaS。我当时的计划是尽可能多的往墙上扔东西，并把注意力集中在粘在什么东西上。

为了让自己为预期的长期努力做好准备，我计划在每个想法上花费尽可能少的时间和金钱。只有在至少有一个付费客户的情况下，我才会在一个创意上花钱。

在这样做的过程中，我发现了许多产品，它们的免费层使得不花一分钱就能在其上构建完整的产品变得可行。

# 使用免费服务来构建您的产品

## AWS 免费层(无过期优惠)

![](img/ec8145beb4d1ab01d5c17c22fe1d945f.png)

**你能用这个做什么**

1.  **构建 API**

借助 API Gateway 和 AWS Lambda，您可以获得 100 万次免费 API Gateway 调用(前 12 个月)、100 万次 Lambda 函数执行和每月高达 320 万秒的计算时间！除此之外，价格相当优惠，每百万 API 网关请求 3.50 美元，每百万 lambda 函数调用 0.20 美元。

请注意，如果您正在构建的产品是一个长期运行的流程，您可能会发现 lambda 非常昂贵，因为您将根据执行时间和在该期间分配给 Lambda 函数的资源来收费。

这种设置对我们来说是有利的，我们已经能够以略高于 100 美元的价格满足来自全球 10 个地区的 2500 万个 API 请求！

> 他的设置对我们来说非常有利，我们已经能够以略高于 100 美元的价格满足来自全球 10 个地区的 2500 万个 API 请求！

2.**可扩展数据库**

Dynamodb 是 AWS 提供的一个快速且高度可伸缩的 NoSQL。无过期空闲层提供 25 GB 的存储、25 个单位的读取容量和 25 个单位的写入容量，足以处理多达 2 亿个请求！

您可以将 Dynamodb 用作键值存储或存储 JSON 文档。如果您使用过 Mongodb 或 Redis，那么迁移到 Dynamodb 应该会感觉很自然。

**3。用户管理(注册、使用电子邮件登录和短信验证)**

在你的生活中，你再也不用编写另一个用户管理系统了！AWS Cognito 为您提供了允许用户注册、登录、验证电子邮件和电话、免费使用 MFA 等所需的所有功能！

多达 50 000 个用户！

超过 50 000 个用户的价格是分层的，接下来的 50000 个用户每个用户 0.00550 美元。

![](img/a84a03f615bdaa93ba15755ddb6ee9fb.png)

**4。您的 HTTPs 网站(S3 + Cloudfront/Netlify + AWS 证书管理器)**

你想要一个静态网站。

您可以使用 javascript 为页面添加一定程度的交互和动态内容。但是有了一个静态的网站。

*   安全性
*   速度
*   低得多的成本(除非你在托管大量巨大的媒体文件)

对于这种设置，您将使用

**S3**

一种数据存储服务，可以用来为一个简单的网站提供 HTML 页面。你获得了 5 GB 的亚马逊 S3 标准存储，20，000 个 get 请求和 2，000 个 Put 请求，但只是第一年(如果一年后你还在运行，你应该会赚钱，并同意付费)

**云锋**

Cloudfront 是一个内容交付网络，它缓存您的站点页面，以便后续用户可以更快地访问您的站点，而不必从 S3 读取。第一年，每月有 50 GB 的数据传输，2，000，000 个 HTTP 和 HTTPS 请求。

**亚马逊证书管理器**给你无限的完全管理的 SSL 证书。续费是自动的，你可以在你的 S3 网站前使用，或者在任何你需要 SSL 证书的地方使用。

# 或者使用 Netlify…

![](img/7aaecf69c85752f36d1e5b8b4e71dcf3.png)

[Netlify](https://www.netlify.com/) 非常受开发者欢迎，它提供了全球 CDN、持续部署、一键 HTTPS、与 git 的紧密集成、流量分割测试、即时缓存失效、即时回滚和无限扩展！

他们的免费层具有难以置信的包容性，包括:

*   个人或商业项目
*   公共或私有存储库
*   自定义域的 HTTPS
*   持续部署
*   表单处理
*   社区支持
*   身份服务
*   分割测试
*   Git 网关

# 免费发送电子邮件

![](img/bb9dc35a61412e355615be9b264096e0.png)

[**发送网格**](https://sendgrid.com/)

AWS Cognito 将处理发送您所有忘记的密码或验证电子邮件。

有关电子邮件简讯和促销电子邮件，请查看 Sendgrid。

他们的免费等级是每天 100 封电子邮件和 2000 个营销联系人。

在免费层之外，发送 40，000 封电子邮件每月需要花费 9.95 美元。

[**Mailchimp**](https://mailchimp.com/)

![](img/38f9cf55452e92748587c76e0a7914fb.png)

Mailchimp 的免费永久计划允许您每月向多达 2000 名订户发送 12000 封电子邮件。

除此之外，你可以从 20 美元开始无限制发送，并根据用户数量计费

![](img/ec3f5fb0fd9198f90289eefe5997411b.png)

# 种类

[Stripe](http://stripe.com/) 傻乎乎的简单集成，开始收集订阅。

这是无处不在的弹出窗口

![](img/2c3f1ac6cd4c91ba594befee53416d08.png)

Stripe Checkouts

以及最近公布的(而且漂亮！)预先设计好的结账表格。

![](img/a5b540d185abf96b13f5119045410062.png)

Stripe Elements

在这里看他们全部行动[。](https://stripe.github.io/elements-examples/)

最精彩的部分；除非你在赚钱，否则你不会收到账单。

# 免费销售和支持

![](img/b88e6f339fa7c8a2c66d9282ba720ad7.png)

[**漂移**](https://www.drift.com/)

你可能在互联网上见过这种聊天弹出窗口。好消息！你所要做的就是在你的网站上添加一个 Javascript 代码片段到你想要它出现的页面上！免费的！如果你需要一个以上的人来处理支持，那么你需要一个团队计划。但是如果你是一个独立制作人，你可以去！

多亏了 drift，我与客户进行了数十次对话，否则我不会有这样的机会。当用户看到小部件弹出“我们能帮什么忙？”，他们回应。与潜在客户展开对话并不费力。

我坚信，我进行的很多对话都直接导致了销售。

![](img/be59f4e89f5924d50cd237126d527634.png)

[**完整版**](https://www.fullstory.com/)

观看你的用户如何与你的网站互动。您可以搜索所有记录，例如，用户点击了购买，但没有结账或从购物车中删除某些商品，或者他们点击了您关心的任何 html 元素！

这对于发现 UI 错误非常有用，因为您可以看到您的站点在用户设备上呈现的样子，您还可以直接查看用户控制台上的 Pro 计划错误。

# 沙特大亨

![](img/8c4ff7ee13d0864d26f9a5d51852a29b.png)

[Chartmogul](https://chartmogul.com) 是一项订阅分析服务，为您的企业生成通用 SaaS 指标，包括:

*   每月经常性收入(MRR)
*   MRR 运动
*   年运行率(ARR)
*   试用到付费转换
*   现金流量
*   搅拌

它支持与 Stripe、BrainTree、Paypal、Chargify 等的集成。

你会得到漂亮的图表，让你深入了解你的 SaaS 做得如何。

他们的发射计划是永远免费的，而你的 MRR 低于$10K！

你不会被收费，除非你真的赚了超过$10K！

# 域和 DNS

![](img/e8ac2cb8be206053b27b5343e8393570.png)

每年花 10 美元就能获得一个网站。

# 公司电子邮件

![](img/640adfe8e4f6fe173c46dafeb8d3f302.png)

Zoho——获得一个类似 gmail 的收件箱，你可以通过它收发来自 youremail@yourdomain.com 的电子邮件

# 新鲜作品组曲

![](img/5c3693da5411570b611d6a268ea083a6.png)

[**生鲜**](https://www.freshworks.com/freshsales-crm/)

我喜欢新鲜销售！该产品是美丽的，难以置信的直观使用一个可怕的免费计划！

用它来；

*   管理销售线索和交易
*   直接从控制面板获取电话/电子邮件线索
*   给潜在客户打分——他们根据您与潜在客户的互动自动给潜在客户打分，我觉得这很直观。

Freshworks 套件中还有很多其他产品，在这里 [查看全部](https://www.freshworks.com/.)[。](https://www.freshworks.com/)

[**小清新**](https://www.freshbooks.com/)

![](img/42a1dfa0b3d9e3d2475efa54d4dc07ab.png)

Freshworks 的又一款漂亮的功能性产品。它包括:发票，时间跟踪，有 Android 和 iOS 移动应用程序，并且只有 15 美元/月！

[**Freshdesk**](https://freshdesk.com/)

如果你不想使用电子邮件来获取支持票，请点击 [Freshdesk](https://freshdesk.com/)

![](img/a70e51904399b7d60f8c77e0bd133f0f.png)

他们的萌芽计划是免费的基本通话，票务和无限代理！

付费计划起价为每个代理每月 19 美元，按年计费。

# 无服务器

[**Zeit.co**](https://zeit.co/now)

![](img/d2ad4d9096d74c85de6d90d0406f2e82.png)

将 Node.js 应用程序部署到全球多个地区！

Zeit 最多可免费部署 3 次。然而，您的代码在部署中的`/_src`处是可见的。

付费计划从每月 15 美元开始，共 10 次部署(并且不会暴露您的代码)。

超过您所在层级的使用将按使用量付费。

[**Heroku**](http://heroku.com/)

![](img/2066f198ade71a0f130ff508caf35e34.png)

运行你的 Python，Ruby，Node，PHP 等应用程序，无需设置服务器。

Heroku 的免费提供

*   不活动 30 分钟后睡眠
*   自定义域
*   使用基于帐户的免费动态小时池

512 MB 内存│ 1 个 web/1 个 worker

超越计划起价为每月 7 美元。

# **监控和状态页面**

![](img/524d474588a89b1183270dd6bca445bb.png)

使用 [Hyperping.io](https://hyperping.io) 进行正常运行时间监控。

你的 SaaS 可能还是有问题。这给了你一个提示，当你的产品是不可及的，在用户愤怒的电子邮件之前。

您可以设置短信和电子邮件通知。还从多个区域验证停机时间，以防止误报。

# 设计

如果主题可以，不要浪费时间为可能失败的产品编写网站代码。

你可以花大约 20 美元在森林里买到一些不错的东西。

如果你需要定制设计工作，结帐[manypixels.co](https://www.manypixels.co)-无限的设计工作，每月 259 美元。如果你不经常使用它，一个好主意是和朋友分担费用！你们都可以有一个设计机构为你们所有的项目服务！

![](img/00728deec2076340183408651d7cb7e6.png)

[**扁平图标**](https://www.flaticon.com/)

![](img/2e069d5de83729eeb61995e989ce8681.png)

[扁平图标](https://www.flaticon.com/)漂亮的扁平图标。

# **养蜂场**

![](img/06e44f22a5afaf31091682b3066b93c7.png)

给你漂亮的 API 文档，你可以从一个 Swagger spec 文件中自动生成，也可以在 markdown 中手工生成！

它还为您提供了一个浏览器内控制台，您的用户可以在其中使用几种语言的完整工作代码示例来测试您的 API 全部都是自动生成的！

它的免费计划允许您访问您需要的大多数功能。然而，如果你想拥有自定义域名下的文档，或者定制页面以符合你的品牌，那么你需要注册他们的付费计划，每月 99 美元起。

这里有一个你可以尝试的例子:

[https://IP data . docs . apary . io](https://ipdata.docs.apiary.io)

# 更多免费的东西

[**AWS 激活**](https://aws.amazon.com/activate/)

![](img/3b6cdee22585a29d076a491fd434b7e7.png)

如果你需要更多的精力来开发 AWS，请点击 [AWS 激活](https://aws.amazon.com/activate/)。

建造者计划是最容易进入的，因为你不需要成为加速器的一部分。

有了这个你就有了；

*   1，000 美元的 AWS 促销积分，有效期长达 2 年
*   [AWS 业务基础知识](https://aws.amazon.com/activate/benefits/training/#business)在线培训(价值 600 美元)
*   [AWS 技术要点](https://aws.amazon.com/activate/benefits/training/#technical)在线培训(价值 600 美元)
*   80 学分的[自定进度实验室](https://aws.amazon.com/activate/benefits/training/#labs)(价值 80 美元)

**微软 Azure 免费层**

![](img/353575709c42ce2c7b76e7d589e79bd2.png)

我不太熟悉 Azure 和 GCE 的免费层，但也可以看看！如果没有别的，那就是他们的云功能(相当于 AWS Lambda)。

最后，为了给你的启动之旅带来灵感，这里有一个来自 WPEngine 的 Jason Cohen 的演讲:[https://vimeo.com/74338272](https://vimeo.com/74338272)

我在[公共](https://wip.chat/products/747)建筑[ipdata.co](https://ipdata.co)。在 Twitter 上关注我，我会分享我的进步和数据。

## 更新:

查看我们对 8 个最佳 IP 地理定位 API 的详细分析。

也可以看看下面我错过的一些很棒的服务的评论。