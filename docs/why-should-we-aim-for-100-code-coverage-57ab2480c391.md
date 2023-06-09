# 为什么我们应该以 100%的代码覆盖率为目标？

> 原文：<https://medium.com/hackernoon/why-should-we-aim-for-100-code-coverage-57ab2480c391>

我从未从事过代码覆盖率为 100%的[项目](https://hackernoon.com/tagged/project)。我已经参与了几个高覆盖率的项目，并且测试套件给了团队信心。但是即使在那些项目中，有时我们在产品中会有一个讨厌的 bug，这个 bug 可以通过一个简单的测试很容易地检测出来。

这是一个棘手的问题，通常开发人员不太关心这个问题，或者认为不值得花这个钱，或者甚至认为它没有那么有用。我在这里收集了一些支持全面保险的观点。

# 什么不是 100%的覆盖率

绝对不是程序没有 bug 的说法。它只是一个数据点，表明所有行都被覆盖。如果我们有程序的某些部分没有被覆盖，我们确实知道如果某些东西改变了，我们将需要执行一些手动验证。我们也不会确信我们的改变不会破坏任何东西。

另一方面，我们也可能对被覆盖的代码缺乏信心。我们可能会有大量的测试，并且害怕将某些东西投入生产。事实是，我们的测试只是覆盖了一组预定义的组合和用例。这可以通过[生成测试](https://www.pivotaltracker.com/blog/generative-testing/)来缓解，但是最终我们还是有可能错过一些东西。

# 那么我们有什么收获呢？

我们必须认识到，一个新的补丁不会破坏我们认为已经正确的场景。这个争论更多的是关于测试而不是全面覆盖。但是有了全保，你知道损坏已经在工作的东西的可能性就小了。

请务必考虑到，有时代码会在没有人类干预的情况下被破解。想象一下一个复杂的合并以某种方式改变了一些逻辑。覆盖这些代码可以更好地保证我们没事。

当我在解决 bug 问题，并且我有一个异常的 bug 时，我总是试图改变代码中的一些行，运行测试套件，并检查测试是否有问题。当它出现的时候，那真是太好了。因为如果我已经有了那条线的测试，我知道几件事:

*   我已经准备了一个带上下文的测试
*   我可以很容易地复制那个 bug 的环境/场景

如果我还没有一个测试*接近*那个问题，我可能会面临一个挑战，去构建产生这个问题的特定环境。这在使用第三方服务/组件或复杂代码的代码中很常见。创建测试环境非常困难，而我们忽略了这一点。

但是这些正是我们真正需要创建测试的场景。我们都接触过使用 PayPal 处理支付的代码，它很难测试并使用回调等。有虫子的时候呢？我们有麻烦了。

如果某样东西很难测试，它就很难维护。

难以维持意味着生产力和信心的低下。根据我的经验，覆盖率高但不是 100%的项目会遗漏以下内容:

*   难以测试的复杂场景
*   简单而基本的代码并不重要

# 99%还不够吗？

不，请考虑一下[破窗理论](https://en.wikipedia.org/wiki/Broken_windows_theory)。如果 99%是好的，那 98%不也是好的吗？特别是当我们刚刚得到一个测试起来很复杂的集成时。我们怎么知道那丢失的 1%是不是无害的？回到上一个示例，缺失的覆盖范围将是:

1.  难以测试的代码，这是它应该被测试的主要原因
2.  不那么重要的代码

根据我的经验，代码*并不重要*实际上很容易测试。测试*只是因为*允许我们有最大的阈值。呆在那里。

# 改变代码意味着改变测试

但是如果我们涵盖了一切，这意味着每次我们需要改变代码时，我们也需要改变测试——如果你这么说的话……是的，这很麻烦。

但是我要换一种说法:**从改变测试**开始。为新的现实重构测试，看到失败测试的红色标记，然后才转到代码，为绿色标记工作。我相信这是最好的做法。但我也认为有时只是改变一行…而那行可能会影响几个测试。然而，我确实相信利大于弊。

# 轻松检测不可及的代码

有了完全覆盖，我们将很容易检测出不再被使用的代码，因为这些代码将被标记为未被覆盖。让我们不要看一些方法，而要考虑重构一些没有使用的东西的含义。

有 ide 可以检测不可及的代码并警告开发人员。但是，即使有了这些工具，我也看到了存储库中不必要的代码。这可能是因为开发人员分心或者自动合并。

# 跟踪统计数据

更多的测试意味着更慢的测试套件和更长的反馈循环来知道是否一切正常。我相信我们应该随着时间的推移跟踪测试套件的整体速度，并且应该支持纯单元测试。将逻辑拆分为纯函数和有副作用的函数在这里确实有所帮助。

如果我们有了这些统计数据，我们就可以了解每个开发人员每年在测试套件中增加了多少时间，并进行推断。我们可以看看我们现在的工作方式是否会让测试套件在一两年后变得更长。如果它令人担忧，我们可以立即开始改善我们的工作方式。

# 代码示例

我收集了几个场景的代码示例，这些场景可能会让我们放弃完全覆盖。(*很抱歉提供了外部参考，在介质*上格式化这里的所有示例并不容易)。

# 摘要

拥有 100%的 cove 覆盖率有很多好处，但这可能意味着更多的工作，并且会加重开发过程。我确实相信一个测试完全覆盖变化的补丁有更高的质量，我们应该以此为目标。这可能很难，我们可能需要学习新的工作方式，我们可能需要质疑自己的信念。

但是我确信它会让我们成为更好的软件工匠。

*原载于 2018 年 2 月 3 日*[*engineering-management . space*](https://engineering-management.space/post/100-percent-test-coverage/)*。*