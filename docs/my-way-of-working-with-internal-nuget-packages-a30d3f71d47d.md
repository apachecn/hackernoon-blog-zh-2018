# 我使用内部 nuget 包的方式

> 原文：<https://medium.com/hackernoon/my-way-of-working-with-internal-nuget-packages-a30d3f71d47d>

![](img/180fd3be6996b18b665d001b9070f96d.png)

Picture borrowed from [https://cdn.pixabay.com/photo/2013/07/13/10/22/box-157074_1280.png](https://cdn.pixabay.com/photo/2013/07/13/10/22/box-157074_1280.png)

我工作的公司足够大，可以跨越多个开发团队。为了能够共享一种共同的工作方式，并防止轮子被反复发明，我们依赖于共享的 [nuget](https://docs.microsoft.com/en-us/nuget/what-is-nuget) 包。

我是许多软件包的作者，也是这些软件包的主要贡献者，这需要我花一些时间来维护它们。为了能够做到这一点，我需要一种能让我高效完成这项工作的方法。

在这篇文章中，我将一步一步地分享我的方法。

## 版本控制

为了对包进行版本控制，使用了 [git](https://git-scm.com/) 。Git 是一个*事实上的标准*，不需要进一步描述:)

## 命名

决定一个适合你的好的命名约定。我们使用公司前缀，用点分隔，范围缩小。比如:CompanyA。AspNetCore.Mvc 将是我们自己的包，为 ASP.NET 核心 Mvc 定制类。

## 项目结构

为了保持一致，应该使用项目结构。我的观点是基于大卫·福勒的这个要点。它并没有被严格遵循，而是受到了启发。例如，测试项目没有被分离到它自己的测试文件夹中，而是包含在 src 文件夹中。这个设置只做一次，当我引入额外的包时，我只是从另一个项目中复制结构。这个结构可以很容易地提取到一个[点网模板](https://docs.microsoft.com/en-us/dotnet/core/tools/custom-templates)中。

**Nuspec 文件**

通过添加您的 nuspec 文件，遵循[这个](https://docs.microsoft.com/en-us/nuget/reference/nuspec)引用。

**测试**

确保用单元测试覆盖您的包。这将给你信心，使你的代码像你期望的那样工作，也将帮助其他贡献者。我正在使用 [xUnit](https://xunit.github.io/) 和一些项目中的 [AutoFixture](https://github.com/AutoFixture/AutoFixture) 来最大化可维护性和减少安排阶段的代码。

**文献**

向项目中添加一个 README.md，解释如何使用该包。更多关于 README.md 下面关于*制作包*的部分。我正在考虑调查[阅读文档](https://readthedocs.org/)作为补充。

## 如何引入变化？

使用的工作流是 [Git Flow](https://nvie.com/posts/a-successful-git-branching-model/) ，这意味着我们不直接将变更推送到主分支，而是推送到我们针对开发分支集成的特性分支。我不打算在这里详细介绍 Git 流。

当一个特性完成时，一个拉取请求被打开，利益相关者被邀请。希望这个特性能被合并回去开发，并为下一个即将到来的版本做好准备。git 标签应该反映将要发布的版本。我在*制作软件包一节中解释了我这样做的过程。*

**突破性变化还是没有？**

那么，什么是突破性的改变呢？

> 一个[软件](https://en.wiktionary.org/wiki/software)系统的一个部分的变化可能导致其他组件[失效](https://en.wiktionary.org/wiki/fail)；最常出现在多个应用程序使用的共享代码库中— [维基百科](https://en.wiktionary.org/wiki/breaking_change)

如果更改向使用者公开的现有方法的定义，或者从对象中移除属性，这是一个重大更改。请确保您有一个针对您的包的消费测试客户端，以便您可以验证您所做的更改是否正确。

当需要更新一个已经存在的包时，你需要考虑你的消费者。你需要问问自己，你引入的是否是一个突破性的改变。如果不是，那也没关系。该更改应该只改变次要版本和/或补丁版本。

如果你要引入一个突破性的改变，在删除它之前，你需要保持对不推荐的特性的支持至少一个版本。我倾向于这样做

1.  添加新功能
2.  通过添加一个 [ObsoleteAttribute](https://docs.microsoft.com/en-us/dotnet/api/system.obsoleteattribute.message?view=netcore-2.1) 弃用旧特性，并描述消费者应该如何迁移
3.  删除次要版本并发布新版本

在下一个主要版本中，删除过时的功能。通过这种方式，给消费者一些时间来迁移他们的现有代码以使用新版本。

## 制作包装

在发布新的包之前，我要确保

1.  nuspec 文件中的依赖项已经更新，并且支持的目标框架已经就位
2.  README.md 更新了新的功能和示例
3.  版本控制正确

**nu spec 中的依赖关系**

nuspec 文件清单中有一个部分可以让您指定包所拥有的[依赖项](https://docs.microsoft.com/en-us/nuget/reference/nuspec#dependencies)。即使包只支持一个目标框架，它们也包含在一个[依赖组](https://docs.microsoft.com/en-us/nuget/reference/nuspec#dependency-groups)中。这使得将来添加额外的目标框架变得容易。

**更新 README.md**

为了确保我更新了 README.md 以便于理解，我在编辑时使用了 [Markdown Live Preview](https://markdownlivepreview.com/) 。这给了我一个可视化的编辑器来显示文件。然后，我将内容复制到我的 README.md 并提交它。

**对包进行版本控制**

我用的是[语义版本](https://semver.org/)。要对包进行版本控制，编号必须在*中保持一致。csproj，。nuspec 和 git 存储库中的。为此，我使用了定制的 PowerShell 脚本。

这是通过从 src 文件夹运行它来执行的。

`.\bump-assembly-version.ps1 -Version 0.0.1`

这是通过从 src 文件夹运行它来执行的。

`.\bump-nuspec-version-ps1 -Version 0.0.1`

然后，我提交这些更改

`git commit -am "Incremented project version"`

在推送任何东西之前，我确保我的存储库被标记了相同的版本，这样我们就有了一个对应于发布版本的标签

`git tag -a 0.0.1 -m "Release 0.0.1"`

*(上述所有内容都可以轻松构建到一个脚本文件中。我仍然单独做，但将来可能会合并:)*

然后按下标签

`git push origin 0.0.1`

代码呢

`git push`

## 发布包

有不同的方法可以做到这一点。要么在本地使用 nuget CLI 将包推送到您的提要，要么让您的 CI 服务来完成这项工作。

我让 Azure DevOps 和 Azure Pipelines 来做这件事。构建过程由标准组成。NET 生成/运行测试任务。如果你对它的外观感兴趣，你可以在这里找到例子。

最后，nuget 包是基于 nuspec 文件制作的。然后这个包被推送到我们内部的 nuget feed。为了无缝地工作，Azure DevOps 中添加了到我们的 nuget 提要的服务连接。使用的包管理器是 [ProGet](https://inedo.com/proget) 。

当这个包发布的时候，我通常会通知 Slack 上的所有开发者新的包已经发布，并从那里开始任何进一步的讨论。

这是一个适合我的过程。如果你对此有任何意见或其他方法，我想听听。什么样的特性应该被提取到 nuget 包中实际上取决于你的公司以及你如何在团队之间共享代码。我更喜欢有一个一致的做事方式，我注意到如果我们可以重用其他人已经构建好的东西，我们可以在项目中节省很多时间。

如果你喜欢这个帖子，你知道该怎么做！👏