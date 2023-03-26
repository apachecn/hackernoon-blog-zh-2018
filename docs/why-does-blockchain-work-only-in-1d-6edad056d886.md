# 为什么区块链只在 1D 工作？

> 原文：<https://medium.com/hackernoon/why-does-blockchain-work-only-in-1d-6edad056d886>

![](img/8a1283a7276dc4d5fcb5e8ec0dd86509.png)

It is pretty interesting to think why current blockchain has to work linearly.

在我看来，我相信这是区块链可以扩展其功能的地方。

区块链给了安全和信任，但我认为它也需要两样东西:

1.  用户数据与其他数据混合在一个块中；ze 必须遍历整个链数据来找到 UTXOs 以获得平衡
2.  元数据的内存太多。它们被过多地用于将所有用户的信息放在一个链表中。例如，[比特币](https://hackernoon.com/tagged/bitcoin)在键值存储中存储带 hash 的交易数据，在块数据中存储那些键。不仅如此，事务是用 merkle-tree 形成的，因为块需要比较而不需要经过每个用户的事务，所以增加了额外的元数据。

发生这些问题是因为所有用户的信息都混合在一个区块链中。为什么人们不能拥有自己的区块链，这样他们就可以拥有自己的信息，并且只在发送者和接收者之间的交易中得到验证？这是比目前的区块链系统更加分散的方式。