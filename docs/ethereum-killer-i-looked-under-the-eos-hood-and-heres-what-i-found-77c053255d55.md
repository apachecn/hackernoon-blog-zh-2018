# 以太坊黑仔？我看了下引擎盖，这是我的发现

> 原文：<https://medium.com/hackernoon/ethereum-killer-i-looked-under-the-eos-hood-and-heres-what-i-found-77c053255d55>

## *—Hunter Long，区块链工程师，Element Group*

![](img/093e9affe08148d2b689ff26dc8e624c.png)

回到 2017 年 5 月，当 Dan Larimer 在当年的共识会议上发表他的 EOS 演讲时，区块链社区的兴奋几乎是显而易见的。一年的风风雨雨过去了，今天，2018 年 6 月 14 日，EOS [终于推出了它的 mainnet](http://dev.cryptolions.io/mainnet/) 。这意味着，是时候看看 EOS 是否能实现其作为“以太坊黑仔”的宣称了。

作为一名区块链工程师，我对以太坊和过去几年为其开发的各种工具有着丰富的经验。例如，我知道以太坊很容易安装，但是，它会延迟和崩溃。它还会加热你的电脑，使它成为一个完美的暖手器，这不是加州夏天的最佳使用案例。然而，我从未直接进入 EOS。对于本文，我决定看看一个几乎没有 C++经验的开发人员(我)是否能够为 EOS 区块链创建一个定制的智能契约。所以让我们开始吧！

**以太坊和 EOS 合约有那么大的不同吗？**

对于不经意的观察者来说，最终的结果可能看起来极其相似。创建一个 EOS 合同与创建以太坊合同完全不同。当以太坊首次推出时，它受到了欢迎，因为安装过程很简单，开发人员无需许多额外的技能或知识就可以创建所有类型的合同。

以太坊智能合约是使用 Javascript/Solidity 开发的。这意味着大多数开发人员可以直接参与进来。另一方面，EOS 契约是用 C++开发的，c++不是一种无处不在的语言，也不像 Javascript 那样友好。因此，尽管 C++仍然是一种非常流行的编程语言，但它仍然可能被许多开发人员视为入门的障碍。

那么，引擎盖下是什么？

我创建的第一个 EOS 智能合约叫做 [EOSCRUD](https://github.com/element-group/eoscrud) ，(创建，读取，更新，删除)。这个契约相当简单:它被设计成保存一个包含 ID、用户名和文本评论的简单数据库。虽然这个智能合约不会让我们更接近世界和平，甚至不会成为新的 Wordpress，但我也根本不是 C++专业人士。像这样简单的功能让我真正测试了所谓的 EOS 的简单性。

在花了大约 2 或 3 个小时挖掘了 [EOS 合同回购](https://github.com/EOSIO/eos/tree/master/contracts)中列出的例子后，我能够在不把我的电脑扔出窗外的情况下建立我的第一个 EOS 智能合同。我很清楚这个合同写的不是很好，但是确实管用，这是最终目的。

我已经多次提到这一点，但我想重申:我不是 C++开发人员。我通常使用 Go、Ruby、Node/JS，有时会加入一点 C#和一些其他语言。有了这些知识，我能够开发一个非常[基本的 C.R.U.D .契约](https://github.com/element-group/eoscrud)，包含大约 79 行代码。

当然，这个合同完全没有安全性，但是它确实可以在 EOS 测试网络上工作，并且可以在我的本地机器上运行。EOS 智能合约是用 C++编写的，这一事实可以扩展区块链合约的功能。以太坊智能合约非常简单，有了 EOS，智能合约就有可能包含像 HTTP 这样的 C++库。

**ETH vs EOS——对决**

那么，在构建了我的第一个 EOS 智能合约之后，该平台与以太坊相比究竟如何？让我们来看看:

- **易用性:**除非你是 C++专家，否则 Solidity 更容易上手并开始使用。我认为，即使是一个完整的 n00b 也可以在最不舒服的情况下开始建造。**以太坊胜。**

**-安装友好:**以太坊的安装相当简单，如果你真的遇到了麻烦，有一个庞大的社区随时准备提供帮助。EOS 并不简单，很少有人分享他们的知识。**以太坊胜。**

- **契约特性:**以太坊契约在它们所能拥有的特性方面非常有限。虽然有像 Oraclize 这样的应用程序允许你的合同进行 HTTP 调用，但以太坊在功能上还是非常原始的。但是它确实有效！如果 EOS 合同允许开发人员实现 C/C++库，这将大大扩展他们的能力。 **EOS 胜。**

**-观感:**现阶段我完全不能说 EOS 有什么观感。我做的大部分合同建设工作都是通过码头完成的。目前还没有全新的用户界面，或者任何面向用户的应用程序。以太坊更面向用户。**以太坊胜。**

**结论**

那么，我能说 EOS 是“以太坊黑仔”吗？没有，至少现在还没有。虽然 C/C++库的实现可以给这个系统一个很大的帮助，但是在它的易用性方面还有一些很大的障碍。随着今天 EOS 主网络的启动，C/C++库是否可以实际用于 EOS 智能合约还没有定论。在系统的可访问性方面还有很多工作要做。这仍然是 EOS 的早期阶段，但是，就目前而言，以太坊是两者中更有用和功能更强的。

*   在 Twitter 上关注我们，了解区块链科技和加密货币的每日汇总。
*   订阅我们的[网站](https://elementgroup.com/blog/)，获取 Element Group 的所有专有研究、报告和专栏文章。
*   对 EOS 和以太坊的争论有什么想法？请在下面的评论区告诉我们！