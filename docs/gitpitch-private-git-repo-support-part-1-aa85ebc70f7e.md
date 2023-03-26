# GitPitch 私有 Git 回购支持—第 1 部分

> 原文：<https://medium.com/hackernoon/gitpitch-private-git-repo-support-part-1-aa85ebc70f7e>

> 使用私有 Git repos 发布公共演示文稿。

当 GitPitch 推出时，它为 GitHub、GitLab 和 Bitbucket 上的每个人引入了一个简单的新约定。如果您将`PITCHME.md` markdown 文件添加到任何**公共 Git 存储库**中，GitPitch 会自动将该 markdown 文件的内容转换为完全响应的幻灯片演示。

就像`README.md`一样，`PITCHME.md`大会正迅速变得非常棘手。

![](img/4d539d1469b211375347e7e6cf8262e3.png)

这种从 markdown 到在线、离线和 PDF 幻灯片演示的转换不需要注册。而且没有配置。[刚刚好](https://github.com/gitpitch/in-60-seconds) :-)

# 私有 Git 存储库支持

[GitPitch Pro](https://gitpitch.com/pro-features) 通过支持在**私有 Git 存储库中找到的`PITCHME.md` markdown 文件的转换，扩展了这个新的约定。**

> 你现在就可以开始使用 GitPitch 私人回购支持，登录[这里](https://gitpitch.com/login)。

然而，根据设计，私有存储库中的`PITCHME.md`不会被 GitPitch 自动转换。

作为使用私有 Git repos 的演示文稿作者，**你必须明确授权你的观众**。此步骤是一项重要的保护措施，因为它有助于您:

1.  清楚地定义你演讲的目标受众。
2.  防止无意中泄露您的演示内容。

# 授予公众访问权限

那么，你如何在自己的私人回购中与公众分享幻灯片演示呢？同时保持所有其他回购内容的 100%隐私？

![](img/dea7e4e8cda6aebdce36821f697a4486.png)

简单。激活 GitPitch Pro [**发布模式**](https://gitpitch.com/pro-guide) **。**

激活非常简单，只需为私有 Git 存储库中的任何幻灯片演示向`PITCHME.yaml`添加一个`published`属性:

```
published : true
```

发布模式是公开宣传、推销或展示新闻、技术、产品、服务甚至任何项目培训的理想模式**，而不会泄露您私人存储库中的代码、配置或数据**。

作为演示文稿作者，您可以通过随时启用或禁用发布模式来管理对演示文稿的访问。

下面的简短演示是一个使用私有 GitHub 库发布的公开演示的例子:

[ Sample GitPitch public presentation published using a private GitHub repo ]

这个演示对应的 Github 存储库是这里的。如果你点击这个链接，你会发现这是一个私有的 Github 库，所以你没有权限查看它。

对于 GitPitch 用户来说，使用私有 Git repos 的公开演示是一个很棒的新特性。但是有时候公开你的演示内容可能并不符合你的需要。在那些情况下，你可能要考虑使用 GitPitch Pro [**隐身模式**](https://gitpitch.com/pro-guide) 。

隐形模式将是本系列下一篇文章的主题。

# 从创意到展示的最快方式

有了对私有 Git 存储库的支持，您现在可以保护您的演示内容并管理您的观众。毫不费力。符合 GitPitch 核心设计理念的方法— **保持简单**。

> 您现在就可以开始使用私人回购支持，登录[此处](https://gitpitch.com/login)。

记住，GitPitch 可以让你制作和分享关于你关心的事物的美丽内容。**用它来推销、推销或呈现绝对的任何东西；)**

[![](img/522b2e4ace3cfcecd43bba30fcf0a317.png)](https://twitter.com/gitpitch)

**您可以在**[**Medium**](/@gitpitch)**或**[**Twitter**](https://twitter.com/gitpitch)**上关注我，了解 GitPitch 社区的更多新闻、技巧和独特创意。**