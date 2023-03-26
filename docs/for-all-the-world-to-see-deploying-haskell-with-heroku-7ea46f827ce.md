# 让全世界都看到:与 Heroku 一起部署 Haskell

> 原文：<https://medium.com/hackernoon/for-all-the-world-to-see-deploying-haskell-with-heroku-7ea46f827ce>

![](img/04c6d96c89be0f8fdad398b3f4d25225.png)

在现在的几篇不同的文章中，我们已经探索了如何使用 Haskell 构建 web 应用程序。例如，看看我们的 [Haskell Web 系列](https://www.mmhaskell.com/haskell-web)和我们的 [API 集成](https://www.mmhaskell.com/apis)系列。但是，如果我们没有一种方法来部署我们的代码，以便互联网上的其他人可以找到它，这一切最终都是没有意义的！在下一个系列中，我们将探索如何使用公共服务来部署 Haskell 代码。这比用更受支持的语言编写代码要多几个步骤！

如果您从未使用 Haskell 编程，那么在开始部署代码之前，您需要学习一些东西！下载我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)获取如何开始学习的提示！但是也许你已经做了一些 Haskell，并且需要更多的想法供库使用。在这种情况下，请查看我们的[生产清单](https://www.mmhaskell.com/production-checklist)以获得指导！

# 在 Heroku 上部署代码

在本文中，我们将重点关注使用 [Heroku](https://www.heroku.com/) 服务部署我们的代码。Heroku 让我们可以轻松做到这一点。我们可以免费得到一个快速原型，使它成为黑客马拉松的理想选择。不过像大多数平台一样，Heroku 最容易与更常见的语言一起使用。Heroku 可以自动检测 Javascript 或 Python 应用程序，并采取适当的措施。由于 Haskell 不常用，我们需要一个额外的规范来获得 Heroku 的支持。幸运的是，大部分艰难的工作已经为我们完成了。

# 构建包

Heroku 使用“buildpack”的概念来决定如何将你的项目转换成可运行的代码。您将通过将代码推送到远程存储库来部署您的应用程序。然后 buildpack 会告诉 Heroku 如何构建你需要的可执行文件。如果你指定一个 Node.js 项目，Heroku 会找到你的`package.json`文件并从 NPM 下载所有东西。如果是 Python，Heroku 会安装`pip`做同样的事情。

Heroku 没有 Haskell 项目的默认构建包。然而，Github 上有一个 [buildpack 我们可以使用(启动这个库！).它将告诉我们的 Heroku 容器下载 Stack，然后使用 Stack 构建我们所有的可执行文件。因此，让我们看看如何使用这个过程来构建一个基本的 Haskell 项目。](https://github.com/mfine/heroku-buildpack-stack)

# 创建我们的应用程序

我们需要在 Heroku 上创建一个免费账户。然后，我们将下载 Heroku CLI，这样我们就可以从终端进行连接。使用`heroku login`命令并输入您的凭证。

现在我们想创建我们的应用程序。在您的终端中，`cd`进入包含您的 Haskell 堆栈项目的目录。确保它已经是一个 Github 库了。如果存储库目前只是本地的，那也没关系。运行此命令创建您的应用程序(用您想要的应用程序名称替换`haskell-test-app`):

```
heroku create haskell-test-app \
  -b [https://github.com/mfine/heroku-buildpack-stack](https://github.com/mfine/heroku-buildpack-stack)
```

`-b`参数指定了我们的构建包。我们将从指定的 Github 库中提取它。如果可以的话，你应该可以在 Heroku 的仪表板上看到你的新应用程序的条目。你的项目将会有一个 Heroku 域，你可以在项目设置中看到。

现在我们需要制作一个 Procfile。这告诉 Heroku 我们需要运行特定的二进制文件来启动我们的 web 服务器。确保您的`.cabal`文件中有一个启动服务器的可执行文件。然后在`Procfile`中，您将在`web`名称下指定可执行文件:

```
web: run-server
```

但是请注意，您不能使用硬编码的端口！Heroku 会为你选择一个港口。您可以通过检索`PORT`环境变量来获得它。下面是您的代码可能的样子:

```
runServer :: IO ()
runServer = do
  port <- read <$> getEnv “PORT”
  Run port (serve myAPI myServer)
```

现在，您需要“扩展”应用程序，以确保它至少有一台机器可以运行。从存储库中，运行以下命令:

```
heroku ps:scale web=1
```

最后，我们需要将应用程序推送到 Heroku 容器中。为此，确保 Heroku 添加了远程`heroku` Github 库。您可以使用以下命令来完成此操作:

```
git remote -v
```

它应该显示两个名为`heroku`的遥控器，一个用于获取，一个用于推送。如果这些不存在，您可以像这样添加它们:

```
heroku git:remote -a haskell-test-app
```

然后，您可以通过运行以下命令来结束:

```
git push heroku master
```

您应该看到终端输出表明 Heroku 识别了您的应用程序。如果您等待足够长的时间，您将开始看到堆栈构建过程。如果您的项目有任何环境变量，请从应用程序仪表板设置它们。您也可以使用以下命令设置变量:

```
heroku config:set VAR_NAME=var_value
```

一旦我们的应用程序完成构建，您就可以访问 Heroku 提供给您的 URL。看起来应该是`https://your-app.herokuapp.com`。现在，您已经将 Haskell 代码部署到云中了！

# 弱点

这个系统有一些弱点。最主要的一点是，我们的整个构建过程都发生在云上。这似乎是一个优势，也有它的好处。尽管 Haskell 应用程序可能需要很长时间来编译。如果项目很大，并且涉及到模板 Haskell，情况尤其如此。像 Heroku 这样的服务在构建过程中经常会超时。因此，如果编译时间过长，构建将会失败。幸运的是，容器将缓存以前的结果。这意味着 Stack 不必重新下载所有的库。因此，即使我们的第一个构建超时，第二个也可能成功。

# 结论

我们的 Haskell 部署系列的第 1 部分到此结束。在这个系列中，我们将会经常看到相同的主题。使用公共服务部署我们的 Haskell 代码绝对是可能的。但是我们经常需要做更多的工作来达到这个目的。下周，我们将看到如何通过整合 [Circle CI](https://circleci.com/) 来自动化我们的部署过程。

想要更多关于使用 Haskell 开发 web 应用程序的技巧吗？下载我们的[制作清单](https://www.mmhaskell.com/production-checklist)，了解一些你可以使用的其他库！对于一种方法的更详细的解释，请阅读我们的 [Haskell 网络技能](https://www.mmhaskell.com/haskell-web)系列。