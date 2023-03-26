# 如何控制 AWS Lambda 函数的异步调用

> 原文：<https://medium.com/hackernoon/how-to-control-asynchronous-invocations-of-your-aws-lambda-functions-ad2def033222>

![](img/75f002e2d4bd7f441e978da8349a0692.png)

Photo by [Markus Spiske](https://unsplash.com/photos/8OyKWQgBsKQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 问题是

我有一个连续运行的无服务器进程——一个 Lambda 运行，在它结束之前，它调用另一个 Lambda。由于速率限制和其他错误，我需要知道调用是否成功，但我不想等到新的 Lambda 结束。这很有挑战性。

另一件恼人的事情是，运行 Lambda 的数量正在不受控制地增加，尽管我应该一次有一个 Lambda 处于活动状态(可能有两个，因为我启动了一个新的 Lambda，然后才关闭当前的 Lambda——有几毫秒的重叠)。

# 解决方案

lambda 调用有两种类型:Sync(“request response”)和 Async(“Event”)。

最初，我从[异步](https://hackernoon.com/tagged/async)选项开始，因为我不想等待新的 Lambda 结束。结果是 [AWS](https://hackernoon.com/tagged/aws) 将这个调用请求添加到一个队列中，您不知道它是否被成功调用。此外，如果调用失败，那么 AWS 可以再次重新运行它..

我的下一个尝试是同步调用。为了知道 Lambda 调用是否成功，我需要等待新的 Lambda 结束。所以我用了一个小技巧:

1.  调用同步 Lambda
2.  等一下
3.  一秒钟后，发生了三件事:
4.  我在一秒钟内从 AWS 获得了一次成功的调用(这意味着新的 lambda 运行了不到一秒钟——这不太可能发生)
5.  我从 AWS 得到一个错误
6.  发生了超时，所以我假设调用是成功的。新的 Lambda 正在运行，但我不再等待它。

# 根据我的经验，重要的是:

1.  如果发生超时，我假设调用成功并继续执行我的代码**，但是**在某个时间点，新函数将结束并返回一个真正的成功或失败响应。您必须准备代码来处理它。你可能会得到两股水流，而不是一股——小心！
2.  与前一个场景相同，但是这一次，在被调用的 Lambda 用一个答案回复之前，第一个 Lambda 已经结束——AWS 将认为调用失败，并重新运行 Lambda！
3.  Lambda 套接字超时:如果 Lambda 以同步方式调用一个新的 Lambda B(**“request response”**)，它会根据 Lambda 套接字超时 **(lambda。config . http options = { time out:1500 }；)** —即使你的代码结束了，回复了 200 或者 500 个响应！
4.  您可以使用: **"context** 强制 Lambda 结束，而不等待套接字超时。**callbackWaitsForEmptyEventLoop**=**false**；"—请注意，当调用的 lambda 结束时，您会得到一条错误消息(B ),在这种情况下，它也会重新调用。

# 结论

*   我想知道 Lambda 调用是否成功，但我找不到直接的解决方案。您需要使用同步调用来“入侵”系统。
*   当 AWS 仅仅因为你没有等待它的响应就认为你的 Lambda 失败时，要注意额外的调用。
*   你的 Lambda 函数会被多次调用——你的代码应该是幂等的——代码逻辑应该知道如何处理同一个输入(事件)的多次调用。

**本文未讨论的其他解决方案:**

*   Lambda 死信队列——当失败的 lambda 被插入到需要管理的队列中时，在这种情况下仍会发生错误重试调用。
*   使用外部数据库来管理您的调用。例如，当 Lambda 开始时增加发电机计数器，当它结束时减少它。

[*阿莫斯【沙哈尔】*](https://www.linkedin.com/in/amos-shahar-483b182/) *是 DevOps &的总监 IT at*[***superQuery***](https://chrome.google.com/webstore/detail/superquery-bigquery-optim/lfckfngaeoheoppemkocjjebloiamfdc/reviews?hl=en)*。通过我们用于 Google BigQuery 的 SQL IDE，superQuery 使用人工智能和智能缓存来使数据团队能够在 Google BigQuery 上运行最高效和最具成本效益的查询。*