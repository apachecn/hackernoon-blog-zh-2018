# 关于 AWS CloudWatch 警报的一切

> 原文：<https://medium.com/hackernoon/aws-cloudwatch-alarm-tips-3a6a9ae06860>

警报对于监控指标和设置采取行动的阈值非常有用。

1)服务或应用以特定的频率发布度量数据；通常每 1 分钟或 5 分钟一次。

2)此指标数据通常在一个时间窗口(也称为时间段)内进行汇总，以发现趋势。每个汇总数据都是一个数据点。总结可以是平均值、错误计数、第 90 百分位等等。时间段可以是 5 分钟或 15 分钟等等。

3)使用警报，在几个时间段(称为[警报](https://hackernoon.com/tagged/alarm)评估间隔)内监控数据点，以检测持续问题并排除自动解决的临时问题(例如，在一个时间段内观察到的临时负载峰值)。

您可以通过两种方式配置报警:
A .连续数据点需要保持在阈值以上。如果报警评估间隔为 3 个时间段，则需要有 3 个连续的数据点高于阈值

B.N 个连续数据点中的 m 个需要保持在阈值以上。如果 M=3，N=3，则与(A)相同。如果 M=2 且 N=3，则即使三个连续数据点中只有两个数据点高于阈值，也会触发警报。

通过 [AWS](https://hackernoon.com/tagged/aws) 上的 [my udemy](https://www.udemy.com/aws-certified-solutions-architect-guide-question-bank-i/?couponCode=EVOLVE) [课程](https://www.udemy.com/aws-certified-solutions-architect-guide-question-bank-i/?couponCode=EVOLVE)(搜索: [Chandra Lingam](https://www.udemy.com/user/e7c3b177-f449-448a-aff8-9ef970b23152/) )来了解这一点以及更多。使用优惠券代码:EVOLVE 购买我的课程，仅需 10.99 美元