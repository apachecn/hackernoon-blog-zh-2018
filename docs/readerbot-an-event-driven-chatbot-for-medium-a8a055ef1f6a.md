# ReaderBot:一个事件驱动的聊天机器人

> 原文：<https://medium.com/hackernoon/readerbot-an-event-driven-chatbot-for-medium-a8a055ef1f6a>

## 这是“飞行汽车工程师的日记”系列的第四部分

这篇文章讨论了事件驱动的[编程](https://hackernoon.com/tagged/programming)范例。

![](img/1e7b21d39c2d66b4eaaadb82219a5880.png)

Photo by [rawpixel.com](https://unsplash.com/@rawpixel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 事件驱动编程

**事件驱动编程**是一种编程范式，其中程序的流程由事件决定。

*   有一个不断监听回调的循环。
*   对于给定的回调，可能会发生状态转换。
*   侦听器()恢复。

## 事件驱动编程与标准编程范式有何不同？

*   事件驱动是基于事件的，不同于具有固定流程的程序。
*   根据与环境的交互和发生的事件，它在执行过程中可能会有不同的结果。

# 飞行汽车中的 EDP:

*   航班是动态的。
*   允许对环境的不同变化做出反应。
*   允许实时重新规划飞行时间。

# 解释性例子

下面是 ReaderBot 的代码:一个基于 EDP 的聊天机器人[与你互动，并根据你的回答打开你想读的文章。](https://hackernoon.com/tagged/chatbot)

请随意检查 GH 库。下面，是对关键功能的解释。

[](https://github.com/init27/ReaderBot/blob/master/ReaderBot.ipynb) [## init27/ReaderBot

### 在 GitHub 上创建一个帐户，为 ReaderBot 的开发做出贡献。

github.com](https://github.com/init27/ReaderBot/blob/master/ReaderBot.ipynb) 

步骤:

*   注册回调:
    维护回调响应的本地副本。

例如，考虑自动驾驶汽车的召回。

这是一个回调，当提供了“SDCs”输入时，机器人将执行该回调

```
self.register_callback("Self Driving Cars", 
                               self.respond_to_sdc)
```

我们必须为预定义响应的已接受消息填充一个本地字典，比如上面显示的 SDC 回调。

```
**def** register_callback(self, message, callback):
        *"""*
 *Registers a callback to a message.*
 *"""*
        **if** message **not** **in** self.accepted_messages:
            self.accepted_messages[message] = []
        self.accepted_messages[message].append(callback)
```

这里，我们为标准响应创建一个接受消息的本地字典。

*   注册具体的动作:
    如果你想阅读一些关于自动驾驶汽车的内容，你应该期待读者机器人给 SDC 回电。

```
**def** respond_to_sdc(self):
        webbrowser.open_new("https://hackernoon.com/a-self-driving-new-year-33284e592f35")
```

这将在一个新标签中打开，一个关于自动驾驶汽车的帖子。

下一步，是将 EDP 扩展到飞行汽车领域，并为自主无人机编制飞行计划。

查看我的第一个飞行汽车 Nanodegree 项目的视频，完全基于 EDP:

*如果你觉得这篇文章很有趣并且想保持联系，你可以在 Twitter 上找到我* [*这里*](http://twitter.com/bhutanisanyam1) *。*