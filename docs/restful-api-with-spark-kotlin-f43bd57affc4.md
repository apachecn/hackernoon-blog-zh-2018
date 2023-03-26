# 带 Spark Kotlin 的 RESTful API

> 原文：<https://medium.com/hackernoon/restful-api-with-spark-kotlin-f43bd57affc4>

![](img/7ac53a218378bc5313706757be7849aa.png)

Photo by [Stephanie McCabe](https://unsplash.com/@stephaniemccabe?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 构建 API 的非主流方式

说到构建后端 API，最常用的工具要么是 Java 的 Spring Boot，要么是 JavaScript 的 ExpressJS。即使对于科特林，Spring Boot 和 JetBrain 自己的 ktor 也是通常的选择。但是今天我将探索 spark-kotlin 这个不太受欢迎的选项。

*免责声明:我无论如何都不属于这个项目，也不在这个项目中工作。我只是偶然发现了它，并在做副业时开始使用它。(即将推出****)***

# **理解休息**

**如果你已经知道或者构建了一个 RESTful API，你可以跳过这一部分。已经有相当多的指南详细介绍了什么是休息，以及所有令人惊奇的事情。因此，相反，我将只涉及基本的(更像是，这就是我所知道的)。这是通过 HTTP 在互联网上进行通信的标准方式。在互联网的世界里，前端 web 界面需要一种与后端通信的方式。虽然前端可以说各种各样的事情，但他们仍然需要在他们之间声明一组标准的“短语”,以便后端知道前端在要求什么。**

# **安装**

**在开始之前，您可能需要安装以下几样东西:**

*   **玛文/格拉德勒**
*   **科特林**
*   **Java？(不太确定，但你可能需要它)**
*   **(可选)IntelliJ**

# **入门指南**

**根据 spark-kotlin 的官方 GitHub repo[显示，以下内容可用于 Maven 或 Gradle。就我个人而言，我使用 Gradle 只是因为我从未在 Android 开发之外使用过它。(而且，这是新的性感事物。算是吧。)](https://github.com/perwendel/spark-kotlin)**

```
**dependencies {
    ...
    compile** "com.sparkjava:spark-kotlin:1.0.0-alpha"
    ...
}
```

# **基础知识**

**这就是吸引我在弹簧靴上使用它的原因。相比之下，它看起来简单明了多了。不可否认，我从未在服务器开发中使用过 spring-boot，所以我可能完全错了，尤其是当它涉及到大规模应用时。**

## **获取请求**

**就是这样！我可以在这里结束这篇文章，你可以自己想出剩下的部分。但是，让我们探索一些更有趣的特性。**

## **发布请求**

**对于帖子请求，他们工作起来也一样轻松！您需要知道的是，可以用`request.body()`检索请求体。**

# **更多有趣的东西**

## **重定向()，参数()**

**现在，如果您希望不同的链接有相同的输出呢？嗯，你可以只做`redirect("/path")`。你也可以通过`params(":path")`标签从网址获取信息。类似这样的…**

## **实例 API**

**您还可以拥有一个单独的实例 API，它运行在不同的端口上，或者具有与静态 API 不同的设置，只需将它声明为一个单独的变量。**

**当然，还有很多更高级的特性(比如会话、uri 等。)这一点我没有涉及。请按照回购的[自述文件自行探索。](https://github.com/perwendel/spark-kotlin/blob/master/README.md)**

**从现在开始，我会在上面加入一些相关但不完全是 spark-kotlin 的东西。**

# **处理 JSON**

**在许多(或大多数)场合，请求和响应都是 JSON 格式的。这就是[谷歌开发者](https://medium.com/u/991272e72e68?source=post_page-----f43bd57affc4--------------------------------)’[Gson 库](https://github.com/google/gson)派上用场的地方。它真的很容易使用，并得到了广泛的支持。我在下面放了一个简单的例子来展示它是如何被使用的。**

# **单元测试**

**我花了一段时间才找到一个像我希望的那样简单的测试库。这是由 [Despegar](https://www.despegar.com) 专门为 spark 框架制作的测试库，名为 [spark-test](https://github.com/despegar/spark-test) 。**

**不过，有几件事要记住:**

*   **这是为 [spark-java](https://github.com/perwendel/spark) 而不是 [spark-kotlin](https://github.com/perwendel/spark-kotlin) 制作的 Java 库**
*   **服务器需要在后台运行(在我的例子中，在端口 4567 上)才能工作**
*   **无法为删除请求附加请求正文**

***注意:正如在撰写本文时所提到的，删除请求无法附加任何类型的请求主体，这使得很难测试您的删除请求。就我个人而言，我对每个特性使用不同的 url，所以我只是将一个 POST 请求从同一个 URL 重定向到 DELETE 请求，并通过 POST 请求测试它。不理想，但目前有效。***

# **参考**

*   **[科特林 WebApp 教程](/@codemwnci/kotlin-webapp-tutorial-todolist-part-1-c544b9a70f29)作者[韦恩埃利斯](https://medium.com/u/6f0e89485e0d?source=post_page-----f43bd57affc4--------------------------------)**
*   **[由](/@v.souhrada/build-rest-service-with-kotlin-spark-java-and-requery-part-1-1798844fdf04)[瓦茨拉夫·苏赫拉达](https://medium.com/u/3f2747b2fdec?source=post_page-----f43bd57affc4--------------------------------)用 Kotlin、Spark Java 和 Requery 构建 REST 服务**