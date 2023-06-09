# 什么是无服务器—第 2 部分:选择正确的无服务器解决方案的挑战和考虑事项

> 原文：<https://medium.com/hackernoon/what-is-serverless-part-2-challenges-and-considerations-for-choosing-the-right-serverless-58c830061db8>

请看这个由 5 部分组成的博客系列的第一部分[这里](https://platform9.com/blog/what-is-serverless-and-what-it-means-for-you-part-1/)。

![](img/c66f9e815c5e47148aca7ab1fc15d2b2.png)

AWS Lambda？Azure 函数？Openwhisk？裂变？

当您正在考虑无服务器解决方案并寻找着手方式(摆脱所有基础设施方面的担忧:-)，[在选择合适的无服务器解决方案来满足当今大规模企业的需求时，以下是一些需要注意的事项](https://platform9.com/blog/what-is-serverless-part-2-challenges-and-considerations-for-choosing-the-right-serverless-solution/)和“注意事项”。

# 1.锁定特定的云提供商

这是显而易见的。所有领先的云提供商都将客户锁定在其无服务器框架的独特实施中。例如，AWS Lambda 依赖于横跨 DNS (Route53)、API 网关、S3、数据库、网络(VPCs)等的全套 AWS 产品。组成复杂的无服务器应用程序需要这些专有组件。例如，这意味着 Lambda 函数不能跨其他云提供商移植。一旦编写完成，这些功能在其他环境中的可移植性或重用性几乎是不可能的，因为需要重写的不仅仅是应用程序逻辑和功能，还有云提供商提供的所有基本服务。

本质上，您可以用另一种类型的依赖来交换应用程序组件和基础设施之间的紧密耦合。这是一个问题，特别是因为现代软件交付的世界已经不断地证明，努力实现尽可能多的解耦、对象重用和可移植性——对于确保业务敏捷性和操作的简易性都是至关重要的。

除了这种依赖性，特定的云提供商还引入了额外的限制，开发人员在选择首选服务时需要注意这些限制。例如，AWS Lambda 限制了工件的大小(在编写本文时为 50 MB)、并发执行的数量以及每次调用分配的内存量。

# 2.成本(和隐性成本。)

正如我们在本系列的前一部分中提到的，无服务器的计费优势非常敏感地取决于实际的使用模式。此外，使用特定 FaaS 框架的成本不应与运行该功能所需的周围生态系统服务的成本相分离。例如，在大型企业中使用 Lambda 的财务影响不仅限于普通的 CPU/RAM/网络成本，还包括 API 网关、S3、发电机的相关费用、跨 VPCs 发送数据的成本等。大多数用户发现公共云提供商的费用很快就会增加。

如果您的事务量一直很高(并且规模越来越大)，Lambda 函数等解决方案可能会超出您的预算。可能的解决方案包括以这样一种方式设计应用程序，即可以将更大批量的数据接收到函数中，通过编写更高效的代码来降低执行时间，降低跨 VPC 和可用性区域(az)的数据传输成本等。跨 VPC 传输需要 Lambda 函数来打开弹性网络接口(ENI ),这导致更长的执行时间和传输本身的更高费用。

无论解决方案是什么，也应该在私有云和内部基础架构上提供功能，这是显而易见的。

# 3.启动延迟

公共云的各种用户指出的一个问题是与使用 FaaS 框架相关的冷启动挑战。

一旦(Lambda)函数在一段时间内没有被使用，系统就会回收它所拥有的资源，这意味着需要额外的启动时间来重新启动该函数——实例化另一个容器，加载它的依赖项，然后使其可用。对于物联网中的某些实时或接近实时的应用或服务于实时终端用户的认知应用，100 毫秒的延迟太高了。

相比之下，开源无服务器框架裂变允许您预调优一些跨频谱的保留资源，以确保您的应用程序以最小的延迟就绪。

# 4.私有云/内部部署的无服务器应用

有时，您希望拥有并完全控制您的基础架构，并确保环境之间的可移植性。也许您的工作负载对业务过于关键，也许您的业务组织不太愿意向新的云服务添加依赖项。也许您想要更多地了解您所使用的系统的开发。而且，最常见的情况是，您可能希望通过利用现有基础架构来节省 IT 成本，而不是增加您的公共云占用空间。

尽管如此，即使在使用本地基础设施时，您仍然希望能够让您的开发人员更新他们的应用程序，并利用无服务器等新模式。

在私有云中，大多数无服务器实施都基于 PaaS 平台。PaaS 的限制模型本质上质疑了无服务器框架在其上的使用。从这个意义上说，无服务器框架已经作为一种事后想法被添加到商业 PaaS 中。围绕这种集成的限制使得这一点非常困难，因为它给已经复杂的体系结构增加了另一层复杂性。最终结果是，在低效设计的应用程序的情况下，技术债务会变得更加复杂。

# 5.复杂的 CI/CD 工具链

FaaS 框架仍在发展，它们在复杂的 CI/CD 工具链中的位置仍在形成中。开发团队需要大量的前期投资和努力才能将无服务器框架集成到他们的持续交付管道中。

举个例子，

*   一个新开发或修改的功能需要通过一系列检查——从单元测试到 UAT——才能升级到产品。这可能会使流程更加繁琐。
*   对于 FaaS，需要对每个单独的功能进行额外的负载和性能测试。在将这些部署到生产环境之前，这一点至关重要。
*   需要为每个功能提供回滚和前滚功能。
*   与基于微服务的开发相比，运营团队需要更早地参与进来。

# 7.与其他 IT 运营部门隔离的无服务器运营

开发者可能不用担心服务器。然而，运营团队——尤其是在复杂的混合环境中运营的大型企业——仍然需要具备可见性，并且能够管理无服务器应用程序及其占用空间。如果您试图在私有云上启用无服务器，这一点更是如此。

虽然与其他技术一样，无服务器可能涉及特定的工具或服务，但它仍然需要能够对所有环境中的所有类型的应用程序(传统、微服务、无服务器)拥有单一控制台和粒度可见性和控制，无论是内部部署、公共云、私有云、容器还是更多。您需要一个解决方案，让运营部将基于无服务器的应用程序整合到他们的整体 IT 战略、流程和工具中，就像他们整合任何其他类型的应用程序一样。

# 8.可见性和监控

例如，在 Lambda 上，用户最大的抱怨是他们不知道发生了什么。相比之下，开源无服务器框架[裂变](https://fission.io/)提供了与本地 Kubernetes 监控工具的内置集成，为您提供了良好的可视性和对无服务器功能的故障排除，就像您习惯于其他容器化应用程序一样。

当然，无服务器架构要求采用它们的企业具备更高的技术和文化成熟度。本系列的下一篇文章将讨论如何利用 Kubernetes 应对这一关键的企业架构挑战。