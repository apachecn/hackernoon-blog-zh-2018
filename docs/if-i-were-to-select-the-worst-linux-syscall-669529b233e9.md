# 如果我选择最差的 Linux 系统调用

> 原文：<https://medium.com/hackernoon/if-i-were-to-select-the-worst-linux-syscall-669529b233e9>

## 我的选择是精选

如果你喜欢我的博客，你可以在我的博客上阅读这篇文章。

![](img/16a18dc40e669768cb0f49dfafa27fb5.png)

Select、poll 和 epoll 都是实现类似目的的 [Linux](https://hackernoon.com/tagged/linux) 系统调用，它们提供了一种高效的异步 I/O 方式

换句话说，它们等待某个事件在文件描述符上发生。通常，文件描述符代表一个网络套接字，我们正在等待数据从另一台机器通过 TCP 或 UDP 传输过来。

在一个我们通过网络做很多事情的时代，这些 asyncio 系统调用对大多数程序员来说至关重要。

然而，很少有开发者真正听说过它们中的任何一个。实际上直接使用它们的人更少。尽管如此，几乎每个使用 Node、Ruby、Go 或 Python 的人都在不知不觉中依赖它们。

我们大多数人可能更喜欢在大多数时间避免直接使用系统调用。然而，我们使用的库包装器通常是微不足道的，只要我们需要降低一些性能或灵活性，我们可以在任何时候依靠本机接口。

我敢打赌，几乎所有正派的开发人员都会对他们的 Linux 线程知识有足够的信心，毫不犹豫地使用 pthread.h。我还敢打赌，如果这些开发人员不得不快速创建一个无错误的基于 epoll 的应用程序，他们几乎 100%都会冒冷汗。

首先，我认为我们应该看看 Linux 并发性宝库中的另一个生物，线程。

让我们看 3 个使用线程的例子。首先，使用 Python(一种“高级”语言)和 Rust(一种“低级”语言)的标准库，然后使用 Linux 为其第一类公民 c 提供的本机接口。

**巨蟒**

*注意:虽然有人可能听说 Python 线程由于 GIL 而不是“真正的线程”,但它们仍然在实现中使用 pthread。在 python 的非 GIL 实现中，它们可以像“真正的”线程一样工作。*

**生锈**

**C (POSIX API)**

显然，使用原生 pthread API 稍显不便，但是 pthread 上的包装器仍然非常类似于直接使用它，它们只是稍微简化了一下。如果你理解了如何使用任何语言的标准库中的线程，你就会明白如何使用本地 API。

接下来，让我们看看 Python 和 Rust 中异步 I/O 的“最低级别”方法，然后让我们看看如何使用本机 API 来实现它。

在这个例子中，我们将看看通过 TCP 与两个不同的地址同时通信。

**Python**

**生锈**

在这里，我应该提一下我曾有过一点欺骗。因为 futures 和 tokio 不是 Rust 标准库的一部分。然而，rust 还没有一个 asyncio 标准，围绕实现该标准的讨论主要围绕着将 tokio & futures 集成到标准库中。所以 rust 中 asyncio TCP 客户端的最终版本很有可能与上面的代码非常相似。

**C 语言(POSIX API)**

问题来了。

我很乐意向您展示使用 poll、epoll 或 select 通过 TCP 同步完成两个请求的惯用方法，但我可能会出错。此外，代码会很长，很难浏览。

这里有一个[非常全面的 TCP](https://gist.github.com/gonzus/f811f4d0926076acccd7) 客户端，它使用轮询。注意到问题了吗？它跨越了 700 行代码。

当然，这部分是 C 语言的错误和本地网络功能的错误。但是[编写一个简单的 TCP 客户端](http://www.cs.tau.ac.il/~eddiea/samples/IOMultiplexing/TCP-client.c.html)并不难，这是我们必须包含一个 asyncio 的部分，这使得它不可能正确。

我向任何有不同想法的读者挑战，让他们给我找出一个使用 poll 的惯用例子，它甚至接近 Rust 和 Python 方法的简单性。

![](img/5df674b18c5dcfabd38f495c577c2520.png)

# 如何修复异步系统调用

如果上面的 C 代码对您来说有点难读，让我用一些更小的代码片段来为您解释异步系统调用的用法:

*   创建一个非阻塞的文件描述符，[，如这里看到的](https://gist.github.com/gonzus/f811f4d0926076acccd7#file-tcp-client-poll-c-L598)。
*   遍历非阻塞文件描述符，查看其中是否发生了任何事件。[如此处所示，尽管只有一个 fd](https://gist.github.com/gonzus/f811f4d0926076acccd7#file-tcp-client-poll-c-L529) 。
*   如果事件已经发生，处理它然后进行循环。如果什么都没发生，继续。

除了理解如何编写基于 poll/epoll/select 的代码和编写它是两个完全不同的概念之外，这似乎不是一个糟糕的接口。

即使假设接口更简单，对于使用任何其他 asyncio 库的人来说，它仍然是完全陌生的。如果您想使用原生 API，基本上是从零开始。

***要是有更简单的方法就好了……***

嗯，有一个，几乎所有其他库处理 asyncio 的方式，使用回调和/或未来。这是每一种具有原生 asyncio 支持的其他语言的方式，比如 Go、Python 和 Node。所有 asyncio 库都是这样做的，不管是 C、C++、Java、Scala、Ruby、Perl、Php、Rust 还是其他语言的库。

但是，对于 Linux 来说，将所有的复杂性隐藏在引擎盖下是合适的吗？拥有一个用户友好的 API，让那些不懂真正的[编程](https://hackernoon.com/tagged/programming)美学的肮脏农民可以使用。

对此我回答说，请看 pthread。同样，我们使用`pthread_create`而不是`clone`系统调用。通过函数指针的魔力，我们可以在异步调用上有一个用户友好的包装，而不会失去任何功能。

当然，可能存在需要使用原始系统调用的边缘情况，类似于可能需要使用`clone`而不是`fork`或`pthread`的边缘情况。

但是对于 99.9%的情况，我认为我们会更乐意使用这样的包装:

`poll_events(underlying_syscall, fds_array, callback)`

其中回调函数将被标准化以接收文件描述符和特定于底层 syscall 的任何附加参数。例如`poll`的两个 shorts(事件和事件)。

或者这个接口甚至可以为同一个函数提供一个标准化的方法来处理这三个系统调用。

无论哪种方式，我都不想在这里规定原生 Linux 异步接口的设计。我只是建议我们可能需要一个更简单的。

如果人们不能使用你的 API，也许这是你的 API 是垃圾的标志，而不是你的用户是愚蠢的标志。如果面包黄油系统调用只有少数库维护者理解和使用，这意味着你需要一个更好的 API。

自从 select 出现以来，有两次机会提供更好的界面。一个是引入 poll 的时候，另一个是引入 epoll 的时候。然而，我们被 3 种繁琐的 asyncio 机制所困，它们的 API 和实现略有不同，都同样无法使用。

如果您喜欢这篇文章，您可能还会喜欢:

*   [明智对待所有权](https://blog.cerebralab.com/#!/a/Encouraging%20the%20Wrong%20State%20of%20Mind%20for%20Creating%20Good%C2%A0Code)
*   [请重新发明轮子](https://blog.cerebralab.com/#!/a/Please,%20reinvent%20the%20wheel)