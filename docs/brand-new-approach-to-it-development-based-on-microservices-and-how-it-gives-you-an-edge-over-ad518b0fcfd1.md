# 全新的 IT 开发方法，以及它如何让您超越竞争对手

> 原文：<https://medium.com/hackernoon/brand-new-approach-to-it-development-based-on-microservices-and-how-it-gives-you-an-edge-over-ad518b0fcfd1>

![](img/19824693a8c2e2fb08bdb092f04b16c7.png)

还有什么比您的应用程序运行缓慢更令人恼火的吗？在大多数人已经习惯了快速响应、高效工作、定期更新的 app 的时候，市场化的公司发现自己处于一个必须跟上步伐，否则就会灭绝的境地。

*对于*[*Crypterium*](https://crypterium.com/)*以及其他许多 fintech 平台来说，一种全新的基于微服务的* [*IT 开发*](https://hackernoon.com/tagged/it-development) *方式已经成为可扩展性和速度的关键。微服务架构到底是什么，为什么它让早期采用者比其他人更有优势？让我们把我们的应用翻个底朝天，看看界面之外还有什么。*

**巨微解决方案**

微服务是目前科技界最热门的词汇之一。网飞、易贝、亚马逊、[、Twitter](https://hackernoon.com/tagged/twitter) 、PayPal、优步以及其他许多大型网站都从单片架构发展到了微服务架构。根据 Dzone 的说法，通常情况下，规模较小的公司很快就会效仿这一做法——现在 10 家公司中有 8 家表示，他们将在最近的功能方面向新模式转移。那么什么是微服务呢？

基本上，它们是可以独立运行的独立模块，可以使用不同的编程语言创建。这些模块中的每一个都负责一个离散的任务，并且可以与其他模块通信来解决更大的复杂业务问题。此外，它们更容易理解和维护，开发速度更快，这意味着可以尽快为客户交付新的价值。

与我们使用的模式相反，整体结构是作为一个独立的单元来构建的。Crypterium 的首席技术官 Pavel Ivanov 说:“每当需要稍作修改时，通常都会重建和部署一个全新版本的应用程序。”。“这一切都导致无休止的延误。当更新应用程序时，系统甚至可能会停机约 10-15 分钟”，他补充道。

然而，对于像国有银行这样的老牌企业来说，转向微服务可能太冒险了——这真的不像把你的核心系统塞进模块那么简单。为了真正采用微服务架构并保持相关性，公司不仅要改变他们正在部署的内容，还要改变进行部署的人员。

**“服务器端”发生的事情会留在“服务器端”**

如果你一直在关注我们过去几个月的进展，你可能已经看到了重大的转变:我们现在几乎每周都推出新功能。但是你看不到的是我们应用程序的“服务器端”发生了什么。

发布任何产品的过程总是伴随着修复错误。目标是确保整个过程顺利进行。为了理解产品开发的魔力，让我们更深入地了解一下细节。这是 Crypterium 应用系统的简化模型。

![](img/32a0fd4a7400bb37db471d9858eecb84.png)

如您所见，用户的请求通过负载均衡器，负载均衡器分配流量并将请求与所需的服务相匹配。这些服务是在容器中运行的——你可以把它们想象成软件的包装，包括它运行所需的一切:代码、系统工具、系统运行时和设置。让容器成为像我们这样的动态和不断更新的应用程序的完美匹配的是它们的即插即用特性——它们可以混合、重新分配给新任务或从系统中删除，而不会影响整个系统。这意味着当对一个服务进行更改时，它可以在整个容器集群的最小子集上展开:仅仅是那些支持该功能的容器。

**运行中的动态基础设施**

现在来添加新功能。比方说，我们将更新我们的应用程序，增加一些重要的功能，比如“加密银行转账”功能。在公开之前，我们用真实流量进行了测试。

首先，一个支持未来功能的容器出现了，然后平衡器将该容器考虑在内来分配工作负载。如果它无法处理传入的流量或工作不正常，系统会自动将其恢复。如果运行顺利，平衡器会逐渐用升级后的容器替换保存旧功能的容器。在它们中的每一个都被替换之后，新的特性就被激活了。

*“具有讽刺意味的是，更新就发生在我们面前，但我们无法从用户的角度看到它们。这实际上是应该的，对吗？Crypterium 的系统架构师 Sergey Positurin co-* CTO 表示:“得益于我们成功创建的架构，我们可以在不破坏现有功能的情况下添加新功能。

最后但同样重要的是，这种方法让我们能够更好地控制资源分配和扩展。如果没有太多的用户请求，系统会将不需要的容器关闭。如果流量出现峰值，它会激活额外的资源，确保应用程序工作流程不会中断。我们要说的是 Crypterium 已经准备好迎接大发布和随之而来的高负载。顺便说一句，一些大的发布就在眼前。几个月后，Crypterium 将推出一项期待已久的功能，该功能将使在任何 NFC 终端上使用 crypto 支付或通过扫描二维码支付成为可能。

*你也可以[阅读一篇关于 Crypterium 如何躲过 DDoS](https://hackernoon.com/we-survived-a-ddos-attack-that-peaked-at-250k-requests-per-second-it-cost-us-10-b0bdfe028dd2) 攻击的文章，以了解更多关于我们网络安全系统的信息。

**关于地穴**

Crypterium 正在开发一个移动应用程序，它将把加密货币转换成你可以像使用现金一样轻松消费的货币。

在世界各地购物，在任何 NFC 终端或通过扫描二维码用硬币和代币支付。在网上商店购物，支付您的账单，或者只是在几秒钟内可靠地跨境汇款，而且只需几分之一便士。

在 https://crypterium.com/[了解更多信息，并加入我们的电报聊天讨论。](https://crypterium.com/)