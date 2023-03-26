# 作为 Android 工程师部署后端应用程序—第 2 部分

> 原文：<https://medium.com/hackernoon/deploy-a-backend-app-as-an-android-engineer-part-2-b572245a3f3e>

## 用 Ktor 快速迭代

![](img/0094ebe6b9980ed475d441bf0d3cbde5.png)

Original photo by [SpaceX](https://unsplash.com/photos/VBNb52J8Trk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/VBNb52J8Trk)

在第 1 部分中，我概述了如何通过部署**来部署 AppEngine 应用。jar** 文件。从那时起，谷歌听取了需要建立服务器端解决方案的移动开发者的意见，并在谷歌云平台上推出了[kot Lin](https://cloud.google.com/kotlin/)。

[](https://hackernoon.com/deploy-a-backend-app-as-an-android-engineer-49bd94b4c367) [## 作为 Android 工程师部署后端应用程序

### 用你已经拥有的技能

hackernoon.com](https://hackernoon.com/deploy-a-backend-app-as-an-android-engineer-49bd94b4c367) 

其中一个突出的解决方案是 Ktor，它允许在 Kotlin 中有一个独立的后端应用程序，可以像在客户端一样与其他库进行交互。Ktor 最棒的一点是[模糊的库兼容性问题，比如这个](https://stackoverflow.com/questions/52881622/guava-breaking-kotlin-jar-how-to-handle-versioning)与 Guava 的兼容性问题，这仍然是一个谜，不太是个问题，因为代码的运行方式类似于它在 IDE 上的运行方式，因为你可以定义应用程序的 main 方法并让它运行。

Google 云平台文档还有改进的空间。我浏览了另外两个配置教程， *Standard* 和 *Flexible* AppEngine 设置，以便认识到 Ktor 是我部署 Kotlin 应用程序在没有端点的情况下运行服务器任务所需要的。

尽管使用 Jar 很慢，但我仍然在一些用例中使用 Jar deploy。例如，用 Firebase 认证 Jars 中使用的服务帐户简单可靠，而用 ktor 认证就有我发现的这个问题。

**罐子被耍了**

*   大约 10 到 20 分钟的部署时间。
*   必须重建。jar 来测试新代码或不同的环境类型。
*   库不兼容问题。

# **教程:** [**在 Google App Engine 上运行一个 Kotlin Ktor app 标准**](https://cloud.google.com/community/tutorials/kotlin-ktor-app-engine-java8)

本教程快速、清晰、简洁。我强烈推荐！它忽略的一个方面是版本控制，这很重要，因为我喜欢在 AppEngine 上部署一个*试运行*和*生产*版本。

## 文件注释

*build.gradle*

[App Engine Gradle 插件任务和属性](https://cloud.google.com/appengine/docs/flexible/java/gradle-reference)

找到最新版本的 Ktor 来使用:[github.com/ktorio/ktor/releases](https://github.com/ktorio/ktor/releases)。

```
buildscript {ext.ktor_version = '*1.0.0*' **...** }
```

定义 AppEngine 版本:

```
appengine {
    deploy {
        version = '1-2-0'
    }
}
```

*ClassWithMainMethod.kt*

为了从 IntelliJ 等 IDE 中运行 **main** 方法，您需要注释掉 **Application.main** 方法，并在 IntelliJ 可以从*运行/调试配置中配置的对象中使用 main 方法。*为了运行 AppEngine 并进行部署，请使用 **Application.main** 方法。

```
importio.ktor.application.Applicationfun Application.main() {
    // App logic here.
}
```

使用 IntelliJ 运行:

```
objectInitialization {
    @JvmStatic
    funmain(args: Array<String>) {
        // App logic here.
    }
}
```

[*app engine-web . XML*](https://cloud.google.com/appengine/docs/standard/java/config/appref)*示例用途:*

*   *[指定 Java 8 运行时](https://cloud.google.com/appengine/docs/standard/java/migrating-to-java8)*
*   *缩放比例*
*   *使用 include 的资源文件*
*   *SSL 已启用(HTTP 与 HTTPs)*

*[*web.xml*](https://cloud.google.com/appengine/docs/standard/java/config/webxml) :下*src/main/WEB app/we b-INF/*示例使用:*

*   *web.xml:定义 URL 路径和使用这些路径处理请求的 servlets 之间的映射*

# *[管理 SDK 配置](https://cloud.google.com/sdk/docs/configurations)*

*命令行可以快速帮助您创建 AppEngine 项目并在项目间导航，运行您的应用程序进行测试，并部署到服务器，而不是在 IntelliJ 中重复构建工件。*

*请参见本[终端命令指南](https://github.com/AdamSHurwitz/open-dev-guide/blob/master/gcp/gcp.md#terminal-commands)来使用它。*

**我是亚当·赫维茨——点击拍手图标，看看我剩下的* [*写作*](/@AdamHurwitz) *如果你喜欢以上内容。感谢阅读！**

*![](img/8d30ab065284bfc08db9285c7cfef221.png)*

# *资源*

***GCP***

*   *[安装最新的云工具……](https://cloud.google.com/sdk/docs/#install_the_latest_cloud_tools_version_cloudsdk_current_version)*
*   *[快速入门](https://cloud.google.com/tools/intellij/docs/quickstart-IDEA)*
*   *[GCP 控制台](https://console.cloud.google.com/cloud-resource-manager)*
*   *[AppEngine 定价](https://cloud.google.com/appengine/pricing)*

***IntelliJ***

*   *[配置您的开发环境](https://cloud.google.com/appengine/docs/standard/java/building-app/environment-setup#configuring_your_development_environment)*
*   *[创建新的应用引擎项目](https://cloud.google.com/appengine/docs/standard/java/building-app/environment-setup#creating_a_new_app_engine_project)*
*   *[添加(GCP)框架支持(到 IntelliJ)](https://cloud.google.com/tools/intellij/docs/create-flexible#framework)*
*   *[创建新的(IntelliJ)应用程序(用于 GCP)](https://cloud.google.com/tools/intellij/docs/create-flexible#creating_a_new_application)*
*   *[签出现有的(IntelliJ)项目(与 GCP 一起使用)](https://cloud.google.com/tools/intellij/docs/create-flexible#checking_out_an_existing_project)*

***科特林***

*   *[**在谷歌应用引擎上运行一个 Kotlin Ktor 应用标准**](https://cloud.google.com/community/tutorials/kotlin-ktor-app-engine-java8)*
*   *[应用引擎梯度插件任务和属性](https://cloud.google.com/appengine/docs/flexible/java/gradle-reference)*
*   *[github.com/ktorio/ktor-samples](http://github.com/ktorio/ktor-samples)*
*   *[测井控制台](https://console.cloud.google.com/logs?_ga=2.214847829.-399896800.1533853483)*
*   *[ktor](https://ktor.io/)*
*   *[谷歌云平台上的科特林](https://cloud.google.com/kotlin/)*

***社区***

*   *[讨论谷歌云平台](https://cloud.google.com/support/docs/groups)*
*   *[云连接社区](https://www.cloudconnectcommunity.com/ccc/)*
*   *[谷歌云平台支持](https://cloud.google.com/support/)*
*   *[stackoverflow.com/questions/tagged/google-cloud-kotlin](https://stackoverflow.com/questions/tagged/google-cloud-kotlin)*
*   *[云科特林谷歌集团](https://groups.google.com/forum/#!forum/cloud-kotlin)*
*   *[github.com/GoogleCloudPlatform/kotlin-samples](https://github.com/GoogleCloudPlatform/kotlin-samples)*
*   *[github.com/GoogleApis](https://github.com/googleapis)*
*   *[github.com/google-cloud-java](https://github.com/googleapis/google-cloud-java)*
*   *【github.com/GoogleCloudPlatform 号*
*   *【github.com/app-gradle-plugin *