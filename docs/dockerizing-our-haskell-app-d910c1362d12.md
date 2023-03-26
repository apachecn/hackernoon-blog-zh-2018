# 整理我们的 Haskell 应用程序

> 原文：<https://medium.com/hackernoon/dockerizing-our-haskell-app-d910c1362d12>

![](img/a68c82d4d88aa66da8d7be85b0f6d550.png)

[上周](https://mmhaskell.com/blog/2018/4/23/deploying-confidently-haskell-and-circle-ci)，我们探索了如何用 Circle CI 自动部署我们的 Haskell 应用程序。每当我们推出一个分支时，Circle CI 会将我们的代码加载到一个容器中，构建它，并运行我们的任何测试。我们还配置 Heroku 在主分支通过构建时部署我们的新代码。

我们的系统有几个弱点。首先，我们的配置要求我们每次都要下载堆栈程序，这有点麻烦。设置堆栈需要我们 Circle 配置中大约一半的命令！第二个缺点是我们在每次部署时都构建了两次代码。首先，Circle 容器会构建它。然后 Heroku 也会编译它。本周，我们将使用 Docker 图像解决这些问题。

# 使用 Docker 图像

上周我们用了一个香草圆形容器。我们可以通过使用预先存在的 Docker 映像来简化我们的配置。还记得我们`build_project`部分的开头吗？它看起来像这样:

```
jobs:
  build_project:
    machine: true
```

关键字`machine`告诉 Circle 使用一个未配置的 Linux 机器。因为上面什么都没有，我们需要自己下载并安装 Stack。然而，Circle 也允许我们使用 Docker 图像作为我们机器的起点。我们将使用来自 [Haskell Docker 库](https://hub.docker.com/r/library/haskell/tags/)的图像。这些都有一个特定版本的 GHC 安装，后来的也有堆栈。这些图片比 GHC 发布的稍晚一些。因此，我们将使用 GHC 8.0.2，并更新我们的`stack.yaml`文件，以使用 LTS 9.21，这是 GHC 的最新版本。下面是我们如何编写我们的圆配置来使用这个图像:

```
jobs:
  build_project:
    docker:
      - image: haskell:8.0.2
```

现在我们可以从根本上简化文件的其余部分！堆栈和 GHC 将预装，所以我们可以删除所有相关的步骤。我们还将删除已安装的堆栈目录上的缓存步骤。这留给我们以下配置文件:

```
version: 2
jobs:
  build_project:
    docker:
      - image: haskell:8.0.2
    steps:
      - checkout
      - restore-cache:
        keys:
          - stack-work-{{checksum “stack.yaml”}}-\
                       {{checksum “HaskellTestApp.cabal”}}
      - run: stack setup
      - run: stack build
      - run: stack test
      - save_cache:
        key: stack-work-{{checksum “stack.yaml”}}-\
                        {{checksum “HaskellTestApp.cabal”}}
        paths:
          - “.stack-work”workflows:
  version: 2
  build_and_test:
    jobs:
      - build_project
```

# 打造我们自己的码头工人形象

现在我们的构建更有效率了，但是我们还没有解决系统中更大的问题。在本文的其余部分，我们将使用 Docker 创建一个新的图像，并在其上构建我们的代码。然后我们可以将这个映像推送到 Heroku，而不是用 buildpack 重新构建我们的代码。

为此，我们将把一些现有的 Circle 配置工作并入 Docker 本身。首先，我们需要在项目的根处定义一个`Dockerfile`。这个文件指定了 Docker 需要运行的命令，以便用我们的代码创建一个映像并运行服务器。这是我们的样子:

```
# Use the existing Haskell image as our base
FROM haskell:8.0.2# Checkout our code onto the Docker container
WORKDIR /app
ADD . /app# Build and test our code, then install the “run-server” executable
RUN stack setup
RUN stack build --test --copy-bins# Expose a port to run our application
EXPOSE 80# Run the server command
CMD [“run-server”]
```

第一个重要的部分是我们将“继承”我们在 Circle 和`FROM`上使用的 Haskell Docker 图像。然后，我们将运行安装命令，并构建项目。我们将把参数传递给`build`，它将运行测试，并安装我们的可执行文件。然后，我们将从容器中运行服务器。

# 在 Circle 上建立我们的码头工人形象

为了在远程存储库上实际保存 Docker 映像，我们需要创建一个 Docker 帐户。我们不需要创建自己的存储库，因为我们最终会将我们的图像存储在 Heroku 存储库中。

我们不再需要运行堆栈命令作为循环配置的一部分。码头工人替我们处理。我们可以回到使用普通机器，因为 Docker 也处理使用 Haskell 映像。以下是我们在 Circle 上的核心配置:

```
jobs:
  build_project:
    machine: true
    steps:
      - checkout
      - run: echo $DOCKER_PASSWORD | docker login \
                                     --username=$DOCKER_USERNAME \
                                     --password-stdin
      - run: docker pull \                
               registry.heroku.com/$HEROKU_APP/web:$CIRCLE_BRANCH
      - run: docker build -t \
               registry.heroku.com/$HEROKU_APP/web:$CIRCLE_BRANCH
      - run: docker push \
               registry.heroku.com/$HEROKU_APP/web:$CIRCLE_BRANCH
```

关键命令显然是四个`docker`命令。首先，我们使用凭据作为环境变量登录 Docker 帐户。接下来，我们将从与我们的应用程序相关的 Heroku 图像库中提取现有图像。我们不需要做任何事情来设置这个存储库，但我们需要配置应用程序来使用它。然后我们构建我们的容器并用我们当前的分支名称标记它。只要成功了，我们就会把这个新图像推回我们的 Docker 存储库。

# Heroku 集成

为了在 Heroku 上使用这个图像，我们需要从仪表板再次更新应用程序的“部署”部分。我们将使用 Heroku 注册表选项，而不是使用 Circle CI。现在，我们成功的构建将把我们的代码上传到我们的 Heroku 注册表。然后 Heroku 自动更新我们的 app！另外，我们现在也不需要在 Heroku 上重建代码了！

不过，还有一点需要注意。为了从 Heroku 注册表进行推和拉，我们还需要从我们的 circle 机器登录 Heroku。Circle CI 版本 2 还没有对此的内置支持，所以有点棘手。在我们自己的机器上，我们将使用带有`heroku login`命令的 CLI 登录 Heroku。但是我们不能像使用 Docker 的 login 命令那样使用`stdin`命令。

但是我们可以用一个小脚本复制登录的最终结果。登录 Heroku 会创建一个名为`~/.netrc`的文件来存储我们的凭证。我们可以编写这样的脚本来输出所有的信息:

```
#! /bin/bashcat > ~/.netrc << EOF
machine api.heroku.com
  login $HEROKU_LOGIN
  password $HEROKU_PASSWORD
machine git.heroku.com
  login $HEROKU_LOGIN
  password $HEROKU_PASSWORD
EOFheroku container:login
```

我们运行最后的`heroku container`命令来实际连接到存储库。注意`$HEROKU_PASSWORD`环境变量应该使用您的 Heroku API 密钥，而不是您的 Heroku 密码。我们称该变量为`PASSWORD`，因为`HEROKU_API_KEY`环境变量是特殊的。过早设置它可能会导致 CLI 出现问题。

这个脚本保存为`setup_heroku.sh`，我们可以像这样从我们的 Circle 脚本中调用它:

```
jobs:
  build_project:
    machine: true
    steps:
      - checkout
      - bash .circleci/setup_heroku.sh
      ...
```

现在一切都应该工作了！我们的应用程序应该会自动部署到 Heroku，无需重新编译！

# 结论

我们现在已经大大提高了部署流程的效率。首先，我们使用 Docker Haskell 映像来避免手动下载 Stack。然后，我们在此基础上创建了自己的 Docker 映像，并将其推送到注册表中。一旦我们将 Heroku 应用程序连接到这个注册表，我们就不再需要在那里编译它了。下周，我们将通过使用类似的过程将我们的代码推送到 AWS 而不是 Heroku 来结束这个系列。

现在您可以部署您的代码，您可以创建任何您想要的 Haskell 应用程序！下载我们的[制作清单](https://www.mmhaskell.com/production-checklist)，获得更多可以在应用中使用的库的想法。

如果你以前从未使用过 Haskell，下载我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)开始吧！