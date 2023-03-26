# 用 Node.js 和 MongoDB 构建无服务器 REST API

> 原文：<https://medium.com/hackernoon/building-a-serverless-rest-api-with-node-js-and-mongodb-2e0ed0638f47>

![](img/444303eca3e4d0438f9568bf60f16d41.png)

在过去的几个月里，无服务器运动已经获得了一些动力。每个人似乎都在谈论它。有些人甚至称之为革命！但是，我们不要太兴奋。不要像我一样。我对这种很酷的东西过于兴奋，于是开始写文章。如果这对你来说是全新的，这里有一篇我不久前写的文章，解释了核心概念。

[](https://hackernoon.com/a-crash-course-on-serverless-with-node-js-632b37d58b44) [## Node.js 无服务器速成班

### 不管您的开发背景如何，在过去的一年中，您不可避免地会听到“无服务器”这个术语。这个词…

hackernoon.com](https://hackernoon.com/a-crash-course-on-serverless-with-node-js-632b37d58b44) 

本着这种精神，我花在探索使用无服务器架构构建什么是合理的时间可能超过了被视为健康的时间。我的结论是，几乎所有东西都可以用无服务器的方式构建。你唯一需要问自己的问题是，你是否真的需要它。Lambdas 是无状态的，这意味着编写服务器端代码的整个概念需要重新学习。

听起来很有趣？是啊，对我来说也是。我最近发表了一篇关于在现实生活中使用无服务器架构的实践课程。我把我所有的发现和使用无服务器的合理理由都倾注到这个课程中。我不断问自己这个问题*“为什么我需要无服务器？”*贯穿整个创作过程。你可以在下面找到我的想法。

[](https://www.packtpub.com/web-development/serverless-javascript-example-video) [## 无服务器 JavaScript 示例[视频] -视频|现在只需 5 美元

### 通过无服务器 web 开发的实时演示变得更加熟练

www.packtpub.com](https://www.packtpub.com/web-development/serverless-javascript-example-video) 

# 为什么对 REST APIs 使用无服务器？

为什么不呢？是因为我们可以，还是我们看到了相对于传统服务器的明显优势？硬币的两面都有有效的论据。无服务器被认为是永远运行的。因为你不需要管理任何东西，你不用担心正常运行时间，它只是工作。它还可以自动扩展。真好。真的很好。扩展服务器并不有趣。

但是持久存储呢？我们不能像过去那样在服务器上运行 MongoDB 数据库。然而，如果你一直遵循在过去一年中不断增长的**【关注点分离】**的生活方式，你可能已经习惯了将你的数据库与你的后端分离。如果你习惯写微服务就更是如此。你只需给你的应用一个连接 URL，数据库就准备好了。

# 你准备好接受挑战了吗？

本文将向您展示如何将 MongoDB 数据库作为服务连接到无服务器 REST API。可能有点厚脸皮，因为使用 AWS 无服务器架构的首选方式是使用他们的 NoSQL DBaaS，名为 [DynamoDB](https://aws.amazon.com/dynamodb/) 。但我喜欢组合奇怪的东西。而且，说实话， [MongoDB 图集](https://www.mongodb.com/cloud/atlas)很惊艳。是 MongoDB 自己的 DBaaS。你可以免费获得一个专用的 MongoDB 集群。

这个设置最棒的地方是，我将向您展示如何以您已经习惯的方式编写代码。你在 Node.js、Express 和 Mongoose 中所知道的一切都将在本教程中重复使用。

新的是使用 [Lambda 计算服务](https://aws.amazon.com/lambda/)背后的思维模式。AWS Lambda 函数基本上是一个 [Docker](https://www.docker.com/what-docker) 容器。一旦 Lambda 被调用，容器就开始旋转并运行代码。这是我们想要初始化数据库连接的时候，第一次调用函数，第一次初始化 Docker 容器。对 Lambda 函数的每个后续请求都应该使用现有的数据库连接。够简单吗？让我们开始吧！

# 启动并运行

我假设您已经对无服务器框架有了基本的了解。我也希望你有一个 AWS 帐户设置。如果没有，请看一下我[链接在顶部](https://hackernoon.com/a-crash-course-on-serverless-with-node-js-632b37d58b44)的文章。

## 1.创建服务

首先，让我们创建一个新的服务来保存我们所有的代码。

```
$ sls create -t aws-nodejs -p rest-api && cd rest-api
```

这个命令将搭建出所有必要的文件和代码来创建我们的 Lambda 函数和 API 网关事件。它将按照我们用`-p`标志给它的路径来做这件事。这意味着它将创建一个名为`rest-api`的目录。我们想换到那个目录，从那里开始工作。

## 2.安装模块

我们需要几个模块。首先，我们需要无服务器离线插件，以便在部署到 AWS 之前能够在本地运行我们的代码。然后我们需要抓住[猫鼬](https://www.npmjs.com/package/mongoose)，我选择的 ORM，和 [dotenv](https://www.npmjs.com/package/dotenv) ，因为我喜欢**而不是**按下 GitHub 的键。把钥匙推到 GitHub 上糟透了。不要那样做。每当你按下 GitHub 的一个键，就会有一只小企鹅死去。我是说，不完全是，但还是很糟糕。

确保你在`rest-api`目录中。先离线安装 Serverless，再安装 mongoose 和 dotenv。

```
$ npm init -y
$ npm i --save-dev serverless-offline
$ npm i --save mongoose dotenv
```

就这样，让我们从终端休息一下，跳到 Atlas 来创建一个数据库。

## 3.在 MongoDB Atlas 上创建数据库

准备好接受更多配置了吗？是啊，没人喜欢这部分。但是和我一起。跳到 MongoDB Atlas 并注册。

[](https://www.mongodb.com/cloud/atlas?jmp=nav) [## 完全托管的 MongoDB，托管在 AWS、Azure 和 GCP 上

### MongoDB Atlas 是一个云托管的 MongoDB 服务，由构建数据库的同一个团队设计和运行。它…

www.mongodb.com](https://www.mongodb.com/cloud/atlas?jmp=nav) 

这是免费的，不需要信用卡。这将是我们玩耍时需要的沙箱。设置好帐户后，打开您的帐户页面并添加一个新组织。

![](img/ee523bd3120c46b4af4d615fe0a42f99.png)

加个你觉得合适的名字，我还是坚持用`rest-api`。点击“下一步”,继续创建组织。

![](img/9f64de4dd8c708bae1cd5f5c58511de1.png)

很好。这将带您进入组织页面。按下新项目按钮。

![](img/8a9c349d707ca5855999c9792f4332ed.png)

这将打开一个页面来命名您的项目。只需再次输入`rest-api`并点击下一步。

![](img/e77ecc910320b9924792aa0fc4a114fb.png)

MongoDB 关心权限和安全性，所以 Atlas 将向您展示另一个管理权限页面。我们现在可以跳过这一步，创建项目。

![](img/2fcfc1b3bc2557a226758ec6e5c2f82e.png)

唷，我们找到了。最后，我们可以创建实际的集群！按下巨大的绿色**“建立新集群”**按钮。这将打开一个巨大的集群创建窗口。您可以保留所有的默认值，只需确保选择 **M0** 实例大小，并禁用备份。

![](img/0ddaef1fb9ce795fbfcd4df1317da9c6.png)![](img/3e165382de429a84db55e9ab091110fa.png)

完成所有这些后，只需为集群添加一个管理员用户，并给他一个非常强的密码。如您所见，该集群的价格将为**$ 0.00/永久**。相当不错。就是这样，点击**“确认&部署”**。

部署您的集群需要几分钟时间。在此过程中，让我们最终开始编写一些代码。

# 写一些代码

那个装置很难。现在我们需要在 **serverless.yml** 文件中编写资源配置，并将实际的 CRUD 方法添加到 **handler.js** 中。

## 4.配置所有 YAML

无服务器框架的神奇之处在于它有很好的初始架构。您几乎可以仅使用 **serverless.yml** 文件中注释掉的代码来创建一个很好的配置。但是，因为我是一个爱干净的人，所以让我们把它全部删除，添加下面的代码。在你把它复制到你的 **serverless.yml** 文件之后，我会继续解释这一切。

这种配置非常简单，刚好满足我们的需求。我们已经将 Lambdas 的最大内存大小设置为 128MB，这对于我们的需求来说已经足够了。在我自己测试了几天后，它们从未超过 50MB。

让我们进入有趣的部分，即**函数**部分。我们一共增加了 5 个函数:**创建**、**获取一个**、**获取全部**、**更新**、**删除**。它们都指向 **handler.js** 文件中同名的导出函数。它们的路径都遵循标准 REST API 的命名约定。令人惊讶的是，这就是我们设置 API 网关资源来触发 Lambda 函数所需要的全部。

差不多就这些了，最后就是增加一个插件部分和 **serverless-offline** 。我们在上面安装了这个模块，在部署到 AWS 之前，我们将用它来测试服务。我猜接下来我们准备好玩 **handler.js** 了。我们走吧！

## 5.充实功能

我们准备好享受真正的乐趣了。我们将首先定义我们需要的 5 个函数，并创建我们想要的行为的初始布局。之后，我们可以创建数据库连接，并添加与 Mongoose 的数据库交互逻辑。

首先打开 **handler.js** 文件。您将看到默认的 hello 函数。继续删除它，并添加下面的代码。

Step 1 of adding logic to the handler.js

好吧，有点不知所措是正常的。但是，没必要担心。这只是 5 个简单的功能。每个函数都有相同的值`context.callbackWaitsForEmptyEventLoop`设置为`false`，并以`connectToDatabase()`函数调用开始。一旦`connectToDatabase()`函数解决了这个问题，它将继续通过 Mongoose 执行数据库交互。我们将在实际的数据库交互中使用`Note`模型方法。但是等等，我们还没有定义或创建这些！你们一定在问自己我怎么了。我是故意这样做的，首先我想让你看到这并不复杂，与用 Node.js 和 Express 创建 REST API 也没有什么不同。

***注意*** *:* `context.callbackWaitsForEmptyEventLoop` — *默认情况下，回调会等到 Node.js 运行时事件循环为空后，才会冻结进程，并将结果返回给调用者。您可以将该属性设置为 false，以请求 AWS Lambda 在调用* `*callback*` *后立即冻结进程，即使事件循环中有事件。AWS Lambda 将冻结 Node.js 事件循环中的流程、任何状态数据和事件(下一次调用 Lambda 函数时，如果 AWS Lambda 选择使用冻结的流程，将处理事件循环中的任何剩余事件)。
-* [*AWS 文档*](https://docs.aws.amazon.com/lambda/latest/dg/nodejs-prog-model-context.html)

是时候添加实际的数据库连接了。在我们添加代码之前，重要的是要理解连接将被建立一次。当 Lambda 第一次被调用时，这被称为冷启动，AWS 将启动一个 Docker 容器来运行代码。这是我们连接到数据库的时候。所有后续请求都将使用现有的数据库连接。从概念上讲，这很容易理解，但是当我们需要在代码中理解它的时候，就很难了。开始了。

## 6.添加数据库连接

连接到 MongoDB 的过程是双重的。我们需要创建一种动态的方式来创建连接，但也要确保重用相同的连接(如果可用的话)。我们会慢慢开始。

在服务的根目录下创建一个新文件，就在 **handler.js** 旁边。给它起一个很有逻辑的名字 **db.js** ，并添加下面的代码。

***注意*** *:此语法对 mongose 5 . 0 . 0-rc0 及以上版本有效。它将不会与低于 5 的任何版本的猫鼬一起工作。*

在第 1 行，我们需要 mongose，就像我们习惯的那样，在第 2 行，我们添加了 mongose 使用的本地 promise 库。这是因为当我们用`Note`模型方法调用它们时，我们希望`.then`在 **handler.js** 中正常工作。

那么`isConnected`变量呢？我们正在创建一个闭包，并将`isConnected`视为正在运行的 Docker 容器中的当前数据库状态。看看我们输出的`connectToDatabase`函数。在第 12 行，我们正在用一个环境变量提供的连接字符串建立一个连接。这个函数返回一个承诺，我们简单地用`.then`并得到一个`db`对象。这个对象表示当前的连接，并且有一个我们特别感兴趣的属性。`.readyState`将告诉我们连接是否存在。如果是，就等于`1`，否则就是`0`。

我们基本上是缓存数据库连接，确保它不会被创建，如果它已经存在。那样的话我们就马上解决承诺。

随着 **db.js** 文件的创建，让我们在 **handler.js** 中要求它。只需将这段代码添加到处理程序的顶部。

```
// top of handler.js
const connectToDatabase = require('./db');
```

## 7.添加注释模型

再看一下 handler.js。可以看到我们在函数中调用了`Note`模型来检索数据，但是没有定义模型。好吧，现在正是时候。

在服务根目录下创建一个新文件夹，命名为 **models** 。在其中创建另一个文件并将其命名为 **Note.js** 。这将只是一个简单的 mongoose 模式和模型定义。

我们将导出模型本身，以便在 **handler.js** 中使用它。这就是数据库连接性。我们只需要在处理程序的顶部添加另一个 require 语句，就可以开始了。

```
// top of handler.js
const connectToDatabase = require('./db');
const Note = require('./models/Note');
```

很好，现在剩下的就是添加一个环境变量来保存我们的 MongoDB 数据库连接 URL。跟 **dotenv** 那是小菜一碟。

## 8.对环境变量使用 dotenv

用 dotenv 将配置文件和密钥放在一个完全独立的文件中非常容易，这是一个真正的生活保护程序。您只需将文件添加到**中。gitignore** 并确保你不会冒险泄露任何密钥。让我展示给你看。

添加一个新文件，将其命名为 **variables.env** 。请确保将其放在服务的根目录中。文件本身只有一行，那是环境变量的名字和它的值。它看起来应该有点像这样。

```
DB=mongodb://<user>:[<password>@](mailto:admin123@cluster0-shard-00-00-4ra8y.mongodb.net)mongodb.net:27017/db
```

但是，首先我们需要找到连接 URL。为此，我们需要回到阿特拉斯。在您之前创建的项目的主集群页面上，您将看到您的集群已经被创建。它有一个我们想按的连接按钮。

![](img/ad4ebef8f13ce7ead67106f86690b689.png)

这将打开一个新的弹出窗口，您需要将一个 IP 地址添加到白名单中，这样您就可以访问数据库了。然后，您通过按下**“连接您的应用程序”**按钮获取连接 URL。

![](img/04b404351fd6a433c5039875a1b0e492.png)

按下**“连接应用程序”**后，系统会提示您“**复制连接字符串”**。按“**我使用的是 3.4 或更早版本的驱动程序”**，你就可以复制网址了。哇，这是一个令人厌倦的旅程。

![](img/30a3feab1de3785dad035985cacc44a5.png)

一旦你复制了它，回到 **variables.env** 文件并添加实际的连接 URL。

```
DB=mongodb://dbadmin:[reallystrongpassword@cluster0-shard-00-00-e9ai4.mongodb.net](mailto:reallystrongpassword@cluster0-shard-00-00-e9ai4.mongodb.net):27017,cluster0-shard-00-01-e9ai4.mongodb.net:27017,cluster0-shard-00-02-e9ai4.mongodb.net:27017/test?ssl=true&replicaSet=Cluster0-shard-0&authSource=admin
```

确保不要在 **DB** 和连接 URL 之间添加空格。更改您之前设置的密码`<password>`。我的密码是“真正的强密码”。现在会发生什么？这个文件中的变量将被加载到 Node.js 中的`process.env`对象中，这意味着你可以用你已经习惯的标准方式访问它们。

***注意*** *:不要忘记将变量. env 添加到。gitignore！*

最后，在开始测试之前，我们需要 dotenv 模块并指向保存环境变量的文件。将这个代码片段添加到您的 **handler.js** 文件的顶部。

```
require('dotenv').config({ path: './variables.env' });
```

就是这样。是时候尝试一下了。

# 做些测试怎么样？

我们准备好测试 API 了。首先，我们需要离线运行无服务器。但是，由于我们在 **Note.js** 中的 Mongoose 模型定义，我们需要在运行它时添加一个标志。

```
$ sls offline start --skipCacheInvalidation
```

**在 Node.js 中当你 `*require()*` *一个模块的时候，它存储了该模块的一个缓存版本，这样以后所有对* `*require()*` *的调用都不用从文件系统重新加载该模块。***

一旦您在终端中运行了该命令，您应该会看到类似这样的内容。

![](img/edec64054855723ac7b9baefcb91430b.png)

我们所有的路线都正常运行。打开您选择的 REST 客户端，邮递员、失眠或任何您喜欢的，让我们继续测试。

使用[失眠](https://insomnia.rest/)，我用 JSON 主体创建了一个对`[http://localhost:3000/notes](http://localhost:3000/notes)`的 POST 请求。

![](img/0477efccb8a2dfcc7d504b5842a1bbe8.png)

检查终端，您可以看到`=> using new database connection`被记录，这意味着初始数据库连接已经建立。再发一个帖子请求，你会看到`=> using existing database connection`被登录了。

太棒了，添加一个新笔记很有效。让我们使用 **getOne** 方法检索刚刚添加的注释。从响应中复制`_id`并粘贴到 GET 请求的 URL 中。

![](img/dc5673878b7812319e1dd70c703abfe5.png)

检索单个笔记也很好。把他们都找回来怎么样？删除 ID 路由路径参数，再次点击**“发送”**。

![](img/12d2ddaa7615527ac21b43a42e91ba12.png)

只需再尝试两个方法，即编辑和删除方法。从检索到的音符中选择一个`_id`并再次将其添加为路径参数。现在将方法改为 PUT 并添加一个 JSON 主体。输入不同的标题和描述，然后点击**“发送”**。

![](img/c7d6b1a6352b140d1d4c8a53edbcf890.png)

编辑工作很好，正如我们所希望的那样。只剩下删除。更改删除方法，删除请求正文，最后一次点击**“发送”**。

![](img/a41d6d0c1e8867053f9fc777503789f4.png)

该注释已成功删除。关于测试，这已经足够了。我们已经准备好将服务部署到 AWS。

# 负责部署和监控

唷，你需要考虑很多事情。我们在最后冲刺阶段。唯一剩下的事情就是部署服务，并通过使用一个名为 [Dashbird](https://www.dashbird.io/) 的监控工具来确保它按照我们想要的方式运行。

## 9.部署

无服务器框架使部署变得快速而轻松。你需要做的就是运行一个命令。

```
$ sls deploy
```

它将自动在 AWS 上提供资源，打包并将所有代码推送到 S3，然后从那里发送到 Lambdas。终端应该显示类似如下的输出。

![](img/7b7bbfb9c35d52d0e55870f644f7e42b.png)

***注意*** *:您可以使用提供的端点从上面重复测试过程。*

这就是部署流程的全部内容。简单对吗？这就是我如此热爱无服务器框架的原因。

## 10.监视

让我们用另一个很酷的工具来结束它。我用 Dashbird 监控我的 Lambdas，我很喜欢它。它有一个[免费层](https://dashbird.io/pricing/)并且不需要信用卡注册！我向您展示这一点也是为了让您看到 Lambda 函数调用的控制台日志。它们会告诉你 Lambda 何时使用新的或现有的数据库连接。这是主仪表板的样子，在这里我可以看到我所有的 Lambdas 和它们的统计数据。

![](img/6b562da04a77d4edc9bcd8b6ca148b23.png)

按下**rest-API-dev-get all**Lambda 函数后，我将进入一个屏幕，显示这个特定函数的所有统计数据和日志。

![](img/33907f9cd2db68c3b5e92db757afd7de.png)

在底部，您可以看到对 getAll 函数的两次调用。在按下两者中较老的一个之后，它把我带到另一个页面，显示关于那个特定调用的信息。

![](img/79e4426fd3d81d8860aa2540581d84c4.png)

正如你所看到的，控制台用`=> using new database connection`进行了记录，实际的请求花费了大约 1.5 秒。

后退并按下另一个调用，我们可以看到一个类似的，但幸运的是，我们看到的是一个不同的图像。

![](img/98d09cbc2ccabf9a4a81d6b15f1734f8.png)

一旦相同的 Lambda 函数被再次调用，它将重用现有的连接。在这里的日志中可以清楚的看到。

# 线的尽头

真是情绪过山车。您已经踏上了用 MongoDB 创建无服务器 REST API 的旅程。我已经尽了最大努力来传递我到今天为止所收集的经验，以向您展示创建合适的 API 的首选方式。我展示的许多技巧都是我自己日常使用的。明智地使用这些技能，并享受深入挖掘无服务器架构的可能性及其带来的一切。

如果你想看看我们上面写的所有代码，[这里是库](https://github.com/adnanrahic/building-a-serverless-rest-api-with-nodejs)。或者如果你想看我最新的文章，请到这里来。

[](https://medium.com/@adnanrahic/latest) [## 阿德南·拉希奇写的最新故事

### 阅读 Adnan Rahi 在 Medium 上写的最新故事。软件工程师@bookvar_co .编码教育家@ACADEMY387…

medium.com](https://medium.com/@adnanrahic/latest) 

*希望你们喜欢读这篇文章，就像我喜欢写这篇文章一样。*
*你觉得这个教程会对某人有帮助吗？不要犹豫分享。如果你喜欢，击碎下面的* ***拍手*** *这样其他人会在 Medium 上看到这个。*