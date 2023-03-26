# 下面是一些简单的优化来提高 CPU 的性能

> 原文：<https://medium.com/hackernoon/here-are-some-simple-optimisations-to-squeeze-performance-out-of-cpu-6306342ed1a5>

![](img/832c17af3dc57f215d272f2e6a580587.png)

Photo by [Johannes Plenio](https://unsplash.com/@jplenio?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在作为软件开发人员的职业生涯开始时，大多数人认为程序的 T2 性能归结于其操作的渐近复杂性。但是一旦你应用了最好的算法，你的代码仍然不能给你想要的性能，你该怎么办？

这里有一些基本的技术可以帮助你从 CPU 内核中获得额外的能量。

# 循环展开

一个现代的 CPU 内核有多个功能单元，如 [ALU](https://en.wikipedia.org/wiki/Arithmetic_logic_unit) ，加载/存储，在其[执行单元](https://en.wikipedia.org/wiki/Execution_unit)中分支。

[](https://en.wikichip.org/wiki/intel/microarchitectures/kaby_lake#Architecture) [## Kaby Lake -微架构-英特尔- WikiChip

### Kaby Lake (KBL)是英特尔 Skylake 的继任者，sky Lake 是一款面向主流台式机的增强型 14 纳米制程微架构…

en.wikichip.org](https://en.wikichip.org/wiki/intel/microarchitectures/kaby_lake#Architecture) 

程序应该总是试图利用这一点来实现并行性。

其中一个技巧是使用[循环展开](https://en.wikipedia.org/wiki/Loop_unrolling)。循环展开允许您打破参数的依赖链。一个无序的 CPU 可以并行执行多条指令，并使你的程序运行得更快。

```
**func F1**() int {
       **var** acc int = 1;
       **for** j := 0; j < length; j++ {
              acc = acc + inputList1[j]
       }
       **return** acc
}**func F2**() int {
       **var** acc1 int = 1;
       **var** acc2 int = 1;

       **for** j := 0; j + 1 < length; j += 2 {
              acc1 = acc1 + inputList1[j]
              acc2 = acc2 + inputList1[j + 1]
       }
       **return** acc1 + acc2
}
```

在 F1 中，在执行下一个循环之前，程序通常必须等待 *acc* 更新。然而，在 F2 中，您的程序将利用内核中的多个整数加法单元，并且将并行进行。如果您运行基准测试，您会注意到 F2 的速度几乎是 F1 的两倍。

# 向量指令

现代 CPU 可以将向量(原始类型的数组)作为一个单元而不是单个元素来操作。向量指令包括一次加载多个值，在一条指令中对它们进行操作，然后存储它们。这些指令被称为 [SIMD](http://qr.ae/TU1wkb) (单指令，多数据)。SIMD 比较流行的分机是[上交所](https://en.wikipedia.org/wiki/Streaming_SIMD_Extensions)、 [AVX](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions) 以及那里的分机。

SIMD 指令通常可以一次处理 8 个 32 位或 4 个 64 位值，因此可以使程序运行得更快。通常编译器会注意这一点。

# 贮藏

程序员通常认为缓存是理所当然的。您编写的代码通常会对是否有有效的缓存产生很大的影响。

以 [github](https://github.com/ardanlabs/gotraining/tree/47261e94421eaad383d5c6bcfdb4e60087fa86f0/topics/go/testing/benchmarks/caching) 为例

```
**func** ColumnTraverse() int {
       **var** ctr int

       **for** col := 0; col < ***cols***; col++ {
              **for** row := 0; row < ***rows***; row++ {
                     **if** matrix[row][col] == 0xFF {
                            ctr++
                     }
              }
       }

       **return** ctr
}
**func** RowTraverse() int {
       **var** ctr int

       **for** row := 0; row < ***rows***; row++ {
              **for** col := 0; col < ***cols***; col++ {
                     **if** matrix[row][col] == 0xFF {
                            ctr++
                     }
              }
       }

       **return** ctr
}
```

这两个函数在做同样的事情，并且具有相同的渐近复杂度。如果您运行基准测试，您会发现 RowTraverse 通常比 ColumnTraverse 快 4 倍。

这是因为行遍历利用了缓存中已经存在的值，但是列遍历必须在每次比较时从内存中获取一个值。

你可以在我的文章中读到更多关于缓存的内容

[](https://codeburst.io/understanding-hardware-to-push-performance-to-the-max-and-then-some-part-1-c048021114e3) [## 为什么您应该关心知识库？

### 让我们充分利用 CPU 缓存的潜力

codeburst.io](https://codeburst.io/understanding-hardware-to-push-performance-to-the-max-and-then-some-part-1-c048021114e3) 

# 分支预测

现代 CPU 在看到分支时不会停止(即 if else 类型的条件)。相反，他们[推测性地执行](https://www.extremetech.com/computing/261792-what-is-speculative-execution)下一条指令，假设分支是否被采用。仅当确定预测是否正确时，才提交结果。在错误预测的情况下，CPU 会丢弃结果，并从分支后的第一条语句开始执行指令。除了执行更多指令的额外开销之外，这种未命中通常会导致 5–10 ns 的损失。

如果你的程序在 if-else 之间频繁切换，那么分支失误预测惩罚将对你的性能产生影响。避免这种情况的最佳方法是使用尽可能少的分支，如果绝对必要，使它们可预测(例如，在比较之前对值进行排序)。

# 并发！=并行度

您将线程数量从 10 个增加到 100 个，程序的性能变得更差。为什么会这样？

问题出在 CPU 上。它只有有限数量的内核来支持并行操作。在应用中并行发生的情况通常是处理器在线程之间快速切换。但是，这种切换成本很高，因为它涉及来回复制 PC、SP 和内存/缓存中的所有寄存器。

如果你有大量的线程，CPU 将花费大部分时间在这些线程之间周旋，而不是执行实际的指令。现代语言试图通过在几个操作系统线程上复用大量抽象来解决这个问题。

# 互斥（体）…

如果您实现了最佳的并发性，但是您使用锁来同步数据结构，您仍然不会注意到应有的性能提升。

这是因为一个简单的[互斥](https://searchnetworking.techtarget.com/definition/mutex)锁定/解锁需要大约 25ns。这看起来不是很多，但是一旦你以 1000 万 RPM 运行你的程序，这 25ns 就变成了 250ms，这就很多了。

避免互斥的最简单的方法之一是使用线程本地数据结构。如果你需要在线程间共享数据，使用无锁的通信技术(比如 goroutines 中的通道)而不是共享内存 ds。

# 内存引用

除非绝对需要，否则不应该使用内存引用。编译器通常避免对内存引用进行优化，因为这会产生前所未有的影响。

```
**func** AddPointers(x *int, y *int){
       *x += *y
       *x += *y
}
```

一个简单的优化编译器应该做的是使它** x = 2 *(x)+* y*从而减少指令的数量*。*

然而，如果 x 和 y 指向同一个变量， *AddPointers* 将给出输出 4(*x)，而优化将给出 3(*x)，这是不正确的。编译器在遇到这种情况时会安全运行，并会避免任何优化。

还有更多技术可以应用，例如内核旁路、避免内核间通信等。但是那些不满足简单的标准。我将在另一篇文章中详细讨论这些。

深入研究这些主题的一些资源包括:

*   [计算机系统程序员的视角](https://www.amazon.com/Computer-Systems-Programmers-Perspective-3/dp/9332573905/ref=sr_1_1?ie=UTF8&qid=1522789499&sr=8-1&keywords=computer+systems+a+programmer%27s+perspective)
*   [http://highscalability.com/all-time-favorites/](http://highscalability.com/all-time-favorites/)
*   [Scott Meyers: Cpu 缓存和你为什么关心它](https://www.youtube.com/watch?v=WDIkqP4JbkE)
*   [每个程序员都应该知道的延迟数字](https://gist.github.com/jboner/2841832)

***在***[***LinkedIn***](http://www.linkedin.com/in/kartik-khare)***或*** [***【脸书】***](https://www.facebook.com/KK.corps) ***上与我联系或给***[***【kharekartik@gmail.com***](mailto:kharekartik@gmail.com)***留言分享反馈。***