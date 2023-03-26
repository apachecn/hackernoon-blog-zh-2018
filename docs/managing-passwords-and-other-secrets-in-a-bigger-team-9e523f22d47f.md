# 在更大的团队中管理密码和其他秘密

> 原文：<https://medium.com/hackernoon/managing-passwords-and-other-secrets-in-a-bigger-team-9e523f22d47f>

![](img/4832a952cbda50107bc93327df5c4522.png)

几年前，我在帮助一家公司整理它的秘密。秘密存储在 Google Docs、Chef、git 中，存储在 git 中的 OSX 图像中，电子邮件中，手动放置在你附近某处 Jenkins 实例中的文件中。这有助于将您的解决方案按照常见用例进行划分，而不是使用一种工具来处理所有事情，因此，这里有 4 件事情您的组织可能需要一种安全可靠的方式来完成(因为它们现在很可能还没有完成)。声明:我与下面提到的这些公司没有任何关系。

1.  能够在内部共享凭据，包括非技术用户。使用类似于 [LastPass Enterprise](https://www.lastpass.com/enterprise-password-management) 、[1 pass Teams](https://1password.com/teams/)的工具来共享诸如管理员登录和计费凭证之类的东西。这些服务还为大多数浏览器提供了一个扩展，可以在你所有的网站上自动填充你的密码，你仍然可以与群组共享这些密码。
    数据库证书之类的东西不太适合这里，它们更靠近需要读取它们的机器，在那里你的工程师可以与它们互动。
2.  有办法安全地存储和共享敏感文件，包括非技术用户。LastPass 可以让你保存文件，非常适合包含 SSL 证书的 zip 之类的东西。它不太适合发送像敏感的 pdf 这样的东西。
    Google Drive/Dropbox 在一定程度上可以工作，但缺乏良好的审计功能来了解哪些文件与哪些用户共享。[Egnyte](https://www.egnyte.com/)&[Accel lion](https://www.accellion.com/platform/simple/secure-file-sharing/)在您真正需要之前，为这种特殊类型的用例提供付费服务(您可能有合规/监管要求)。
3.  有一种安全的方式将秘密发送给其他人(而不是一组人)。你的一些用户可能会发送 slack，然后删除消息——这是一个糟糕的解决方案——但我也对此感到内疚——这太容易了。onetimesecret.com 是一个有用的网站，让你使用一次性使用链接发送秘密(他们可以有一个密码，也过期)。 [Vault 可以做“响应包装”](https://www.vaultproject.io/docs/concepts/response-wrapping.html)让你共享一个秘密，而你永远看不到这个秘密——不太用户友好，但对开发人员更友好。
4.  有办法将面向机器的凭证存储在计算机之外的某个地方。如果你在一个更大的团队的云环境中工作，使用像 [Vault](https://www.vaultproject.io/) 或 [AWS 参数存储库](/@tdi/ssm-parameter-store-for-keeping-secrets-in-a-structured-way-53a25d48166a)这样的工具会变得非常简单。你也可以使用 [git-crypt](https://www.agwa.name/projects/git-crypt/) 或者[AWS KMS 在本地加密秘密](https://github.com/adieuadieu/aws-kms-thingy)将它们存储在 git 中。如果你使用这些配置管理系统中的一个，你总是可以使用来自 Chef 、 [Ansible Vault](https://serversforhackers.com/c/how-ansible-vault-works) 或 [Puppet hiera-eyaml](https://puppet.com/blog/using-node-side-secrets-puppet) 的[加密数据包。](https://docs.chef.io/knife_data_bag.html)

最后一件事是找出一个合适的授权方案，以便知道谁可以访问什么。这在很大程度上取决于你如何定义“谁”，不同类型的“访问”和“什么”是人们可以访问的所有事物的排列。

在 AWS 上进行开发运维时，很难找出最佳实践是什么。我也想帮你弄清楚。[注册我的邮件列表](https://intricatecloud.us18.list-manage.com/subscribe/post?u=98290aa07ec2096a7f6189138&id=e161e3fdeb)获取在 AWS 上构建应用程序的技巧和经验。

*原载于 2018 年 10 月 17 日*[*www . intra cide cloud . io*](https://www.intricatecloud.io/2018/10/managing-passwords-and-other-secrets-in-a-bigger-team/)*。*