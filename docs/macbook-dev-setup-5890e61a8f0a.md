# MacBook:开发设置

> 原文：<https://medium.com/hackernoon/macbook-dev-setup-5890e61a8f0a>

![](img/eebb58456720226bc083899d9a27a47d.png)

“MacBook Pro” by [Fabian Grohs](https://unsplash.com/@grohsfabian?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

多年来，我一直主要使用 Linux，但我刚刚获得了一台新的 Macbook Pro (2018)。我以前对 macOS 有过一定的经验，但是我从来没有把这些机器作为我的主要装备。在我最初对它的屏幕感到“惊叹”和对它的(缺少)端口感到“困惑”之后，我开始迁移我的环境，并搜索我必须安装的任何东西，以使这个庞然大物成为我自己的。下面是这个过程的一步一步的指南，它涵盖了我作为 Java / Javascript 开发人员的几乎所有需求，尽管大多数部分对大多数人来说也是有价值的。

> **缺失的包管理器**

首先，我们应该安装 Homebrew，MacOS 缺失的软件包管理器。家酿主要用于安装包和命令行工具，但它也可以用来安装正常的非开源应用程序。这将有助于安装您需要的所有剩余软件和软件包，并维护它们(删除、清理依赖项等)。为此，打开一个终端并运行下面的命令。

```
≈/usr/bin/ruby -e “$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

只要遵循一些屏幕上的指示，完成后，移动到好的东西。

> **类固醇晚期**

我在日常工作中经常使用终端，所以这是我尝试升级的第一部分。经过一番搜索，我找到了 iTerm2，它涵盖了我以前的终端(Linux 上的 tilix，Windows 上的 cmder)的所有特性。你可以从它的官方[页面](https://www.iterm2.com/index.html)下载，或者使用我们之前安装的自制软件:

```
brew cask install iterm2
```

安装后，有无数的东西你可以修补这个应用程序，但随着一些更多的步骤，我们会得到更多。接下来我们应该将 MacOS 的默认 unix shell 从 Bash 改为 Z shell。这样做有很多好处，比如更好的自动完成和命令历史。Zsh 安装在 MacOS 上，但是最好安装它的最新版本，所以在你的终端上输入这个。

```
brew install zsh
```

之后，检查您的版本:

```
zsh — version
```

*写这个的时候应该是 zsh 5.6.2*

接下来让 zsh 成为默认的 unix shell:

```
chsh -s $(which zsh)
```

现在，我们可以只使用 zsh，也可以安装一个框架，为我们的 shell 带来许多新的插件和主题。这里有各种选择，但我最终得到的是 [*哦，我的 zsh*](https://github.com/robbyrussell/oh-my-zsh) 。要安装它，只需键入:

```
sh -c “$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

现在，我们可以从 [*框架带来的*](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins) 或在线找到的新插件中启用我们想要的插件。要启用你喜欢的插件，你必须编辑你的。zshrc 文件。

```
vi ~/.zshrc
```

在那里你必须找到插件数组并添加你想要的插件。

*这里值得一提的是，zsh 语法高亮和 zsh 自动提示*

最后，我们应该为 iTerm2 安装一个首选主题。最简单的解决方案是 [*dracula*](https://draculatheme.com/) ，它也可以应用于大多数其他应用程序/编辑器，尽管我选择了 [*材质颜色*](https://github.com/MartinSeeler/iterm2-material-design) 。只需下载首选的，并通过 iTerm2 的 Preferences 页面应用即可。

> **构建您的开发堆栈**

现在我们已经设置好了终端，我们可以开始安装所有需要的应用程序了。下面的大部分工作要么可以通过自制软件完成，要么从各自的网站下载所有的东西。还有一件事是开发者特别需要的，那就是需要在你的机器上安装多个版本的 SDK。默认情况下，Homebrew 会安装最新的版本，对于某些包来说,*可能是旧版本，但这种情况并不常见。为此，首先我们将安装另一个工具，SDKMAN！。在您的终端上，键入以下命令，并按照屏幕上的指示进行操作。*

```
$ curl -s “https://get.sdkman.io" | bash
```

下面是一些我和大多数人都必须要做的非常基本的安装。

*   Java SDK `sdk install java`
*   格雷德`sdk install gradle`
*   mysql `brew install mysql`
*   mongodb `brew install mongodb`
*   弹性搜索`brew install elasticsearch`

现在，在我们进入实际应用程序之前，NodeJS 也是必需的。为此，我们需要 nvm 工具能够安装和更改为多节点版本。使用以下工具安装工具本身:

```
curl -o- [https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh](https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh) | bash
```

或者安装 [*zsh-nvm*](https://github.com/lukechilds/zsh-nvm) ，它将负责以后轻松升级 nvm。nvm 安装后，只需通过键入以下命令来安装(最新或特定版本)节点:

```
nvm install node
```

它将安装最新的节点并伴随 npm。

现在，让我们用一些工具和所需的编辑器来完成这个。我需要 MySQL Workbench、Docker、GitKraken 作为我的 git GUI 客户端，PostMan 用于 API 测试，Android Studio 用于 Android 开发，Jetbrains 的工具箱将处理 WebStorm 用于 Javascript 项目，IntelliJ 用于 Java 项目。此外，我通常也使用 SublimeText 或 Visual Studio 代码来处理更简单的事情。以上所有内容都可以用家酿软件安装，所以你只需输入:

```
brew cask install \ 
    mysqlworkbench \ 
    docker \ 
    gitkraken \ 
    postman \ 
    android-studio \ 
    jetbrains-toolbox \ 
    sublime-text \ 
    visual-studio-code
```

一段时间后，一切都将被下载和安装，随时可以使用。

**必需的仿制药**

最后，根据个人选择，我列出了一些日常使用中需要的通用应用程序。

```
brew cask install \
    gimp \
    skype \
    viber \
    slack \
    google-chrome \
    microsoft-office \
    spotify \
    vlc \
    evernote \
    the-unarchiver
```

![](img/61973f3036f246255889907052f079da.png)

所有这些安装完成后，我觉得我的新的强大的 MacBook 已经准备好取代我的旧 Dell 作为我的日常驱动程序。其中许多不适用于任何人，但在 brew /木桶安装之后，设置新机器并立即投入使用应该是轻而易举的事情。