# 初创企业首席技术官生存指南——第二部分

> 原文：<https://medium.com/hackernoon/early-stage-startup-cto-survival-guide-part-2-90d49ee87696>

## 协作和质量

[上一次，我们用一个基本的设置离开了我们的创业公司](/@gil_x/early-startup-cto-survival-guide-a67ebad2a166)。刚好够发布 MVP，验证这个想法。

**此外，你猜怎么着，初创企业“宠物中介”正取得巨大成功！**

![](img/42ee4340e0509421ca15ff710117b6e4.png)

Photo by [taylor griffith](https://unsplash.com/@tayg3?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

由于该项目在财务上有所收益，您雇佣了技术团队的前两名成员。Nico 是前端，Sally 是后端开发人员。

现在，作为一家发展良好的初创企业的首席技术官，你的任务是为协作和质量保证建立适当的基础设施。

目前，应用程序是托管在你的好友服务器上的，每次你改变一些东西的时候，你都需要通过 FTP 上传一个修改过的文件，然后让你的队友下载修改过的文件。哎哟！

## 让我们合作吧！

有了团队，就必须有一种协作和共享代码的方式。在这里，像 [GIT](https://git-scm.com/) 这样的系统派上了用场。由于 GIT 是开源软件，我们可以自己安装和托管中央/远程存储库，但我们不从事托管分布式版本控制系统的业务。我们当然需要一种简单的方法来控制访问，请求合并到主分支，并能够审查我们的团队成员提交的代码。

![](img/31ff8c6f56b8a5938196886f429ef75d.png)

Photo by [Anoir Chafik](https://unsplash.com/@anoirchafik?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

这里有很多选择。这个领域最有趣的解决方案之一是 [GitLab](https://about.gitlab.com/) 。最初是作为超级流行的 GitHub(已被微软收购)的一个开源克隆开始的，GitLab 是非常先进的、功能丰富的、许多人喜欢的解决方案。我们将选择 GitLab 提供的 SaaS(托管)解决方案，尽管也可以使用他们的社区版并自行设置和运行。我们还将选择我们的工作流程，这样每个人在课程方面都是一致的。为了保持简单和灵活，我们将使用 [GitHub 流](https://guides.github.com/introduction/flow/)。

完成所有设置后，我们应该能够创建功能分支，并将我们的代码推送到中央存储库，在那里我们可以打开拉/合并请求，并让我们的同行评审员将我们的代码分开，作为我们代码质量防御的第一道防线。然而，这仅仅是一个开始。

## 自动和连续质量

git 存储库中的代码使我们能够比较软件的版本，并在非常精细的级别上跟踪变化。我们可以逐行检查正在发生的事情，并保持我们与团队定义的质量和标准。就其本身而言，这是非常有用的！然而，我们可以做得更好。

如果有一种方法可以自动检查最近提交的代码是否符合标准、是否违反标准以及是否有 bug 就好了。另外，如果我们也能自动运行我们的单元和验收测试套件，那将是真正的酷！

我们需要一些系统来检测我们的 git 库的变化，拉一个新的代码，执行一些清理和运行一些命令。

![](img/ef60e6b3d95a11787bdffd290acca866.png)

Photo by [Nathan Fertig](https://unsplash.com/@nathanfertig?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

对于许多人来说，自动化服务器的首选是詹金斯。这是一场历经多年考验的开源解决方案之战。Jenkins 的基本用法很容易设置。功能可以通过大量的插件进行扩展，这些插件几乎适用于任何可以想到的集成。跨多台机器分配工作的可能性使得构建、测试和部署的运行速度非常快。

尽管这一切听起来很神奇，但我们需要看看其他作为服务提供的托管解决方案，因为我们的团队非常小，无法运行额外的软件系统，因为他们的主要目标是交付出色的产品。

[Travis CI](https://travis-ci.org/) 是最古老的托管自动化服务器之一，它赢得了许多人的信任，尤其是 GitHub 用户和开源项目维护者。公司 [Cloudbees](https://www.cloudbees.com/) ，Jenkins 项目的大支持者，收购了 [CodeShip](https://codeship.com/) ，这是填补我们自动化服务器位置的另一个强有力的竞争者。值得一提的其他几个因其速度和简单性而受到称赞的产品是[信号量 CI](https://semaphoreci.com/) 和[圈 CI](https://circleci.com/) 。

考虑到选择的广泛性，为自动化选择合适的解决方案确实很难。然而，考虑到我们是一个技术资源有限的小公司，我们需要选择“最便宜”和最方便的。由于我们已经决定使用 GitLab 作为我们的代码版本和存储解决方案，我们不能忽视他们在自动化领域的产品。

使用 GitLab CI，我们可以编写管道和作业描述文件并提交到我们的存储库中，只需在其 UI 中进行最少的配置工作，我们就可以立即运行构建、质量和测试作业。

```
# .gitlab-ci.yml**stages:** - build
  - test
  - quality

**build:
  stage:** build
  **script:** - php composer.phar install -o

**code_test:
  stage:** test
    - php ./vendor/bin/phpunit

**code_quality:
  stage:** quality
    - codeclimate analyze -f text app
```

为了保证质量，我们将使用 Code 气候分析平台。
[代码环境](https://github.com/codeclimate/codeclimate)支持许多不同的“引擎”,它们通常对你的代码进行静态分析，并在源代码中寻找潜在的问题，比如可能的错误、次优代码、过于复杂的表达式以及未使用的参数、方法或属性。还可以帮助您检测是否违反了已定义的编码标准。支持的引擎有 PHP 代码嗅探器、PHP Mess Detector、Pylint、Radon、Rubocop、TSLint、SonoraJava、SonoraPHP……
你懂了。代码氛围涵盖了质量。

```
# [.codeclimate.yml](https://docs.codeclimate.com/docs/phpcodesniffer)**plugins:
  phpcodesniffer:
    enabled:** true
    **config:
       standard:** "PSR1,PSR2"
```

我们的**测试工作**将使用类似 PHPUnit、Unittest (python)、MochaJS、JUnit、 [Behat](http://behat.org/en/latest/) 、 [RSpec](https://relishapp.com/rspec) 这样的工具。

随着我们所有的协作(Git，GitLab)和自动化工具(GitLab CI，CodeClimate)的到位和运行，我们已经提升了我们团队的游戏多样性。

## 三赢三赢🎉🎉🎉

我们的团队拥有最好的代码管理、测试和质量分析工具。每当团队成员将他们的工作推送到存储库时，代码可供每个人审查，它被自动测试，并且质量分数是立即可访问的。**拜拜，技术债。**

有了这样的信心，我们就可以开始讨论一键式直接部署到生产服务器了！

但是，下一次会有更多的内容！

*感谢阅读！如果你喜欢这个故事，请点击**👏 ***按钮*** ***并分享*** *帮助别人找到它！欢迎随时发表评论*💬*下图。
有反馈吗？在推特上做朋友*[](https://twitter.com/gil__x)**。***