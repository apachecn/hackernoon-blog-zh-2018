# 合同中的随机数生成器

> 原文：<https://medium.com/hackernoon/random-number-generator-in-contracts-44abfe48240d>

## Ardor 轻量级契约有一个快速安全的随机数生成方法。

![](img/6bbc9a2abdbc4b2320004b027e4ac8c7.png)

Random Number Generator in Contracts. Image Credit: Nick Hillier from Unsplash

[基于区块链](https://hackernoon.com/tagged/blockchain)的合约往往需要[生成](https://hackernoon.com/tagged/generate)随机数，用于游戏、赌博、模拟等用例。

此外，这些随机数必须是**不可预测的**，这样开发者、用户和块生成器就不能提前预测它们并利用这些知识来获利。

能够生成可再现不可预测随机数的关键是:

1.  所有节点应该使用相同的随机数生成器，只要所有节点使用相同的运行时引擎，如 JVM 或 EVM，这应该很简单。
2.  所有节点应该同意随机种子；用于初始化随机数生成器的唯一数字。只要所有节点同意相同的种子，所有节点将生成相同的可再现的随机数序列。
3.  种子不应该是简单的预先预测。如果用户可以预测种子，他们可以调整他们的行动，使合同输出有利于他们的事业。例如，预测轮盘赌游戏中所选的号码。

在正常操作期间，区块链生成的数据有点像块 id 或事务 id 一样随机。问题是提交交易的帐户很容易操纵交易 id，而块生成器也很容易操纵块 id。这意味着单独使用该信息来初始化随机种子将为超级用户和阻塞生成器提供不公平的优势。

正如我们所见，由于您不能直接依赖区块链中生成的数据，因此生成这种可重复且不可预测的种子并不是一项简单的任务。

你可能会问，我们可以使用某种 Oracle 契约从外部来源获得随机数据吗(比如[random.org](https://www.random.org/))？当然，我们可以，但这打开了其他攻击媒介，如 DNS 欺骗或潜在的对 random.org 的直接攻击。此外，将这些数据写入区块链供其他合同使用的成本也很高。考虑到这一点，谁在为甲骨文合同的运作提供资金，他们的动机是什么？

以太坊团队推广的另一种生成随机数的方法是 [Randao](https://github.com/randao/randao) 。这个想法是使用一个两阶段的方法和一个复杂的激励结构来补偿在区块链上注册随机数据的用户。这个想法缓慢而昂贵，当然我们可以做得更好。

**介绍 Ardor 轻量级合同随机种子生成**

快进到轻量级合同。在 Ardor 平台中，我们可以使用轻量级契约特有的两个属性。

第一，只有特定的节点执行契约。这为我们提供了其他协议无法获得的随机数据的唯一来源，即契约运行者节点本身。

其次，发送加密信息的能力是 Ardor 区块链固有的。这使得用户可以直接向签约跑步者账户提供随机数据，而无需向其他任何人透露。

给定这些属性，契约现在可以从三个不同的独立来源组成随机种子:

1.  契约运行者随机种子，安全地存储在私有节点配置中。
2.  触发交易加密种子，作为加密消息附加到交易，只有合同运行人帐户可以解密。
3.  只有块生成器可以控制的块 id。

契约运行器将这三个随机源连接并散列在一起，以生成可再现的种子。这个种子很难预测，因为提交交易的帐户和块生成器需要与契约运行者合作和勾结。块生成器和触发契约的账户不能单独预测随机数。

但是和轻量级契约一样，我们仍然需要相信契约运行者不会欺骗，为了验证这一点，契约运行者必须在执行契约之前与验证器节点安全地共享其秘密随机种子。然后，验证者可以重复种子计算，并验证合同运行者确实执行了存储在区块链中的合同，并根据协议计算了随机种子。在这种情况下，多重签名设置提供了额外的保护，其中验证者帐户需要批准合同运行者提交的每一笔交易。

轻量级契约为生成可再现的不可预测的随机数这一复杂问题提供了一个简单而实用的解决方案。

热情地了解更多关于轻量级合同的信息。