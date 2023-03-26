# git greenbase 意味着永远不要退出一个失败的构建

> 原文：<https://medium.com/hackernoon/git-greenbase-means-never-pulling-a-broken-build-165d36b005ce>

![](img/ea79e522bd0b33f03493c790bebd9e6b.png)

如果您的团队使用 Git 和持续集成，那么您的工作流很有可能受益于`git greenbase`。但是首先，戴上你的虚拟现实耳机，通过我们经验丰富的开发人员 Karen 的眼睛体验一下`git pull`的危险。

# 凯伦 vs 破碎的建筑

你带着晨跑的疲惫，脸上带着微笑，迈着轻快的步伐去上班——灯泡在跑步途中熄灭了:你知道为什么身份服务在每天早上 11:48 就停止了。启动控制台，你`cd`到正确的回购和`git pull`从你的指尖敏捷地流动，你的脚跟敲打出播放列表上最后一首歌的节拍；这是个好主意。

Git 决定它需要重新打包并清除你的超大回购上的垃圾。你微微翻了翻眼睛，但没关系——今天没有什么能让你沮丧。你瞬间进出 Vim。很难相信这样一个简单的三行变化会减轻这么多的痛苦。你看了一眼时钟。在每日站立会议之前进行代码审查是有可能的。只剩下一件事要做…运行测试。

> *“真好笑……”*

“真有意思……17 次测试失败？？?"你惊恐地皱起眉头。“我的改变怎么可能打破时间窗口”，你摇摇头想。在下面，你曾经快乐的脚停止了跳动，它的好节拍被遗忘了。

接下来的 10 分钟像美洲狮一样悄悄溜过你，因为你倾倒了所有的源文件，甚至与你的修复有一点点关系。还是一无所获。屏幕上的一个光点就像一个冷馅饼砸在脸上一样，让你无法集中注意力。"你要来单口相声吗？"哎呀，我迟到了！

在敏捷的大半圆附近，你侧身走到你的伙伴安妮旁边，像土狼一样停了下来。每个人看起来都有点被逗乐了，但他们没有看着你。汤姆为弄坏了房子道歉。

“对不起，各位，我迟到了”，你温顺地插嘴说。"我在看一些我打破的测试。"

汤姆不好意思地看着你。"有没有可能，它们是时间控件中的 17 个测试？"

向前弯腰，你发出一声过于戏剧性的愤怒沮丧的叹息。

队员们扫视了一下四周，然后大笑起来。这是一种会心的笑声，一种同情和相互理解的笑声。

# 香草`git pull`的不足之处

在我们的故事里你发生了什么？你拉了一个破碎的建筑！这让你徒劳地寻找一个不是你造成的问题。

你跑去`git pull`拉最新的变更，但是仔细想想，你真的想要*最新的*变更吗？或者你想要最新的*好的*变化？当人们建造房子时，他们需要时间准备土地和地基。他们不会在一堆垃圾上开始建造。同样，当我们开始开发一个新的特性或缺陷修复时，我们应该从一个已知的好地方开始。这让你相信任何构建或测试失败都是你的错，而不是安妮、汤姆或那个在办公室里骑独轮车的实习生的错。

如果您的团队实践[持续集成(CI)](https://www.thoughtworks.com/continuous-integration) 并拥有一套可靠的自动化测试，那么您的 CI 服务器就是构成“已知良好构建”的事实来源。

# `git greenbase`来救援了！

这就是`git greenbase`的用武之地。不要像老虎机一样盲目地运行`git pull`并期待最好的结果，而是运行`git greenbase`。它会向您的 CI 服务器查询最新的通过的*，或者“绿色”版本，然后重置您的本地主机以匹配。瞧啊。`master`现在处于已知的良好状态。满怀信心地启动您的编码引擎！*

# 地方特色分支呢？

如果你在跟踪`master`的本地特征分支上，并且你的团队使用基于 rebase 的工作流，那么`git greenbase`仍然重置`master`以匹配绿色构建，但是它*也*在主的顶部重新设置你的本地特征分支。很酷吧。

# 安装`git greenbase`

`git greenbase`安详地坐在 Github 上:[https://github.com/tygerbytes/git-greenbase](https://github.com/tygerbytes/git-greenbase)。如果你使用的是 Linux，安装如下:

```
git clone [https://github.com/tygerbytes/git-greenbase.git](https://github.com/tygerbytes/git-greenbase.git) cd git-greenbase ./install
```

`./install`脚本将创建一个到 git-greenbase 的符号链接，并将其添加到您的路径中。Git 会在您的路径中找到它，并允许您运行`git greenbase`，就像任何 Git 命令一样。

(最新安装说明见[报告](https://github.com/tygerbytes/git-greenbase#installation)。)

# 但是 Mac 和 Windows 呢？

截至 2018 年 3 月，git greenbase 仅在 Linux 上进行了测试。它*可能*也适用于 Mac OS X。它很可能无法像 Windows 上的那样运行**。我完全接受拉请求。如果需求足够大，我会用 PowerShell 写一个版本。**

# 配置`git greenbase`与您的 CI 提供者一起工作

现在它已经安装好了，您如何配置它来与您的 CI 提供者一起工作呢？通常，您需要向 Git 提供 CI 服务器的 URL、API 令牌和项目的 ID。信息存储在您的 Git 配置中。

例如，如果您使用 Travis CI，您将运行类似于以下内容的命令:

```
git config **greenbase**.**provider** travisci
git config greenbase.**travisci.token** *<TOKEN>*
git config greenbase.travisci.**serverUri** *<SERVER_URI>*
git config greenbase.travisci.**repoId** *<GITHUB_REPO_ID>*
```

([最新配置说明见 repo](https://github.com/tygerbytes/git-greenbase#configuring-a-ci-provider) 。)

## 支持哪些 CI 提供程序？

截至 2018 年 3 月，OSS 盒子支持的 CI 提供商只有:

*   Gitlab
*   特拉维斯·CI
*   TFS(未经充分测试)

这是一个很短的列表！如果您想添加对另一个 CI 提供程序的支持，请随时向我发送请求。见下文“促进发展”

# 为`git greenbase`发展做出贡献

`git greenbase`用 Bash 编写，并使用 [Bats 测试框架](https://github.com/sstephenson/bats)进行测试。

为了可测试性和可维护性，我尽力以模块化的方式设计它。例如，与每个 CI 提供者通信的逻辑存在于它自己的 Bash 脚本中，它的 Bats 测试与它在同一个目录中。

就我个人而言， [Bash 不是我最喜欢的语言](https://twitter.com/tygertec/status/832089675301003266)，我也不是一个超级熟练的 Bash 开发者。也就是说，我觉得像`git greenbase`这样的工具会给 DevOps 带来更多的和平、爱和蝴蝶。所以我坚持。:)

最新开发详情见[开发页面](https://github.com/tygerbytes/git-greenbase/blob/master/DEVELOPMENT.md)。

# 我能请你喝杯咖啡吗？

如果你对`git greenbase`和它拯救了你和你的团队的所有痛苦感到超级兴奋，请给我买杯咖啡。我发现咖啡是一个巨大的动力。

*原载于 2018 年 3 月 5 日 tygertec.com**的*[T22。](https://tygertec.com/git-greenbase/)