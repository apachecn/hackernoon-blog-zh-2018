# 用 MongoDB、Express 和 Docker 构建 CRUD API

> 原文：<https://medium.com/hackernoon/build-a-crud-api-with-mongodb-express-and-docker-70510c6f706b>

![](img/9eb8750d5f9777576a80a6c2066f6c04.png)

Photo by [Tim Easley](https://unsplash.com/photos/KjNGa3HvGgY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/container?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

世界正在被 CRUD APIs 蚕食，为什么不学着造一个呢？类似这个的项目是很常见的面试带回家的项目。在本教程中，我们将使用 Express.js 构建一个 API，由 MongoDB 数据库支持，所有这些都用 Docker Compose 部署，并用 Mocha 和 Travis CI 测试。该 API 及其各种组件的设计大致基于 [12 因子应用方法](https://12factor.net/)。

# 设置

您需要安装 Node.js、Docker 和 Docker Compose。此外，您还需要一个免费的 Github 帐户和一个免费的 Travis CI 帐户。本教程假设您对 Javascript(也会用到一些 ES6 特性，比如 arrow 函数)和 Bash(或者您的操作系统的 shell)有基本的了解。本教程的所有代码也可以在 Github 上找到。

首先让我们创建一个新目录(`mkdir`)并将`cd`放入其中。然后用`npm init`在里面创建一个新的 Node.js 包。还用`git init`初始化一个新的 Git 存储库。接下来，我们将使用`npm i --save express mongodb body-parser`和`npm i --save-dev mocha tape supertest`安装 npm 依赖项。之后，创建如下所示的目录结构:

```
crud-api
├── docker/
├── models/
├── routes/
└── tests/
```

接下来的几个步骤是环境和忽略的辅助文件。首先让我们创建一个`.dockerignore`和一个`.gitignore`。Docker ignore 文件通知您的 Docker 构建不包括哪些文件，而 Git ignore 为您的 Git 提交做同样的事情。我的看起来像这样:

之后，你需要一个看起来像我的`example.env`的`.env`文件。这为 Docker Compose 提供了对我们的应用程序至关重要的信息，但是不应该存储在版本控制中。

可选地，但是强烈推荐，您可以创建一个`README.md`和一个`LICENSE`。我们写点代码吧！

# 编写服务器

我们将跨 3 个文件构建这个 Express 服务器:`index.js`、`routes/routes.js`和`models/Document.js`。Express.js 是一个极简的 Node.js web 框架，它利用了中间件的概念。当一个请求进入服务器时，它依次流经每个中间件，直到它到达终点并出错，或者某个函数基于它进行一些计算并返回。让我们从`index.js`开始。

## 索引. js

如果您以前使用过 Express.js，这应该看起来很熟悉，但是我们将一节一节地介绍这一部分。首先是导入，`body-parser`可能不是一个显而易见的包，但它是中间件中非常重要的一部分。它解析传入请求的主体，以便在下一节中更容易地在我们的路由内部处理。接下来，我们根据开发或生产环境设置数据库名称，从环境变量中分配 MongoDB url，并为 MongoDB 客户机设置选项。第一个选项使用较新的解析器，否则会得到一个方法弃用错误，另外两个选项控制数据库客户机失去连接时的行为。接下来，我们导入路由器，从环境中设置我们的服务器端口，但默认为 80，接下来的两行是样板快速设置。

现在我们进入服务器的核心部分，中间件堆栈。您通常希望您的`body-parser`中间件解析器首先出现在 API 中，这样每个请求都会得到解析。然后你的请求到达你的服务器的路由器，如果它匹配里面描述的任何一条路由，那么相应的函数将被触发，一切正常。如果没有匹配的路线，那么我们的服务器将返回一个`404 Not Found`。

我们的最后一部分将我们的`MongoClient`连接到我们的 MongoDB 实例。总是以某种方式处理您的错误，但在这种情况下，我们希望记录一个错误并退出。在错误处理之后，我们将数据库连接分配给一个服务器全局变量，并启动服务器。当我们的服务器已经正确启动时,`app.emit`向我们的测试套件发出警报，我们导出服务器，这样我们就可以在测试中导入它。

一旦你写完了所有这些，让我们开始规划我们的路线吧！

## routes/routes.js

在这个文件中，我们初始化并导出一个包含所有 API 路由的 Express 路由器。每个路由都采用 router 对象的形式，后跟 HTTP 方法。在每条路由中，我们定义路径和一个箭头 lambda 来处理我们的请求(`req`)和响应(`res`)。如果函数没有返回，而是希望将请求进一步传递到中间件堆栈，那么通常会调用`next`变量。在箭头 lambda 内的每一条路径中，我们首先从我们的服务器级变量`req.app.locals.db`获取 MongoDB 连接，然后是我们想要的集合。在这种情况下，我们只有一个集合，`documents`。然后在每种情况下，我们调用一个方法从数据库返回一些数据或错误。以下是这些功能及其对应的路径:

*   `find()`获取集合中的所有文档— `/documents/all`
*   `findOne()`在这种情况下，根据客户端提供的文档 id 获取特定的文档— `/documents/:id`
*   `insertOne()`上传新文档到数据库— `/documents/new`
*   `deleteOne()`根据客户提供的文档 id 删除文档— `/documents/delete/:id`
*   `updateOne()`基于客户端发送的 JSON 请求体修改文档— `/documents/edit/:id`

route 中单词前面的冒号表示在处理程序中使用`req.params`访问的参数。由 MongoDB 自动分配给每个文档，这就是为什么我们的数据模型中没有唯一标识符或其他主键。在我们进行数据库查询之后，我们要么得到数据(`result`)要么得到错误(`err`)。我们的错误处理行为是使用我们的`res`对象发回一个包含错误的 HTTP 400 和 JSON。否则，我们发回 HTTP 200 和查询结果。

之后，我们导出路由器对象，这样我们就可以在`index.js`中将它用作`/api`路由的路由中间件。这意味着我们每个 API 路径的完整路径是`/api/documents/`。最后，我们继续定义我们的数据模型。

## 模型/文档. js

这份文件应该相当清楚。这是一个 Javascript 类，带有一个构造函数，接受 3 个字符串并存储它们。这充当了 MongoDB 中数据的模式。

处理好服务器后，让我们继续用 Docker 部署我们的服务器！

# 将你的 API 分类

首先，我们需要为我们的生产构建编写`Dockerfile.production`。我们从基于`node:10.12.0-alpine`图像的容器开始。之后的第一层下载一个脚本，它将等待任意服务启动，这样服务就不会在没有依赖关系的情况下上线，我们用`chmod +x`使它可执行。之后，我们设置我们的工作目录。接下来，我们通过分配环境变量`NODE_ENV`让我们的应用程序知道我们正在生产中(在我们的服务器中，我们只检查`NODE_ENV === 'dev'`，但是我们稍后可能想要明确地检查`'prod'`)。在复制其他文件之前，我们复制我们的`package.json`并安装我们的依赖项，以便 Docker 可以缓存它们用于后续的构建。接下来，我们从`docker-compose.yml`和`.env`获取 ARG 端口，并将其暴露给内部 Docker 网络。成功复制了我们所有的文件。最后，我们执行脚本来等待 MongoDB 上线，然后运行我们的服务器。

我们的`Dockerfile.test`和第一个类似，其实就是基于第一个。拉出生产容器后，我们安装`dev-dependencies`和我们的测试转轮摩卡。最后，我们使用我们的测试运行器(`--exit`指定了一个旧版本的行为，所以 Mocha 一旦完成就退出)。该文件的其余部分应该与生产版本几乎相同。

现在我们已经准备好了两个 docker 文件，让我们构建将所有容器联机的`docker-compose.yml`。我们使用的是`docker-compose.yml`的`3.0`版本，与`2.x`版本有些不同，所以在查看堆栈溢出或其他教程时要小心。我们的主要部分是`services`，由我们的 Node.js/Express.js`backend`和我们的 MongoDB `database`组成。在`backend`中我们第一次遇到`build`；由于我们的文件在`docker/`中，我们需要将上下文设置为项目根目录，指定一个相对于根目录的 Dockerfile，并注入我们的 port 环境变量。接下来，我们加载包含重要应用程序机密的`.env`,这些机密不打算提交给 Git。我们希望我们的服务器经常并且优雅地失败，所以我们让 Docker Compose 总是重启它。接下来，我们绑定容器的暴露端口。最后，对于我们的脚本来说`WAIT_HOSTS`是必要的，它确保我们的服务器不会在我们的数据库之前上线。接下来我们有我们的`database`，它基于 Docker Hub 上发布的图像，而不是需要构建的本地 Docker 图像。我们再次为这个部分导入我们的`.env`。然后我们安装一个存储数据的地方，这样它将在容器之间持久化。接下来我们`expose` MongoDB 到内部 Docker 网络的端口，但不是主机操作系统。最后，我们发出一个命令，以便 Docker Compose 可以启动我们的 MongoDB 实例。

这个文件中只有几处不同，但它们相当重要。首先我们将 Dockerfile 更改为`Dockerfile.test`。其次，我们使用不同的数据存储位置进行测试，以免污染我们的生产数据。

最后，我们来看看我们的 npm 脚本。这些只是`docker-compose`命令的别名，所以我们没有输入长命令，如果需要，我们有一个单一的位置来改变命令。`-f`标志指示了`docker-compose.yml`的位置，因为我们没有将它们存储在项目根中。在所有容器联机或失败后，`up`后的`-d`标志使过程后台化。

一旦你完成了所有这些，就该进入最后一部分，测试我们的代码和部署了。

# 测试您的代码

我们要写一个集成测试。对于实际的生产应用程序，除了至少一个集成测试之外，您可能还需要一系列的单元测试。对于单元测试，你需要一个独特的`it()`函数用于每个测试，一个`beforeEach()`函数用于插入测试文档，一个`afterEach()`函数用于在测试完成后移除文档。

让我们从编写一个`before()`函数开始，该函数等待`app.emit`在`index.js`触发，表示我们的服务器已经成功启动。一旦完成，我们就调用`done()`回调，这样 Mocha 就知道要继续测试了。

我们从包含单个`it()`函数的`describe()`块开始，因为我们只有一个测试。每个连续步骤被描述为一个`test()`功能。为了插入一个新文档，获取所有文档并存储一个用于其他测试的文档 id，获取一个特定的文档，更新一个特定的文档，最后删除一个特定的文档。

一旦我们完成了所有的测试，我们就可以进入 Travis CI 配置了。默认情况下，Travis 不启动大多数服务，所以我们显式启动 Docker。然后我们检查我们的 Docker 版本(不是绝对必要的，方便调试版本不匹配或者 Docker 没有运行)，将我们的`example.env`复制到`.env`以便我们的构建正确运行，并且我们停止一些不必要的服务以便我们的构建和测试运行得更快。之后，我们使用我们的 npm 脚本来构建我们的生产容器，并使用它们作为运行我们测试的资源。

一旦你写完了所有这些，你就应该能够进入 Github，并在 Travis CI 上查看你的存储库页面，以获得一个通过的版本！要在本地运行您的服务，键入`npm run build`，一旦完成`npm run production`。一旦这些都完成了，你就可以运行`docker ps`来显示所有正在运行的容器，这将显示你的 MongoDB 容器和一个名为`docker_backend`的容器。你现在可以运行`curl localhost:80/api/documents/all`，它将返回`{“error": “No documents in database"}`。我建议使用下面的 Markdown 代码片段将当前的构建状态嵌入到您的`README.md`中:

```
[![Build Status]([https://travis-ci.org/<your Github username>/<your repository name>.svg?branch=master)](https://travis-ci.org/](https://travis-ci.org/torchhound/mongo-crud.svg?branch=master)](https://travis-ci.org/torchhound/mongo-crud))[<your Github username>](https://travis-ci.org/torchhound/mongo-crud.svg?branch=master)](https://travis-ci.org/torchhound/mongo-crud))[/](https://travis-ci.org/torchhound/mongo-crud.svg?branch=master)](https://travis-ci.org/torchhound/mongo-crud))[<your repository name>](https://travis-ci.org/torchhound/mongo-crud.svg?branch=master)](https://travis-ci.org/torchhound/mongo-crud))[)](https://travis-ci.org/torchhound/mongo-crud.svg?branch=master)](https://travis-ci.org/torchhound/mongo-crud))
```

感谢阅读，如果这篇教程对你有帮助，请留下一个或几个掌声！

***Joe Cieslik 是 Whiteboard Dynamics 的 CEO，这是一个专门从事函数式编程和 Android 的全栈开发团队。你可以雇佣我或我的团队在***[***【whiteboarddynamics.co】***](https://whiteboarddynamics.co/)***打造你的下一个杀手级 app。***