# AWS Secrets Manager vs . hashi corp Vault vs . AWS 参数存储

> 原文：<https://medium.com/hackernoon/aws-secrets-manager-vs-hashicorp-vault-vs-aws-parameter-store-bcbf60b0c0d1>

![](img/4550f3b62be2ce597a7f77d6d9b7daa9.png)

Photo Credit: [Ewere Diagboya](https://medium.com/u/66218173572e?source=post_page-----bcbf60b0c0d1--------------------------------)

随着你的 DevOps 练习越来越快，似乎有一个小障碍。在系统安全、管理和更新方面，这个障碍会让你损失惨重。应用参数/配置；这些人实际上决定了我们的应用程序如何运行。它跨越了数据库连接、RabbitMQ 的登录凭证和其他秘密凭证。作为一种安全实践，当将代码提交到 VCS(版本控制系统)时，您不希望这些凭据在您的代码库中可见。保护这些凭证的方法之一是使用环境变量，这些变量可以在特定的环境中注入，您需要这些机密和配置，而不是它们的值。还需要有一个地方来存储这些数据，以便安全保存，如果可能的话，在存储这些数据的地方进行加密。

我们将看到 DevOps 工程师使用的这三种秘密存储解决方案。这些方法各有利弊，我们将根据以下标准进行分析:

a.成本
b .安全性
c. API/SDK 集成
d .特性

# 费用

**AWS Secrets Manager** 这是一项由 AWS 提供的托管服务，根据 AWS 的定价，这项服务的费用为每个秘密每月 0.40 美元，每 10，000 次 API 调用 0.05 美元。就上下文而言，如果你存储 100 个秘密(密码、API 密钥等)，你每月支付 40 美元，如果你在一个月内通过 40，000 次 API 调用来请求秘密的值，你支付 0.2 美元。[更多在此](https://aws.amazon.com/secrets-manager/pricing/)

**Hashipcorp 的金库** 这是一个开源工具。这意味着您负责设置和扩展服务。您将需要在虚拟机上设置它，并且您使用的虚拟机将确定更新、保护将在其上安装和配置 Vault 的服务器所涉及的成本和其他运营成本。

**AWS 参数存储** 仍然是 AWS 上的托管服务，但这一次它是在一个名为系统管理器的特性下。基本不用花钱。你刚才看到了吗？是的，我的意思是它不花钱。[看这里](https://aws.amazon.com/systems-manager/pricing/)

# 安全性

**AWS Secrets Manager** Secrets Manager 是一项全新的服务，完全由 AWS 管理，存储在其上的凭证的安全性与您的 AWS 帐户上的 IAM 访问权限密切相关。你也可以将 Secrets Manager 与 AWS KMS 整合在一起。这有助于加密存储的数据。Secrets Manager 还附带了一个秘密轮换功能，允许您自动轮换 API 密钥、密码等。这可以用 Lambda 函数来配置和连接，以帮助旋转。

**Hashipcorp 的 Vault** 与 Vault 应用程序的安全性有关的一切都是用户的责任。Vault 将密码存储在安装它的计算机中，并对数据进行加密。它支持各种后端存储；文件系统，AWS S3，Azure，谷歌云存储，MongoDB。[更多此处](https://www.vaultproject.io/docs/configuration/storage/index.html)
秘密旋转功能此处([https://www.vaultproject.io/docs/internals/rotation.html](https://www.vaultproject.io/docs/internals/rotation.html))

**AWS 参数存储** 就像 Secrets Manager 一样，安全性与您在 AWS 中的 IAM 帐户相关联。所有请求都是通过 API 或 CLI 发出的。两者的密钥都是从控制台生成并使用的。没有任何类型的秘密轮换功能，除非您想要自定义一个。

# API/SDK 集成

这里它们都有 API 和 SDK 来检索存储的键。所以这个等级他们都通过了

# 一般特征

**AWS 机密管理器** -AWS 上机密的安全存储
-允许通过 KMS 对存储的密钥进行加密
-可以在特定时间段内配置密钥轮换
-特权访问管理(IAM)

**哈希公司的保险库** -在文件系统或数据库中存储机密
-加密即服务
-特权访问管理

**AWS 参数存储** -**AWS 上秘密的安全存储
-允许加密通过 KMS
存储的密钥-特权访问管理(IAM)**

**基于这些特征。您可以选择用来存储和检索您的秘密。但是有一个保险库来存储密码、API 密钥和连接字符串是至关重要的。**