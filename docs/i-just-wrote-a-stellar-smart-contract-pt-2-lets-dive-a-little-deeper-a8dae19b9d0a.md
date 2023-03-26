# 我刚刚写了一个恒星智能合同 Pt。2:让我们再深入一点

> 原文：<https://medium.com/hackernoon/i-just-wrote-a-stellar-smart-contract-pt-2-lets-dive-a-little-deeper-a8dae19b9d0a>

![](img/18de52a0533a58277f4d6face2f26ca7.png)

一个多月前，我写了这个多部分系列的第 1 部分，详细描述了我进入恒星智能合约(SSC)世界的旅程。

从那以后，我收到了一些美妙的[反馈](https://hackernoon.com/tagged/feedback)，赞美、怀疑、担忧等等。

这些担心很多都是有根据的，今天，有了完全自由的时间表，一杯热咖啡，一台充满电的电脑，我坐下来，期待着建立我在 pt 中描述的基础。1.

# 概念

我将首先定义几个基本概念，以便我们都在同一页上。如果您已经熟悉 SSC 的基本构建模块，请随意跳过这一部分。

## 序列号:

每个账户都有一个[序列号](https://www.stellar.org/developers/guides/concepts/transactions.html#sequence-number)，用于定义该账户在恒星[网络](https://hackernoon.com/tagged/network)内的交易顺序。每当一个账户建立一笔交易时，它就给那笔交易一个序列号。这个数字必须严格递增—如果帐户 A 的序列号为 1，则帐户 A 的唯一有效交易是序列号为 2 的交易。

## 操作:

在恒星网络中定义了 11 个(即将成为 12 个)[运算](https://www.stellar.org/developers/guides/concepts/list-of-operations.html)。一个操作被定义为:*一个单独的命令，使分类帐发生变异。*最常见的操作是 [**支付**](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#payment) 和 [**创建账户**](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#create-account) 还有许多其他有用的操作，如 [**设置选项**](https://www.stellar.org/developers/guides/concepts/list-of-operations.html#set-options) 允许您更改账户的各种设置。

## 阈值:

每个操作都有一个相关的[阈值](https://www.stellar.org/developers/guides/concepts/multi-sig.html#thresholds) —低、中、高。这定义了执行操作所需的签名权重。

## 签名人:

在 Stellar 中，每个帐户都有一个签名者列表，每个签名者都有一个相关的权重。为了使操作有效，添加到事务中的所有签名者的权重必须大于或等于阈值权重。例如，如果阈值设置为 2，并且帐户上的每个签名者的权重为 1，则我们需要至少 2 个这样的签名才能使该事务有效。

签名者可以轻松地添加和删除，允许创建 n-of-m [多重签名账户](https://www.stellar.org/developers/guides/concepts/multi-sig.html)。

有三种不同类型的签名者可以添加到帐户中——帐户 id(公钥)、[预授权交易](https://www.stellar.org/developers/guides/concepts/multi-sig.html#pre-authorized-transaction)和[哈希(X)](https://www.stellar.org/developers/guides/concepts/multi-sig.html) 。在本文中，我们将忽略 Hash(X)

# 向我们的工具箱添加新工具

> 技术什么都不是。重要的是你对人们有信心，他们基本上是善良和聪明的，如果你给他们工具，他们会用它们做很棒的事情。—史蒂夫·乔布斯

所以，是的，本着史蒂夫·乔布斯和创新的精神，让我们大胆尝试，开始使用 Stellar 的一些鲜为人知的功能。

## 预授权交易:

预授权交易就像它听起来的那样——它是一个已经被构造和签名的交易，允许该交易的持有者在他/她感觉到的任何时候将其发布到网络(但是，不要忘记序列号必须仍然有效)。酷吗？没有吗？

越来越好了。

此预授权交易可以作为签名人添加到帐户中。如果给这个签名赋予了正确的权重，这允许您在没有任何其他签名的情况下广播一个事务。这里有一个例子:

```
**Account A**
 *balance:* *9*
 *sequence #:* *3*
 *low threshold:* *1*
 *med threshold:* *1*
   *high threshold:* *1***TX A:**
*Sequence #5*
*Pay B 5 XLM
(NO SIGNATURE NEEDED)***TX B:**
*Sequence #4*
*Set Options:*
    *Signer:* HASH of TX A
    *Signer_weight:* 1Now:
1) Account A submits transaction B
2) Account A, or anyone with transaction A, can submit transaction A without needing an additional signature
```

*注*:交易 B 提交后，将作为签名人从账户 a 中删除。

还不酷吗？

别担心，这个工具的用处很快就会显而易见:)

# 改进了我之前的例子

让我们将这些知识应用到上次中[的例子中。作为一个快速提醒，下面是我希望解决的**问题**的概述:](/@robdurst/i-just-wrote-a-stellar-smart-contract-7f54a391f5e1)

```
Consider the user A who seeks to pay B,C,D for a service. If the service succeeds, B,C, and D are paid. If the service fails, only B and C are paid. B is a trusted party, capable of determining whether the service succeeds or fails.
```

这是上次的解决方案:

```
**Account A**
   balance:    9 XLM
   sequence #: 3A constructs two transactions:***TX A:*** Sequence #4 Pay B 3 XLM
pay C 3 XLM
Pay D 3 XLM***TX B:*** Sequence #4
Pay B 3 XLM
pay C 3 XLMA signs both these transactions and gives them to B. Once the service is finished, B submits **TX A** or **TX B** depending on the success of the service.
```

然而，请注意我在前面的例子中没有提到的一个主要缺陷(感谢 Alexander 的评论):

> Alice[上例中的 A]可以发出另一个增加序列号的事务

不幸的是，她可以。

那么，我们怎样才能*锁定*这些资金，并防止爱丽丝花掉她*留出来*用来支付 B、C 和/或 D 的钱呢？

为此，我们将使用我们的新工具:*预授权交易*。

让我们直接开始吧。

# 设置:

```
A creates an account E with her 9 XLM and sets all thresholds to 1.**Account E**
   balance: 9 XLM
   sequence #: 1
   low threshold: 1
   med threshold: 1
   high threshold: 1B constructs two transactions, just like A did last time, except this time B will set the sequence number to be sequence number+2:***TX A:*** Sequence #3 Pay B 3 XLM
pay C 3 XLM
Pay D 3 XLM***TX B:*** Sequence #3
Pay B 3 XLM
pay C 3 XLMA will construct a third transaction with the hashes of the transactions given to her by B:***TX SETUP:*** Sequence #2
Master Key weight: 0
Add **TX A HASH** as a signer w/ weight: 1
Add **TX B HASH** as a signer w/ weight: 1A signs **TX SETUP** with Master Key E and submits it to the network.
```

哇，哇，哇，刚刚发生了什么？！？！

我们现在的设置基本上和以前一样，但是这一次，9 个 XLM 被锁定了——A 无法访问她的资金。更重要的是，因为 A 不能访问账户 E，所以保证 **TX A** 和 **TX B** 都是有效的，直到其中一个被花掉。另外，由于 B 构建了两个事务，并且只将散列发送给 A，因此只有 B 有权提交最终的事务。

根据 pt 上的一条评论。1(感谢哈维):

> 如果您对 Bob 何时可以发送事务 A 和事务 B(例如，30 天后，房子应该建好)的那些操作实施时间限制，这将会很有趣。

a 可以构造第三个交易， **TX C** ，如果 B 在某个特定日期之前没有提交任何一个交易，这将退还她 9 XLM。以下是经过编辑的设置:

```
A creates an account E with her 9 XLM and sets all thresholds to 1.**Account E**
   balance: 9 XLM
   sequence #: 1
   low threshold: 1
   med threshold: 1
   high threshold: 1B constructs two transactions, just like A did the last time, except this time B will set the sequence number to be sequence number+2:***TX A:*** Sequence #3 Pay B 3 XLM
pay C 3 XLM
Pay D 3 XLM***TX B:*** Sequence #3
Pay B 3 XLM
pay C 3 XLMA will construct a third, refund, transaction that is only valid after X days:***TX C:*** Sequence #3
Pay A 9 XLM
[Time bound](http://TX B: Sequence #3 Pay B 3 XLM pay C 3 XLM): X daysA will construct a fourth transaction with the hashes of the transactions given to her from B and from her own refund transaction:***TX SETUP:*** Sequence #2
Master Key weight: 0
Add **TX A HASH** as a signer w/ weight: 1
Add **TX B HASH** as a signer w/ weight: 1
Add **TX C HASH** as a signer w/ weight: 1A signs **TX SETUP** with Master Key E and submits it to the network.
```

# 结论

那么信任在哪里呢？

*   我们必须相信 B 会做出正确的判断

是的，大概就是这样…

考虑以太坊，NEO，EOS 等的替代品。在某些时候，您需要将信任引入到系统中——必须有人对服务是否已经完成做出最终判断。

因此，对于像这样简单的二元操作，我们基于可观察的(但不一定是可编程观察的)结果来分配支付，我们可以使用恒星智能合约。

所以嘣！你现在有了一个相当复杂的智能契约，可以用 JavaScript、Go、C#、Ruby、Python、Scala、Swift 和(即将推出的)Rust 实现，可以部署到一个可负担的 0.00001 XLM/操作。

代码:[这里的](https://github.com/robertDurst/stellar-examples-in-js/blob/master/preAuthTx.js)是用 JavaScript 编写的添加预授权交易作为签名人的基本例子。

*免责声明:虽然我是 Stellar.org 的现任雇员，但这些观点仅代表我个人，并不代表 SDF 的观点。也就是说，这些结论是我在空闲时间做实验时得出的，是基于真实代码而不是个人偏见。*

*免责声明继续:虽然我袖手旁观我的话，我是一个新手智能合同程序员，我的假设可能是不正确的。如果是这样，请在下面评论——我一直在寻求反馈。*