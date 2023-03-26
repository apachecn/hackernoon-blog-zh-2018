# GitPitch 私有 Git 回购支持—第 2 部分

> 原文：<https://medium.com/hackernoon/gitpitch-private-git-repo-support-part-2-e6aea33565d7>

> 使用私有 Git repos 共享 GitPitch 私有 URL。

这个由三部分组成的系列文章中的第一篇文章介绍了 [GitPitch Pro](https://gitpitch.com/pro-features) 对使用私有 Git 存储库与公众分享演示文稿的支持。

本文将解释如何使用私有 Git 存储库创建和管理私有表示 URL。然后，这些 URL 可以与私人受众共享。

> 您现在就可以开始使用私人回购支持，登录[此处](http://https//gitpitch.com/login)。

# 向私人受众授予访问权限

出于[介绍性文章](https://hackernoon.com/gitpitch-private-git-repo-support-part-1-aa85ebc70f7e)中概述的所有原因，演示文稿作者**必须明确授权**访问任何使用私有 Git 存储库交付的 GitPitch 演示文稿。

![](img/b4667014224a7cd41a71ac857026cb19.png)

当演示文稿作者没有明确授权访问私有 Git repo 中的演示文稿时，**任何查看演示文稿内容的尝试都将被拒绝**。

那么，如何使用私有的 Git 存储库创建和共享一个到幻灯片演示的私有链接呢？

简单。激活 GitPitch Pro [**隐身模式**](https://gitpitch.com/pro-guide) **。**

# 激活隐形模式

激活非常简单，只需为私有 Git 存储库中的任何幻灯片演示向`PITCHME.yaml`添加一个`stealth`属性。

`stealth`属性接受一个由一个或多个*隐藏标记*组成的逗号分隔列表。令牌可以是任意字符串值，例如:

```
stealth : 01234, AcmeCorp, sneakpeek
```

激活隐藏模式后，需要一个与原始演示 URL 相结合的有效令牌才能访问幻灯片内容。

使用`?s=`查询参数在表示 URL 上指定令牌。**表示 URL 加上有效令牌查询参数的组合是一个私有 URL** 。

例如，任何打开以下示例演示文稿 URL 的尝试都将被拒绝，因为该演示文稿尚未发布供公众访问:

```
https://gitpitch.com/gitpitch/pro/stealth
```

但是，如果您尝试使用以下私有 URL 打开同一演示样本，您将能够观看幻灯片:

```
https://gitpitch.com/gitpitch/pro/stealth?s=sneakpeek
```

下面的简短演示是使用 GitPitch 私有 URL 从私有 GitHub 存储库交付的 GitPitch 演示示例:

隐藏令牌管理就像在 PITCHME.yaml 文件中的`stealth`属性上添加和删除令牌一样简单。以这种方式管理令牌为您提供了一种快速简便的方法来管理您的受众。

# 1.演示文稿作者的隐藏模式

隐形模式的一个重要用例是允许演示文稿作者**预览他们自己的作品**，而不会有将演示文稿内容暴露给外界的风险。

作为演示文稿作者，您只需在您的演示文稿的`PITCHME.yaml`中注册您自己的`stealth`令牌。然后在一个私有 URL 上使用这个令牌来预览您自己的幻灯片内容。

# 2.演示反馈的隐藏模式

隐藏模式的另一个重要用例是让演示文稿作者能够快速**与其他人(如内容合作者、同事、客户等)共享其演示文稿内容的预览**。

通过与协作者共享有效的私有 URL，您可以管理临时访问者。任何时候，您都可以简单地撤销私有 URL 上使用的`stealth`令牌，再次限制对您内容的访问。

# 3.私人观众的秘密模式

如果您与可信的受众共享私人 URL，您甚至可以使用隐藏模式作为最终的演示交付机制。

在这种情况下，可信任的观众是任何您可以信赖的观众，他们不会将您的演示文稿的私有 URL 与第三方共享。

但是，如果您的演示文稿的私有 URL 可能会被比预期更广泛的受众共享，例如通过社交媒体或公司电子邮件，那么您可能需要考虑使用 GitPitch Pro [**机密模式**](https://gitpitch.com/pro-guide) 。

机密模式将是本系列下一篇文章的主题。

# 从创意到展示的最快方式

随着 GitPitch Pro 中私有 Git 存储库支持的到来，您现在可以轻松保护您的演示内容并管理您的观众。对于任何提供私人、机密甚至付费内容的人来说，这都是一个巨大的胜利。

> 您现在就可以开始使用私人回购支持，登录[此处](http://https//gitpitch.com/login)。

记住，GitPitch 可以让你制作和分享关于你关心的事物的美丽内容。**用它来推销、推销或呈现绝对的任何东西；)**

[![](img/522b2e4ace3cfcecd43bba30fcf0a317.png)](https://twitter.com/gitpitch)

**您可以在** [**Medium**](/@gitpitch) **或**[**Twitter**](https://twitter.com/gitpitch)**上关注我，了解 GitPitch 社区的更多新闻、技巧和独特创意。**