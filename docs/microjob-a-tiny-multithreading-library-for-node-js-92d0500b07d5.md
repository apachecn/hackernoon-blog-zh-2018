# micro job:node . js 的小型多线程库

> 原文：<https://medium.com/hackernoon/microjob-a-tiny-multithreading-library-for-node-js-92d0500b07d5>

对 CPU 负载不再有限制

![](img/d674a3fce25b207c680725a77d875993.png)

# TL；DR；

Node.js 10.5 引入了 [**worker threads**](https://nodejs.org/en/blog/release/v10.5.0/) 和 [**microjob**](https://github.com/wilk/microjob) 是围绕它们的一个微小包装器。
**microjob** 帮助您轻松生成新线程，就像调用匿名函数一样，与 promises 和 async/await 机制接口。

## Node.js 简史

Node.js 以其 [**事件循环**](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/#what-is-the-event-loop) 而闻名，这是一种高性能处理 I/O 绑定操作的强大机制。
异步接口的早期实现是通过回调实现的，然后它们演变成承诺，最后演变成众所周知的 async/await 模式。
今天，**使用 Node.js 进行 I/O 加载是很常见的**，像 HTTP 请求、DB 查询、文件系统调用、定时器、tickers 等等，当然 Node.js 对于这些操作来说确实很强大。然而， **Node.js 一直苦于缺乏一个好的系统来承受繁重的 CPU 负载**。

## CPU 限制的操作

但是那些“*CPU 负载重*”是什么呢？这里有一个来自 Quora 回答的很好的解释:

1.  搜索算法
2.  视频/音频/内容转换/压缩算法
3.  视频流
4.  图形处理/渲染，例如游戏、视频
5.  繁重的数学计算，如矩阵乘法、计算阶乘、寻找质数等。

对于这些操作，Node.js 因为其性质一直吃亏:**非阻塞单线程事件循环**。等等，什么？Node.js 的主要特性是事件循环，这是一种在底层产生和处理线程的机制。然而，这仅适用于异步操作，但是同步操作是在单个线程上执行的，一次只有一个线程在执行。
这意味着如果一个长时间运行的任务被执行，主线程将一直被阻塞，直到执行结束，阻塞整个系统。
为了避免这种不便，Node.js 支持[多重处理](https://nodejs.org/api/cluster.html)，但是**进程间的上下文切换代价很高**。

## 工作线程

随着 10.5 版本的发布，Node.js 提供了[工作线程](https://nodejs.org/api/worker_threads.html)，多线程技术无与伦比！
你可以生成一个新线程，让它处理那些繁重的 CPU 负载。
那么，它是如何工作的呢？
工作线程自带**事件接口**，可以通过**消息传递**与主线程通信。
文档中的[示例是不言自明的。
处理事件和消息传递非常适合长时间工作，在这种情况下，您需要在后台运行一个线程，但是如果我只想在一个单独的线程中运行一个**单次函数**呢？](https://nodejs.org/api/worker_threads.html#worker_threads_example)

## 输入微作业

在语言中，比如 GoLang，你可以毫不费力地用**轻量级例程**来实现:

那个 *fmt。Println* 在一个不同的线程中执行，其中定义了一个匿名函数并以内联方式调用。
如果可以用 Node.js 的 worker threads 同样的方法制作呢？
这就是为什么我创造了 [**微操**](https://github.com/wilk/microjob) :

一个微小的包装器，允许你使用[**async**](https://hackernoon.com/tagged/async)**/await 接口**，跳过实例化一个新线程的枯燥部分，并通过事件与之同步。

## 引擎盖下是什么？

microjob 启动一个新线程，向它发送带有所需数据的给定函数，并在指定的上下文中执行(评估)它。
这意味着你可以像在 GoLang 中一样施展这个魔法:

而且你实际上可以**传递自定义数据**:

在 worker 内部调用异步函数怎么样？
microjob 附带了**[**Javascript**](https://hackernoon.com/tagged/javascript)的全部功能，允许您在线程内部使用 async/await 模式:**

## **微作业的未来**

****由于用户的请求和 Node.js 生态系统的不断变化，microjob 正在迅速发展**。
它在社区中获得了良好的反馈，在第一周就在 Github 上获得了类似 [**800+ stars**](https://github.com/wilk/microjob/stargazers) 的成绩，扩大了 Github 趋势页面。
很快，microjob 将拥有自己的[线程池系统](https://github.com/wilk/microjob/issues/3)，然后它将被[用 Typescript](https://github.com/wilk/microjob/issues/4) 重写，并且它将[支持 CI/CD](https://github.com/wilk/microjob/issues/5) 进行单元测试。**

**最后，我很高兴 JS 的人对 Node.js 中的多线程这么感兴趣！**