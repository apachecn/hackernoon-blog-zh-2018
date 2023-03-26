# 无敌阿辛西奥

> 原文：<https://medium.com/hackernoon/asyncio-the-invincible-16f5c4924755>

当谈到多任务时，异步编程是新的热点。它因 NodeJS 而出名，但你可以在大多数主要语言中找到支持它的库。

例如在 Python 中，两个最著名的方法是使用 Tornado Web 服务器和内置库 asyncio。今天我们将对后者做一个介绍，因为它最近经历了大量的迭代和改进，并且它已经准备好以其直观的方式吸引人们。

# 什么是异步编程？

这是一种编程方法，当遇到“停滞时间”时，我们会保持警惕。例如，如果 I/O 正在发生(例如，我刚刚发送了一个 GET 请求，等待响应)，应用程序可以继续做其他事情。有人称之为，理想秘书效应。

它可能不适合 Python，但是本文中的文章[有一个很好的图表解释了潜在的异步流。](/from-the-scratch/javascript-writing-your-own-non-blocking-asynchronous-functions-60091ceacc79)

![](img/9adef840a469113fdd3c178468d3f44e.png)

# 多线程有什么问题？

当有不同的线程来执行所需的任务时，程序就在使用多线程。这需要大量的关注、协调和主动性，因为线程至少需要不把彼此锁在共享资源之外(死锁)。更不用说额外的内存开销，因为线程需要访问自己的数据副本。

[在这里](https://stackoverflow.com/questions/1762418/what-resources-are-shared-between-threads)你可以找到一个相关的 StackOverflow 线程。

不是说多线程没用。相反，有些操作本质上是同步的。例如，从文件系统中删除一个巨大的文件。有些人可能会说，你可以通过使用基于流的方法来克服这一点，一次删除一小块，他们是对的，我只是试图证明一点。:-)

异步编程虽然可以与多线程一起工作，但也可以只与一个线程一起有效地工作。大多数情况下，您无法区分性能上的差异(我将在以后的文章中讨论这个问题)。这里唯一的“开销”是要知道一个接一个地写命令并不意味着它们会以相同的顺序执行

# 阿辛西奥——我选择你

[asyncio](https://docs.python.org/3/library/asyncio.html) 是一个使用 async/await 语法编写并发代码的库。这样，即使您编写了异步代码，您最终也会获得同步的视觉感受，这使得可维护性变得更容易一些。

因此，下面是如何用 python 编写异步代码，并用注释解释每一步

```
# First import the library that provides all those bells and whistles
import asyncio

# Import datetime and calendar as our example is based on date handling
import datetime
import calendar

# Here is the important part. We declare a function as async. That means its execution
# will not block the application. For example, if you run the function outside
# of an async context it will print something like: 
# <coroutine object show_me_the_day at 0x7f89d55a0e60> 
# and not the result you would expect in a synchronous execution.
async def show_me_the_day():
    # Calendar is not asynchronous, so we duse asyncio.sleep to emulate the async nature
    # If for any reason you want to block the execution while
    # an async function is executed (eg you need the return value), use await.
    # Await can only be used in async functions, and only inside async functions
    await asyncio.sleep(1)
    return calendar.day_name[datetime.date.today().weekday()]

async def main():
    # If for any reason you want to block the execution while
    # an async function is executed (eg you need the return value), use await.
    # Await can only be used in async functions, and only inside async functions
    day = await show_me_the_day()
    print('Yo, today is ', day)

asyncio.run(main())
```

除了一些样板文件，这就像你在阅读一个经典的连续脚本。经典的 Python 魔术。:)

# 不过要小心

使用 asyncio 时有一个很大的注意事项。也就是说，当涉及到 I/O 时，我们必须依赖异步库。否则，收益可以忽略不计。

# 结论

感谢你阅读这篇文章，我希望你喜欢它。在上一个版本中，Python 在处理异步编程的方式上做了很多改进。一如既往，欢乐和杰作在一起。我将带着一篇以性能为导向的文章回来，也许还有几个框架。

如果想深入挖掘，官方[文档](https://docs.python.org/3/library/asyncio.html)很棒，请看一下。

*原载于* [perigk.github.io](https://perigk.github.io/) *。*