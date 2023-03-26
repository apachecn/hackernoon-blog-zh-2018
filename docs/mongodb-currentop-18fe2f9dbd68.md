# 彻底了解当前在 MongoDB 服务器上执行的操作

> 原文：<https://medium.com/hackernoon/mongodb-currentop-18fe2f9dbd68>

![](img/90bae2fa214cd096d57107a6c6410623.png)

“Dashboard in the pilot's cockpit” by [Mitchel Boot](https://unsplash.com/@valeon?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 介绍

有没有人问过—*‘为什么你的*[*MongoDB*](https://hackernoon.com/tagged/mongodb)*[*数据库*](https://hackernoon.com/tagged/database) *服务器运行缓慢？’*。如果您想知道，*“哪些操作使速度变慢了？”那么你的思考方向是正确的。这是多部分系列文章之一，[掌握 MongoDB —一天一个技巧](/p/mastering-mongodb-5544e16df023)，专为您创建，通过学习*“一天一个技巧”*来掌握 [MongoDB](https://www.mongodb.com) 。**

*在几篇系列文章中，我将给出各种提示来帮助您回答上述问题。本文讨论了*current top*命令——它的应用、用例场景，最后是用例场景，最后是一些动手实验练习。*

# *母带制作—当前顶部*

# *什么是 currentOp*

*[current top](https://docs.mongodb.com/manual/reference/command/currentOp/)是一个管理命令，它提供关于 MongoDB 服务器上正在执行的当前操作的信息。*

**current top*必须针对管理数据库运行。当授权打开时，当前用户必须在 *admin* 数据库上拥有“*in Prog”*权限才能查看所有操作，或者必须使用“*$ ownOps”*查看自己的操作。*

# *如何使用 currentOp*

*您可以使用 adminCommand 或 current top 包装函数在 mongo shell 中运行 current top，如下所示。*

*The MongoDB currentOp command via db.adminCommand*

*上述 adminCommand 的另一个包装函数*

*The MongoDB currentOp command via a db.currentOp() wrapper function.*

# *当前顶部的使用案例*

**current top*命令的用例是收集关于当前在 MongoDB 服务器上执行的操作的信息。*当前顶部*命令帮助您找到*

*   *不使用任何索引的查询*
*   *具有高数值字段的操作*
*   *等待锁定的操作*
*   *长时间运行的操作*

## *不使用任何索引的查询*

*以下示例将帮助您找到不使用索引*{ " plan summary ":" coll scan " }*的*查询*操作*

*The MongoDB currentOp command to help find queries not using any index*

## *具有高数值字段的操作*

*以下示例将帮助您在*指南*数据库中找到*num fields*大于或等于 100 的所有操作。注意正则表达式*“ns”:/^guidebook./* 还用于进一步过滤*指南*数据库的操作。*

*The MongoDB currentOp command to help you find database operations with number of yields greater than or equal to 100*

## *等待锁定的操作*

*下面的示例返回所有等待锁的写操作的信息。*

*The MongoDB currentOp command to help you find all the write operations that are waiting for a lock.*

## *长时间运行的查询*

*以下示例返回运行时间超过 300 毫秒的所有操作的信息。*

*The MongoDB currentOp command to help you find all the operations running longer than 300 milliseconds.*

# *动手实验练习*

*这个实验练习使用了来自 MongoDB 的[餐馆](https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/primer-dataset.json)样本数据集。请按照下面的说明导入*餐馆*数据，生成一些负载来帮助您学习这里讨论的概念。*

*A bash script to help you download, import and generate load on the sample restaurant database.*

# *摘要*

**db . current top()*命令提供了查找当前正在 MongoDB 服务器上运行的所有查询的方法。可以进一步利用可选字段来帮助根据特定标准筛选结果。*

> *我想重申非常重要的一点——“*db . current top()*返回关于*正在进行的操作*的信息”。*

*如果您感兴趣的查询执行得非常快(这是一件好事)，如果您稍微早一点或晚一点运行*db . current top()*命令，它甚至可能不会出现在*current top*输出中(这是最令人沮丧的)。*

*为了确保在一定时间内捕获所有操作，可以在如下所示的 *while* 循环中执行*db . current top()*命令。*

*A JavaScript function to execute currentOp in a while loop to capture operations over a period of time.*

*然而，捕获长时间的操作并整合输出以供进一步处理将变得相当具有挑战性。为了帮助您正确地捕获一段时间内的所有查询，您需要使用*概要分析*。但那是另一天的话题。*

*希望你今天在攀登“[掌握 MongoDB——一天一个技巧](/p/mastering-mongodb-5544e16df023)”之路时学到了一些新东西。*