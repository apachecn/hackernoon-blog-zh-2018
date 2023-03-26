# 自信地部署:Haskell 和 Circle CI

> 原文：<https://medium.com/hackernoon/deploying-confidently-haskell-and-circle-ci-53cc44a300b4>

![](img/0526aeb0cc4d045ff16c2b5975c71094.png)

在上周的文章中，我们使用 Heroku 将 Haskell 代码部署到云中。我们的解决方案是可行的，但是这个过程也是非常基础和非常手动的。让我们回顾一下使用这种方法在实际项目中部署代码的步骤。

1.  对主分支机构提出拉取请求
2.  将代码合并到母版中
3.  在本地拉主控，运行测试
4.  手动运行`git push heroku master`
5.  希望 Heroku 一切顺利

这不是一个好方法。在我们的开发过程中，无论哪里有手工步骤，我们都可能忘记一些东西。这几乎总会在某个时候反咬我们一口。在本文中，我们将看到如何使用 Circle CI 来自动化我们的开发工作流。

# Circle 入门

要阅读本文，您应该已经将项目存储在 Github 上了。一旦有了这个，你就可以很容易的和 Circle 融合了。去[圈子网站](https://www.circleci.com/)用 Github 登录。然后转到“添加项目”。您应该会看到您所有的个人存储库。单击您的 Haskell 项目应该允许您集成这两个服务。

现在 Circle 知道了我们的资源库，每当我们将代码推送到 Github 时，它都会尝试构建。但是我们必须告诉 Circle CI，一旦我们发布了代码，该做什么！对于这一步，我们需要创建一个配置文件，并将其存储为我们的存储库的一部分。请注意，我们将使用 Circle CI 配置的第 2 版。为了定义这个配置，我们首先在存储库的根目录下创建一个名为`.circleci`的文件夹。然后我们制作一个 YAML 文件叫做`config.yaml`。

在 Circle V2 中，我们为 Circle 容器指定要运行的“工作流”。为了简单起见，我们将把我们的操作限制在一个工作流中。我们在配置的底部指定了`workflows`部分:

```
workflows:
  version: 2
  build_and_test:
    jobs:
      - build_project
```

现在在顶部，我们将再次指定版本 2，然后列出我们的`build_project`工作的基本定义。

```
version: 2
jobs:
  build_project:
    machine: true
    steps:
      - checkout
      - run: echo “Hello”
```

`machine`部分显示了我们在项目中使用的默认圆机图像。没有我们可以使用的内置 Haskell 机器配置，所以我们使用一个基本映像。然后对于我们的步骤，我们将首先检查我们的代码，然后运行一个简单的“echo”命令。现在，让我们考虑如何让这台机器获得堆栈实用程序，这样我们就可以实际构建我们的代码了。

# 安装堆栈

所以现在我们的 Circle 容器没有 Haskell 工具。这意味着我们需要从头开始。这是一个有用的学习练习。我们将学习在 Linux 机器上构建 Haskell 项目所需的最少步骤。下周，我们将看到一条我们可以使用的捷径。

幸运的是，Stack 工具为我们处理了大部分问题，但是我们首先必须下载它。因此，在检查了我们的代码之后，我们将运行几个不同的命令来安装 Stack。下面是它们的样子:

```
steps:
  - checkout
  - run: wget https://github.com/commercialhaskell/stack/releases/download/v1.6.1/stack-1.6.1-linux-x86_64.tar.gz -O /tmp/stack.tar.gz
  - run: sudo mkdir /tmp/stack-download
  - run: sudo tar -xzf /tmp/stack.tar.gz -C /tmp/stack-download
  - run: sudo chmod +x /tmp/stack-download/stack-1.6.1-linux-x86_64/stack
  - run: sudo mv /tmp/stack-download/stack-1.6.1-linux-x86_64/stack /usr/bin/stack
```

`wget`命令从 Github 下载堆栈。如果您使用的 Stack 版本与我们的不同(1.6.1)，您当然需要更改版本号。然后，我们将创建一个临时目录，将实际的可执行文件解压缩到该目录。然后我们使用`tar`来执行解压缩步骤。这使得我们将`stack`可执行文件放在适当的文件夹中。我们将赋予这个可执行文件`x`权限，然后将它移动到机器的路径上。那我们就用`stack`！

# 构建我们的项目

现在我们已经完成了大部分艰难的工作！从这里开始，我们将只使用堆栈命令来确保我们的代码正常工作。我们从运行`stack setup`开始。这将下载我们的项目需要的任何版本的 GHC。然后我们将运行`stack test`命令来确保我们的代码编译并通过所有的测试套件。

```
steps:
  - checkout
  - run: wget …
  ... 
  - run: stack setup
  - run: stack test
```

注意，Circle 希望我们的命令以退出代码 0 结束。这意味着如果它们中的任何一个有非零的退出代码，构建将是“失败”的。这包括我们的`stack test`步骤。因此，如果我们推送的代码没有通过任何测试，我们会将其视为构建失败！这使我们省去了手动运行测试的额外步骤，并“希望”它们能在我们部署的环境中工作。

# 贮藏

目前这一过程有一个相当大的弱点。我们做的每一个圆形容器都是从零开始的。因此，我们将不得不下载 GHC 和所有不同的库，我们的代码依赖于每一个构建。这意味着您可能需要等待 30-60 分钟来查看您的代码是否通过，这取决于您项目的大小！我们不想这样。所以为了让事情变得更快，我们将告诉 Circle 缓存这个信息，因为它不会在大多数构建中改变。我们将采取以下两个步骤:

1.  仅在`stack.yaml`改变时下载 GHC(因为 LTS 可能已经改变)。这涉及到缓存`~/.stack`目录
2.  仅当`stack.yaml`或我们的`.cabal`文件改变时才重新下载库。为此，我们将缓存`.stack-work`库。

对于其中的每一个，我们将创建一个适当的缓存键。在构建过程的开始，我们将尝试根据特定的键从缓存中恢复这些目录。作为每个密钥的一部分，我们将使用相关文件的校验和。

```
steps:
  - checkout
  - restore-cache:
      keys:
        - stack-{{ checksum “stack.yaml” }}
  - restore-cache:
      keys:
        - stack-{{checksum “stack.yaml”}}-{{checksum “project.cabal”}}
```

如果这些文件改变，校验和将是不同的，所以圆将无法恢复目录。然后，我们的其他步骤将完全运行，下载所有相关信息。在这个过程的最后，我们希望确保我们已经在同一个键下保存了这些目录。我们用`save_cache`命令来做这件事:

```
steps:
  …
  - stack test
  - save-cache:
      key: stack-{{ checksum “stack.yaml” }}
      paths:
        - “~/.stack”
  - restore-cache:
      keys: stack-{{checksum “stack.yaml”}}-{{checksum “project.cabal”}}
      paths:
        - “.stack-work”
```

现在，下一次构建将不会花这么长时间！还有其他方法可以创建缓存键。例如，我们可以使用堆栈 LTS 作为键的一部分，并且每次我们改变使用哪个 LTS 时都要删除它。缺点是需要更多的手工操作。但这项工作不会经常发生。积极的一面是，当我们向`stack.yaml`添加额外的依赖项时，我们不需要重新下载 GHC。

# 部署到 Heroku

最后但同样重要的是，每次我们推进到主分支时，我们都希望将代码部署到 heroku。Heroku 让我们很容易做到这一点！首先，进入 Heroku 的应用仪表板。然后找到部署选项卡。您应该会看到一个连接 Github 的选项。使用它来连接您的存储库。然后，确保选中指示 Heroku 应该等待 CI 的复选框。现在，只要你的构建成功完成，你的代码就会被推送到 Heroku！

# 结论

你可能已经注意到我们现在的方法有一些冗余！我们的 Circle CI 容器将构建代码。然后我们的 Heroku 容器也会构建代码！这是非常低效的，并且会导致部署问题。下周，我们将看到如何在这个过程中使用 Docker。Docker 完全融入了 V2 圈。这将简化我们的圆配置定义。这也将使我们不再需要在 Heroku 上重新构建所有代码！

有了所有这些工具，是时候最终构建您一直想要的 Haskell 应用程序了！下载我们的[制作清单](https://www.mmhaskell.com/production-checklist)，了解一些你可以使用的酷库！

如果你以前从未用 Haskell 编程，希望你能看到它并不太难用！下载我们的 [Haskell 初学者清单](https://www.mmhaskell.com/beginners-checklist)并开始吧！