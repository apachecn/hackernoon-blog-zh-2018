# 界面分离原理

> 原文：<https://medium.com/hackernoon/interface-segregation-principle-bdf3f94f1d11>

## 以及如何解读

![](img/26ef161bdd68828759333cef7be68bb8.png)

I wish all of my interfaces were segregated as good as this fish at Catania fish market

这是第四篇关于坚实原则的文章。检查前一个，[里斯科夫替代原则](https://hackernoon.com/liskov-substitution-principle-a982551d584a)，如果你错过了它。

## 定义

根据罗伯特·马丁的说法，

> **接口分离原则** ( **ISP** )声明，任何客户端都不应该被迫依赖它不使用的方法。

此外， [Wikipedia](https://en.wikipedia.org/wiki/Interface_segregation_principle) 有一个简明的实践描述，引导你到一个你的代码符合 ISP 的情况:

> ISP 将非常大的接口分成更小、更具体的接口，这样客户只需知道他们感兴趣的方法。这种收缩的接口也称为角色接口。

我相信这个原则背后有一个很深的基础，就像 Kent Beck 的 XP 价值观是他的 XP 原则的基础一样。

## 正确的抽象是接口分离原则的关键

Wiki 的定义只说明了你的抽象应该是正确的，因此实现它们的类最终应该是小的、内聚的、可靠的。

找到正确的抽象更像是一门艺术。毫无疑问，你应该探索你的领域，可能建立一些语义网络，提出一组用户故事，绘制交互图——所有这些不一定会引导你找到正确的抽象。错误的抽象比根本没有抽象更糟糕，所以不要忘记三条规则。

当你认为你已经完成了一些抽象的时候，把它们表现为接口。上面提到的技术加强了它们成为[角色接口](https://martinfowler.com/bliki/RoleInterface.html)。使用这种方法，具体的类只实现那些抽象所要求的。所以我们最终满足了界面分离原则，即使我们不知道它的存在。

## 违反界面分离原则

当客户端依赖于它不使用的方法时，这意味着你的抽象是错误的。马丁·福勒的[角色接口](https://martinfowler.com/bliki/RoleInterface.html)的例子(这是应用 ISP 的自然结果)恰恰暴露了错误的初始对象分解。这并不需要一个独立的原则。这段代码显然不够连贯。所以不要考虑你的代码是否违反了接口分离原则，想想你的抽象是否正确。

## 代码示例？

没有，在里面没多大意义。给出一些随机的代码不会告诉你太多领域抽象和他们遵循的契约。不管怎样，互联网上有很多例子，一些类不得不实现一些胖接口的行为，而这是不应该的。

## 最后的话

当你开始[分解你的问题空间](https://hackernoon.com/how-to-avoid-anemic-domain-model-5e1c3e6fe4d0)并确定参与你领域的主要角色时，这个原则就自然而然地出现了。而且从来都不是机械动作。没有一个原则能自动引导你找到正确的对象模型。

所以 ISP 在设计软件的时候是一个很差的指导，但却是一个很好的健康与否的指标。设计软件的时候不要考虑 ISP。考虑你的抽象是否是可重用的和可组合的，你的对象是否是封装的和内聚的。