# iOS 版 Firebase ML 入门

> 原文：<https://medium.com/hackernoon/getting-started-with-firebase-ml-for-ios-71e578469367>

![](img/a2baacf8b4dfcfb846f3e1c96534a92d.png)

[https://firebase.google.com/products/ml-kit/](https://firebase.google.com/products/ml-kit/)

昨天在谷歌 I/O 大会上， [Firebase](https://hackernoon.com/tagged/firebase) 团队以 [Firebase ML 套件](https://firebase.google.com/products/ml-kit/?gclid=Cj0KCQjwuMrXBRC_ARIsALWZrIhIflglbz3VJyEbhOhWJdW3VqZzdwavKsyJ18q9zQyIV7fwFp6FN5caAp2tEALw_wcB)的形式发布了 Firebase 平台的机器学习功能。Firebase ML 套件附带了一个现成的 API，用于常见的移动用例，包括识别文本、检测人脸、扫描条形码、标记图像和识别地标。

Firebase ML 工具包可以与使用设备 API 或云 API 的应用程序集成。设备上的使用是免费的，云的使用有很大的限制。在这篇文章中，我们将介绍如何创建一个简单的 [iOS](https://hackernoon.com/tagged/ios) 应用程序，它将与 Firebase ML 框架和标签图像集成。

## 安装 Firebase CocoaPods:

创建 Xcode 项目后，您需要安装所需的 Firebase CocoaPods。在这样做之前，确保你已经安装了最新版本的 CocoaPods。创建一个 pod 文件和，并指定要安装的 pod，如下所示:

从终端执行**吊舱安装**命令，终端将按照**吊舱文件**的规定安装吊舱。安装完所需的 pods 后，确保为您的项目使用**工作空间**文件，而不是 **xcodeproj** 文件。

## 在 Firebase 控制台上配置 Firebase 项目:

为了使用大多数/所有 Firebase 特性，您必须在 [Firebase 控制台](https://console.firebase.google.com/)上创建一个 Firebase 项目。Firebase 控制台提供了一个很好的用户界面，您可以在其中创建自己的 Firebase 项目。每个 Firebase 项目都可以配置为使用 Firebase platform 提供的许多服务。

Firebase 安装程序将创建一个**Google service-info . plist**文件，您可以下载并将其复制到 Xcode 项目中。最后，通过调用**diddfinishlaunchingwithoptions**函数中的 **Firebase.configure()** 来配置 Firebase，如下所示:

**设备 API 上的 Firebase ML:**

现在，我们已经设置了 Firebase 平台，下一步是将其与 Firebase ML API 集成。信不信由你，下面是你需要与 Firebase ML On Device API 集成的所有代码。我们有一个想要检测的图像列表。图像是 Xcode 项目的一部分，名称存储在一个数组中。

vision 实例可以访问返回 labelDetector 的 labelDetector 函数。标签检测器帮助检测与图像相关的标签。VisionImage 类使用 UIImage 或 CMSampleBufferRef 创建 VisionImage 实例。labelDetector 实例调用 detect 函数，传入 visionImage 对象，该对象会导致有关图像的预测或错误。如果没有错误，那么我们只需使用 max 函数找到最高的标签，并将其设置为 UILabel 的文本。

*   你不需要运行循环(52–55)，我只是显示所有不同的预测以及它们从 Firebase ML 接收的置信度。

结果如下所示:

## Firebase ML 云 API:

Firebase ML Cloud API 在云上执行图像标记。这也使它能够拥有一个不断更新和改进的非常大的数据集。

云 API 的代码与设备上的 API 非常相似。唯一微小的变化是我们使用了 **cloudLabelDetector** 而不是默认的 **labelDetector** ，如下所示:

如果您运行以上代码，您将在控制台中看到错误消息。最有可能的错误信息与 Firebase ML 云帐户未设置有关。点击[链接](https://console.cloud.google.com/)，设置你的 Firebase ML 云账户。谷歌会在你的账户中存入 300 美元，可以在第一年使用。

结果如下所示:

我希望你喜欢这个帖子，快乐编码！

[ [下载示例代码](https://github.com/azamsharp/FirebaseML)

如果您有兴趣了解更多关于将 Firebase 与您的 iOS 应用程序集成的信息，请查看我下面的课程“**使用 Swift 语言掌握 Firebase for iOS】”。感谢您的支持！**

[](https://www.udemy.com/practical-firebase-for-ios-using-swift/?couponCode=ILOVEFIREBASE) [## 使用 Swift 语言掌握 iOS 版 Firebase

### 通过构建真实世界的项目，学习将 Firebase 与您的 iOS 应用程序相集成！

www.udemy.com](https://www.udemy.com/practical-firebase-for-ios-using-swift/?couponCode=ILOVEFIREBASE)