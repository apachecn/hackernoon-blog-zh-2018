# 你需要知道的关于 AWS Lambda 冷启动的一切

> 原文：<https://medium.com/hackernoon/cold-starts-in-aws-lambda-f9e3432adbf0>

![](img/5669f7883a7f1a9516bd806216d838b3.png)

有许多关于 AWS Lambda 冷启动的博客帖子。我做了一些研究，并想在这里列出一些好的，以及一些快速深入了解这个主题的要点。

## 什么是冷启动？

触发函数时经历的延迟。

只有当没有空闲容器等待运行代码时，才会发生冷启动。这对于用户来说是不可见的，AWS 可以完全控制何时杀死容器。

## 冷启动的效果是什么？

*   因响应缓慢而受挫的用户
*   为速度支付更多的钱(有时)
*   如果考虑不周，调用函数会超时—连锁反应
*   关心一个与你无关的运营问题

## 什么时候应该关心？

*   如果你使用的是静态类型的语言，比如 Java 和 C#
    (没什么不好意思的！:D)
*   如果您有面向客户/同步应用程序
*   如果您的请求量很低或很少
*   一旦部署了新版本(所有容器都被销毁)

## 增加冷启动时间的因素是什么？

*   语言选择
*   存储容量
*   代码大小
*   VPC
*   HTTPS 来电
*   需要类路径扫描的东西(Java)

## 这个问题有什么解决方法？

首先，承认你不能保证你不会经历冷启动。最终的解决方案必须来自云提供商。我们只能努力提高。

*   如果问题不大，什么也不做(推荐)
*   等 AWS 来改进吧
*   增加内存(并支付更多费用)
*   做些热身运动

## 关于冷启动的好链接

[](https://read.acloud.guru/does-coding-language-memory-or-package-size-affect-cold-starts-of-aws-lambda-a15e26d12c76) [## 语言、内存、封装尺寸如何影响 AWS Lambda 的冷启动？

### 比较使用不同语言、内存分配和部署规模的 AWS Lambda 的冷启动时间…

read.acloud.guru](https://read.acloud.guru/does-coding-language-memory-or-package-size-affect-cold-starts-of-aws-lambda-a15e26d12c76) [](/thundra/dealing-with-cold-starts-in-aws-lambda-a5e3aa8f532) [## 在 AWS Lambda 中处理冷启动

### 假设你想买一张你最喜欢的乐队的音乐会的票，而你知道票快卖完了。你…

medium.com](/thundra/dealing-with-cold-starts-in-aws-lambda-a5e3aa8f532) [](/@lakshmanLD/resolving-cold-start️-in-aws-lambda-804512ca9b61) [## 在自动气象站 Lambda 中解析冷 Start️

### 是的，我知道冷启动在 lambda 令人沮丧。让我们以一个正式的介绍开始这篇文章。AWS Lambda 是…

medium.com](/@lakshmanLD/resolving-cold-start️-in-aws-lambda-804512ca9b61)  [## 如何热身

### 这一部分不是强制性的，但是如果你想预热你的 lambda 函数，就跟着做吧。

docs.thundra.io](https://docs.thundra.io/docs/how-to-warmup)