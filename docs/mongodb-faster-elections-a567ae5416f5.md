# 掌握 MongoDB —滚动维护期间更快的选择

> 原文：<https://medium.com/hackernoon/mongodb-faster-elections-a567ae5416f5>

在 [MongoDB](https://hackernoon.com/tagged/mongodb) [副本集](https://docs.mongodb.com/manual/replication/)中的维护/升级通常以滚动方式执行。滚动维护/升级流程要求您一次对一个 [*次级*](https://docs.mongodb.com/manual/core/replica-set-members/#secondaries) 进行维护，最后由 [*初级*](https://docs.mongodb.com/manual/core/replica-set-members/#primary) 成员进行维护。

> *当你*退出*初选时，所有符合条件的二级选举将举行新的初选。在选出新的主数据库之前，* [*数据库*](https://hackernoon.com/tagged/database) *不可用于写入。因此，“在执行滚动维护/升级时，您将如何快速选择新的主服务器？”*

![](img/bec3888bae16dc43dfb2591b39cf7923.png)

Photo by [annie bolin](https://unsplash.com/@anniebolin?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

这是多部分系列中的许多文章之一，[掌握 MongoDB —一天一个技巧](/p/mastering-mongodb-5544e16df023)，专为您创建，通过学习*‘一天一个技巧’*来掌握 [MongoDB](https://www.mongodb.com) 。在几篇系列文章中，我将给出各种提示来帮助您回答上述问题。

本文讨论了滚动维护、没有*主*的含义、快速选举新的*主*所需的步骤以及该方法的利与弊。

# 掌握—滚动维护

MongoDB 通过副本集提供数据库的冗余和高可用性。副本集不仅可以帮助数据库从节点故障/网络分区中快速恢复，还可以让您在不影响高可用性的情况下执行维护任务。

保持高可用性并能够执行维护的关键是“滚动维护”；一次对一个次级*进行维护*

*   在*辅助服务器*上停止 MongoDB 进程/服务
*   在服务器上执行所需的维护/升级
*   在服务器上启动 MongoDB 进程/服务
*   等待服务器上的 MongoDB 赶上操作日志
*   在副本集中的其他辅助节点上重复上述操作

给定一个具有 3 个 MongoDB 服务器的副本集— mon01 *(主服务器)、* mon02 *(辅助服务器)*和 mon03 *(辅助服务器)，*滚动维护过程通常需要

*   在*辅助*服务器`mon03`上执行维护
*   在另一个*辅助*服务器`mon02`上执行维护
*   *降级*主*服务器*，`mon01`
*   等待新的*初选*选出，比如说`mon02`
*   对原*主*服务器`mon01`进行维护

有关滚动升级的更多详细信息，请阅读 Bryan Reinero 的[滚动升级终极指南](https://www.mongodb.com/blog/post/your-ultimate-guide-to-rolling-upgrades)。

# 没有初选的影响

默认情况下，两种读/写操作都在主服务器上执行。您可以使用*辅助*读取首选项来读取*辅助*中的一个。然而，*主服务器*是副本集中唯一接收写操作的成员。因此，在副本集中始终有一个主副本是至关重要的。

当一个*主*不可用/不能被大多数*次*到达时，所有合格的*次*将为一个新的*主*进行选举。在选择新的主服务器之前，源自客户端驱动程序的所有写入和读取(在主服务器上)操作将等待*主服务器*可用和/或超时。因此，迅速选举出主要候选人很重要，这样等待主要候选人的操作数量就很少。

# 如何快速选出新的初选

如果主服务器意外终止和/或面临来自大多数服务器的网络连接问题，辅助服务器可以在错过心跳 10 秒后请求选举。所以需要一些时间。

## 逐步退出初选

关闭主服务器会加快故障切换过程。因此建议*降低*主节点来强制触发选举，而不是*关闭*主节点*主节点*并让*从节点*发现无法到达的主节点。我敢打赌，你们大多数人已经在使用这种方法了。所以，让我们回顾一下初选前你可以利用的其他一些技巧。

## 仅使一个辅助节点可被选择

如果其中一个辅助节点上的复制延迟较低，那么您可以主动选择它作为下一次选举的唯一辅助节点。通常，您会选择一个具有以下特征的辅助节点

*   低复制延迟
*   低网络延迟
*   与当前主服务器相似的优先级
*   或者具有次高优先级成员

假设您想要将一个*辅助*服务器`mon02`固定为下一个*主服务器*，那么您可以通过在其上运行`rs.freeze(60)`来使*辅助*服务器`mon03`在 60 秒内没有资格成为*主服务器*。这将使选举更快，因为当你让服务器`mon01`下台*时，辅助服务器*`mon02`是唯一可当选的*主服务器*。

## 降低设置。electionTimeoutMillis

检测副本集的主副本何时不可访问的默认时间限制是 10 秒。通过将`settings.electionTimeoutMillis`减少到比如说 2 秒，你可以更快地进行检测和选举。

# 加快选举的步骤总结

我总结了以下步骤，以便在维护期间更快地进行选择。请在生产环境中运行它们之前进行测试。

*   确定您希望它成为下一个主服务器的服务器
*   在所有其他辅助设备上执行`rs.freeze(60)`
*   在副本集配置上设置`settings.electionTimeoutMillis=2000`
*   在当前主节点上执行`rs.stepDown()`
*   等待新的初选被选出
*   重置新主设备上的`settings.electionTimeoutMillis=10000`

# 该方法的利弊

假设以上所有的建议对你都很有效。你可能想知道-

> “如果降低选举时间有助于加快选举，那么我为什么不能一直保持低选举时间呢？”

很棒的问题！在滚动维护期间，您的应用程序可能会面临流量减少。最重要的是，您正在密切监视所有服务器，并手动将一个*辅助服务器*锁定为下一个*主服务器*。因此，在那个时刻，您可以使用较低的 electionTimeoutMillis 值。

*但是，将 electionTimeoutMillis 设置为较低的值不仅会导致更快的故障转移，还会对主节点或网络速度慢或不稳定的敏感度增加产生负面影响。*

当出现短暂的网络连接问题时，这可能会导致过多的选举。相反，将 electionTimeoutMillis 设置为较大的值会使您的副本集更能适应短暂的网络中断，但也会导致平均故障转移时间变慢。

底线是 YMMV 您需要测试各种 electionTimeoutMillis 值，并选择更适合您的值。或者保留默认值 10 秒。

> *无论您做什么，“永远不要将 electionTimeoutMillis 设置为小于您的两个成员之间的往返网络延迟时间的值。”*

# 动手实验练习

本实验练习帮助您了解在滚动维护期间快速选择新主节点所需的步骤。

## 设置环境

首先，你需要一个玩耍的环境。我已经在 AWS 中创建了 3 个 RHEL 7.5 版实例，你也可以在本地主机上使用服务器的`/etc/hosts`条目来运行它们。如果您已经有一个 MongoDB v3.6 副本集环境，您可以跳过这一步。

下载并解压 MongoDB v3.6 二进制文件，启动 MongoDB 服务器监听绑定端口 27000 上的所有 IP。

A bash script with download MongoDB v3.6.5 and start mongod on port 27000

## 启动副本集

使用服务器`mon01`上的上述主机启动 MongoDB 副本集

A bash script to initiate a replica set with 3 hosts we created earlier

## 显示副本集配置和状态

请注意分别来自`rs.config()`和`rs.status()`的输出。它们帮助您确定当前的`settings.electionTimeoutMillis: 10000`，并根据`priority`、`optime`、`lastHeartbeat`和`pingMs`中的值选择一个*次级*作为下一个*初级*。

A JavaScript method to show the replica set configuration settings

A JavaScript method to show the replica set status information

## 选择潜在的下一个初选

`rs.status()`和`db.printSlaveReplication()`命令显示，两个*次级*、`mon02`和`mon03`都在*主*、`mon01`的操作日志条目上。然而，`pingMs`显示`mon02`比`mon03`更靠近`mon01`。因此，您可以选择`mon02`作为下一个潜在的初选，同时退出当前初选。

A JavaScript function to show the database printSlaveReplicationInfo command output

## 冻结其他次级

基于上述`pingMs`，我们不希望服务器`mon03`被选为*主服务器*。所以，运行下面的命令来阻止它在下一届选举中竞争。

A JavaScript method invoking rs.freeze to make the replica set member ineligible to become primary

## 设置 electionTimeoutMillis 并逐步退出初选

在当前主服务器`mon01`上重新配置副本集设置的 electionTimeoutMillis。最后执行命令`rs.stepDown()`强制触发选举，选举`mon02`为下一届*初选*。

A JavaScript code to set the electionTimeoutMillis to 2 seconds and stepDown the primary

您可能会注意到，新的主服务器在大约 2 秒内可用，而默认情况下为 10-12 秒。各台机器上的以下 *mongod.log* 文件显示`mon02`到主服务器的转换在大约 2 秒内完成。

A bash script to show the transition of mon02 from Secondary to Primary

## 在新的主服务器上重置 electionTimeoutMillis

一旦选择了新的主节点，请将 electionTimeoutMillis 恢复为默认值，以避免在短暂的网络连接问题期间频繁进行选举。

A JavaScript code to reset the electionTimeoutMillis back to its default value

# 摘要

我想提醒重要的一点——

> *尽管 MongoDB 数据库应用程序在选举期间高度可用于从辅助节点读取数据，但在选举主节点之前，数据库不可用于写入数据。因此，务必确保主映像尽早可用，以满足您的写入 SLA 要求。*

根据这里讨论的技巧，你可以在 3 秒钟内选出新的初选。如果您的应用程序每秒处理大约 10，000 次操作，那么您将有大约 30，000 次操作在新的主服务器上等待。现在，您可能想知道—“*当所有 30，000 个操作同时冲击新的主服务器时，我可以采取什么措施来确保数据库服务器不会瘫痪*？”

同样，这是一个很好的问题，但这是另一天的主题。希望你今天在通往“[掌握 MongoDB——一天一个技巧](/p/mastering-mongodb-5544e16df023)”的道路上学到了一些新东西。

# 以前的文章

*   掌握 MongoDB — [一天一个技巧系列](https://hackernoon.com/mastering-mongodb-one-tip-a-day-series-5544e16df023)
    专为掌握 MongoDB 而打造的系列文章
*   提示# 003: [事务](/@shyam.arjarapu/mongodb-transactions-5654cdb8fd24)
    许多人期待已久、最想拥有的功能终于到来了
*   技巧# 002: [createRole](/@shyam.arjarapu/mongodb-createrole-e1ca1346d3bb)
    如何防止有人掉你的收藏？
*   技巧# 001:[current top](/p/mongodb-currentop-18fe2f9dbd68)
    彻底了解 MongoDB 服务器上当前正在执行的操作