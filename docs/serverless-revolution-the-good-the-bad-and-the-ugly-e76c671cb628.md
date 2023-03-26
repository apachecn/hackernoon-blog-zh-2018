# 无服务器革命:好、坏、丑

> 原文：<https://medium.com/hackernoon/serverless-revolution-the-good-the-bad-and-the-ugly-e76c671cb628>

> 是愚蠢。这比愚蠢更糟糕:这是一场营销炒作活动。
> ‐ *理查德·斯托尔曼*[云计算点评](https://www.theguardian.com/technology/2008/sep/29/cloud.computing.richard.stallman)*2008 年 9 月*

而且，10 年后，当有人提到这个词时，你会开始三思而行:它是天空中的那个东西，还是预计到 2020 年托管全球 83%的企业工作负载的另一个东西？

不管你喜不喜欢，另一场革命正在进行。AWS 一马当先，Azure 女士和 GCP 紧随其后，他们都怀有一个共同的目标:

从基础架构中分离软件。

[无服务器](https://hackernoon.com/tagged/serverless)。

FaaS。

德沃普斯之死。

你说吧。

不管名字是什么(为了方便起见，我们称之为“无服务器”)，这种新的范例已经在重塑软件景观方面发挥了作用。我们已经看到[像可口可乐](https://aws.amazon.com/blogs/aws/things-go-better-with-step-functions/)这样的巨头在他们的生产栈中采用无服务器组件，像[无服务器](https://serverless.com/)这样的框架获得了数百万的资金[。然而，我们应该记住，无服务器并不适合任何人、任何地方、任何时间——至少目前是这样。](https://techcrunch.com/2016/10/12/serverless-raises-3m-to-help-developers-go-serverless/)

![](img/fda808e963b6bacfd0ba88ffdf9e206a.png)

Take a wild guess: where would you fit In?

# 服务器(少)=状态(少)

作为一个传统的程序员，当谈到无服务器时，我看到的最大的“障碍”是“无状态”。而之前我可以相当确定我存储在内存中的复杂计算结果；或者我提取到`/tmp`里的相当大的元数据文件；或者我刚刚生成的助手子流程；一旦我的程序重新得到控制，它仍然存在，无服务器打破了所有这些假设。尽管像 lambda [这样的实现倾向于在一段时间内保留状态](https://aws.amazon.com/blogs/compute/container-reuse-in-lambda/)，但一般的契约是，你的应用程序应该能够放弃所有希望，优雅地从零开始，以防它被从头开始调用。不再有内存状态:如果你想保存，你就保存。你没有，你就输了。

从另一个角度考虑，这也可能被认为是无服务器的(无意的)巨大优势之一；因为瞬态(它的存在仅仅是因为有了[“服务器式”架构](https://www.urbandictionary.com/define.php?term=serverful))是大多数——如果不是全部——邪恶的根源。现在，通过设计，您犯错误的空间更小了——这可能是一个公平的权衡，特别是对于像我这样臭名昭著的程序员来说，通过内存状态管理来寻求(通常是过早的)优化。

然而，我们不应该忘记内存状态管理和缓存容量的减少所导致的性能损失；您的状态管理器(数据存储)以前在几个[【电路跳】](https://gist.github.com/understeer/4d8ea07c18752989f6989deeb769b778)之外，现在将在几个网络跳之外，导致几毫秒——甚至几秒——的延迟，以及更多的故障空间。

# 亚秒计费

如果你生活在过去的十年里，你会看到它的到来:一切都逐渐进入现收现付的模式。现在它已经发展到这样的长度，以 0.1 秒的执行间隔对[λ进行充电——并且量化将继续。虽然这可能并不意味着太多的优势，有时甚至可能意味着](https://aws.amazon.com/lambda/pricing/)[劣势](https://dzone.com/articles/the-rise-of-lambda)，但对于持续负载，具有高负载变化的应用可以获得巨大的优势，因为不必一直为其预期的峰值负载进行配置和支付。更不用说[事件驱动的](https://hackernoon.com/going-serverless-with-amazon-web-services-aws-the-modern-approach-245fa9203f47)和[批处理处理器](/@uditharosha/implementing-a-serverless-batch-file-processing-application-part-2-the-sigma-way-885951575a25)系统，它们具有稀疏的负载分布，可以节省一个数量级的成本，特别是当它们是小规模和地理上本地化的时候。

此外，新的按资源使用付费模型(考虑到时间——或者更具体地说，执行时间——也是一种非常有价值的资源)鼓励面向性能的编程，这确实是一个好迹象。FaaS 提供商通常使用复合计费指标，将执行时间与内存分配等结合起来。，进一步加强平衡优化的动力，最终实现更好的资源利用、更少的浪费以及由此带来的财务和环境效益。

# 不可见的红外线

在物理硬件、虚拟化(后来)或容器化(后来)操作系统环境中，现在您只能看到单个进程:实际上是单个功能或工作单元。虽然这一开始听起来很棒(不再需要基础架构/硬件/操作系统/支持设施监控或维护，希望无服务器提供商能为我们解决这些问题！)，这也意味着灵活性方面的巨大挫折:即使在容器时代，我们至少有选择我们喜欢的基本操作系统的灵活性(尽管仍然绑定到底层内核)，而我们现在所拥有的只是编程语言的选择(有时还有它的版本)。然而，那些经历过 devops 难题的人肯定会同意后者是一个非常合理的权衡。

# 更强的隔离

由于你不再能接触到真实的世界(你通常会是一个短命的[容器化的](https://aws.amazon.com/blogs/compute/container-reuse-in-lambda/)过程)，犯错误的空间更小了(这是不可避免的，因为实际上你能做的事情更少了！).即使你受到了威胁，你短暂的生命和[有限的特权](https://docs.aws.amazon.com/lambda/latest/dg/intro-permission-model.html)可以防止进一步的污染，除非漏洞足够强大到[影响底层的编排框架](https://aws.amazon.com/security/security-bulletins/)。因此，不幸的是，如果这样的漏洞被发现，它可能会被大规模利用，因为基于 lambda 的恶意软件主机将比以往任何时候都更具可扩展性。

大多数提供商故意限制 lambda 尝试恶意活动，例如[发送垃圾邮件](https://aws.amazon.com/lambda/faqs/#AWS_Lambda_functions)，这可能会受到合法用户的反对，但却受到垃圾邮件困扰的称赞(想象一下每月数百万 lambda 运行时的高峰——[AWS](https://hackernoon.com/tagged/aws)已经提供了[100 万次免费调用和 320 万秒的执行时间](https://aws.amazon.com/free/#AWS_Lambda)——向一组用户发送垃圾邮件；十几个免费的 AWS 订阅将会给攻击者带来巨大的优势！)

# 供应商锁定:副作用？

这是每个云平台固有的问题，或者，如果你仔细考虑的话，是任何平台、实用程序或服务固有的问题。当你决定利用平台的一个“酷”或“高级”的特性时，你就有效地与它结合了。对于无服务器平台来说，这一点比以往任何时候都更加真实:除了语言构造，几乎所有的东西都是特定于提供者的，试图编写一个“通用”的函数要么以一堆难以理解的复杂代码和[重新发明的轮子](https://dictionary.cambridge.org/dictionary/english/reinvent-the-wheel)告终，要么，最有可能的是，什么也没有。

从某种意义上说，这是一种必不可少的、不可避免的回报；非要特别就要具体！像[无服务器](https://serverless.com/)这样的框架正在积极尝试解决这个问题，但是根据[普遍意见](/@PaulDJohnston/why-vendor-lock-in-with-serverless-isnt-what-you-think-it-is-d6be40fa9ca9)通用的解决方案仍然遥不可及。

# 能力越大，责任越大

鉴于其简单性、多功能性和可扩展性，无服务器应用程序对于公司的 IT 基础设施来说是一笔宝贵的资产；但是，如果设计、部署、管理和监控不当，事情很容易失控，无论是在体系结构的复杂性方面还是在财务方面。因此，知道如何驯服野兽比简单地学习野兽能做什么要重要得多。

祝你的[无服务器冒险](https://slappforge.com)好运！

*原载于 2018 年 2 月 20 日 randomizd.blogspot.com*[](http://randomizd.blogspot.com/2018/02/serverless-revolution-good-bad-and-ugly.html?m=1)**。**