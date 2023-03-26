# 构建超级总账解决方案——需要牢记的事项

> 原文：<https://medium.com/hackernoon/architecting-a-hyperledger-solution-things-to-keep-in-mind-78033e6fee75>

![](img/9100953b85609c040c1a9e15a1d17305.png)

Architecting a Hyperledger Solution

在撰写本文时，没有太多的资源可用于派生节点和构建 Hypderledger Fabric 解决方案。我希望这篇文章能让设计企业级 hyperledger fabric 解决方案变得更加清晰

在设计 hyperledger 解决方案时，您必须牢记以下事项——

1.Fabric 具有高度模块化和可配置的[架构](https://hackernoon.com/tagged/architecture)，适用于广泛的行业用例，包括银行、金融、保险、医疗保健、人力资源和供应链。

结构平台也是允许的。与参与者相互认识的公共无权限网络不同，fabric 是匿名的，因此完全不受信任。这意味着，虽然参与者可能不完全信任彼此(例如，他们可能是同一行业的竞争对手)，但网络可以在一种治理模式下运行，这种模式建立在参与者之间确实存在的信任基础上，如法律协议或处理争议的框架。

Fabric 使用共识协议，不需要任何加密货币或挖掘来形成区块或执行智能合同。它支持可插拔的共识协议，即您可以在 hyperledger fabric 中更改共识协议。这允许针对特定用例或需求定制解决方案。例如，当部署在单个企业内或由可信机构操作时，完全拜占庭式的容错共识可能会被认为是对性能和吞吐量的不必要的过载。在这种情况下，崩溃容错(CFT)共识协议可能更合适，而在多方、分散的用例中，可能需要更传统的拜占庭容错(BFT)共识协议。

2.“节点”只是一种逻辑功能，因为不同类型的多个节点可以在同一台物理服务器上运行。重要的是节点如何在“信任域”中分组，以及如何与控制它们的逻辑实体相关联。

根据 hyperledger 文档，有三种类型的节点:

1.客户端或提交客户端 **:** 向背书者提交实际事务调用，并向订购服务广播事务提议的客户端。

2.对等:提交事务并维护分类帐的状态和副本的节点。此外，对等体可以有一个特殊的签署者角色(不需要一个单独的节点)

3.订购服务节点或订购方:运行通信服务的节点，实现交付保证，如原子或总订单广播。

交易的验证通过链代码的复制执行来发生，并且给定 BFT 共识所基于的错误假设，即，在企业的 n 个验证对等体中最多 f < n/3 may “lie” and behave arbitrarily, but all others execute the chaincode correctly. When executed on top of PBFT consensus, it is important that chaincode transactions are deterministic, otherwise the state of the peers might diverge.

Apart from the above mentioned nodes, you may also want to consider a node for certificate authority (CA). As the fabric implements a permissioned ledger, it contains a security infrastructure for authentication and authorization. For connecting to the network every peer needs to obtain an enrolment certificate from an enrolment CA that is part of the membership services. It authorizes a peer to connect to the network and to acquire transaction certificates, which are needed to submit transactions.

Other things to keep in mind while architecting any [个区块链](https://hackernoon.com/tagged/blockchain)个解决方案

1.区块链网络的参与者必须是可识别的

2.需要对网络进行许可，以便没有未经授权或匿名的用户能够加入网络

3.交易确认时间不应有太多延迟。延迟应该很低

4.特定时间内可以处理的事务数量应该很高。高吞吐量是必须的。您可能需要对贵组织的预期交易量进行更多研究，然后将其与您计划使用的区块链平台的性能或 TPS 进行比较

5.交易的保密性和隐私性对企业来说至关重要。数据必须保密，并且只允许目标受众访问

在设计区块链解决方案时，记住以上几点将有助于您改进决策(在所有权、安全性、节点、成本方面)。太好了！干杯！

请随时在 [Linkedin](https://www.linkedin.com/in/pranjal-baweja/) 和 [Instagram](https://www.instagram.com/h3ypb/) 上联系我