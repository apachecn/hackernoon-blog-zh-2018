# Python 为什么这么慢？

> 原文：<https://medium.com/hackernoon/why-is-python-so-slow-e5074b6fe55b>

Python 越来越受欢迎。它用于 DevOps、数据科学、Web 开发和安全。

然而，它没有赢得任何速度奖牌。

![](img/e50a22c6931e62cd96208cfc24d102aa.png)

> 就速度而言，Java 与 C 或 C++或 C#或 Python 相比如何？答案很大程度上取决于您运行的应用程序的类型。没有完美的基准测试，但是计算机语言基准测试游戏是一个很好的起点。

十多年来，我一直在参考计算机语言基准游戏；与其他语言如 Java、C#、Go、JavaScript、C++相比，Python 是[最慢的](https://benchmarksgame-team.pages.debian.net/benchmarksgame/faster/python.html)之一。这包括 [JIT](https://en.wikipedia.org/wiki/Just-in-time_compilation) (C#，Java)和 [AOT](https://en.wikipedia.org/wiki/Ahead-of-time_compilation) (C，C++)编译器，以及 JavaScript 这样的解释型语言。

*注意:当我说“Python”的时候，我说的是语言的引用实现，CPython。我将在本文中引用其他运行时。*

> 我想回答这个问题:**当 Python 完成一个可比的应用程序比另一种语言慢 2-10 倍的时候，*为什么会慢*而我们*不能让它更快*** ？

以下是最热门的理论:

*   "*是 GIL(全局解释器锁)*"
*   *因为它是解释的而不是编译的*
*   "这是因为它是一种动态类型语言"

这些原因中哪一个对性能影响最大？

# “这是 GIL”

现代计算机的 CPU 有多个内核，有时有多个处理器。为了利用所有这些额外的处理能力，操作系统定义了一个称为线程的低级结构，其中一个进程(例如 Chrome 浏览器)可以产生多个线程，并在内部为系统提供指令。通过这种方式，如果一个进程特别占用 CPU 资源，该负载可以在内核间共享，从而有效地使大多数应用程序更快地完成任务。

在我写这篇文章的时候，我的 Chrome 浏览器打开了 44 个线程。请记住，基于 POSIX(例如 Mac OS 和 Linux)和 Windows OS 的线程的结构和 API 是不同的。操作系统还处理线程的调度。

如果你以前没有做过多线程编程，你需要快速熟悉锁这个概念。与单线程进程不同，您需要确保在更改内存中的变量时，多个线程不会试图同时访问/更改同一个内存地址。

当 CPython 创建变量时，它分配内存，然后计算存在多少个对该变量的引用，这是一个称为引用计数的概念。如果引用的数量是 0，那么它从系统中释放那块内存。这就是为什么在 for 循环的范围内创建一个“临时”变量，不会增加应用程序的内存消耗。

当变量在多个线程中共享时，挑战就变成了 CPython 如何锁定引用计数。有一个“全局解释器锁”仔细控制线程的执行。解释器一次只能执行一个操作，不管它有多少个线程。

## 这对 Python 应用的性能意味着什么？

如果你有一个单线程、单解释器的应用程序。**对速度**没有影响。移除 GIL 不会影响代码的性能。

如果您希望通过使用线程在单个解释器(Python 进程)中实现并发，并且您的线程是 IO 密集型的(例如网络 IO 或磁盘 IO)，您将会看到 GIL 争用的后果。

![](img/f5ececadc8cfbc845c187d75987b120d.png)

From David Beazley’s GIL visualised post [http://dabeaz.blogspot.com/2010/01/python-gil-visualized.html](http://dabeaz.blogspot.com/2010/01/python-gil-visualized.html)

如果你有一个 web 应用程序(例如 Django ),并且你正在使用 WSGI，那么对你的 web 应用程序的每个请求都是一个**单独的** Python 解释器，所以每个请求只有一个锁*。因为 Python 解释器启动缓慢，所以一些 WSGI 实现有一个“守护模式”[,它可以让 Python 进程一直运行。](https://www.slideshare.net/GrahamDumpleton/secrets-of-a-wsgi-master)*

## 其他 Python 运行时呢？

PyPy 有一个 GIL，通常比 CPython 快 3 倍。

[Jython 没有 GIL](http://www.jython.org/jythonbook/en/1.0/Concurrency.html#no-global-interpreter-lock) ，因为 Jython 中的 Python 线程由 Java 线程表示，并受益于 JVM 内存管理系统。

## JavaScript 是如何做到这一点的？

首先，所有的 Javascript 引擎[都使用标记-清除垃圾收集](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management)。如上所述，GIL 的主要需求是 CPython 的内存管理算法。

JavaScript 没有 GIL，但是它也是单线程的，所以不需要。JavaScript 的事件循环和承诺/回调模式是异步编程代替并发性的实现方式。Python 对 asyncio 事件循环也有类似的处理。

# “因为它是一种解释性语言”

我经常听到这种说法，我发现这是对 CPython 实际工作方式的粗略简化。如果你在一个终端上写了`python myscript.py`，那么 CPython 将开始一长串的读取、词法分析、解析、编译、解释和执行代码。

如果你对这个过程感兴趣，我以前写过:

[](https://hackernoon.com/modifying-the-python-language-in-7-minutes-b94b0a99ce14) [## 在 6 分钟内修改 Python 语言

### 本周，我向 CPython 核心项目提出了我的第一个 pull-request，但是被拒绝了

hackernoon.com](https://hackernoon.com/modifying-the-python-language-in-7-minutes-b94b0a99ce14) 

这个过程中很重要的一点是创建一个`.pyc`文件，在编译阶段，字节码序列被写入 Python 3 上的`__pycache__/`中的一个文件，或者 Python 2 中的同一个目录中。这不仅适用于你的脚本，也适用于你导入的所有代码，包括第三方模块。

所以大多数时候(除非你写的代码只运行一次？)，Python 是解释字节码，本地执行。与 Java 和 C#相比。网络:

> Java 编译成一种“中间语言”，Java 虚拟机读取字节码，**实时**将其编译成机器代码。的。NET CIL 是一样的。NET 公共语言运行时(CLR)使用实时编译来编译机器代码。

那么，如果它们都使用虚拟机和某种字节码，为什么 Python 在基准测试中比 Java 和 C#慢这么多呢？首先，。NET 和 Java 都是 JIT 编译的。

JIT 或即时编译需要一种中间语言来将代码分成块(或帧)。提前(AOT)编译器被设计成在任何交互发生之前确保 CPU 能够理解代码中的每一行。

JIT 本身并没有使执行更快，因为它仍然在执行相同的字节码序列。然而，JIT 支持在运行时进行优化。一个好的 JIT 优化器会看到应用程序的哪些部分被频繁执行，称之为“热点”。然后，它会对这些代码进行优化，用更高效的版本替换它们。

这意味着当您的应用程序一次又一次地做同样的事情时，速度会明显加快。另外，记住 Java 和 C#是强类型语言，所以优化者可以对代码做更多的假设。

PyPy 有一个 JIT ,如前一节所述，比 CPython 快得多。这篇性能指标评测文章更详细地介绍了—

[](https://hackernoon.com/which-is-the-fastest-version-of-python-2ae7c61a6b2b) [## Python 最快的版本是哪个？

### 当然，“视情况而定”，但它取决于什么，以及如何评估哪一个版本的 Python 速度最快…

hackernoon.com](https://hackernoon.com/which-is-the-fastest-version-of-python-2ae7c61a6b2b) 

## 那么 CPython 为什么不使用 JIT 呢？

JIT 也有缺点:其中之一是启动时间。CPython 的启动时间已经比较慢了，PyPy 的启动速度比 CPython 慢 2-3 倍。众所周知，Java 虚拟机启动缓慢。的。NET CLR 通过在系统启动时启动来解决这个问题，但是 CLR 的开发人员也开发运行 CLR 的操作系统。

如果您有一个运行了很长时间的 Python 进程，其中的代码因为包含“热点”而可以优化，那么 JIT 就很有意义。

然而，CPython 是一个**通用**实现。因此，如果您正在使用 Python 开发命令行应用程序，那么每次调用 CLI 时都必须等待 JIT 启动将会非常慢。

CPython 必须尝试服务尽可能多的用例。有可能[在 CPython](https://www.slideshare.net/AnthonyShaw5/pyjion-a-jit-extension-system-for-cpython) 中插入一个 JIT，但是这个项目在很大程度上被搁置了。

> 如果您想要 JIT 的好处，并且您有适合它的工作负载，那么使用 PyPy。

# "*因为它是一种动态类型语言*"

在“静态类型”语言中，你必须在声明变量时指定变量的类型。这些语言包括 C，C++，Java，C#，Go。

在动态类型语言中，仍然有类型的概念，但是变量的类型是动态的。

```
a = 1
a = "foo"
```

在这个玩具示例中，Python 创建了第二个具有相同名称和类型`str`的变量，并释放了为第一个`a`实例创建的内存

静态类型语言并不是为了让你的生活变得困难而设计的，它们是因为 CPU 的工作方式而设计的。如果一切最终都需要等同于一个简单的二进制操作，那么您必须将对象和类型向下转换到一个低级数据结构。

Python 为你做到了这一点，你只是从来没有看到过，也不需要在意。

不必声明类型并不是 Python 速度慢的原因，Python 语言的设计让你几乎可以做任何动态的事情。您可以在运行时替换对象上的方法，您可以将低级系统调用修改为运行时声明的值。几乎一切皆有可能。

正是这种设计使得优化 Python 变得极其困难。

为了说明我的观点，我将使用一个在 Mac OS 中工作的名为 Dtrace 的系统调用跟踪工具。CPython 发行版没有内置 DTrace，所以您必须重新编译 CPython。我的演示使用的是 3.6.6

```
wget [https://github.com/python/cpython/archive/v3.6.6.zip](https://github.com/python/cpython/archive/v3.6.6.zip)
unzip v3.6.6.zip
cd v3.6.6
./configure --with-dtrace
make
```

现在`python.exe`将在整个代码中使用 Dtrace 跟踪程序。[保罗·罗斯在 Dtrace](https://github.com/paulross/dtrace-py#the-lightning-talk) 上写了一篇精彩的闪电演讲。你可以[为 Python 下载 DTrace starter 文件](https://github.com/paulross/dtrace-py/tree/master/toolkit)来测量函数调用、执行时间、CPU 时间、系统调用，各种乐趣。例如

`sudo dtrace -s toolkit/<tracer>.d -c ‘../cpython/python.exe script.py’`

`py_callflow`跟踪器显示了应用程序中所有的函数调用

![](img/68d1ae9479945d64b444929de919b73e.png)

那么，Python 的动态类型化是不是让它变慢了？

*   比较和转换类型的成本很高，每次读取、写入或引用变量时，都要检查类型
*   很难优化如此动态的语言。Python 的许多替代品如此之快是因为它们以性能的名义对灵活性做出了妥协
*   看看 [Cython](http://cython.org/) ，它结合了 C-Static 类型和 Python 来优化已知类型的代码[可以为](http://notes-on-cython.readthedocs.io/en/latest/std_dev.html)提供 84 倍的性能提升**。**

# 结论

> Python 速度慢主要是因为它的动态性和多功能性。它可以用作解决各种问题的工具，在这种情况下，可能会有更优化、更快速的替代方案。

然而，有一些方法可以优化您的 Python 应用程序，比如利用 async，理解分析工具，以及考虑使用多重解释器。

对于启动时间不重要并且代码对 JIT 有益的应用程序，可以考虑 PyPy。

对于性能至关重要且有更多静态类型变量的代码部分，考虑使用 [Cython](http://cython.org/) 。

## 进一步阅读

杰克·VDP 的优秀文章(虽然略显过时)[https://jakevdp . github . io/blog/2014/05/09/why-python-is-slow/](https://jakevdp.github.io/blog/2014/05/09/why-python-is-slow/)

戴夫·比兹利在 http://www.dabeaz.com/python/GIL.pdf GIL 的演讲

关于 JIT 编译器的一切[https://hacks . Mozilla . org/2017/02/a-crash-course-in-just-in-time-JIT 编译器/](https://hacks.mozilla.org/2017/02/a-crash-course-in-just-in-time-jit-compilers/)