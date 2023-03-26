# ArchitectNow 使用 API 构建了一个 AngularJS 供应商搜索引擎

> 原文：<https://medium.com/hackernoon/architectnow-builds-an-angularjs-vendor-search-engine-w-apis-1a0f13f917e0>

![](img/d9b187c164145b41e1832aa3233657b5.png)

[ArchitectNow](http://architectnow.net/) 是一家位于密苏里州圣路易斯的定制软件开发公司。他们专门帮助各行各业的客户设计、构建和发布丰富的 web 和移动应用程序。在最近接受 [Cosmic JS](https://cosmicjs.com) 采访时，ArchitectNow 负责人凯文·格罗斯尼克劳斯解释了他领导公司的方向:

> “在 ArchitectNow，我们的技术体系和关注领域在过去 10 年中发生了巨大变化。行业推动了很多，我们团队的能力也推动了一些。现在，我们非常喜欢 Angular，TypeScript，[ASP.NET](https://asp.net/)Core/c#，Xamarin，MongoDB，所有 Azure 的东西。像我们这样的小型开发公司的好处在于，我们有机会使用大量的语言和平台。
> 
> 我们还可以在许多场景中使用第三方组件和 API(比如 Cosmic JS)。这使我们能够为给定的项目挑选最好的平台和工具。我们在选择技术和第三方工具方面取得了很多成功，但也使用了一些我们不推荐的工具。我们给客户带来的一个重要好处是，我们知道什么是“有效”的，什么是无效的。"

最近，ArchitectNow 与 VendorSurf 合作实现了一个强大的抵押贷款行业供应商搜索引擎。与许多 ArchitectNow 定制应用程序一样，项目团队很快发现客户需要管理应用程序中使用的特定类型的内容。他们没有定制构建一个伪内容管理系统(就像他们过去多次做的那样),而是迅速利用 [Cosmic JS](https://cosmicjs.com/) 为 VendorSurf 提供自我管理他们自己的动态内容的能力。

![](img/1d3e2ccbc9347535461428421166a73b.png)

由于易于使用的 [Cosmic JS JavaScript 库](https://github.com/cosmicjs/cosmicjs-node)，ArchitectNow 开发团队能够快速将 [Cosmic JS](https://cosmicjs.com) 功能集成到他们自己丰富的 [Angular](https://cosmicjs.com/knowledge-base/angularjs-cms) 应用程序中。他们还紧密集成了 VendorSurf 服务器端 API 和后台基础设施(用。NET 并托管在微软的 Azure 环境中)通过 Cosmic JS [REST API 的](https://cosmicjs.github.io/rest-api-docs/)与 Cosmic JS 进行交互。通过能够直接从浏览器(通过 JavaScript)和从服务器(通过 REST)进行集成，ArchitectNow 团队能够为 VendorSurf 提供管理定制内容的简化工作流。

> “ArchitectNow 已经在计划如何利用 Cosmic JS 来帮助未来的客户简化其定制内容的管理，同时仍然利用完全定制的软件应用程序的优势。”

关注供应商冲浪，因为他们将供应商添加到他们的第一个 B2B 抵押贷款供应商搜索引擎:

[查看更多的 Cosmic JS 客户](https://cosmicjs.com/case-studies)围绕一个与编程语言无关的 API 协作来管理他们的内容🚀

![](img/4d5ba6415332614f68610f1a2af044a7.png)

[https://cosmicjs.com/case-studies](https://cosmicjs.com/case-studies)

要了解如何向 [Cosmic JS 社区](https://cosmicjs.com/community)贡献应用、文章和扩展，请通过[build@cosmicjs.com](mailto:build@cosmicjs.com)联系我们。