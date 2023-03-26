# 为 GHC 1 做出贡献:准备

> 原文：<https://medium.com/hackernoon/contributing-to-ghc-1-preparation-c1f3a3cbe71b>

在过去的几周里，我们看了一些不同的开源 Haskell 项目，比如 [HNix](https://mmhaskell.com/blog/2018/5/28/hnix-enhancing-nix-with-haskell) 和 [Codeworld](https://mmhaskell.com/blog/2018/6/4/bxit5i954uafn0n4gah3yrzcxnc3q6) 。本周，我们将开始关注 Haskell 生态系统中最大也是最重要的开源元素。这是 [GHC](https://www.haskell.org/ghc/) ，格拉斯哥哈斯克尔编译器。没有 GHC 和许多志愿者的辛勤工作，Haskell 就不会成为今天的语言。因此，在接下来的几周里，我们将探索建设的过程，并(希望)为 GHC 做出贡献。

我目前在 Windows 笔记本电脑上操作，这带来了许多挫折。让 GHC 在 Windows 上运行并不是一件容易的事情，有很多潜在的障碍。从好的方面来看，我认为这是一个机会，表明一个人即使在最不利的情况下也能有所贡献。因此，本文的大部分内容将集中在使用 Windows 的尝试上。下面有一节介绍了构建 Mac 和 Linux 的最重要的部分。我将跟随西蒙·佩顿·琼斯的这个指南，分享我自己的并发症。

现在，你需要先走，然后才能跑。如果你以前从未使用过 Haskell，你必须先试用它来理解 GHC！下载我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)开始吧！你也可以阅读我们的[起飞系列](https://www.mmhaskell.com/liftoff)来学习更多的语言基础知识。

# MSYS

Windows 的主要复杂性在于，GHC 的构建工具是为类似 Unix 的环境设计的。这些工具包括像`autoconf`和`make`这样的程序。并且它们不能在正常的 Windows 终端环境中工作。这意味着我们需要某种方式来模拟 Windows 中的 Unix 终端环境。对此有几种不同的选择。一个是 [Cygwin](https://www.cygwin.com/) ，但是更受 GHC 支持的选项是 [MSYS 2](https://www.msys2.org/) 。所以我的第一步是安装这个程序。这个终端将应用“极简 GNU for Windows”库，缩写为“MinGW”。

第一次安装时运行良好。然而，确实有那么几个时刻，我决定放弃一切，从头开始。重新安装确实带来了一个问题，我将分享。在一些情况下，当我决定重新开始时，我会运行安装程序，却发现一个错误`bash.exe: permission denied`。发生这种情况是因为该程序的旧版本仍在一个进程上运行。你可以删除这个进程，或者重启你的机器来解决这个问题。

一旦 MSys 开始工作，你就要设置你的终端默认使用`MinGW`程序。要做到这一点，您首先需要设置放置`mingw`目录的路径:

```
echo “export PATH=/mingw<bitness>/bin:\$PATH” >> ~/.bash_profile
```

根据您的系统，使用`32`或`64`作为`<bitness>`。另外，不要忘记命令本身的引号！

# 包装准备

我们的下一步将是为 GHC 准备所有必要的包裹。MSys 2 使用了一个更老的叫做`pacman`的包管理器，它的操作有点像`apt-get`。首先，您需要使用以下命令更新您的包存储库:

```
pacman -Syuu
```

按照 SPJ 描述中的说明，如果连接超时，您可能需要运行几次。我曾经遇到过这种情况。现在 pacman 开始工作了，你需要安装一系列程序和库来帮助构建 GHC:

```
pacman -S --needed git tar bsdtar binutils autoconf make xz \
    curl libtool automake python python2 p7zip patch ca-certificates \
    mingw-w64-$(uname -m)-gcc mingw-w64-$(uname -m)-python3-sphinx \
    mingw-w64-$(uname -m)-tools-git
```

这个命令通常对我来说很好。我们需要的最后一项是`alex`和`happy`。这些是用于词法分析和语法分析的 Haskell 程序。我们要安装阴谋集团来做这件事。首先让我们为我们的系统设置几个变量:

```
arch=x64_64 # could also be i386
bitness=64  # could also be 32
```

现在我们将获得一个预构建的 GHC 二进制文件，稍后我们将使用它来引导我们自己的构建:

```
curl -L https://downloads.haskell.org/~ghc/8.2.2/ghc-8.2.2-${arch}-unknown-mingw32.tar.xz | tar -xJ -C /mingw${bitness} --strip-components=1
```

现在我们要利用 Cabal 拿到那些包裹。我们将把他们(和阴谋集团)放在`/usr/local/bin`中，因此我们将确保首先创建它:

```
mkdir -p /usr/local/bin
curl -L https://www.haskell.org/cabal/release/cabal-install-2.2.0.0/cabal-install-2.2.0.0-${arch}-unknown-mingw32.zip | bsdtar -xzf- -C /usr/local/bin
```

现在我们将更新我们的阴谋库，并获得`alex`和`happy`:

```
cabal update
cabal install -j --prefix=/usr/local/bin alex happy
```

有一次运行这个命令时，我发现由于`mtl`库的问题`happy`无法安装。运行`ghc-pkg check`命令时，我得到了这类错误:

```
Cannot find any of [“Control\\Monad\\Cont.hi”, “Control\\Monad\Cont.p_hi”, “Control\\Monad\\Cont.dyn_hi”]
Cannot find any of [“Control\\Monad\\Cont\\Class.hi”, “Control\\Monad\Cont\\Class.p_hi”, “Control\\Monad\\Cont\\Class.dyn_hi”]
```

我通过手动重新安装`mtl`包解决了这个问题:

```
cabal install -j --prefix=/usr/local/ mtl --reinstall
```

经过这一步，在`ghc-pkg check`上没有错误，我可以毫无问题地安装`happy`。

```
cabal install -j --prefix=/usr/local/ happy
Resolving dependencies…
Configuring happy-1.19.9…
Building happy-1.19.9…
Installed happy-1.19.9
```

# 获取源代码并构建

现在我们的依赖项都已经设置好了，所以我们现在可以开始获取源代码了！为 GHC 做贡献的主要工作流程使用了一些其他工具，但是我们可以从 Github 开始。

```
git clone --recursive git://git.haskell.org/ghc.git
```

现在，您应该从`ghc`目录运行`./boot`命令。由于我的杀毒软件，这给我带来了一些特别严重的问题。它认定`perl`对我的系统是一个现存的威胁，并把它扔进了病毒箱。您可能会看到类似这样的错误:

```
sh: /usr/bin/autoreconf: /usr/bin/perl: bad interpreter: No such file or directory
```

甚至在将另一个版本的 perl 复制到该目录后，我看到了如下错误:

```
Could not locate Autom4te/ChannelDefs.pm in @INC (@INC contains /usr/share/autoconf C:/msys64/usr/lib .) at C:/msys64/usr/bin/autoreconf line 39
```

实际上，`@INC`路径应该有更多的条目！我花了一段时间(和几个完整的重做)才发现我的杀毒软件是这里的问题。一旦我把`perl`从病毒箱里挖出来，一切都正常了。一旦`boot`运行，你就差不多准备好了！您现在需要配置一切:

```
./configure --enable-tarballs-autodownload
```

额外选项仅在 Windows 上是必需的。最后，您将使用`make`命令来构建一切。预计这将需要一段时间(12 个小时，并为我计数！).一旦你熟悉了代码库，有几种不同的方法可以让你构建得更快。例如，您可以用几种不同的方式定制`build.mk`文件。可以设置`BuildFlavor=devel2`，也可以设置`stage=2`。后者将跳过编译器的第一阶段。

您也可以从子目录而不是主目录运行`make`。这只会构建子组件，而不是完整的编译器。最后，还有一个`make fast`命令，它将跳过许多依赖项的构建。

# Mac 和 Linux

我不会在这里深入讨论 Mac 和 Linux 的指令，因为我还没有机会亲自尝试。但是由于这些系统对开发者友好的特性，它们可能比 Windows 有更少的问题。

例如，在 Linux 上，您实际上可以通过使用 Docker 容器来完成大部分设置。首先下载源代码，然后运行 docker 命令:

```
>> sudo docker run --rm -i -t -v `pwd`:/home/ghc gregweber/ghc-haskell-dev /bin/bash
```

在 Mac 上，你需要安装一些与 windows 类似的程序，但没有必要使用像 MSys 这样的终端模拟器。如果你已经有了基本的开发工具和一个 GHC 和阴谋集团的工作版本，事情可能会很简单:

```
>> brew install autoconf automake
>> cabal install alex happy haddock
>> sudo easy_install pip
>> sudo pip install sphinx
```

更多详情，请点击[这里](https://ghc.haskell.org/trac/ghc/wiki/Building/Preparation/MacOSX)。但是一旦你设置好了，你将会遵循和 Windows 一样的`boot`、`configure`和`make`指令。

# 结论

这就结束了我们对 GHC 的第一次观察。光是建造它就有大量的工作要做！但是下周我们将开始研究一些我们能对 GHC 做的最简单的修改。这样，我们可以开始了解代码库是如何工作的。

如果你没有写过 Haskell，就很难体会到 GHC 的辉煌！从下载我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)和阅读我们的[起飞系列](https://www.mmhaskell.com/liftoff)开始吧！