# 5 种高级围棋测试技术

> 原文：<https://medium.com/hackernoon/5-advanced-go-testing-techniques-7199b686b6c1>

![](img/030d442facc856970d53d56f2dc70b44.png)

## 工程师艾伦·布莱斯维特

> 这篇文章最初出现在[板块博客](https://bit.ly/2J0mhcp)上。 [Segment](https://segment.com/?utm_source=hacker%20noon%20blog%20post) ，Hacker Noon 的每周赞助商，目前提供 90 天的免费试用——发送电子邮件至 friends@segment，并提及 Hacker Noon 进行兑换。

Go 有一个健壮的内置测试库。如果你写 Go，你就已经知道这个了。在这篇文章中，我们将讨论一些提高围棋水平的策略。我们从大型 Go 代码库的经验中了解到，这些策略可以节省维护代码的时间和精力。

# 使用测试套件

如果你从这篇文章中只学到一件事，那就是:使用测试套件。对于那些不熟悉该模式的人来说，套件测试是针对一个通用接口开发测试的过程，该接口可用于该接口的多个实现。下面，您可以看到我们如何传递多个不同的`Thinger`实现，并让它们运行相同的测试。

```
type Thinger interface {
    DoThing(input string) (Result, error)
}// Suite tests all the functionality that Thingers should implement
func Suite(t *testing.T, impl Thinger) {
    res, _ := impl.DoThing("thing")
    if res != expected {
        t.Fail("unexpected result")
    }
}// TestOne tests the first implementation of Thinger
func TestOne(t *testing.T) {
    one := one.NewOne()
    Suite(t, one)
}// TestOne tests another implementation of Thinger
func TestTwo(t *testing.T) {
    two := two.NewTwo()
    Suite(t, two)
}
```

幸运的读者可能使用过使用这种技术的代码库。经常在基于插件的系统中使用，针对接口编写的测试可被该接口的所有实现使用，以确定是否满足行为要求。

使用这个策略可能会节省几个小时、几天，或者甚至足够的时间来解决 P 对 NP 的问题。此外，当交换两个底层系统时，您不必编写(许多)额外的测试，并且它提供了这样做不会破坏您的应用程序的信心。这隐含地要求您创建一个定义您正在测试的表面区域的接口。使用依赖注入，您可以从您的包中设置套件，并为包传递实现。

此处提供了一个完整的示例[。虽然这个例子是精心设计的，但是您可以想象一个实现是远程数据库，而另一个是内存数据库。](https://github.com/segmentio/testdemo)

标准库中另一个极好的例子是`golang.org/x/net/nettest`包。它提供了验证`net.Conn`是否满足其接口的方法。

# 避免界面污染

不谈接口，就谈不上 Go 中的测试。

接口在测试环境中很重要，因为它们是我们测试武器库中最强大的工具，所以正确使用它们很重要。包经常导出一个接口供消费者使用，这反过来导致:a .消费者实现他们自己的包实现的模拟，或者 b .包导出他们自己的模拟。

> *界面越大，抽象性越弱。*
> 
> *—抢长枪，走箴言*

在导出接口之前，应该仔细考虑它们。开发人员通常倾向于导出接口，作为消费者模仿其行为的一种方式。相反，记录您的结构满足哪些接口，这样您就不会在消费者包和您自己的包之间创建硬依赖关系。一个很好的例子就是[错误包](https://godoc.org/github.com/pkg/errors)。

当我们的程序中有一个我们不想导出的接口时，可以使用一个`[internal/](https://golang.org/doc/go1.4#internalpackages)` [包子树](https://golang.org/doc/go1.4#internalpackages)来保持它在包范围内。通过这样做，我们消除了其他消费者可能依赖它的顾虑，因此可以随着新需求的出现而灵活地发展接口。我们通常围绕外部依赖创建接口，并使用依赖注入，这样我们就可以在本地运行测试。

这使得消费者能够实现他们自己的小接口，只包装库的消费表面用于他们自己的测试。关于这个概念的更多细节，参见 [rakyll 关于界面污染的帖子](https://rakyll.org/interface-pollution/)。

# 不要导出并发原语

Go 提供了易于使用的并发原语，这有时也会导致它们的过度使用。我们主要关心渠道和`sync`包。有时从您的包中导出一个频道供消费者使用是很诱人的。此外，嵌入`sync.Mutex`而不使其私有是一个常见的错误。和任何事情一样，这并不总是坏的，但是当测试你的程序时，它确实带来了挑战。

如果您正在导出渠道，您会将软件包的消费者暴露在他们不应该关心的额外复杂性中。一旦从包中导出了一个通道，您就开始了对使用该通道的测试的挑战。为了做好测试，消费者需要知道:

*   当数据在信道上发送完毕时
*   接收数据时是否有任何错误
*   如果有的话，包在完成后如何清理通道？
*   如何在包 API 周围包装一个接口，这样就不用直接调用了？

考虑一个读取队列的例子。这是一个示例库，它从队列中读取数据，并公开了一个供消费者读取的通道。

```
type Reader struct {...}
func (r *Reader) ReadChan() <-chan Msg {...}
```

现在，您的库的一个用户想要为他们的消费者实现一个测试:

```
func TestConsumer(t testing.T) {
    cons := &Consumer{
        r: libqueue.NewReader(),
    }
    for msg := range cons.r.ReadChan() {
        // Test thing.
    }
}
```

然后，用户可能会认为依赖注入是一个好主意，并沿着通道编写自己的消息:

```
func TestConsumer(t testing.T, q queueIface) {
    cons := &Consumer{
        r: q,
    }
    for msg := range cons.r.ReadChan() {
        // Test thing.
    }
}
```

但是等等，错误怎么办？

```
func TestConsumer(t testing.T, q queueIface) {
    cons := &Consumer{
        r: q,
    }
    for {
        select {
        case msg := <-cons.r.ReadChan():
            // Test thing.
        case err := <-cons.r.ErrChan():
            // What caused this again?
        }
    }
}
```

现在，我们如何生成事件来实际写入这个模拟，充分复制我们正在使用的实际库的行为？如果这个库只是简单地写了一个同步 API，那么我们可以在我们的客户端代码中添加所有这些并发性，这样测试起来就简单多了。

```
func TestConsumer(t testing.T, q queueIface) {
    cons := &Consumer{
        r: q,
    }
    msg, err := cons.r.ReadMsg()
    // handle err, test thing
}
```

如果有疑问，请记住，在消费包中添加并发总是很容易的，一旦从库中导出，就很难/不可能删除。最后，不要忘记在包文档中提到一个结构/包对于多个 goroutines 的并发访问是否安全！

有时，导出通道仍然是可取的或必要的。为了缓解上面的一些问题，您可以通过访问器而不是直接公开通道，并强制它们在声明中成为只读(`←chan`)或只写(`chan←`)通道。

# 使用`net/http/httptest`

`httptest`允许你运行你的`http.Handler`代码，而不需要实际启动服务器或者绑定端口。这加快了测试的速度，并允许它们以更少的工作量并行运行。

这里有一个使用两种方法实现的相同测试的例子。它看起来不怎么样，但是它为您节省了相当多的代码和资源。

```
func TestServe(t *testing.T) {
    // The method to use if you want to practice typing
    s := &http.Server{
        Handler: http.HandlerFunc(ServeHTTP),
    }
    // Pick port automatically for parallel tests and to avoid conflicts
    l, err := net.Listen("tcp", ":0")
    if err != nil {
        t.Fatal(err)
    }
    defer l.Close()
    go s.Serve(l) res, err := http.Get("http://" + l.Addr().String() + "/?sloths=arecool")
    if err != nil {
        log.Fatal(err)
    }
    greeting, err := ioutil.ReadAll(res.Body)
    res.Body.Close()
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println(string(greeting))
}func TestServeMemory(t *testing.T) {
    // Less verbose and more flexible way
    req := httptest.NewRequest("GET", "http://example.com/?sloths=arecool", nil)
    w := httptest.NewRecorder() ServeHTTP(w, req)
    greeting, err := ioutil.ReadAll(w.Body)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Println(string(greeting))
}
```

也许使用`httptest`带给你的最大好处是能够将你的测试划分为你想要测试的功能。没有路由器，中间件或任何其他副作用来自建立服务器，服务，处理器工厂，处理器工厂工厂或任何你以前认为是好的想法扔给你。

要了解更多这种模式的应用，请看马克·伯格的文章。

# 使用单独的`_test`包装

生态系统中的大多数测试都是在文件`pkg_test.go`中创建的，但是仍然在同一个包`package pkg`中。一个单独的测试包是您在一个新文件`foo_test.go`中创建的包，在您想要测试的包`foo/`的目录中，声明`package foo_test`。从那里，您可以导入`github.com/example/foo`和其他依赖项。这个特性支持很多事情。对于测试中的循环依赖，这是推荐的解决方法，它防止了脆弱的测试，并且它允许开发人员感受消费他们自己的包是什么感觉。如果你的包很难使用，那么用这种方法测试也很难。

这种策略通过限制对私有变量的访问来防止脆弱的测试。特别是，如果您的测试中断了，并且您正在使用一个单独的测试包，那么几乎可以保证使用中断了测试的特性的客户端在被调用时也会中断。

最后，这有助于避免测试中的导入周期。除了那些被测试的包之外，大多数包很可能依赖于你编写的其他包，所以你最终会遇到一个情况，一个导入周期作为一个自然的结果发生。在包层次结构中，外部包位于两个包之上。以 Go 编程语言(Chp)为例。11 秒 2.4)，`net/url`实现了一个 URL 解析器，`net/http`导入它以供使用。然而，`net/url`希望通过导入`net/http`来使用真实用例进行测试。于是`net/url_test`诞生了。

既然您正在使用一个单独的测试包，您可能需要访问包中未导出的实体，而这些实体以前是可以访问的。大多数人在测试基于时间的东西时首先点击这个(例如`time.Now`通过一个函数被模仿)。在这种情况下，我们可以使用一个额外的文件在测试期间专门暴露它们，因为`_test.go`文件被排除在常规构建之外。

# 要记住的东西

重要的是要记住，以上建议的方法都不是灵丹妙药。最佳解决方案是始终对情况进行批判性分析，并决定适合问题的最佳解决方案。

想学习更多的围棋测试技巧吗？

看看这些帖子:

*   Dave Cheney 编写 Go 中的表格驱动测试
*   [关于测试的 Go 编程语言章节。](http://www.gopl.io/)

或者这些视频:

*   [桥本的高级测试与 Gophercon 2017 的 Go talk】](https://www.youtube.com/watch?v=yszygk1cpEc)
*   【2014 年 Andrew Gerrand 的测试技术讲座

> 这篇文章最初出现在[栏目博客](https://bit.ly/2J0mhcp)上。Hacker Noon 的每周赞助商 [Segment](https://segment.com/?utm_source=hacker%20noon%20blog%20post) ，目前提供 90 天免费试用——发送电子邮件至 friends@segment，并提及 Hacker Noon 进行兑换。