# 有效扩展:当库本内特斯遇到芹菜

> 原文：<https://medium.com/hackernoon/https-medium-com-talperetz24-scaling-effectively-when-kubernetes-met-celery-e6abd7ce4fed>

这是一个关于**软件**架构的故事，是一个关于个人之痒和可扩展性的故事。像任何好的科技故事一样，它始于一个摇摇欲坠的架构。

在[【2 号提示】—标签。到处都是标签。](https://medium.com/u/3352818b2dd1#1 提示</strong> -名称的唯一标识符。<br/>但我们仍然想知道最初的工作名称和公司名称，这让我想到了—<br/><strong class=)

# [我们考虑的框架](https://medium.com/u/3352818b2dd1#1 提示</strong> -名称的唯一标识符。<br/>但我们仍然想知道最初的工作名称和公司名称，这让我想到了—<br/><strong class=)

[在实现我们自己的工作流引擎之前，我们检查了一些现有的解决方案。](https://medium.com/u/3352818b2dd1#1 提示</strong> -名称的唯一标识符。<br/>但我们仍然想知道最初的工作名称和公司名称，这让我想到了—<br/><strong class=)

*   [气流](https://airflow.apache.org/) -气流很大。它的工作原理是将 python 文件渲染成代表工作流的 Dag。
    如果您有一个在运行前确定的静态工作流，您希望像 ETL 流一样执行，我建议尝试使用气流解决方案。Airflow 的问题在于动态工作流——看看这个[在栈溢出时在 airflow 中创建动态工作流的正确方法](https://stackoverflow.com/questions/41517798/proper-way-to-create-dynamic-workflows-in-airflow)。
    我们决定不使用它的原因是我们需要生成动态工作流，它会根据我们的 REST API 请求而变化。
*   [Google Pub/Sub](https://cloud.google.com/pubsub/docs/overview)——是 Google 的 pub sub 解决方案，我们没有使用它，因为它需要在所有“工作”方面进行大量的代码更改。
*   你可以查看这个[任务队列帖子](https://www.fullstackpython.com/task-queues.html)以获得更多选择。

# 下一步是什么？

**UI** —我们希望添加一个 UI，以便于对活动和已完成的工作流进行监控和故障排除。

通用化——如果我们把传送器做得更通用一点，也许我们可以把它作为开源软件发布。

# 行动呼吁

如果您有一个用例涉及到根据某些依赖关系运行批处理作业(例如数据采集、Web 爬行系统)，并且您对使用**传输器**进行扩展感兴趣，请**评论**或**联系**让我知道。

如果你喜欢这篇文章，请按下鼓掌键👏🏽如果你对接下来的帖子感兴趣，一定要关注我

**中:**[**https://medium.com/@talperetz24**](/@talperetz24) **推特:**[**https://twitter.com/talperetz24**](https://twitter.com/talperetz24) **领英:**[**https://www.linkedin.com/in/tal-per/**](https://www.linkedin.com/in/tal-per/)