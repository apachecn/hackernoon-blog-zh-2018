# 掌握 MongoDB——啊啊啊！有人刚刚丢了一个收藏

> 原文：<https://medium.com/hackernoon/mongodb-createrole-e1ca1346d3bb>

你有没有遇到过这样的情况:*“有人在生产过程中不小心掉了整个系列？”*除非你能从备份中恢复数据，否则你的处境很糟糕。

![](img/89c3c366e9870955c6d07c1fbe06446c.png)

“A man stretching out his two hands with careless written on them in black and white” by [Mitchel Lensink](https://unsplash.com/@lensinkmitchel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

我遇到过几个曾经面临这种情况的客户。因此，首先采取安全措施来防止这种情况发生是很重要的。您可以通过使用用户定义的角色轻松实现这一点。

这是多部分系列文章之一，[掌握 MongoDB -一天一个技巧](/p/mastering-mongodb-5544e16df023)，专为您创建，通过学习*‘一天一个技巧’*来掌握 [MongoDB](https://www.mongodb.com) 。在几篇系列文章中，我将给出各种各样的技巧来加强 [MongoDB](https://hackernoon.com/tagged/mongodb) 上的安全性。在本文中，我将讨论用户定义的角色如何帮助防止有人意外删除集合。

# 掌握用户定义的角色

# 什么是角色

MongoDB 使用基于角色的访问控制(RBAC)来管理对 MongoDB 系统的访问。一个[角色](https://docs.mongodb.com/manual/tutorial/manage-users-and-roles/#create-a-user-defined-role)授予用户访问 MongoDB 资源的权限。在角色分配之外，用户无权访问系统。

这里有一些我想让你知道的概念

*   用户被授予一个或多个角色。
*   角色授予对资源执行多组操作的权限。
*   权限由资源和对资源允许的操作组成。
*   资源是一个[数据库](https://hackernoon.com/tagged/database)，一个集合或一组集合。
*   动作指定资源上允许的操作。

# 为什么是用户定义的角色

MongoDB 提供了许多内置角色，管理员可以使用这些角色来控制对 MongoDB 系统的访问。每个数据库都包括以下角色

数据库用户角色

*   阅读
*   读写

数据库管理角色

*   dbAdmin
*   用户管理
*   dbOwner

但是，如果这些角色不能描述所需的权限集，您可以使用 *db.createRole()* 方法创建一个新的用户定义的角色。创建角色时，您可以指定要授予访问权限的权限集。

# 读写角色具有 dropCollection 操作

数据库管理员通常利用内置的*‘read’*和*‘read write’*角色来限制对数据的访问。下面的*‘getRole’*命令显示了具有*‘read write’*角色的用户可以执行的各种操作。

The MongoDB getRole command to show all the granted actions for readWrite role

根据本文的上下文，其中最危险的操作是*‘drop collection’*操作。如果(人类)用户确实需要拥有读&写权限，建议拥有一个用户定义的角色，该角色拥有除*‘drop collection’*动作之外的所有动作，来自*‘read write’*角色。通过将此用户定义的角色分配给这些用户，管理员可以防止有人意外删除集合。

# 动手实验练习

本实验练习帮助您创建一个用户定义的角色，并说明与具有 *readWrite* 角色的用户相比， *readWriteMinusDropRole* 如何防止有人意外删除集合。

## 设置环境

首先，你需要一个玩耍的环境。我推荐使用 *mlaunch* ，一个来自 [*mtools*](https://github.com/rueckstiess/mtools) 的实用工具，在您的本地机器上设置一个测试环境。如果您已经有一个启用了身份验证的环境，您可以跳过这一步。

A bash script to setup a MongoDB environment with authentication on your local machine

## 创建具有读写角色的 app_user

使用上述凭证登录测试环境，并在*社交*数据库上创建具有*读写*角色的 *app_user* 。

A bash script to create a user with readWrite role on the social database

## 具有读写角色的用户可以删除集合

使用 *app_user* 凭证登录到测试环境，并在 *social* 数据库上创建一个示例 *person* 文档。由于 *app_user* 具有 *readWrite* 角色，该角色授予对 *dropCollection* 动作的访问权限，因此命令 *db.person.drop()* 执行成功，集合被丢弃。

Set of bash & JavaScript to show that user with readWrite role can drop a collection

## 创建用户定义的角色

使用*用户*凭证登录到测试环境，并在*社交*数据库上创建以下内容。

*   一个用户定义的角色，*readwriteminusdroplerole*
*   具有*角色*的用户 *human_user*

请注意，在授予*readwriteminusdroplerole*的动作集中，没有 *dropCollection* 动作。

A bash script with MongoDB commands to create a user-defined role and a user.

## 具有 readWriteMinusDropRole 角色的用户无法删除集合

A user with user-defined role, readWriteMinusDropRole, cannot drop a collection

# 摘要

虽然 MongoDB 提供了各种内置角色来提供数据库系统中通常需要的不同访问级别，但是您需要用户定义的角色来授予对 MongoDB 资源的细粒度操作。

> 不要等到你后悔没有早点做。使用用户定义的角色来加强您的安全措施，并防止有人意外丢失集合。

以下是我的客户为加强安全而采取的一些措施。

*   开发人员无法访问生产环境(更极端)
*   如果需要访问，将*【读取】*角色交给开发人员(非常需要)
*   创建一个用户定义的角色，具有所有*‘读写’*动作，但不具有*‘删除集合’*
*   如果任何用户需要*的【读】写*权限，则分配上述用户定义的角色(强烈推荐)
*   为您的应用程序创建一个单独的 *app_user* ，并授予*‘read write’*权限，以便与 MongoDB 交互

使用 MongoDB v3.6，您可以通过定义允许用户使用[*authenticationRestrictions*](https://docs.mongodb.com/manual/reference/method/db.createUser/#authentication-restrictions)进行身份验证的 IP 地址范围来进一步加强安全性。但那是另一天的话题。希望你今天在“掌握 MongoDB -一天一个技巧”的道路上学到了一些新东西。