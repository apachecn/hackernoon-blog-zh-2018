# 作为 Android 工程师部署后端应用程序

> 原文：<https://medium.com/hackernoon/deploy-a-backend-app-as-an-android-engineer-49bd94b4c367>

## 用你已经拥有的技能

![](img/59db3b98e4f8939df7b8c7a5c65ff75a.png)

Original photo by [SpaceX](https://unsplash.com/photos/VBNb52J8Trk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/VBNb52J8Trk)

# 2018 年 10 月 24 日更新:[以 Android 工程师的身份部署后端应用程序——使用 Ktor 加快迭代速度](https://hackernoon.com/deploy-a-backend-app-as-an-android-engineer-part-2-b572245a3f3e)

作为一名移动开发人员，使用现有的 API 可以做很多事情来证明概念。虽然当逻辑需要在后台不断运行以检查数据的情况下，后端应用程序是更好的选择，因为你不想把你的移动设备变成移动服务器。如果你问一个在旧金山一家中型公司的初创公司工作的后端工程师，你可能会得到学习 Javascript(特别是 Node.js)的建议，承诺一旦熟悉 Javascript 就可以快速编写，并且易于部署。

在询问了一些 Node.js 开发者之后，我和我的朋友[福恩](https://www.linkedin.com/in/fawnbertram/)进行了同样的对话，他已经做了几年 Node 了。我期待着同样的答案，因为她重申了上面的估计，我将花费 2-3 周的时间来学习 Javascript (Js)。然后，她提出了一个替代方案:

> 学习 Js 是有用的，但是你已经知道如何用 Java 来做这件事，并且今天就可以构建它。

## **我:**

福恩是对的。鉴于他们已经拥有的最佳实践和库，大多数 Android 工程师都有能力构建基本的后端应用程序。例如，可以使用 rewinch 通过 RxJava 发出网络请求，以处理传入和传出的数据流，并进行高级操作，同时通过 Kotlin 避免代码膨胀。使用这种方法，我开始部署后端实例来收集和操作来自多个 API 的数据。我正在使用谷歌云平台(GCP)的 AppEngine 与 Firebase 集成，以保存可以从移动/网络客户端轻松访问的数据。Firebase 还可以直接与谷歌的 [BigQuery](https://cloud.google.com/bigquery/) 集成，以进行进一步的分析(从 Firestore 到 BigQuery 的[带有 Firebase 功能](https://blog.questionable.services/article/from-firestore-to-bigquery-firebase-functions/))。

谷歌云平台一旦设置好就很容易使用，而且非常强大。也就是说，知道你需要知道什么本身就是一个研究项目。在你对你的项目所需的步骤有了一个大致的轮廓之后，你就可以开始过滤文档和指南，寻找那些符合最终目标的。希望下面的步骤和资源能为你节省大量时间，让你直接进入执行阶段。

托管 Kotlin 服务器应用程序似乎比我在 Node.js 上看到的“一键”部署有更多的设置，如在 [Digital Ocean](https://www.digitalocean.com/products/one-click-apps/) 上看到的，但一旦用 IntelliJ 设置了该过程，不到一分钟就可以开始部署。如果你从事更复杂的计算，Kotlin/Java 的多线程也有继承的好处。

# 概观

找出所需的步骤需要一个寻宝游戏。我仔细阅读了重叠的 Google 和 IntelliJ 文档以及关于 AppEngine Flexible vs Standard(仍然不确定重要的区别，我只知道 Flexible 可以很好地与 IntelliJ 和 GCP 集成)、Apache 和 Maven 集成、Java 网站托管等的指南。没有一个注释简单地概述了 Kotlin/Java 需要如何格式化才能被托管到服务器上。

1.  一旦应用程序逻辑完成，构建一个**。JAR** 文件，它将在服务器上运行。
2.  曾经的**。JAR** 文件构建完成后，您可以在 IntelliJ 中测试它是否在本地成功运行。
3.  展开一个**。JAR** 文件到服务器，部署配置将在服务器上运行，直到暂停或停止。

现在进入细节…

*好消息是，第一次设置应用程序后，您可以跳到* **步骤 4:管理服务器**，设置重新部署只需不到 1 分钟的时间。

# 设置本地开发环境—第 1 步，共 4 步

## GCP AppEngine 设置

**1。按照** [**的步骤安装最新的云工具…**](https://cloud.google.com/sdk/docs/#install_the_latest_cloud_tools_version_cloudsdk_current_version)

a)解压目录，存储在库目录>调用云目录外一级。

```
**./google-cloud-sdk/install.sh**
```

b)重启终端，使 **gcloud** 命令生效。

**2。安装应用引擎并登录。**

```
**sudo gcloud components install app-engine-java****...****gcloud auth application-default login**
```

## IntelliJ 配置

1.  **按照** [**下的步骤配置您的开发环境**](https://cloud.google.com/appengine/docs/standard/java/building-app/environment-setup#configuring_your_development_environment) **。**
2.  **使用 IntelliJ 中现有的 gradle 或 maven 项目或** [**创建新的 IntelliJ 项目**](https://cloud.google.com/appengine/docs/standard/java/building-app/environment-setup#creating_a_new_app_engine_project) (确保下载 IntelliJ*Google Cloud Tools*插件并重启)。

# 创建 GCP 项目并与 IntelliJ 集成—第 2 步，共 4 步

## GCP 设置

**创建*新项目*下** [下 **GCP 控制台**](https://console.cloud.google.com/cloud-resource-manager) **用** [**快速启动**](https://cloud.google.com/tools/intellij/docs/quickstart-IDEA) **步骤和确保项目已启用计费。**

## 与 IntelliJ 集成

1.  **在*编辑配置*下用*项目模块名 _ 主设置普通 Java 构建配置。***
2.  **要么** [**用 GCP 启用当前打开的项目，**](https://cloud.google.com/tools/intellij/docs/create-flexible#framework) [**启动一个新的**](https://cloud.google.com/tools/intellij/docs/create-flexible#creating_a_new_application) **，要么** [**从 GitHub repo 中使用现有项目**](https://cloud.google.com/tools/intellij/docs/create-flexible#checking_out_an_existing_project) **。**

**针对当前打开的项目:**选择*工具>谷歌云工具>添加 AppEngine 支持>谷歌* AppEngine *灵活*选择创建**。yaml** 当给出选择并确保它在***src/main/app engine/app . YAML***>**下时添加框架支持选择 *projectModuleName* (而不是*project name*_*main*或*project name*_*test*)**

*****app.yaml*****

**如果应用程序正在处理一项任务，以便为不处理用户网络请求的后端服务填充数据，那么确保只有一个实例在运行就很重要。否则，多个实例可能会用重复数据填充后端。(参见[使用 app.yaml 配置您的应用](https://cloud.google.com/appengine/docs/flexible/nodejs/configuring-your-app-with-app-yaml))**

```
manual_scaling:
 instances: 1
```

# **生成可部署的 JAR —第 3 步，共 4 步**

## **[将应用程序打包在一个罐子里](https://www.jetbrains.com/help/idea/creating-and-running-your-first-java-application.html#package)**

***文件* > *项目结构* > *项目设置* > *工件*>**+**>*Jar*>*从具有依赖关系的模块…***

*   ***模块* : *项目 ModuleName_main***
*   ***main class*:*YourMainClassWithMainMethod***
*   **清单:***src/main/****清单名称***
*   ***输出目录:project name***/out/artifacts/***jarName*_ jar**

## **构建并运行 JAR**

1.  **构建>*构建构件……*>*动作* > *构建***
2.  **运行 Jar: *运行* > *编辑配置* : **+** 并选择 Jar 应用程序(*启动前:***+>*构建工件*>*artifact name*)****

# **配置并部署到 GCP —第 4 步，共 4 步**

## **[部署配置](https://cloud.google.com/tools/intellij/docs/deploy-flex)**

*   ***部署档案*:*yourpath route/project jarname . jar***
*   ***项目*:选择 GCP**
*   ***app.yaml:* 从文件路径选择**
*   ***快跑！***

****调试****

*   **如果未设置计费，第一次可能会失败>单击输出中的链接并启用计费。**
*   **(7/25/2018)如果应用程序中使用了. json 文件，则 AppEngine 的部署过程中会出现错误。AppEngine 不会处理。json 文件包含在。尽管有。在 IntelliJ 中独立运行时，jar 成功读取文件。我概述了 Stackoverflow 的一个[解决方案，包括创建一个对象并使用](https://stackoverflow.com/questions/51619324/unable-to-read-firebase-json-within-jar-file-on-appengine#51621514) [Gson 库](https://github.com/google/gson)将其转换为 json。当需要使用时，这是相关的。json 文件来认证 Firebase 等服务。**

## **管理服务器**

****启动/停止服务器:**你可以在[谷歌云控制台](https://console.cloud.google.com/)下 *AppEngine* > *版本中控制你的服务器。***

****重新部署未来版本****

1.  **在 *AppEngine* > *版本下停止当前版本的服务器 app。***
2.  **在*构建>构建工件下… >重建*将重建。**罐子。****
3.  **使用上面相同的 AppEngine 配置进行部署，因为它应该指向相同的**。JAR** 路径。**

****缩放****

**AppEngine 将自动负责缩放，因此如果你不只是做原型，了解[定价](https://cloud.google.com/appengine/pricing)和监控应用程序是很重要的。**

***我是 Adam Hur witz——点击拍手图标，看看我剩下的* [*写作*](/@AdamHurwitz) *如果你喜欢以上内容。感谢阅读！***

**![](img/8d30ab065284bfc08db9285c7cfef221.png)**

# **资源**

****GCP****

*   **[安装最新的云工具…](https://cloud.google.com/sdk/docs/#install_the_latest_cloud_tools_version_cloudsdk_current_version)**
*   **[快速入门](https://cloud.google.com/tools/intellij/docs/quickstart-IDEA)**
*   **[GCP 控制台](https://console.cloud.google.com/cloud-resource-manager)**
*   **[如何管理实例](https://cloud.google.com/appengine/docs/standard/python/how-instances-are-managed)**
*   **[使用 app.yaml 配置您的应用](https://cloud.google.com/appengine/docs/flexible/nodejs/configuring-your-app-with-app-yaml)**
*   **[AppEngine 定价](https://cloud.google.com/appengine/pricing)**

****IntelliJ****

*   **[配置您的开发环境](https://cloud.google.com/appengine/docs/standard/java/building-app/environment-setup#configuring_your_development_environment)**
*   **[创建新的应用引擎项目](https://cloud.google.com/appengine/docs/standard/java/building-app/environment-setup#creating_a_new_app_engine_project)**
*   **[添加(GCP)框架支持(到 IntelliJ)](https://cloud.google.com/tools/intellij/docs/create-flexible#framework)**
*   **[创建新的(IntelliJ)应用程序(与 GCP 一起使用)](https://cloud.google.com/tools/intellij/docs/create-flexible#creating_a_new_application)**
*   **[签出现有的(IntelliJ)项目(与 GCP 一起使用)](https://cloud.google.com/tools/intellij/docs/create-flexible#checking_out_an_existing_project)**

****震击器****

*   **[将应用程序打包在一个罐子里](https://www.jetbrains.com/help/idea/creating-and-running-your-first-java-application.html#package)**
*   **[部署到 App Engine 灵活环境](https://cloud.google.com/tools/intellij/docs/deploy-flex)**

****燃烧基地****

*   **[从 Firestore 到具有 Firebase 功能的 big query](https://blog.questionable.services/article/from-firestore-to-bigquery-firebase-functions/)**

****在线社区****

*   **[stackoverflow.com/questions/tagged/google-cloud-kotlin](https://stackoverflow.com/questions/tagged/google-cloud-kotlin)**
*   **[云科特林谷歌集团](https://groups.google.com/forum/#!forum/cloud-kotlin)**
*   **[谷歌云平台支持](https://cloud.google.com/support/)**
*   **[Google cloud platform/kot Lin-samples](https://github.com/GoogleCloudPlatform/kotlin-samples)**
*   **G [oogleApis](https://github.com/googleapis) / [谷歌云 java](https://github.com/googleapis/google-cloud-java)**
*   **[Google cloud platform](https://github.com/GoogleCloudPlatform)/[app-gradle-plugin](https://github.com/GoogleCloudPlatform/app-gradle-plugin)**