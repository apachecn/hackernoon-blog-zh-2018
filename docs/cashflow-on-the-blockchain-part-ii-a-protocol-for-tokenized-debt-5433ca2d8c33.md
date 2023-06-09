# 区块链上的现金流第二部分:令牌化债务协议

> 原文：<https://medium.com/hackernoon/cashflow-on-the-blockchain-part-ii-a-protocol-for-tokenized-debt-5433ca2d8c33>

![](img/9a4393043d26a5d1117b62d680e93fb0.png)

这是探索基于债务资产的安全令牌潜力系列文章的第二部分。在第一部分中，[在第一部分](https://hackernoon.com/cashflow-on-the-blockchain-part-i-tokenized-debt-and-security-tokens-18054dbf7dc)中，我们探讨了令牌化债务作为一种可以在短期内释放安全令牌潜力的产品的潜力。今天，我将深入研究基于债务的安全令牌的构建模块，并探索这种加密安全协议的动态。

基于债务的安全令牌激增的主要障碍是缺乏抽象债务工具生命周期的本地链上协议。虽然基于股权的证券令牌的行为类似于股票的半静态表示，但基于债务的加密证券在其生命周期中表现出更多的动态行为，如股息支付或违约。从这个角度来看，很明显，基于债务的安全令牌将需要新的协议，以区块链令牌的形式抽象这些特征。[正如本文第一部分所探讨的](https://hackernoon.com/cashflow-on-the-blockchain-part-i-tokenized-debt-and-security-tokens-18054dbf7dc)，像达摩这样的协议可以作为一个强大的灵感，但它们是为不同于加密证券的用例而设计的。加密证券债务协议的作用将是以链上智能合约的形式实现债务工具参与者之间的可编程交互。为了创建这样的协议，我们需要从理解不同类型的债务证券令牌及其相应的参与者开始。

# 债务证券凭证的形式

债务工具的生命周期可以通过三个主要角色之间的互动来抽象:

![](img/d8321c46dc04a64e351d38a761128b3f.png)

在证券代币的情况下，债务工具可以以两种主要形式实现，这取决于承销流程是在链上还是在链外发生:

**令牌化的链外债务:**发行代表诸如债券或房地产租赁等链外债务工具的链内令牌。

**本地链上债务:**从承销到到期的整个生命周期都发生在链上的债务凭证。

由于显而易见的原因，我们应该预计第一波债务证券令牌将是链外债务产品的令牌化表示，并且协议应该逐渐发展到支持 100%链内债务的创建。

无论我们是将链外债务工具令牌化，还是为发行债务创建链上基础设施，我们都需要一个封装债务交互主要组件的协议。

# 为债务证券令牌设计协议

最简单的形式是，债务证券代币协议需要包含管理债务工具生命周期的元素。借用债券理论中的一些概念，我们可以使用以下元素构建基于债务的证券令牌:

**到期日:**每个基于债务的证券令牌都应该有一个到期日，该到期日决定了基础债务到期的日期，债务令牌发行人将向债务令牌持有人支付令牌的面值。

**面值:**债基证券代币的面值是标的债务到期时的价值。每个债务代币持有者将在到期日收到与其持有量成比例的面值的一部分。

**利率:**利率是债务代币发行者在代币生命周期内向债务代币持有人支付的名义金额。利率可以根据通货膨胀水平进行调整。

**频率:**利率支付需要根据特定的频率定期发放。

**评级:**债务令牌评级是描述债务令牌的质量或风险的值。通常，评级越高，利率越低。

**收益率:**这是债务凭证持有人在到期日可以预期获得的总回报。

将前面的组件放在安全令牌的上下文中，我们可以为基于债务的加密安全提出一个相对简单的结构。

![](img/7e6d4f5b5222249431653ef8dce3a834.png)

债务证券代币的协议需要超越代币结构本身，并对债务工具中不同参与者之间的交互进行建模。概括地说，该类型的协议中应包含以下角色:

![](img/fa04f8c49490eb8cd4584df5e9ea4462.png)

**债务代币发行者:**创建代币化债务工具的实体。

**债务代币持有者:**拥有债务代币的实体。

**支付分销商:**为债务代币持有者分配利率和收益支付的实体。

**评级机构:**发布与特定债务证券令牌相关的质量或风险评级的实体。

**仲裁者:**实体负责解决债务凭证发行人和持有人之间的违约纠纷。

让我们在一个示例的上下文中看看这些组件的作用。假设一家名为 **IssuerA** 的现金贷款公司将一笔面值为 1000 美元、季度利息为 10%、到期日为 5 年的贷款进行了证券化。代表信用机构的**评级机构**将贷款评级为 AAA 级，表明基础资产的高质量。 **IssuerA** 将分发 10，000 个代币，每个价值 1 美元，代表 10 笔基础贷款。两个人 **HolderA** 和 **HolderB** 每人购买一至 1000 个代币。在第一季度末，协议将使用**支付分配器**向**持有者 A** 和**持有者 B** 支付 100 美元。如果**发行人**未能支付后续款项，债务合同将被锁定并发送至**仲裁人**进行解决。

我知道这个例子过于简单，但我希望它传达了安全令牌债务协议背后的一些主要思想。

安全令牌中的令牌化债务的第一种形式可能非常简单并且受到限制，以避免不良行为并测试概念的基础。随着这一领域的发展，我们可能会看到新形式的债务证券代币，它们模拟了今天不可能创造的债务工具。这将是未来文章的主题。