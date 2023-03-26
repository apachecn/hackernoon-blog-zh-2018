# 第三周与 React Native:为什么使用 Expo？

> 原文：<https://medium.com/hackernoon/week-3-with-react-native-107f6779a831>

![](img/c559b8e8adbf549ded36a7a7f9e0fc98.png)

这个星期对我来说是忙碌的一周。截止日期越来越近，有太多的任务要完成。这也是一个正在发生的事情。我被邀请加入 Gatsbyjs 开源团队，帮助和维护正在进行的项目。我喜欢为开源社区和项目做贡献。

![](img/7fc50c6d65d85f1c588c6b61486d4345.png)

# 👍

本周，我在 Expo 和 React Native 中做了更深入的探索。我成功地使用 Expo API 在一个 RN 应用程序中实现了脸书登录和访问 Firebase 数据库。世博会是美好的工作。它处理了大量原生工作，否则人们将通过打开 Xcode 或 Android Studio 来集成这些工作。我发现这些链接有助于在 React 本机应用程序中提供脸书身份验证。

*   脸书世博会
*   对于 Firebase SDK 的设置和在 React 本地应用中的集成，请通过[链接](https://docs.expo.io/versions/latest/guides/using-firebase#__next)，该链接也由 Expo API 提供。

Expo 的一个好处是它附带了 Create-React-Native-App starter 项目。我本周经历的第二重要的事情是在 React Native 中集成和实现 Redux store。为了快速入门，我参加了一个由 Stefan Hyltoft 主持的 [Udemy 课程，因为注册是免费的，而且似乎很切题。他对概念的解释很清楚，但他的工作方法并不吸引我。任何关于如何组织行动和减少的建议将是有益的。](https://www.udemy.com/learn-redux-in-react-native-in-less-than-2-hours/)

# 为什么用 Expo 做 React Native？

Expo 之所以受欢迎，是因为它本身可以处理许多令人头痛的任务，并提供流畅的 API，可以在盒子外面与 React 本机应用程序一起工作。它是开源的，使用起来不需要任何费用。要在真实的 iOS 设备上进行测试，你需要一个苹果开发者账户(99 美元/年)。您可以在 iOS 和 android 两个平台上使用 Expo 来实现这一点。Expo 提供了一个客户端应用程序，通过根据您的设备运行的移动平台从相应的商店下载，您可以轻松地测试应用程序。

目前，Expo 的 SDK 处理相机、地图、位置跟踪、分析、推送通知等等。向它发布一个 Expo 应用程序很容易。您可以通过运行以下命令来完成该过程。它有专门的[商店](https://expo.io/)，你可以在那里发布应用程序供他人使用。对原型制作很有帮助。

```
exp publish
```

对于独立应用程序，您可以使用 Expo CLI 工具中的命令。独立的应用程序不需要 Expo 客户端来运行应用程序。您可以通过运行以下命令来生成 IPA 或 apk 文件:

```
exp build:ios# ORexp build:android
```

使用 Expo 有缺点。我不打算在这里列出它们，但它背后的团队似乎很快就实现了这些新功能。您可以提交一个功能或使用此功能投票或作为贡献者参与。

[](https://expo.canny.io/feature-requests) [## 功能请求-博览会

### 通过 Expo，你可以使用 React Native 用 JavaScript 编写 iOS 和 Android 体验。

世博. canny.io](https://expo.canny.io/feature-requests) 

# 即将推出

我目前正在做的事情是使用 Expo API 实现`react-navigation`和图像拾取器。我目前正在写一个关于开始使用`react-navigation`的“如何做”的教程，一旦发表，我将与你分享。

 [## React 导航为您的 React 本机应用程序提供路线和导航

### React 原生应用的路由和导航

为您的家乡 appsreactnavigation.org 提供路线和导航](https://reactnavigation.org/) 

如果你想听到更多我的消息，你可以保持联系。

我也在推特上👋

[](https://twitter.com/amanhimself) [## 阿曼·米塔尔·🖖⚛️☕(@阿曼本人)|推特

### 阿曼·米塔尔·🖖⚛️☕的最新推特(@阿曼本人)。#博客宣言的作者:https://t.co/V23cYnNeR7 |…

twitter.com](https://twitter.com/amanhimself)