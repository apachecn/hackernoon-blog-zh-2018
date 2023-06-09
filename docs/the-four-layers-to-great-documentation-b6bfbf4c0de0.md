# 优秀文档的四个层次

> 原文：<https://medium.com/hackernoon/the-four-layers-to-great-documentation-b6bfbf4c0de0>

当我开始发布一些开源项目时，我深入研究了如何记录它们。这是我发现的最有用的方法。

好的文档需要四层，每一层都建立在前一层之上。

*   入门指南
*   指导
*   概念
*   应用程序接口

编写这些的最佳方式是从底层(API)开始，一路向上(入门)。顺便说一句，你的文档的读者会从顶部开始，只是偶尔会找到他们的底部。即便如此，自下而上开始写作有助于更好地通知顶层。

# 应用程序接口

> 这是你的代码中唯一可以自动生成的部分。文档是专门为人类编写的，这通常意味着人类需要编写文档。你可以从你的源代码中自动生成你的 API 文档，如果你已经注释了它的话。

这应该是如何使用项目 API 的全面文档，通常从源代码生成。

你应该记录你的代码，这样当你 12 个月后再来看它的时候，你就能再次理解它。自私一点，善待未来的自己，让他们知道你做了什么，为什么。你不会记得的。(这里的这一面好处，也是其他所有人都能理解的)。

# 概念

这个项目的基本构件是什么？试着把项目分成几个部分，并对每个部分写一个详细的解释。

这里的目标是解释项目的构建模块。希望任何将实现您的项目的人阅读这一部分。你希望这是详细和彻底的。你不需要每篇都写一篇文章，几百个单词和一个图表就足够了。

如果你需要为一个概念写一个很长的页面，善待读者，试着为便于参考而创建章节。虽然有些人会从头到尾读一遍，但更有可能的是，他们会在那个时候略读与读者最相关的部分。你只需要一些标题和目录。

# 指导

指南或教程是解释如何使用您的项目来解决特定用例的理想方式。

它们应该以最终用户为中心，考虑他们的使用案例和知识。过度解释总比不解释好。有些事情对你来说可能简单明了，但对你的用户来说却闻所未闻。

想想你当初为什么要启动这个项目，把你想解决的用例列一个清单。为每个人写一个小的用户故事可能会有帮助，比如:“作为一个项目维护者，我想知道如何编写好的文档，这样我就可以让我的项目有更多的用户”。产生用户故事的典型格式是:“作为一个[角色]，我可以[特写]以便[推理]”。

在最后一段，我做了两件事。首先，我说制作一个用户故事的列表将有助于决定写什么样的指南，但是接下来我给出了一个用户故事的例子，以及编写它们的模板。你可以通过搜索来找出什么是用户故事，但是通过在那里解释它，我不仅为你节省了一点时间——我还消除了我们正在谈论的内容的模糊性。一个用户故事对你来说可能意味着稍微不同的东西。

但是回到指南。你需要至少 3 到 5 个人来解释如何使用你的项目。可能是如何在每个特定的操作系统上使用它，或者如何与 3 个最流行的框架集成。

或者，如何将重点放在上一节中的特定概念上。

当您发布项目时，很可能会有 1-3 个指南。随着问题和常见问题的出现，将其转化为指南。

# 入门指南

这是你要写的最重要的部分，并不是由一个简单的“开始”页面组成的。

如果你的项目是一个小型的库，你可能只需要一个入门部分就可以了。通常，在这种情况下，您只有一个主要概念，以及一组作为指南部分的示例。

首先，找出你的登陆页面。你可能有一个网站，一个 GitHub readme。您可能还有一个关于 npm、godoc、pypi 或其他包管理器的页面。通常，除了网站之外，所有这些都是你的自述文件的直接副本，所以让我们从它开始吧。

# Readme.md

首先，你需要简明扼要地回答以下问题:

这个项目叫什么？这个项目是做什么的？

然后，说明如何获取。也许是通过包管理器下载的，或者是包含二进制文件的发布页面的链接。

现在，展示一个简单的用例。这里你展示的是项目的公共接口。对于许多项目来说，最好的入门就是展示实现代码。不要显示所有内容，只需运行一次即可。

我们现在在自述文件的页脚。添加关于如何在这个项目上开发的信息是很好的，所以关于如何设置开发环境的信息，以及如何运行测试的例子应该在这里。

强烈建议包括关于如何对项目做出贡献、在哪里提出问题、新功能的流程和拉动请求的信息。

如果你正在使用它，你应该包括关于[永远](https://semver.org/)的信息。(如果你没有使用 SemVer，请考虑一下——你的用户会感谢你的)。

许多项目也喜欢在页脚注明他们的主要作者和贡献者。

最后，在最底部包括一个许可证。这通常是许可证类型的注释，带有指向 LICENSE.md 文件的链接。你必须包括一个许可证，没有一个人会警惕使用你的项目。

# 徽章

你不需要它们。尽管它们可能对您的用户有用。在这里你要自己斟酌，不要走极端。他们可以提供一些社会证明。

# 网站(全球资讯网的主机站)

如果你也有一个网站(你并不总是需要)，你的“入门”包括首页和一个专门的入门页面。如果你想把它们都放在 once 页面上，只要确保有一个链接可以直接把读者带到“入门”部分。

首页通常包含与自述文件相同的信息，并且通常包装在漂亮的设计中。不要走极端，你的重点是可读性高于一切。有时候，一个很酷的图形或动画可以让一个项目被分享——但是不要以牺牲你的实际用户为代价。

文档是你的项目最好的[营销](https://hackernoon.com/tagged/marketing) [工具](https://hackernoon.com/tagged/tool)，也是可信度和成熟度的最好指标。很好地记录一个项目需要努力，但是如果你想要用户，这是值得的。

作为这种文档方法的例子，你可以看看我最近发表的一个项目 [Bunjil](https://bunjil.js.org/) 。