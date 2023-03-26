# 在中应用装饰器模式。使用 Autofac

> 原文：<https://medium.com/hackernoon/apply-the-decorator-pattern-in-net-using-autofac-957502b771f7>

![](img/307b700bc493619b07bd76e4ab1afed3.png)

Russian Dolls — metaphor to extend a given functions behavior. Photo borrowed from [Maxpixel](http://maxpixel.freegreatpicture.com/Traditional-Russian-Toy-Russian-Doll-Russian-1090697).

我提倡在开发应用程序时遵循良好的设计原则和结构。

随着开发过程的进行，我们会在应用程序中添加新的功能:这可能是客户的功能请求，也可能是您建议的改进。无论如何，这些即将到来的变化也将改变已经给出的代码库。当引入变更时，我们希望尽可能简单。

在这篇文章中，我将向您展示一个例子，在您想要将功能扩展到一个现有特性的场景中，如何通过强大的 IoC 容器 [Autofac](https://autofac.org/) 利用装饰器模式。

假设您将要构建一个服务于产品的服务。我们将把这个服务称为**iproductreasury**，为了简单起见，我们只使用一个叫做 *GetById* 的方法。它可能看起来像这样，并连接了一个实现:

通常情况下，您可以使用下面的代码行将您的具体实现 XmlProductRepository 注册为 IProductRepository，以支持 [*依赖倒置原则*](https://en.wikipedia.org/wiki/Dependency_inversion_principle) :

比方说，当产品被获取时，您过一段时间需要一些日志记录。你如何做到这一点？

通过注入一个日志服务来修改实现 XmlProductRepository 是很有诱惑力的，如下所示:

但这违反了[打开/关闭原则](https://en.wikipedia.org/wiki/Open/closed_principle)。原则声明不允许修改类，但是可以扩展它们。在这里，装饰器模式非常方便。

你可以通过添加一个日志代理来扩展，而不是搞乱你的原始实现，并将其注册为一个装饰器。

不过，您必须修改一行代码，那就是*原始实现*的注册。现在它需要是一个命名注册，这样我们在注册装饰器时就可以引用它:

然后连接并注册装饰器:

这里我们用来指代实现的关键字是 *productRepository* 。当您准备好这段代码后，当您注入 IProductRepository 时，将调用的第一个类是 LoggingProductRepositoryProxy，由于我们已经向它注入了内部实现，它将作为一个代理(俄罗斯娃娃风格)工作，遵循开放/封闭原则。

假设您的 XmlProductRepository 运行缓慢，您需要将产品缓存一段时间。你将如何实现它？在您的日志实现之上添加另一个装饰器是非常容易的:

Note that the logging implementation now needs to be named

相当灵活！

装饰模式在支持应用程序中的横切关注点时非常强大。

你觉得这个帖子有用吗？你知道该怎么做！👏