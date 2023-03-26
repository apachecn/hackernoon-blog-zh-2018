# AWS 无服务器速成班—在 Lambda 上运行 Node.js 11

> 原文：<https://medium.com/hackernoon/a-crash-course-on-serverless-with-aws-running-node-js-11-on-lambda-6ac676841d8a>

![](img/9af8284e7795fd96c4c282a555e81118.png)

相当令人兴奋的标题，不是吗？当我听说 AWS 正在为 [AWS Lambda](https://aws.amazon.com/lambda/) 增加对[定制运行时和层](https://aws.amazon.com/about-aws/whats-new/2018/11/aws-lambda-now-supports-custom-runtimes-and-layers/)的支持时，我兴奋不已。这意味着您现在可以构建自己的定制工件，使您能够在函数之间共享和管理公共代码。

![](img/03d16dd056bd8adf4783f161e5f19b81.png)

我不会说我听到公告就晕了。但是，我做到了。不要告诉任何人。

# 我们要做什么？

本文将向您展示如何将一个定制的 Node.js 11 运行时连接到 AWS Lambda。我们将用一个示例函数创建一个简单的无服务器项目，并添加一个使我们能够运行 Node.js 11 运行时的层。

# 它是如何工作的

要使用一个[定制运行时](https://docs.aws.amazon.com/lambda/latest/dg/runtimes-custom.html)，您必须指定在部署您的函数时提供一个。当函数被调用时，AWS Lambda 将引导您的运行时代码，并通过运行时 API 与之通信，以执行函数代码。

关于自定义运行时，这就足够了。什么是 [AWS Lambda 层](https://docs.aws.amazon.com/lambda/latest/dg/configuration-layers.html)？它们是一种新型的工件，可以包含任意代码和数据。它可以同时被多个函数引用。太棒了。您的函数通常共享公共依赖项，如 SDK、预建模块、库和框架。这里的踢球者，现在你也可以分享运行时！

通过使用 AWS Lambda 层，您可以管理跨多个功能使用的组件。允许更好的代码重用和更多的干代码。

使用它们很简单，你把公共代码放在一个 zip 文件中，然后把它作为一个层上传到 AWS Lambda。您还可以将它作为 CloudFormation 模板上传，然后配置您的函数来使用它。图层内容将可用于您的功能代码。但是这是另一个教程的主题。

让我们开始使用自定义 Node.js v11 运行时吧！

# 配置项目

我假设你已经对[无服务器框架](https://serverless.com/framework/)有了基本的了解。我也希望你有一个 AWS 帐户设置。如果你没有，[请看看这个](https://hackernoon.com/a-crash-course-on-serverless-with-node-js-632b37d58b44)。

***注*** *:将无服务器框架升级到 v1.34.0 或更高版本，以支持分层*

## 1.创建服务

一如既往，我们需要一个新的服务来保存我们所有的代码。

```
$ sls create -t aws-nodejs -p node11 && cd node11
```

运行这个命令后，您会发现自己在`node11`目录中，旁边有一个很好的样板文件，可以开始构建您的函数。下一步是打开`serverless.yml`并添加我们的层。

# 2.将 Node11 层添加到`serverless.yml`

有许多预置层可供选择。幸运的是，无服务器社区非常棒！我们将继续获取[自定义 Node.js 运行时](https://github.com/lambci/node-custom-lambda)。

![](img/2dc9162edd3e4f02b50a60e9d597f662.png)

你可以选择任何一个，但我会选择`v11`。现在打开`serverless.yml`，删除所有内容并粘贴进来。

```
service: node11

provider:
  name: aws
  runtime: provided # set to provided

functions:
  hello:
    handler: handler.hello
    events:
      - http:
          path: /
          method: get
    layers: # add layer
      - arn:aws:lambda:us-east-1:553035198032:layer:nodejs11:3
```

添加 ARN 层就足够了，这个函数将获得运行时间。不要忘记添加`runtime: provided`字段。

# 3.向`handler.js`添加代码

从这里开始，你会有宾至如归的感觉。你终于可以在 AWS Lambda 上写出血边 Node.js 代码了。我们等这一刻已经很久了。

打开`handler.js`并粘贴到下面的代码片段中。

```
exports.hello = async (event, context) => {
  console.log(`Hi from Node.js ${process.version} on Lambda!`)
  return {
    statusCode: 200,
    body: JSON.stringify({ 
      message: `Hi from Node.js ${process.version} on Lambda!` 
    })
  }
}
```

相当简单的代码片段，但它证明了一个观点。确保我们正在运行`Node.js v11.4.0`。

# 部署项目

无服务器框架使部署变得快速而轻松。你需要做的就是运行一个命令。

```
$ sls deploy
```

它将创建一个 CloudFormation 模板，提供资源并部署代码。全部在一个命令中。

![](img/3e2cdc238048e37bc18179c0d6fe86c4.png)

部署进行得很顺利。用卷发点击网址，以确保它的工作。

```
1 $ curl https://<id>.execute-api.us-east-1.amazonaws.com/dev/
```

你应该看到`{"message":"Hi from Node.js v11.4.0 on Lambda!"}`得到回应。很好用！

# 包扎

随着 AWS Lambda 的最新改进、新的支持语言、新的运行时和层，它不仅仅是对主虚拟机和容器服务的支持服务。无服务器架构正在成为一股不可忽视的力量。我迫不及待地想看看它将把我们带向何方！

[这里是回购](https://github.com/adnanrahic/sls-node11)如果你在跟随教程时卡住了，如果你想让更多人在 GitHub 上看到它，就给它一颗星。如果你想阅读我以前的一些无服务器思考，请前往[我的简介](https://dev.to/adnanrahic)或[加入我的无服务器时事通讯！](https://upscri.be/b6f3d5/)

如果你需要一个[无服务器分析框架](https://statsbot.co/cubejs/)，请查看 [Cube.js](https://statsbot.co/sign-up?cubejs=true) 。它是[开源的，在 GitHub 上](https://github.com/statsbotco/cubejs-client)。或者，如果您想了解更多关于无服务器架构的信息，可以在 [Statsbot 博客](https://statsbot.co/blog/)上阅读更多与无服务器相关的文章。

希望你们喜欢读它，就像我喜欢写它一样。如果你喜欢它，不要犹豫分享。别忘了给 Statsbot 博客一些爱。

*原载于 2018 年 12 月 12 日*[*statsbot.co*](https://statsbot.co/blog/a-crash-course-on-serverless-with-aws-running-node11-on-lambda/)*。*