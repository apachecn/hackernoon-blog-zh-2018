# 掌握 MongoDB —在 v4.0 中引入多文档事务

> 原文：<https://medium.com/hackernoon/mongodb-transactions-5654cdb8fd24>

![](img/d2d173628c40eae17afe48cb8bdfc9ac.png)

Photo by [Madison Grooms](https://unsplash.com/@zeldygirl?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

> 对 MongoDB 中事务的支持是许多人长久以来的愿望。有了 MongoDB v4.0，等待就结束了。欢迎使用副本集的多文档事务。

在一个 [MongoDB](https://hackernoon.com/tagged/mongodb) [数据库](https://hackernoon.com/tagged/database)应用程序中，当两个实体之间有父子关系时，比如 *orders & order details* ，通常会将子文档( *order details* )嵌入到父文档( *order* )中。这种模式设计方式不仅有助于更快的读取，还有助于满足原子性。对单个文档的写操作是原子性的。然而，*‘如何在处理多个文档时保持原子性？’*

这是多部分系列文章之一，[掌握 MongoDB —一天一个技巧](/p/mastering-mongodb-5544e16df023)，专为您创建，通过学习*“一天一个技巧”*来掌握 [MongoDB](https://www.mongodb.com) 。在几篇系列文章中，我将给出各种提示来帮助您回答上述问题。本文讨论了*多文档事务*，这是 MongoDB v4.0 中的一个新特性——它的应用程序、用例场景，最后还有一些动手实验练习。

# 母版制作—多文档事务

# 什么是交易

在 MongoDB 中，对单个文档的写操作是原子的，即使该操作修改了单个文档中的多个嵌入式文档。当像 [updateMany](https://docs.mongodb.com/manual/reference/method/db.collection.updateMany/) 这样的单个写操作修改多个文档时，每个文档的修改是原子的，但是操作整体上不是原子的。一些使用案例可能还要求您将多个写操作作为单个操作的一部分进行修改。在这种情况下，您确实需要*事务*来在多个写操作之间实施原子性。

# 为什么使用事务

在 MongoDB v4.0 之前，您可以模拟*事务*的唯一方式是在您的应用程序中实现[两阶段提交](https://docs.mongodb.com/manual/tutorial/perform-two-phase-commits/)。 [Emmanuel Olaojo](https://medium.com/u/6ee5301c7935?source=post_page-----5654cdb8fd24--------------------------------) 写了一篇文章[“Fawn:*transactions*in MongoDB "](https://codeburst.io/fawn-transactions-in-mongodb-988d8646e564)”，介绍了使用 [Fawn](https://www.npmjs.com/package/fawn) 模块对 [Node.js](https://nodejs.org/) 应用程序进行两阶段提交。请注意，两阶段提交只能提供类似事务的语义。使用两阶段提交时，应用程序可能会在两阶段提交或回滚期间返回中间数据。

MongoDB v4.0 为副本集引入了*多文档事务*，并且可以跨多个操作、集合和文档使用。多文档*事务*提供了一个全局一致的数据视图，并强制执行全有或全无以维护数据完整性。*交易*将具有以下属性

*   提交事务时，将保存事务中所做的所有数据更改。
*   如果事务中的任何操作失败，事务将中止
*   当事务中止/中止时，事务中所做的所有数据更改都将被丢弃。
*   在提交事务之前，事务中的写操作在事务之外是不可见的。

# 动手实验练习

这个实验练习帮助您理解如何在 MongoDB shell 中使用*事务*。由于多文档*事务*仅适用于副本集，请确保您至少有一个成员副本集，而不是独立的 *mongod* 。

在我们开始之前，我希望你注意几点

*   您只能在现有集合上指定读/写(CRUD)操作。
*   多文档事务不能包含会导致创建新集合的插入操作。
*   事务与会话相关联。
*   在任何给定时间，一个会话最多只能有一个打开的事务。
*   要将读写操作与打开的事务相关联，需要将会话传递给操作。

## 设置环境

首先，你需要一个玩耍的环境。如果您已经有了 MongoDB v4.0 replicaset 环境，可以跳过这一步。

A bash script with download MongoDB v4.0 release candidate and create a 1 member replica set

下一组练习将说明 commit transaction/abort transaction 是如何工作的，最重要的是，还将涵盖具有*写冲突*的多个写操作如何导致 *abortTransaction* 。为了帮助您更好地理解，我将这些命令的输出作为注释放在了它们的正下方。

## 使用 commitTransaction 保存数据更改

下面的 MongoDB shell 命令显示了一个包含一些样本数据的 *person* 集合。注意，*事务*中新添加的文档在 *db.person* 集合对象上不可见，直到 *session1* 被提交。

MongoDB commands illustrating the data changes in transaction are save when the transaction is committed.

## 用 abortTransaction 放弃数据更改

下面的 MongoDB shell 命令显示了一个包含一些样本数据的 *person* 集合。注意，*事务*中新添加的文档在 *db.person* 集合对象上不可见，直到 *session1* 被中止。

MongoDB commands illustrating the data changes in transaction are discarded when the transaction is aborted on the session.

## 可以提交没有写冲突的事务

在下面的代码中，您可能会注意到有多个写操作(*插入*、*更新*和*删除*)被从多个作用域调用，在事务内部/外部。只要没有*写冲突*，就可以成功提交这些事务。

MongoDB commands illustrating transactions with no write conflicts can successfully commit

## 具有写冲突的事务被中止

如果两个或多个写操作从不同的范围修改同一文档，则一个事务中的数据更改会影响其他事务中的数据更改。当存在这种*写冲突*时，操作会导致*transient transaction error*和 *abortTransaction* 。这些写入可以是插入/更新/删除操作的任意组合。以下示例显示了在两个不同的事务中调用的对同一文档的两个删除操作。

MongoDB commands illustrating multiple transactions with write conflicts are aborted

# 摘要

对副本集的多文档事务*的支持仅仅是个开始。未来的版本可能会跨分片部署和各种隔离级别解决*事务*，您可能会从其他关系数据库中暴露这些事务。我想提醒重要的一点——*

> “仅仅因为您现在支持事务，您就不能围绕第三范式设计数据模型。您必须始终拥有一个有效的 MongoDB 模式设计，以确保您的应用程序是高性能的。

与单个文档写入相比，多文档事务会导致更高的性能成本。那么，“使用事务的性能成本是多少？”。很棒的问题！但那是另一天的话题。

希望你今天在通往“[掌握 MongoDB——每天一个技巧](/p/mastering-mongodb-5544e16df023)”的道路上学到了一些新东西。