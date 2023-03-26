# 什么是 Micronaut？

> 原文：<https://medium.com/hackernoon/what-is-micronaut-37a6565f217d>

[](http://micronaut.io/) [## micro naut:JVM 的现代微服务框架

### 一个基于 JVM 的现代全栈框架，用于构建模块化、易于测试的微服务应用。

微型机器人](http://micronaut.io/) 

[Micronaut](http://micronaut.io/) 是由 [Grails](https://grails.org/) 团队在 [Greach 2018](http://2018.greachconf.com/) 上正式宣布的全新全栈框架。它专注于模块化、最小内存占用和启动时间，这使它成为在 [AWS](https://hackernoon.com/tagged/aws) lambda 或类似环境中运行的完美解决方案。用 [Java](https://hackernoon.com/tagged/java) 编写的服务器需要不到一秒的时间启动，最小 JAR 大小为 8 MB。 [Micronaut](http://micronaut.io/) 支持 Java、Groovy 和 Kotlin 语言。

Key things about Micronaut

Micronaut 的主要优点是没有为配置和依赖注入保存元数据的运行时损失。你可以把 [Micronaut](http://micronaut.io/) 想象成没有任何运行时反射的 Spring。每个信息都在编译时使用 Groovy AST 转换或 Java 和 Kotlin 的 AST 处理器来处理。 [Micronaut](http://micronaut.io/) 使用受 Spring 启发的内部依赖注入模块，该模块利用了官方的 [JSR-330 上下文和依赖注入注释。](http://cdi-spec.org/)

[Micronaut](http://micronaut.io/) 代码看起来非常类似于 [Spring Boot](https://projects.spring.io/spring-boot/) 启用了[春云](http://projects.spring.io/spring-cloud/)。您可以编写无缝负载平衡的 HTTP 服务器和 HTTP 客户端。对服务发现、Hystrix、跟踪记录、缓存、容错和断路器模式有现成的支持。反应流支持是框架不可或缺的一部分，因此您的控制器和客户端可以使用 RxJava 2 对象进行输入和输出，如`Single<MyObject>`。

*控制器示例*

```
@Controller("/") class HelloController {

    @Get("/hello/{name}") String hello(String name) {
        return "Hello $name"
    }
}
```

*客户端示例*

```
@Client("/") interface HelloClient {
    @Get('/hello/{name}') Single<String> hello(String name)
}
```

*回退客户端示例*

```
@Fallback class HelloFallbackClient {
    Single<String> hello(String name) {
        return Single.just("Hello fallback $name")
    }
}
```

与 [Spring Boot](https://projects.spring.io/spring-boot/) 类似，您可以使用`@Requires`注释声明自己的*可自动配置的*bean。它还与 [Spring Boot](https://projects.spring.io/spring-boot/) 共享配置属性。您甚至可以使用`SPRING_APPLICATION_JSON`环境属性来实现后端兼容性。您可以使用特定于环境的属性，例如`application-aws.yml`来部署到 AWS。

Micronaut 与 Grails 共享一些部分——例如，您将能够在您的 [Micronaut](http://micronaut.io/) 微服务中使用 GORM。

Micronaut 还没有公开发售。该版本计划于 2018 年在 Q2 发布，这意味着它可能会在 [Gr8Conf EU](http://gr8conf.eu/) 上发布，该会议还将举办专门针对这一新框架的会谈。有兴趣尝试 [Micronaut](http://micronaut.io/) 的公司可以向 [OCI](https://objectcomputing.com/) 申请测试版。

你可以在下面由[Á·阿尔瓦罗·桑切斯-马里斯卡尔](https://medium.com/u/d41273300566?source=post_page-----37a6565f217d--------------------------------)制作的幻灯片中看到更多关于 Micronaut 的细节:

*编辑:*[*Micronaut*](http://micronaut.io/)*第一个里程碑已经如期在*[*gr 8 conf EU*](http://gr8conf.eu/)*发布。访问* [*下载部分*](http://micronaut.io/download.html) *开始使用。首选是使用* [*SDKMAN！*](https://medium.com/u/2818e79e6b9a?source=post_page-----37a6565f217d--------------------------------) *:*

```
curl -s https://get.sdkman.io | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"
sdk install micronaut
mn -version
```

*[*文档*](https://docs.micronaut.io/latest/guide/index.html) *已经很全面了:**

*[](https://docs.micronaut.io/latest/guide/index.html) [## 微型机器人

### 此外，使用 Micronaut，您的应用程序启动时间和内存消耗不受您的…

docs.micronaut.io](https://docs.micronaut.io/latest/guide/index.html) 

*还有* [*几个导游*](http://guides.micronaut.io/) *已经有了:*

 [## micro naut:JVM 的现代微服务框架

### 一个基于 JVM 的现代全栈框架，用于构建模块化、易于测试的微服务应用。

guides.micronaut.io](http://guides.micronaut.io/)*