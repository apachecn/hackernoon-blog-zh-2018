# 我如何在一周内将 Scala 的 10K 代码移植到 Kotlin？！

> 原文：<https://medium.com/hackernoon/how-i-ported-10k-lines-of-scala-to-kotlin-in-one-week-c645732d3c1>

![](img/a78aff7b8108e2f06f4bf1d28e487ac9.png)

> 上周在科特林，有几个事件——将在下面描述——引导我将 Scala 的 postgresql-async 移植到 [**jasync-sql**](https://github.com/jasync-sql/jasync-sql) 。仍然有许多缺失的部分，但是 alpha 版本是可用的，工作仍在进行中。

在这篇文章中，我想分享我是如何将代码从 Scala 转换到 Kotlin 的，以及我从中学到了什么，这样它可以帮助其他开发人员处理同样的任务。

但首先，为什么？(而之后，如何？)

# 为什么？

我转到了 Outbrain 的一个新团队，我的任务之一是协调我们的模块从 Scala 2.10 升级到 2.11。事实证明这是可能的，但是这是一个棘手的问题，因为这需要我们为多个工件“修补”我们所有的 JVM 模块，如这里所描述的。甚至我们的 Java 模块！因为它们都依赖于 [ob1k-db](https://github.com/outbrain/ob1k/) ，而 ob1k-db 又依赖于 pstgresql-async，而 pstgresql-async 又依赖于具有不同工件的 Scala 2.10/2.11。

因此，去除所有外部模块中 Scala 依赖性可能是个好主意...你知道我有多喜欢 Scala 和 Kotlin，在我之前的文章中:

[](/@OhadShai/scala-pack-your-bags-kotlin-is-coming-5169f737cfe8) [## Scala -打包你的行李；科特林来了！

### TL；DR——很多人问我为什么我认为 Kotlin 比 Scala 更好，或者相反，所以在这篇文章中我将…

medium.com](/@OhadShai/scala-pack-your-bags-kotlin-is-coming-5169f737cfe8) 

此外，上周，在一年多的不活动之后，终于有一个 commit 批准不再维护[postgres-SQL](https://github.com/mauricio/postgresql-async/commit/5716ac43818b6be0dc4fcc2b2655dde3411cdbe0)。这是最后一根稻草。

此外，我们正在使用 MySQL 异步风格的库，并且没有找到替代它的插件。

一个很大的优势是 Scala 和 Kotlin 非常相似——在特性和语法方面——所以尝试移植[代码](https://hackernoon.com/tagged/code)相对来说很有诱惑力。

# 怎么会？

在我们深入所有那些油腻的语法细节之前，休息一下，通过一次访问和一个明星为新的开源库做贡献😉：

[](https://github.com/jasync-sql/jasync-sql) [## jasync-sql/jasync-sql

### 用于 PostgreSQL 和 MySQL 的异步、基于 Netty 的 JVM 数据库驱动程序，用 Kotlin - jasync-sql/jasync-sql 编写

github.com](https://github.com/jasync-sql/jasync-sql) 

转换本身分为两个主要步骤:

*   自动逐行查找和替换脚本，以节省一些耗时的猴子打字。
*   手动检查文件并修复所有编译错误，决定如何转换并改进脚本。

## 剧本

这真的是一个简单而愚蠢的 [kscript](https://github.com/holgerbrandl/kscript) ，甚至可能是令人尴尬的愚蠢。有些行甚至没有被替换成有效的完整语句:例如，请参见模式匹配和转换。

我既没有时间也没有专业知识来使用像 [antlr](http://www.antlr.org/) 这样的东西，并编写一个解析器或一个成熟的转换器，此外，我还有一些非常定制的和特定的需求。但是我们非常欢迎你这么做。

因此，事不宜迟，下面是该脚本的简化/清理版本:

这个脚本是一个 [kscript](https://github.com/holgerbrandl/kscript) ，它有一个参数:或者是一个已经重命名的 Scala 文件`.kt`，或者是一个递归转换文件的目录。

该脚本做了一个简单的逐行查找和替换:`def`到`fun`，`trait`到`interface`等。没什么特别的。但是正如我之前提到的，语言有相似的语法是有帮助的。例如，转换为 Java 可能更复杂。

## 吸取的教训/我做出的决定

我写这篇博客的原因是提醒我自己我做了什么。一些文件仍然需要转换，其他人也有所贡献，所以这也有所帮助。

其余的只是一个没有特定顺序的条目列表，将来也可能会更新。

## 未来-> CompletableFuture

原始代码广泛使用 Scala Future，我必须找到一个替代方案。而且有很多:

*   Netty future —语法似乎冗长而过时。
*   JavaRX/Guava/ Other lib future —需要另一个外部依赖项。
*   Java 8 可完成的未来——至少必须依赖 Java 8。
*   kot Lin deferred——主要用于 couroutines，所以不太丰富，不确定 java 用户是否感到舒适。对我来说，找到如何作曲有点困难。

决定使用 CompletableFuture，因为这主要是一个后端库，这意味着我看不到在 Android 中使用反应式关系型 sql 库的理由，而且 Java 8 在 Android 之外被广泛使用。

注意 CompletableFuture 替换 Scala Future 和 Promise。

## 属国

由于这是一种驱动程序库，我试图最小化外部依赖的数量，这影响了关于使用的其他决定。

## 完成

事实证明，在 Kotlin 中，您不必重写 finalize 方法。

## 数据结构

我不记得所有，但这里是我做的转换，并记得:

*   序列->列表
*   IndexedSeq ->列表
*   ArrayBuffer ->可变列表

## 比特拨弄

Kotlin 对`byte`有点奇怪，因为它还没有包含所有的操作符。我把一些类转换成了 Java，其他的我留在了 Kotlin 中，希望我没弄错，因为我不是 100%确定 Scala 是如何处理的。欢迎就此发表意见。

## 扩展方法和属性

一开始我并不明白，但在某个时候我意识到我可以通过扩展让 Kotlin 变得非常类似于 Scala，这非常酷。

例如，科特林在列表中有`size`，而在 Scala 中是`length`。

问题？扩展。

## 尝试

我决定从 Scala+Arrow 移植/使用一个类似的类。

## 方法声明和调用中的大括号

Scala 并不强制执行，有时转换起来非常混乱和痛苦。

## 期限->期限

决定使用 java.util.Duration

## 执行上下文和隐式参数

我发现这个特性非常令人困惑，所以我将所有隐式参数都改为强制参数。它使代码更加冗长，但是更加清晰。

我使用公共池作为默认的执行上下文，尽管在 ob1k 中我们使用另一个，我们只是显式地传递它。

## 试验

最初的库使用 specs2。最初我想把它们留在 Scala 中一段时间，但是由于大量的内部代码被修改，这看起来似乎需要做很多工作。多亏了其他贡献者，转换仍然是 WIP。

## [计]选项

我主要用可空类型替换了它，用一些扩展帮助器:[https://github . com/ja sync-SQL/ja sync-SQL/blob/master/d b-async-common/src/main/Java/com/github/ja sync/SQL/db/util/nullable utils . kt](https://github.com/jasync-sql/jasync-sql/blob/master/db-async-common/src/main/java/com/github/jasync/sql/db/util/NullableUtils.kt)

在这里，我认为 Kotlin 方式是更好的方法，因为在 Scala 中有时会使用`Option`，但有时也会直接使用 null。

也有可能用 java `Optional`代替。

## 版本-> kotlinsversion

它有一个特定的逻辑，但是看起来很标准，所以我找到了`KotlinVersion`类来匹配它。

## 隐式转换

万恶之源(加上过早优化)。在我们的例子中，用扩展方法和 Java 静态方法替换用法非常容易。这个例子可以在第 25 行中看到[，我们通过第 25 行](https://github.com/mauricio/postgresql-async/blob/master/mysql-async/src/main/scala/com/github/mauricio/async/db/mysql/binary/decoder/BigDecimalDecoder.scala)中定义的[方法隐式地将 ByteBuf 转换为 ChannelWrapper。在 Kotlin 中，我在 ByteBuf 上使用了扩展方法，比如这里的](https://github.com/mauricio/postgresql-async/blob/master/db-async-common/src/main/scala/com/github/mauricio/async/db/util/ChannelWrapper.scala)，并用静态方法制作了 ChannelWrapper。

## 特征->接口+通过类委托

原来特征只是多重遗传的替代物，因为它们可以有状态。我设法用[类委托(第 55 行)](https://github.com/jasync-sql/jasync-sql/blob/master/mysql-async/src/main/java/com/github/jasync/sql/db/mysql/MySQLConnection.kt)替换了它。缺点是实现需要抛出异常的方法，如果没有被重写，这些方法在运行时会失败。[见此处第 51 行](https://github.com/jasync-sql/jasync-sql/blob/master/db-async-common/src/main/java/com/github/jasync/sql/db/pool/TimeoutScheduler.kt)。

## 就是这样。感谢阅读。

一如既往，欢迎评论！

> 标题图片由[安朵斯瓦斯](https://unsplash.com/photos/EeCfOPSeRik?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)从 [Unsplash](https://unsplash.com/search/photos/driver?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)