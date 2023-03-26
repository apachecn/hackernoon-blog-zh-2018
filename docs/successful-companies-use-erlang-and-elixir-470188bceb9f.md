# 成功的公司使用 Erlang 和 Elixir

> 原文：<https://medium.com/hackernoon/successful-companies-use-erlang-and-elixir-470188bceb9f>

Erlang 是一种可信且稳定的语言，由爱立信发明，用于在主要系统中运行核心程序。Erlang(和 Elixir)被许多领域的许多行业使用，包括金融科技、安全、区块链和物联网。

公司选择 Erlang 和 Elixir，是因为它们易于编写可在分布式网络中部署的容错和可伸缩程序。Erlang 和 Elixir 都是函数式语言，可以使用 Actor 系统来简化并发性的处理，并使错误恢复成为可能。他们能够充分利用多核计算，实现事半功倍。我们现在也看到在生产中更广泛地采用 Elixir，特别是对于那些更习惯于 Ruby 语法的公司。

下面是在核心业务系统的日常运行中使用 Erlang 和/或 Elixir 的 10 家公司的列表。这只是一个快照，许多公司(250 多家)现在都在他们的技术堆栈中使用 Erlang 和/或 Elixir。

# WhatsApp

只有 32 个工程师，怎么支撑 4.5 亿用户？对于 WhatsApp，答案是 Erlang。

WhatsApp 的 Anton Lavrik 将在 Code BEAM SF 2018 上讲述他们是如何做到这一点的。点击查看他的演讲详情[。](https://codesync.global/speaker/anton-lavrik/)

# 拼趣

Elixir 在 Pinterest 上用于管理 Ads API 上的费率限制的系统。它的响应时间大约是 500 到 800 微秒。将他们的通知系统从 Java 转移到 Elixir，意味着在 Elixir 中 10，000 行代码被减少到只有 1000 行。他们的 Elixir 系统现在运行更快，更一致，并且只需要一半数量的服务器。

在 Code BEAM SF 2018 上加入 Pinterest 开发者和 Elixir 核心团队成员 James Fish，在那里他将提供 [Elixir 核心团队更新](https://codesync.global/speaker/james-fish/)。

# 阿德罗尔

AdRoll 在其实时竞价系统的实时监控中使用 Erlang。这包括实时监控每秒接收超过 50 万个投标请求的系统中可能出现的任何问题，并附带可观的货币价值。点击了解更多关于这个[的信息。](https://www.youtube.com/watch?v=qURhXHbxbDU&list=PLWbHc_FXPo2hAfi8bz65wpBrCjeKtncB-&index=43)

来自 AdRoll 的 Miriam Pena 和 Mike Watters 将在 Code BEAM SF 2017 上发表演讲。米里亚姆将做主题演讲，平衡木的无名英雄，点击这里查看她的演讲细节。迈克将发表演讲，畅饮那剂药剂:节省数百万美元，点击这里查看他的演讲详情。

# Bet365

Bet365，使用 Erlang 和 Elixir 帮助 it 服务 2000 多万用户，满足高峰时间 10 万并发用户的需求。选择 Erlang 是因为它在分布性、并发性和弹性方面的优势。使用 Erlang，开发团队能够快速构建一个高度可伸缩的平台，在这个平台上可以快速添加新特性。

# IBM Cloudant

Erlang 和 Scala 一起用于支持 Cloudant 的全文搜索系统。该系统由两个互补的项目 Clouseau 和 Dreyfus 驱动。Dreyfus 是用 Erlang 编写的，它涉及插入现有的 CouchDB/Cloudant 集群技术。它管理 Clouseau 节点来提供全文搜索功能。Clouseau 和 Dreyfus 使用 Erlang 的外部术语格式进行通信(使用 Clouseau 端的 Scalang 库)。

# 高盛

高盛(Goldman Sachs)正在使用 Erlang 作为其高频交易平台的一部分。高盛平台是一个非常低延迟(微秒)的事件驱动的市场数据处理、策略和订单提交引擎。它运行具有低延迟要求的交易算法，能够响应市场条件的变化。Erlang 被用作这个分布式交易系统的实时监控解决方案的一部分。

# 蚊子

领先的 SEO 分析公司 Moz Pro 放弃了 MySQL 数据库存储，转而采用由 Elixir 驱动的数据索引模型支持的无数据库架构。结果是，他们的新 API 服务于核心排名数据集的速度比以前的 API 快 20 倍，平均响应时间始终低于 50 毫秒，而以前是 800 多毫秒。

# 孤独星球

这家著名的旅游公司每月需要服务数百万独立访客，向 booking.com 和 HostelWorld 这样的网站发送请求。他们采用的部分解决方案包括一个带有 API 子服务的 Elixir 微服务器，使用了 Phoenix web 框架。

# Grindr

Erlang 是 Grindr 系统的一部分，用于为超过 320 万的日活跃用户维护一个可靠的、高性能的和可伸缩的堆栈。

# USwitch

USwitch 在 Elixir 中重写了他们的一个小型微服务。由于优秀的文档、有用的错误消息和非常有用的 Elixir 社区，采用起来很容易。

![](img/468749b926c3b947b776b0a6086f3802.png)

Join big names at Code BEAM SF — San Francisco 15–16 March 18

*原载于*[*codesync . global*](https://codesync.global/media/successful-companies-using-elixir-and-erlang/)*。*