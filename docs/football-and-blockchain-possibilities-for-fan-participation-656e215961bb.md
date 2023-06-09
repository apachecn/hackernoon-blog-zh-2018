# 足球和区块链:球迷参与的可能性？

> 原文：<https://medium.com/hackernoon/football-and-blockchain-possibilities-for-fan-participation-656e215961bb>

我的第二个激情，除了[区块链](https://hackernoon.com/tagged/blockchain) [科技](https://hackernoon.com/tagged/technology)，就是我当地的足球俱乐部。我不是巴塞罗那足球俱乐部或梅西魔术队这样的大型商业球队的超级粉丝。他们是伟大的球队，不要误会我，但对我来说，足球的魔力是完全不同的，它存在于看台上，球迷、俱乐部和球员之间的紧密联系，以及比任何事情都重要的是，我们都分享着对我们成长的传统的爱。

成立于 1906 年的 K Lierse SK 是比利时为数不多的保留原始传统的俱乐部之一，许多国际球员都是从我们的队伍中成长起来的(甚至包括罗梅卢·卢卡库)。

当我的球队在 1997 年赢得最后一次甲级联赛冠军时，我只有 6 岁，这标志着历史上最后一次小俱乐部赢得冠军。这些时刻没有人会忘记。直到今天，这一壮举仍被称为“狮子的奇迹”。虽然这是“结束的开始”,管理不善为外国投资者打开了大门，但我很难过地看到，21 年后，由于一个人的行为，我们的俱乐部再次处于财务破产和崩溃的边缘。

球迷们目前正试图想出一个计划来确保俱乐部继续存在。正因为如此，我想看看区块链在解决一个不仅仅是我们俱乐部面临的问题上可以发挥什么样的潜在作用。这个解决方案在下面一篇快速起草的小论文中有所描述，请享用！

![](img/18d35a438f1f6ca9c9f65b97a1a394e9.png)

Promotion from second division to first division in 2009–2010.

# 问题

本文提出了一种通过众筹和称为分散自治组织的流动组织模型来解决球迷参与足球俱乐部的方法。

众筹让小贡献者更容易投资大项目，同时让风险企业有可能获得过去可能无法获得的资金支持。另一方面，小投资者仍然容易受到财务管理不善、欺诈和缺乏控制的影响。合资企业中的小股份可能会导致缺乏发现问题或参与治理的权力。

包括足球俱乐部在内的公司实体受描述允许和禁止行为的规则管理。这些规则可能以私人合同、股东协议、法律规定、参与者之间的书面协议等形式存在。从历史上看，这些规则是由特定数量的参与者制定和控制的，而利益相关者群体通常远远大于管理层和股东。这导致了三个基本问题，(1)人们并不总是遵守规则，(2)对规则的要求往往没有共识，(3)利益相关者的利益可能不一致。组织内部的违规行为并不总是显而易见的，动机可能对利益相关者来说并不重要，直到为时已晚。我们提出了一个使用以太坊的解决方案，以太坊是一种类似比特币的区块链技术，它包括一种图灵完整编程语言，允许创建和执行智能合同，以与不可变的分布式账本进行交互。以太坊区块链允许创建分散的自治组织(Dao)来创建数字组织，而不需要集中管理。足球组织不是数字组织，但利益相关者可以通过数字方式进行治理。结合必要的章程，以数字化方式做出的决策可以具有法律约束力(例如，在不遵守 DAO 决策的情况下替换中央管理层)。

# 区块链

区块链是一种电子解决方案，可以不可逆地进行存储在账本中的交易。网络中的每个节点都有该分类帐的副本，以检查交易是否有效。当进行新的事务时，只有当足够多的节点认为它有效时，它才被添加到区块链中。最常见的用例是一种电子现金。区块链允许我们与互不信任的各方合作，或者在尊重参与者隐私的情况下不需要第三方。

![](img/ade1c59ab2f137e47c71459375af20a2.png)

# 以太坊

以太坊被一些人定义为区块链 2.0，它允许在双方同意的合同条件得到满足时进行不可逆的交易。这些条件被称为“智能合同”，它们是用一种叫做 Solidity 的编程语言编写的。区块链保证合同的正确性和有效性，并作为通过合同执行的交易的见证人。

# 分散自治组织——概念

DAO“契约”的代码(cfr。计算机程序)是用以太坊区块链的实体编程语言编写的。它是通过将合同部署到区块链上来激活的。代码一旦被部署，就不能被更改，这引入了一个核心概念:“代码就是法律”。

以太坊区块链要求“以太”参与交易。以太是驱动网络的数字货币。当 DAO 被部署时，以太网可以被发送到智能合同位于区块链上的地址。为了交换以太网，合同将创建分配给以太网发送者账户的令牌。这些代币授予所有者投票权和所有权，与以太中发送的数量和代币的最终总量成比例。这个阶段被称为“众筹阶段”，其持续时间和融资目标在合同代码中定义。

这就是道的核心功能。除了存储以太和发送令牌，它本身不能做任何事情。它不能发起投票，让球员转会或建立新的青年基础设施。为了完成任何事情，道需要“提议”。这些建议可以使用道的以太来支付任何费用。任何拥有 DAO 所有权令牌的人都可以提交提案。为了接受/拒绝提案，其余的
所有者可以在预定的时间范围内投票。投票是根据每个投票人控制的代币数量进行加权的。在这段时间过后，任何成员都可以验证结果。提议可以采取任何形式:球员转会，职员职位任命，消费价格，装备设计等等。一旦提案被接受，指定的资金将被发送到为其创建的智能合同。根据这份智能合同，指定的支出者(很可能是俱乐部管理层)可以根据提案使用资金。由于区块链技术的不变性和透明性，资金流动是完全可审计的。

# 代币

所有权令牌是可分割、可转让和可追踪的。参与者可以在交易所自由买卖代币，但是他们不能从道中提取代币。为了让俱乐部始终向感兴趣的参与者开放接收新资金，DAO 将在众卖阶段结束后继续发行代币以换取“以太”。然而，就代币/乙醚而言，汇率将显著降低。这反映了这样一种假设，即早期参与者承担了更大的风险，对最终的成功掌握的信息更少。

为了进一步奖励投资者，代币持有者在购买俱乐部相关商品时可以享受折扣，如季票、门票、球迷用品或食品和饮料。这些购买是通过以太(或一个单独的令牌，以确保价格稳定)进行的，以太会立即流回 DAO。

# 反击单一多数攻击

单一多数攻击是指一个参与者或一小群参与者能够获得多数票，从而通过他们选择的任何提案。在传统的 DAO 结构中，攻击者因此可以提议将所有资金发送到他的地址并批准该提议。就一个足球俱乐部而言，一个攻击者可以投票让自己成为经理。有一些技术方法可以解决这个问题，例如:
●限制一个参与者可以持有的令牌数量
●限制一个参与者在众筹阶段可以购买的令牌数量
●如果一个参与者持有的票数超过接受提案所需的票数，法定人数设置为参与者加权票数+ 1
●无论持有多少令牌，投票权重都不能超过 49.9%。

由于没有球迷，足球俱乐部什么都不是，我们提出了一个“黄金令牌”形式的额外/替代解决方案。黄金令牌是等同于传统黄金份额的数字令牌。金股是一种名义上的股票，在特定的情况下，它的票数可以超过所有其他股票。黄金股是一个有争议的法律话题，但当它符合利益相关者的共同利益时，它是可以接受的。

在荷兰足球中，一些俱乐部实施了一种形式的黄金份额，在这里黄金份额并不代表实际份额，而是一种否决权，可以由球迷俱乐部对俱乐部敏感的决定，如颜色、盾牌、地面等。在这个概念中，被一般 DAO 接受的提案将被转发到一个单独的 DAO，在该 DAO 中，只有“黄金令牌”的持有者可以投票。每一票的权重相等，只有 50%+1

接受提议需要多数票。购买季票时会自动获得一个金色令牌，该令牌将在赛季结束后过期。

# 结论

虽然区块链技术仍处于起步阶段，但它的许多特点似乎很有价值，可以以透明和公平的方式提高球迷的决策能力，同时为俱乐部提供筹集资金的机会。这种替代模式可能是当前环境中的一个积极变化，在当前环境中，俱乐部由外国资本所有，其利益往往与俱乐部周围社区的利益大相径庭。

缺点是，就决策而言，所提出的模型并不是最敏捷或最隐秘的(当需要时)。危机情况需要快速决策，而许多参与者的投票过程并不需要。因此，必须保留某种形式的中央控制。