# 不可思议的无服务器安全风险，以及在哪里可以找到它们

> 原文：<https://medium.com/hackernoon/fantastic-serverless-security-risks-and-where-to-find-them-737d2206545a>

![](img/b9ecb5cffea882e4f8818f8c35a02e6f.png)

Image credit: [https://i.imgur.com/atz81.jpg](https://i.imgur.com/atz81.jpg)

随着对无服务器产品的大肆宣传，我经常被问到一个简单的问题:

> 但是，我如何保证我的无服务器应用程序是安全的呢？

我的回答总是一样的。你怎么能保证什么？我害怕我的 EC2 实例被攻破，就像我害怕我的 Lambdas 一样。

大多数服务器漏洞是由于程序员的错误。那一行代码做的比它应该做的多了一点点。你错放的那个应用程序秘密。那些你忘记加密的文件。

为了写出更好的软件，我们开发人员可以做很多事情。

也就是说，无服务器架构的分布式本质给了恶意攻击者更多的活动空间。无服务器的最大优势也是最危险的敌人；它为攻击者提供了更多的切入点。

这让我真的很担心，所以我开始寻找答案。这时候偶然看到 Puresec 的研究， [*无服务器架构十大最关键安全风险*](https://www.puresec.io/hubfs/SAS-Top10-2018/PureSec%20-%20SAS%20Top%2010%20-%202018.pdf?t=1517837443549) 。

我看着它，眼睛都没有离开屏幕。很多事情都变得清晰了。

# 提高无服务器安全性的途径

可悲的是，在开发人员中仍然有一个常见的坏习惯:一旦我们正在构建的软件已经启动并运行，我们就关注安全性。

> *“我正要部署这个应用程序，希望我不会被黑…”*
> 
> *—普通开发人员*

简而言之，这种*“最终获得安全保障”*的心态会害死我们。顶级无服务器漏洞与顶级漏洞非常相似。

请继续阅读 Puresec 安全性研究的收获，以及您现在可以采取的加强应用程序安全性的措施。

# TL；速度三角形定位法(dead reckoning)

***注*** *:强烈建议你* [*看完整份 Puresec 研究*](https://www.puresec.io/blog/serverless-top-10-released) *。太棒了。如果你想快速回顾一下风险，看看 TL；贝博士。或者直接跳到你感兴趣的部分。*

*   [**事件注入**](#ef0b) —通过输入验证和预定义的数据库层逻辑解决，如 ORM 或存储过程。
*   [**断认证**](#b290) —通过内置认证/授权解决方案解决，避免危险的部署设置。
*   [**不安全的部署设置**](#368c) —通过从不使用公共读取 ACL 和保持文件加密解决。
*   [**滥用权限和角色**](#d22e)——用“最小特权原则”解决
*   [**日志记录不足**](#2b31) —使用第三方工具解决，如 [Dashbird](https://www.dashbird.io/) 或精通使用 [CloudWatch](https://aws.amazon.com/cloudwatch/) 。
*   [**应用机密的不安全存储**](#84b8) —通过使用 AWS KMS 加密您的应用机密来解决。
*   [**DoS 攻击和财务枯竭**](#e391) —通过编写高效代码、使用超时和节流来解决。
*   [**异常处理不当**](#6814) —通过记录堆栈痕迹**解决**只到控制台或专用日志文件。永远不要将堆栈跟踪发回给最终用户。

# 越来越冒险

这对你们大多数人来说可能是显而易见的——提高安全性的几个步骤在于我们的应用程序结构的整体质量。我们设计软件的方式，以及我们对细节的关注程度，将最终导致一个健壮和安全的软件产品。

我唠叨够了。让我们从风险开始吧！

## 1.事件注入

总是验证输入是常识。那我为什么还要谈论这个呢？因为我们经常忘记边缘案例。

您是否经常确保输入是您期望的数据类型？我经常会忘记，所以我想大多数人都是这样。可以触发无服务器功能的几种不同的事件类型是怎样的？事件不一定是 HTTP 请求。您可能只进行了检查，以确保来自 HTTP 事件的输入得到了验证。当事件不是你期望的那样时，不要忘记检查情况。

请使用防火墙。它很容易设置，并产生巨大的差异。

从实际事件开始，尝试使用预定义的逻辑进行数据库交互。这将降低注射的风险。尤其是当您确保以完成工作所需的最低操作系统权限运行所有代码时。

## 2.身份验证被破坏

使用内置解决方案对用户进行身份验证并授权他们访问资源。对于[授权者](https://docs.aws.amazon.com/apigateway/latest/developerguide/use-custom-authorizer.html)或 [AWS 认知者](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-integrate-with-cognito.html)，这非常简单。使用它们是显而易见的。

**相关:**这里有一篇关于[设置强大的 IAM 权限](https://serverless.com/blog/abcs-of-iam-permissions/)的帖子，请查看。

您可以放心，使用 Auth0 或 JWT 进行无状态身份验证完全没问题。真正的问题不是实际的身份验证方法，而是包含具有公共读取权限的**组件的**不安全部署设置**。我们将在下一节详细讨论这一点。**

有些人可能不喜欢无状态认证，这没关系。您可以很好地使用**会话**，但是不能在 Lambdas 本身上运行。每个无服务器功能本质上都是无状态的，所以我们不能在其上存储持久数据。因此，对于会话，我们可以使用专用的 Redis 服务器。

AWS 有[elastic cache](https://aws.amazon.com/elasticache/)，这是一个很棒的 Redis 和 Memcached 服务。你只需要确保 Lambda 和 Elasticache 在同一个 VPC 中运行。([这里有一个快速设置的教程。)一旦你这样做了，你就可以把`AWSLambdaVPCAccessExecutionRole`添加到你的 Lambda 的 IAM 语句中，这样就万事大吉了。](https://docs.aws.amazon.com/lambda/latest/dg/vpc-ec.html)

尽管大家都在谈论实际的身份验证原则，但这并不是真正的问题。您的应用层身份验证可能工作得完美无缺，但这并不能阻止恶意攻击者以公共读取权限访问 S3 存储桶。请**不要启用公共读取权限**，除非您使用的是存储图像的存储桶或静态网站。在这种情况下，您只需将这些文件保存在那个桶中，其他什么都不用做！

## 3.不安全的部署设置

如果你甚至有点担心你的文件的隐私，启用所有你可能的加密方法。

幸运的是，AWS 既有[客户端加密](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/KMS.html)，用于在文件通过网络发送之前对其进行加密，也有[服务器端加密](https://docs.aws.amazon.com/AmazonS3/latest/dev/serv-side-encryption.html)，一旦文件被添加到 S3 桶中。但是，如果您的存储桶启用了公共读访问，这些都没有任何意义。

跟踪您的 S3 ACL，并确保访问级别中没有不必要的权限。您可以使用 [SSE-S3](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingServerSideEncryption.html) 和 [SSE-KMS](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingKMSEncryption.html) 启用服务器端加密(SSE)来保护您存储桶中的数据。选择你认为最适合你的用例的。

我还鼓励您在 AWS SDK 中使用客户端加密。这里有一个很好的解释给你看。

## 4.超特权功能权限和角色

开发人员大部分时间都很懒(我也不例外)——我们为包含大量功能的整个服务设置了单一的权限级别。

尽管这一开始可能是有意义的，但它可能是非常危险的。函数应该只拥有实现其目的所需的权限。

例如，从 DynamoDB 获取一些数据的函数不应该有权限向 DynamoDB 添加数据。同样的逻辑也适用于添加图像和从 S3 检索图像。对于一个执行`GetObject`操作的函数来说，拥有执行`PutObject`操作的权限是没有意义的，不是吗？

如果你习惯于使用[无服务器框架](https://serverless.com/framework/)，你可以很容易地在每个函数的基础上配置 IAM 角色语句(或者只使用[这个插件](https://github.com/functionalone/serverless-iam-roles-per-function))。

确保始终遵循最小特权原则。一个功能，一个用例，一个权限模型。

## 5.记录和监控不足

困难的部分来了！蹩脚的日志等于遗漏的错误报告。如果您错过了关键错误，您的用户将遭受更长的停机时间，因为您没有得到正确的通知来修复它们。

但反过来也一样危险。确保永远不要注销包含敏感数据的信息。要掌握这一点，你要么需要成为一名解析 [CloudWatch](https://aws.amazon.com/cloudwatch/) 日志的高手，要么使用第三方工具，比如 [Dashbird](https://www.dashbird.io/) 。

[](https://dashbird.io/) [## 无 Dashbird 无服务器可见性和调试工具

### 通过优化 lambda 函数，获得整个无服务器堆栈的即时概览并节省资金。健康指标…

dashbird.io](https://dashbird.io/) 

根据我使用 Dashbird 的经验，我很喜欢他们的实时监控和错误报告、超时监控、实时跟踪、价格计算和许多其他我还不需要使用的功能。它给你一个无服务器应用程序的鸟瞰图，很大程度上模拟了一个普通的老派服务器应用程序的样子。它还可以向松弛通道发送错误报告。(我们都知道开发者有多爱 Slack。)

Dashbird 有一个[免费等级](https://dashbird.io/pricing/)，所以如果你认为它对你有用，你可以继续尝试。

## 6.保持应用程序机密加密

即使您没有将环境变量推送到 GitHub，如果恶意攻击者获得了运行您的代码的系统的访问权限，他们仍然可以访问这些值。

因此，需要使用 [KMS 来加密环境变量](https://docs.aws.amazon.com/lambda/latest/dg/tutorial-env_console.html)。有一个用于无服务器框架的[插件让这变得简单。](https://github.com/trek10inc/serverless-secrets)

## 7.DoS 和财务枯竭

我喜欢 Lambda 的原则，你为代码运行的时间付费。这促使开发人员编写高效的代码。高效的代码不容易出错，你可以预见它不会运行很长时间。

这使得**添加超时**变得更加容易。

使用无服务器框架时，函数的默认超时是 6 秒。对于几乎任何生产级别的 HTTP 请求来说，这都绰绰有余。默认的内存使用量设置为 1024 MB，这通常已经足够了。

如果你曾经担心 DoS 或一些黑客调用你的 Lambdas 很长一段时间，你总是可以节流传入的 API 调用。这要经过 [API 网关](https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-request-throttling.html)，设置几个字段来限制每秒的请求量就这么简单。

使用无服务器框架在这里也很有帮助，因为它允许对特定 API 可以拥有的调用次数设置每月上限。*居然*方便。

## 8.不正确的异常处理和冗长的错误消息

调试无服务器架构仍然是一个问题。

处理这一点最好通过合理的编程实践来完成。编写单元测试。编写可读的代码。[在本地模拟 AWS 环境](https://github.com/dherault/serverless-offline)，这样在部署到云之前，您可以在本地运行所有代码。

堆栈跟踪应该只记录到控制台或日志文件中；永远不要将堆栈跟踪发送回客户端。确保在错误响应中只发送模糊的消息。

# 风险-vana…？

我在这里提到的许多问题都适用于一般的编码实践，不管您使用的是传统的服务器还是无服务器架构。编写干净的代码，保证秘密的安全，进行输入验证和错误处理，是我们作为开发人员[发誓](http://blog.cleancoder.com/uncle-bob/2015/11/18/TheProgrammersOath.html)要坚持的普遍概念。

真正的问题来自部署设置、每个功能的权限、糟糕的日志记录、不充分的错误报告和财务枯竭。这些问题仍然是可控的，你只是还不习惯解决它们。无服务器仍然是一个年轻的范例，我们需要时间来适应。

使用无服务器的分散特性促使我们寻找将资源分组到逻辑组的方法。它最大的优点也是最大的缺点。

# 包扎

本文向您展示了无服务器安全的基础知识、需要注意的事项以及如何尽可能多地修补漏洞。希望这有助于您更深入地了解无服务器架构的内部工作原理。

如果你想看看 Puresec 的指南，请点击查看[。或者如果你想看我最新的文章，请到这里来。](https://www.puresec.io/hubfs/SAS-Top10-2018/PureSec%20-%20SAS%20Top%2010%20-%202018.pdf?t=1517837443549)

[](https://medium.com/@adnanrahic/latest) [## 阿德南·拉希奇写的最新故事——中型

### 阅读 Adnan Rahi 在 Medium 上写的最新故事。软件工程师@bookvar_co .编码教育家@ACADEMY387…

medium.com](https://medium.com/@adnanrahic/latest) 

如果我想让你了解更多关于无服务器的知识，请随意浏览我写的关于这个主题的课程。

[](https://www.packtpub.com/web-development/serverless-javascript-example-video) [## 无服务器 JavaScript 示例[视频] | PACKT 图书

### 通过无服务器 web 开发的实时演示变得更加熟练

www.packtpub.com](https://www.packtpub.com/web-development/serverless-javascript-example-video) 

或者，也许你只是想被通知有关无服务器的其他很酷的事情。那就在下面订阅吧。

希望你们喜欢读这篇文章，就像我喜欢写这篇文章一样。
*你觉得这个教程会对某人有帮助吗？不要犹豫分享。如果你喜欢，击碎下面的* ***拍手*** *这样其他人会在媒体上看到这个。*

*原载于*[*serverless.com*](https://serverless.com/blog/fantastic-serverless-security-risks-and-where-to-find-them/)*。*