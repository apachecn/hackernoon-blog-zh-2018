# 移动应用开发的微服务方法(下)

> 原文：<https://medium.com/hackernoon/microservices-architecture-for-mobile-application-development-part-ii-1a68db3aa438>

*在此阅读* [***第 1 部分！***](/@prashantramnyc/microservices-architecture-for-mobile-application-development-part-i-20b4f4089a24)

在 [***第一部分***](/@prashantramnyc/microservices-architecture-for-mobile-application-development-part-i-20b4f4089a24) 中，我们讨论了如何将一个大型复杂的 React 原生应用分解成较小的 ***React 原生迷你应用*** (使用电极原生)，以及如何将迷你应用组合在一起以构建更大的应用。迷你应用允许使用 ***为每个迷你应用*** 使用单独的代码库进行开发，并且 ***迷你应用可以围绕特定的业务特性*** 进行开发。这也允许不同的团队开发每个迷你应用程序。

我们还讨论了使用 React Native 时的 ***设备上数据存储选项*** ，而 ***领域数据库*** 成为了一个可行的竞争者。

我们收集了各种文章和论坛，以检查和总结在使用 React Native 时遇到的一些 ***常见性能问题，并探索可能缓解这些问题的解决方案。***

在第二部分中，我们将探讨用 React Native 构建迷你应用程序的一些更深层次的实现问题，并重点关注

*   *将 React Native 与现有本机代码集成*
*   *推送通知(远程和本地)和亚马逊 SNS 移动推送*
*   *持续会话管理*
*   *利用 AWS 为 React 原生应用提供的其他 MBaaS 工具*

我们的目标仍然是实现一种微服务方法来构建移动应用程序，从而允许不同的团队处理移动应用程序的不同部分，以及一个松散耦合的移动应用程序，可以以不同的增量部署，而不是一个单一的整体移动应用程序。

# **将 React Native 与现有 App 代码集成？**

当你从头开始一个新的移动应用时，React Native 是很棒的。然而，对于向现有的本地应用程序添加单个视图或用户流，它也能很好地工作。只需几个步骤，您就可以添加新的基于 React Native 的功能、屏幕、视图等。

此外，当使用 React Native 构建应用程序时，有时可能需要访问本机平台 API 或只是与一些本机代码进行交互。这可能是由许多原因造成的。

*   *性能问题:*为了减少 React Native JS 桥上的传递次数，在 Native 中实现移动应用程序的某个部分可能是一种更好的方法
*   *遗留问题:*在[棕地开发](https://www.youtube.com/watch?v=tUfgQtmG3R0)中，如果您已经有一个现有的移动应用程序，并且正在 React Native 中创建移动应用程序的新部分，您可能需要使用一些现有的 Objective-C、Swift 或 C++代码，而不必在 JavaScript React Native 中重新实现。
*   *平台访问:*在某些情况下，你可以从 React 本地 javascript 代码中访问平台 API，如 iOS 日历 API 或照片 API。

React Native 附带了一个名为 Native Modules 的特性，使用该特性，React Native javascript 代码可以访问底层 Native 平台的全部功能。请注意，任何这样的模块都必须为每个平台单独编写，我们用一些跨平台代码奇偶校验来换取性能效率和本机灵活性。

## ***TL:DR；React Native* 中的原生模块**

一个[本机模块](https://facebook.github.io/react-native/docs/native-modules-ios.html)只是一个 Objective-C 类，它实现了 RCTBridgeModule 协议。除了实现 RCTBridgeModule 协议，您的类还必须包含 RCT _ 导出 _ 模块()宏。React Native 不会向 JavaScript 公开 CalendarManager 的任何方法，除非明确告知这样做。这是使用 RCT _ 导出 _ 方法()宏完成的。

例如，您可以创建一个图像组件，它是一个使用 SDWebImage 的桥接本机组件，这样我们就可以与应用程序的本机端共享缩略图的图像缓存。它的工作原理是:

*   声明一个 JavaScript 组件来表示您的本机组件
*   让 React Native 知道您打算引用组件的本机代码
*   创建一个与你的道具界面相同的原生视图
*   使用桥接宏向 JavaScript 公开您的接口

Swift 不支持宏，因此将其暴露给 React Native 需要更多的设置，但工作方式相对相同。

> **iOS 中单个项目文件中可以使用 Objective C 和 Swift 吗？**
> 
> [Objective-C 和 Swift 文件可以共存于单个项目](https://developer.apple.com/library/content/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html)，无论该项目最初是 Objective-C 还是 Swift 项目。
> 
> 每当您在 iOS 项目中混合使用 Swift 和 Objective-C 时，您将需要一个额外的桥接文件，称为桥接文件头，以将 Objective-C 文件暴露给 Swift。如果您通过 Xcode 文件>新建文件菜单选项将 Swift 文件添加到您的应用程序，Xcode 将为您创建此头文件。您需要在这个头文件中导入 RCTBridgeModule.h。在 Xcode 中混合两种语言时要小心，因为如果没有正确实现，桥可能会泄漏。

# 推送通知(远程和本地)和 Amazon SNS 移动推送

推送通知很难，并且在 React 原生应用中存在独特的问题。这是 React Native 的另一部分，可能需要针对所使用的特定平台进行定制。

在研究 React Native 中的推送通知时，我偶然发现了斯潘塞·卡利的两个关于本地推送通知(使用[*React-Native-Push-Notification*](https://github.com/zo0r/react-native-push-notification))和远程推送通知(使用[*one signal*](https://onesignal.com/)*)*

*   [*如何在 React Native 中设置本地推送通知(iOS & Android)*](/differential/how-to-setup-push-notifications-in-react-native-ios-android-30ea0131355e)
*   [*用一个信号反应本机远程推送通知*](/differential/react-native-push-notifications-with-onesignal-9db6a7d75e1e)

进一步的研究表明，有几个用于本地通知设置的开源 npm 库，包括，
[react-native-notifications](https://www.npmjs.com/package/react-native-notifications)，[react-native-push-notifications](https://www.npmjs.com/package/react-native-push-notifications)， [react-native-onesignal](https://www.npmjs.com/package/react-native-onesignal) 等等。

我看到了另一篇有趣的文章，比较了 OneSignal、urban 导和亚马逊 SNS 移动推送。

***问题现在变成了
1。如何将推送通知整合到使用一组迷你应用构建的宏应用中？***

解决方案似乎是为宏应用程序建立通知，而不是在每个组成的迷你应用程序中。 然而，我们在这里冒险进入稀薄的氛围，因为 React Native 和 Electrode Native 的许多功能仍然是新的和不断发展的，我还没有遇到过自信地实施这种方法的团队。虽然以这种方式连接各个部分似乎是合乎逻辑的方法。

**2*。可以使用亚马逊 SNS 移动推送 vs OneSignal 或其他竞争产品，作为推送通知的 MBaaS 来反应原生应用程序吗？***

这里的研究显示，亚马逊在 2017 年底发布了对 React 原生应用推送通知的支持。这意味着您可以在 Amazon SNS Mobile Push 中为 React Native 应用程序创建一个移动端点(通过使用 iOS 凭据获得 iOS 支持，使用 Google Firebase 注册 ID 获得 Android 支持)。

因此，使用 Amazon SNS Mobile Push 作为推送至宏应用程序(使用组成的迷你应用程序构建)的服务，似乎是在使用微服务方法构建的应用程序中实现推送通知的可行方法。请记住，虽然在这一点上，这仅仅是猜测，我们的团队还没有实现这作为一个彻底的测试。这些技术平台和范例相对较新，但是以这种方式连接它们似乎是一种可行的方法。

# React 本地应用中的持久用户会话管理

持续用户会话是移动应用中的常见功能，用户必须登录才能访问应用中受保护的内容。用户会话必须由应用程序正确管理，以获得无缝的用户体验，这样用户就不会在每次启动应用程序时被注销。

我看到了下面的[文章，作者使用 React Native Async storage 和 react-native-router-flux 实现了一个简单的持久用户会话管理](http://blog.theodo.fr/2017/03/how-to-create-an-authentication-system-and-a-persistent-user-session-with-react-native/)。方法很简单

*   向用户显示一个身份验证页面，在该页面上，用户将被提示输入用户名和密码，并能够注册或登录
*   允许用户使用后端 API 注册或登录，可能使用 OAuth2。
*   从手机的*异步存储器*中存储和恢复身份令牌
*   允许用户使用 id 令牌从 API 的受保护路由获取内容
*   验证 id 令牌是否存在，以创建持久用户会话
*   允许用户访问受保护的主页，用户将能够从 API 获取受保护的内容或注销。

*这种方法非常简单，可以在宏应用程序中使用，异步或领域数据库作为底层持久存储。迷你应用还可以使用持久用户会话和令牌来访问各个迷你应用内的受保护内容。*

另一种可能的替代方法是使用 AWS mobile hub IAM 管理服务来管理用户会话。这当然使用 Amazon Cognito 作为认证后端。

# 摘要

React Native 中的迷你应用是开发实现微服务范式的移动应用的可行方法。除了 React Native 提供的跨平台优势，迷你应用还允许 ***单独的代码库，基于业务功能的团队分离*** 和 ***分别扩展和部署移动应用部分的能力*** 。还可以对使用 React Native 构建的应用程序进行代码推送(现在是 Visual Studio App Center ),这允许用户在手机上更新应用程序，而不必通过应用程序商店。这在推出热补丁或 A/B 测试或 canary 发布时非常有用。

迷你应用和宏应用可以使用 ***异步存储或领域数据库*** 实现设备上的数据持久性。这可以用于持续的用户会话管理，以避免用户每次在手机上打开应用程序时都要登录。

理解 React Native 的细微差别对于充分利用 React Native 的性能非常重要。为此，确定反应本地桥 e 上的 ***通道数量并保持该数量较低非常重要。***

在棕地开发的情况下，React Native 允许 React 本机代码与现有的本机代码接口，并且 ***本机模块允许开发人员从 JS 代码访问本机平台 API。***

***迷你应用的推送通知实现可以在宏应用级别完成，而不是在迷你应用级别。有几个库允许在 React Native 和平台(如 OneSignal)中实现本地通知，允许远程通知，但是这种实现仍然是相对较新的未知领域。***

Amazon SNS 移动推送和 npm 库可以用来实现推送通知服务。值得注意的是，需要添加特定于平台的代码来实现本机反应，并且在应用程序的 iOS 实现和 Android 实现的 GCM 或 Firebase 的情况下将使用 APN。后端管道可以连接到 Amazon SNS Mobile Push 作为移动端点，后者又可以连接到 Amazon SNS publisher，或者在需要时从 AWS Lambda 服务或其他 SQS 服务触发。

*觉得这个帖子有用？点击*👏*下面的按钮显示你有多喜欢它:)*

[***关注我上媒***](/@prashantramnyc) ***获取最新更新和帖子！***

![](img/357de15ef0c461d3d85722efc3bae795.png)

**阅读下一篇:** [移动应用开发的微服务方法](/@prashantramnyc/microservices-architecture-for-mobile-application-development-part-i-20b4f4089a24)

**其他文章:** [如何用 Javascript 构建一个简单的 Sprite 动画](/@prashantramnyc/how-to-build-a-simple-sprite-animation-in-javascript-b764644244aa)

[承诺用 Javascript 解释！](/@prashantramnyc/promises-in-javascript-explained-277b98850de)