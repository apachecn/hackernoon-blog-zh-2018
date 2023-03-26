# 介绍 Cake Kudu 客户端

> 原文：<https://medium.com/hackernoon/introducing-cake-kudu-client-abda40d15f38>

## 使用 Cake 远程发送到 Azure 应用服务

![](img/e07fbdfe148df02d42b5c418f579fb86.png)

我以前写过如何在 Azure App Services 上利用[蛋糕](https://cakebuild.net/)构建脚本和[蛋糕构建和部署你的 web 应用和功能。库都](https://www.nuget.org/packages/Cake.Kudu)艾丁。

虽然这很容易开始，只需添加一个构建脚本并将其连接到您选择的源代码库，但对于某些场景，预构建您的应用程序更有意义。

其中一些场景可能是

*   ***静态网站*** ，在这里它需要更多的资源来构建，而不是提供服务。
*   ***多区域应用*** ，您可以将相同的位部署到多个站点，并且只需构建一次。
*   ***构建需求*** ，通过构建应用程序服务，您将受限于它预装的工具或它的环境支持的工具。
*   ***私有资源*** ，使用私有的掘金提要或其他编译时敏感的信息可能会很麻烦，而且可能不是你想要放在你的网站上的。

# 蛋糕库度客户来拯救

Azure web 和 function apps 背后的“引擎”提供了一个基于 http 的 API，deploy 是它提供的特性之一。正是这个 API“Cake Kudu Client”提供了一个类型化的 C# API，这使得它接近于从目录或 zip 文件部署的一行程序。

上面所做的基本上是

1.  从环境变量获取你的应用服务的 Kudu 端点(即[https://{ your site } . SCM . azure websites . net](https://kuduclienttest.scm.azurewebsites.net))，用户名和密码。
2.  使用`KuduClient`别名实例化一个新的客户机。
3.  使用 ZipDeployDirectory 方法，该方法将文件夹压缩到内存中并进行部署。

一个使用静态站点生成器 WYAM 的真实示例如下所示:

# 不仅仅是部署

部署只是蛋糕库客户端插件处理的特性之一

*   执行远程 shell 命令
*   枚举上的远程文件和目录
*   将文件和目录上传到 AppService
*   从 AppService 下载文件和目录
*   从本地文件夹或 zip 文件部署到 AppService

更多的功能正在计划中。

您可以在 Cake 网站上找到可用方法的完整列表和示例:
[https://cakebuild.net/dsl/kudu/](https://cakebuild.net/dsl/kudu/)

[](https://hackernoon.com/delivering-functions-with-cake-4b269c50f817) [## ⚡Delivering 功能与蛋糕🍰

### C#中的 Azure 函数自定义部署脚本

hackernoon.com](https://hackernoon.com/delivering-functions-with-cake-4b269c50f817)