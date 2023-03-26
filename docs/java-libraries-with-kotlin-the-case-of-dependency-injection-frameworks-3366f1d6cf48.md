# 使用 Kotlin 的 Java 库。依赖注入框架的例子

> 原文：<https://medium.com/hackernoon/java-libraries-with-kotlin-the-case-of-dependency-injection-frameworks-3366f1d6cf48>

![](img/3b31b70d66a3a84583e796b4d72f1342.png)

[https://www.flickr.com/photos/the-money-pit/5900474232](https://www.flickr.com/photos/the-money-pit/5900474232)

科特林为我们打开了一个新的世界。在 [Java](https://hackernoon.com/tagged/java) 世界中，开发人员编写的代码比以前少了。Kotlin 与 Java 的互操作性使得它很容易集成到现有的和新的应用程序中，同时仍然可以自由使用我们最喜欢的 Java 库。

然而，由于这些库是用 Java 编写的，在 Kotlin 中使用它们通常会产生类似 Java 的语法。这并不意味着我们需要重新发明轮子，写一个库的 Kotlin 版本(这是我现在经常看到的)。但也许我们可以在它周围写一些科特林层。

一个很好的例子是[科特林·莫克托](https://github.com/nhaarman/mockito-kotlin)由[#学习者](https://medium.com/u/fceb7a60a849#终身</a><a class=)