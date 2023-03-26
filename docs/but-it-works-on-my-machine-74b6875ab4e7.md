# 但是，它在我的机器上工作。

> 原文：<https://medium.com/hackernoon/but-it-works-on-my-machine-74b6875ab4e7>

![](img/de248b4cc196338e9c7e652d916fbd2c.png)

如果你或你团队中的任何人仍然这么说，这就是一个大问题。随着当前的趋势和新兴技术的发展，有许多方法可以防止这种情况发生。

无论开发人员有多优秀，他或她仍然会犯错误。为什么某个特定的更改在开发人员的机器上有效，但在其他机器上却失败了，这有许多常见的原因。一些常见的原因是:

*   不同的机器和系统环境
*   缺少依赖项
*   代码中的硬编码目录或路径

# 携手合作，提前发货

随着方法论向敏捷开发的转变，跨职能团队紧密合作，并尽早交付/部署软件。

与产品团队和基础设施团队相比，当跨职能团队一起工作时，更容易发现问题并解决问题。当团队中的任何人都被允许参与并解决任何问题时，人们也倾向于变得不那么防御性。

当我们尽早且频繁地部署我们的变更时，我们可以避免部署地狱。任何故障都可以立即发现，并在情况变得更糟之前进行修复。

# 连续累计

您需要与繁琐复杂的 Jenkins 一起运行自己的 CI 服务器的日子已经一去不复返了。有了云基础 CI 公司，像 [CircleCI](http://circleci.com) 和 [TravisCI](http://travis-ci.org) 任何人只需一个简单的`yaml`配置文件就可以设置持续集成。

[CircleCI](http://circleci.com) —为私有和公共回购提供免费实例。它支持多种语言，被脸书、Spotify 和 Kickstarter 等大公司使用。

[TravisCI](http://travis-ci.org) —为公共回购提供免费计划，为私人项目提供付费计划。它在开源社区中非常受欢迎。

通过一台独立的机器来运行我们代码库的变更，我们可以尽早发现问题。一个单独的测试机器上的红旗总比被其他人挑战要少得多。即使没有测试，我们仍然应该确保项目按原样工作，并使用所有必需的依赖项进行编译。

# 码头集装箱

通过用 [Docker](https://hackernoon.com/tagged/docker) 设置我们的开发项目，我们可以很容易地解决不同机器和系统环境的问题。

一篇比较有和没有 docker 的开发好处的真正好的文章可以在这里[阅读](/phytochemia-tech-blog/how-docker-can-improve-your-development-environments-731cdfda0b9a)。

简而言之，任何加入团队的新开发人员都可以通过在他们的机器上安装`Docker`来轻松地开始项目工作。启动时间更短，我们不再需要担心不同的操作系统、系统版本或软件包版本，因为所有的东西都应该在`docker-compose.yml`文件中指定。

# 最后

随着新兴的[技术](https://hackernoon.com/tagged/technology)和发展趋势的出现，任何团队都没有理由仍然让开发人员说“它在机器上工作”。如果它在 CI 机器上不工作，您就没有完成您的工作。当务之急是在转向其他问题之前解决它。

感谢阅读。如果你感兴趣，在另一篇[文章](https://medium.freecodecamp.org/how-to-set-up-continuous-integration-and-deployment-for-your-react-app-d09ae4525250)中，我设置了一个[演示 react 项目](https://github.com/)，它具有与 [CircleCI](https://circleci.com) 、 [CodeClimate](https://codeclimate.com) 和 [Heroku](https://www.heroku.com) 的持续集成和部署设置。

# 感谢阅读！如果你喜欢，请点击👏👏👏

我喜欢阅读和写作关于技术和产品的文章，尤其是与提高开发人员的生产力有关的文章。你可以在我的[推特](https://twitter.com/Zaccc123)或者我的[博客](https://zackwan.app)上跟我打招呼。