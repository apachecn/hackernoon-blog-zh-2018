# 协议聚焦:MimbleWimble 和 Grin

> 原文：<https://medium.com/hackernoon/project-spotlight-mimblewimble-and-grin-48c31ecfce4>

![](img/db5b4c138c2ce8cf50981351eee4e513.png)

**如果你喜欢这篇文章，那么请关注我**[**@ flat outcrypto**](https://twitter.com/flatoutcrypto)**。你可以在**[**flatoutcrypto.com**](http://flatoutcrypto.com)**上找到我所有的文章。**

继续我的系列，分解一些新的或鲜为人知的协议，现在注意力转向光荣命名的 MimbleWimble。

人们很容易厌倦加密，但当我偶然发现这个项目的一些核心原则时，我知道我会喜欢研究 mimble:

*   没有 ico
*   没有奖金
*   没有 instamine
*   没有采矿税
*   没有主节点
*   没有治理
*   没有垃圾邮件的空间

![](img/c970b0b7d20c973206ada707caf6fcbf.png)

然后我在一次会议上看到一位开发者的评论:

> “如果你是一个开发者，直接贡献就好。如果你是风投，我们不能提供任何 ICO 或预挖掘或类似的东西。”

![](img/e1dc79ff9491f196d8f007551bb7742e.png)

> “社区支付我的薪水…没有 ico，没有支持者，没有风险投资”

![](img/5f8704897c4f921abd4c6b74e8c1a7ab.png)

通常的免责声明适用于:

*   我将解释 mimble 的关键概念，但不会详细介绍它是如何工作的——白皮书就是为了这个目的。因此，一些复杂的、例外的和较小的方面，需要很多单词，但影响很小，将被省略
*   我将避免证实或反驳团队的说法。我只是向那些没有兴趣或技术背景的人解释这个团队正在努力做的事情
*   这将不会像[雪崩](https://hackernoon.com/protocol-spotlight-avalanche-3f5dfd366a26)、[迈德赛夫](/@flatoutcrypto/project-spotlight-maidsafe-and-parsec-part-1-4830cec8d9e3)或[根茎](https://hackernoon.com/project-spotlight-rootstock-6bc1144b835b)那样深入——我会在协议发布前做更深入的更新

与我看过的第一批深入细节的其他项目不同，mimble 受益于现有的良好而简洁的解释。

我在这篇文章的底部引用了我使用的资源列表，但应该注意的是，我可以互换地引用匿名的汤姆·埃尔维斯·杰杜索的[原始白皮书](https://github.com/mimblewimble/docs/wiki/MimbleWimble-Origin)(我们在这里从[口袋妖怪](https://hackernoon.com/protocol-spotlight-avalanche-3f5dfd366a26)引用雪崩到哈利·波特)、安德鲁·波尔斯特拉的[继任者论文](https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf)以及同样匿名的 Ignotus Peverell 在 Github 上的[文档。我还参考了来自 Grin(mimble wimble 的第一批实现之一)开发人员和参考资料的工作，尽管我尽可能尝试区分这两者。这样一来…](https://github.com/mimblewimble/grin/blob/master/doc/intro.md)

# **什么是 MimbleWimble？**

MimbleWimble 可以被认为是一个精简的但仍然强大的区块链协议，旨在提高现有实现的可伸缩性和隐私性。有趣的是，它以不同于许多其他协议的方式处理可伸缩性。它的重点不是集中(主节点、协调器)、第 1 层(分片)或第 2 层解决方案(状态通道)，而是创建一个新的、精简的协议。这使得运行一个节点不那么耗费资源。

它还声称改善了比特币的隐私属性。通常情况下，我会抱怨另一个项目如何击败十年前的比特币，但在 MimbleWimble 的案例中，它更有意义。它拥有与比特币早期相似的特征和愿望，并公开宣称是一个“设计一种类似比特币的加密货币”的项目，只是一种具有更高隐私性和可扩展性的加密货币。

匿名创始人、缺乏风险投资支持或 ICO、社区发展都让人想起最初的加密货币，但更多的实施特定决策也是如此，如回归失宠的 PoW，但旨在更好地抵制集中化，有意识地决定不进行链上治理(一组漂亮的词)和长期排放率。

# **它试图解决什么问题？**

所以 MimbleWimble 正在解决隐私和扩展问题。但是他们具体想做什么呢？

## 缩放比例

与其他以巨大吞吐量为目标的项目(令人惊讶的是有多少项目承诺每秒 100 万个事务)不同，mimble 攻击的是伸缩性中一个较少涉及的方面。它选择通过删除多余数据的[区块链](https://hackernoon.com/tagged/blockchain)来根除低效，而不是增加权力。

要运行一个比特币节点，你必须下载整个账本，目前大约有 175GB。与此同时，以太坊账本现在超过 1TB。每一个新的事务都会占用更多的空间。然而，mimble 去掉了旧的和不需要的事务。他们这么说是什么意思？

假设我和鲍勃做了笔交易。Bob 然后与 Alice 进行交易，Alice 又与 Carol 进行交易，Carol 又与 David 进行交易。最终的结果就是我原来的输出现在到了大卫手里。那么，为什么要保留额外的数据呢？MimbleWimble 通过将中间事务组合在一起来消除这些无用的输出。这些交易的授权仍然存在(我们需要这一点，因为否则交易可能被撤销)，但这些现在多余的交易的证据丢失了。

真正聪明的是，这使得网络能够继续运行，“即使没有用户保留绝大多数的区块链历史数据”。因此，MimbleWimble 不会因为包含历史事务的数据而负担过重(尽管必须为每个事务存储一个 100 字节的小“内核”,这意味着早期将区块链减少到仅兆字节的愿望现在可能不再实现)。

这种方法的成功之处在于，我们确切知道现在和将来有多少硬币，就像比特币有 2100 万的固定供应量一样。永远不会超过最大供应量。没有通货膨胀。没有人可以随意印刷更多的比特币。因此，对于每项未用产出，都将有一条通向未用产出的交易路径。

所有数据的总和必须为 0，这也是 MimbleWimble 所做的事情——它验证每笔交易的输出减去输入的总和等于 0，例如，我发送了 1 个 BTC，收到了 1 个 BTC。我没有创建任何新基金。这就是事务检查的全部内容。所有权是由用户的致盲因子来证明的(下一节将详细介绍)。

这种缩放解决方案很重要，因为它是可持续的；我们将总是能够总和为零，从而移除不必要的事务，将区块链保持在可管理的规模。

## 隐私

![](img/baabdade5f2f9e4cf44739798a74c01c.png)

我喜欢 mimble 的一点是，它以不同于其他项目的方式解决问题，这延伸到了隐私领域。大多数隐私解决方案使用类似于[零知识证明](https://hackernoon.com/eli5-zero-knowledge-proof-78a276db9eff)或[环签名](https://getmonero.org/resources/moneropedia/ringsignatures.html)的东西来混淆交易。MimbleWimble 和 Grin 的做法略有不同。

**1。无地址**

鉴于其他加密资产的工作方式，这很难理解，所有的加密资产(除非我健忘)都依赖地址来发送/接收。我复制我的比特币地址，给寄件人，他们寄到我的地址。Grin 的构造不同。最好的理解是，两个钱包可以互相通信来交换数据。然后，接收方为发送方创建一个发送资金的目的地，但是该信息只有双方才能看到。这并不要求双方同时在线，实际上可以通过任何媒介进行交互，尽管这可能不切实际(电子邮件、电话等)。

没有其他人能够重复使用这些信息。它总是需要双方的投入。

**2。无交易明细**

他们也看不到交易的任何细节。除非您是参与者之一，否则任何给定块中的交易都不会被您识别。不像 Monero，如果你查看一个块，你不会看到该块中的交易列表。您将会看到一个混合并合并了所有内容的大事务，剩下的就是“[新输入列表](https://bitcoinmagazine.com/articles/mimblewimble-how-a-stripped-down-version-of-bitcoin-could-improve-privacy-fungibility-and-scalability-all-at-once-1471038001/)、新输出列表和使用上述伪输出创建的加密签名列表。”

这意味着不可能识别在任何给定的块中实际发生了什么，也就是说，你无法判断何时发生了交易，何时用户发送到了另一个用途。它对你来说是显而易见的。

**3。保密交易和致盲因素**

虽然以上几点可能意味着任何人都不可能跟踪历史交易，但它使交易在它们的源头对任何监视网络的人开放。我们要做的是确保没人能看到产量。

为了做到这一点，mimble 使用了一个“盲因子”，它实际上是用户的私钥。这是一个非常大的数字，用于混淆交易值和证明所有权。我不打算详细说明(如果你想了解更多，请阅读[这个](https://github.com/mimblewimble/grin/blob/master/doc/intro.md#balance)),但本质上，这个致盲因素允许网络在不知道任何值的情况下验证交易。

这个过程来源于[保密交易](https://bitcoinmagazine.com/articles/confidential-transactions-how-hiding-transaction-amounts-increases-bitcoin-privacy-1464892525/)，一个已经存在的发明。它们是为比特币和其他加密资产开发和提出的，本质上是为了让发送者和接收者可以看到交易的金额，而其他人只是看到未知金额的交易。

这种实现的传统弱点包括:

*   地址仍然可见
*   如果未来的交易没有被隐藏，那么就有可能追溯出原始交易的金额
*   它们增加了交易的规模(尽管随着开发工作的进行，交易的规模在下降)，使得它们很难在大多数区块链中实现

然而，由于没有任何地址或交易细节，机密交易意味着查看 mimble 区块链的人将无法看到发送者或接收者的任何细节，在任何时间点交易的任何金额，甚至无法识别交易何时发生。

**4。可替代性**

这不是 MimbleWimble 正在解决的问题，而是由 MimbleWimble 解决的问题。

比特币的一个问题是,“肮脏的”BTC 可以被追踪。比方说，有人黑了一家交易所，偷走了 BTC，但你却毫不知情地买入了 BTC 的股票。这些 BTC 将被标记为肮脏，就像从金库中被盗的钞票一样。您已经善意地购买了此 BTC，但您可能会面临使用限制。因此，尽管我们称 BTC 或以太网为可替换令牌(即，都一样)，但严格来说这并不正确。

然而，咧嘴笑会导致真正可替换代币。这是由于它在丢弃所有输入/输出和屏蔽所有事务之前如何验证事务(即求和为 0)的结果。

# 我还应该知道什么？

*   迈尔斯·施奈德(Myles Snider)写了一本关于货币政策的好书，我鼓励人们去读一读。斯奈德认为，格林的货币政策可能意味着鼓励早期持有者消费。八年后，与最大供应量成比例，在生命周期的同一阶段，Grin 将比 BTC 少 25%。Grin 称自己更适合作为“交换媒介”而不是价值储存手段，所以这是合适的
*   尽管 Grin 的本质使得冷存储/硬件钱包(如 Ledger Nano S)更难实现，但这并非不可能
*   Grin 没有内置的脚本语言，所以智能合约也更加困难。然而，Grin 可以使用一种叫做“无脚本脚本”的东西来实现类似的功能
*   这个项目的性质也使得原子交换变得更加困难，但是这也不是不可能的
*   还有另一个 mimble 实现叫做 [Beam](https://www.beam-mw.com/) 。有趣是，BEAM 在其立场文件中指出,“性能不够高，无法将 BEAM 用作“交换手段”。这就是为什么我们认为 BEAM 将主要用作“价值储存”显然这与格林的说法不一致
*   Igno Peverell 在这里概述了 Grin [即将到来的发展。如果没有别的，它显示了这个项目已经进行了多少](https://www.grin-forum.org/t/much-of-the-technology-behind-grin/127)

# **财务**

不适用，但如前所述，无 ICO 或 premine。

# **结论**

对 MimbleWimble 的这种看法应该清楚地表明，仍然有许多工作要做(上面的内容将在未来更新以反映发展)，但它也促进了一种有趣的方法来解决共同的问题，以不同的方式解决它们。

这是一个我期待在未来几个月和几年内跟进的项目，特别是考虑到团队在资金更充足的竞争中资源的不平衡。

**如果你喜欢这篇文章，那么请关注我**[**@ flatoucrypto**](https://twitter.com/flatoutcrypto)**。你可以在**[**flatoutcrypto.com**](http://flatoutcrypto.com)**上找到我所有的文章。**

# 杰拉德·巴特勒前五名

![](img/dba39c54be5f8ada39f4871b9c751cb3.png)

1.  守法公民
2.  罗克诺拉
3.  贼巢
4.  奥林匹斯已经沦陷了
5.  如何训练你的龙

# 资源

[https://github . com/MimbleWimble/docs/wiki/MimbleWimble-Origin](https://github.com/mimblewimble/docs/wiki/MimbleWimble-Origin)

[https://download . WP software . net/bit coin/wizardry/mimble wimble . pdf](https://download.wpsoftware.net/bitcoin/wizardry/mimblewimble.pdf)

[https://github . com/mimble wimble/grin/blob/master/doc/intro . MD](https://github.com/mimblewimble/grin/blob/master/doc/intro.md)

[http://DIY HPL . us/wiki/transcripts/SF-bit coin-meetup/2016-11-21-mimble wimble/](http://diyhpl.us/wiki/transcripts/sf-bitcoin-meetup/2016-11-21-mimblewimble/)

[](https://github.com/mimblewimble/grin/blob/master/doc/grin4bitcoiners.md) [## 颤抖/龇牙

### MimbleWimble 协议的最小实现。-模仿/咧嘴笑

github.com](https://github.com/mimblewimble/grin/blob/master/doc/grin4bitcoiners.md)