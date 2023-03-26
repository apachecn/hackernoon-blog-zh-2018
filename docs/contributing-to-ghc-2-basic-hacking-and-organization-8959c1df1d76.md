# 贡献给 GHC 2:基本黑客和组织

> 原文：<https://medium.com/hackernoon/contributing-to-ghc-2-basic-hacking-and-organization-8959c1df1d76>

上周，我们向 GHC 迈出了第一步，Glasgow Haskell 编译器。我们总结了构建它所需要安装的包和工具。我们甚至在 windows 笔记本电脑相当恶劣的环境中也做到了这一点。但是，在一天结束时，我们现在可以用`make`构建项目，并创建我们本地版本的 GHC。

本周，我们将通过查看我们可以对编译器进行的一个非常简单的更改来建立我们的开发周期。我们还将讨论存储库的架构，以便下周我们可以做一些更酷的更改。

GHC 是开源软件的一些令人敬畏的好处的真实见证。没有它，Haskell 就不是同一种语言了。但是要理解 GHC，您首先必须对 Haskell 本身有一个相当好的理解！如果你以前从未写过一行 Haskell，看看我们的[启动系列](https://www.mmhaskell.com/liftoff)中关于如何开始的一些提示。你也可以下载我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)。

您可能也听说过，虽然 Haskell 是一种简洁的语言，但从行业角度来看，它毫无用处。但是如果你看一下我们的[生产清单](https://www.mmhaskell.com/production-checklist)，你会发现有很多工具可以编写更有趣的 Haskell 程序！

# 入门指南

让我们从用`Main.hs`编写一个非常简单的程序开始。

```
module Main wheremain :: IO ()
main = do
  putStrLn "Using GHC!"
```

我们可以使用`ghc`命令将这个程序编译成可执行文件。我们从跑步开始:

```
ghc -o prog Main.hs
```

这就创建了我们的可执行文件`prog.exe`(或者如果你没有使用 Windows，就只创建`prog`)。然后我们可以像运行任何程序一样运行它:

```
./prog.exe
Using GHC!
```

然而，这是使用系统级 GHC，我们必须安装，而建立它在本地！

```
which ghc
/mingw/bin/ghc
```

当我们构建 GHC 时，它为编译过程的每个阶段创建可执行文件。它在一个名为`ghc/inplace/bin`的目录中生成这些文件。所以我们可以创建一个别名来简化我们的工作。我们将把`lghc`写成一个“本地 GHC”命令:

```
alias lghc="~/ghc/inplace/bin/ghc-stage2.exe -o prog"
```

这将使我们能够用`lghc Main.hs`编译我们的单模块程序。

# 黑客等级 0

最终，我们希望能够验证我们的更改。因此，我们应该能够修改编译器，重新构建它，在我们的程序中使用它，然后看到我们的更改反映在代码中。测试编译器行为的一个简单方法是改变错误消息。例如，我们可以尝试导入一个不存在的模块:

```
module Main whereimport OtherModule (otherModuleString)main :: IO ()
main = do
  putStrLn otherModuleString
```

当然，我们会得到一条错误消息:

```
[1 of 1] Compiling Main (Main.hs, Main.o)Main.hs:3:1: error:
    Could not find module 'OtherModule'
    Use -v to see a list of the files search for.
   |
3  |import OtherModule (otherModuleString)
   |^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```

现在让我们尝试更改此错误消息的文本。我们可以在代码库的`compiler`部分快速搜索这条消息，并找到它的定义位置:

```
cd ~/ghc/compiler
grep -r "Could not find module" .
./main/Finder.hs:cannotFindModule = cantFindErr (sLit "Could not find module")
```

让我们继续将该字符串更新为稍有不同的内容:

```
cannotFindModule :: DynFlags -> ModuleName -> FindResult -> SDoc
cannotFindModule = cantFindErr
  (sLit "We were unable to locate the module")
  (sLit "Ambiguous module name")
```

现在让我们继续重建，除了让我们使用上周的一些技术来使这个过程进行得更快一点。首先，我们将把`mk/build.mk.sample`复制到`mk/build.mk`。根据安装指南的建议，我们将取消对下面一行的注释:

```
BuildFlavour=devel2
```

我们还将取消对`stage=2`行的注释。这将限制编译器只能构建编译器的最后阶段。它将跳过我们已经建立的阶段 0 和阶段 1。

我们还将从`compiler`目录开始构建，而不是从根目录`ghc`开始。请注意，由于我们已经更改了构建文件，我们必须再次引导和配置。但是在我们重新编译之后，我们会发现我们有了新的错误信息！

```
[1 of 1] Compiling Main (Main.hs, Main.o)Main.hs:3:1: error:
    We were unable to locate the module 'OtherModule'
    Use -v to see a list of the files search for.
   |
3  |import OtherModule (otherModuleString)
   |^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
```

# 一般建筑

下周，我们将对编译器进行更复杂的修改。但至少现在我们已经证实了我们可以正常发展。我们可以做一个改变，在很短的时间内编译，然后确定这个改变有所不同。但是现在让我们考虑一下 GHC 存储库的组织。这将帮助我们更多地思考我们将做出的改变的类型。我将借鉴西蒙·佩顿·琼斯和西蒙·马洛的描述。

GHC 代码库有三个主要部分。首先是编译器本身。编译器的工作是获取我们的 Haskell 源代码，并将其转换成机器可执行代码。下面是一个非常不完整的编译器任务列表

1.  确定引用模块的位置
2.  读取单个源文件
3.  将源代码分解成最简单的语法表示

然后是开机部分。本节讨论编译器本身所依赖的库。它们包括像`Int`或`Data.Map`这样的低级类型。这一节稍微稳定一些，就不多看了。

最后一个主要部分是运行时系统(RTS)。这将获取上面的编译器生成的代码，并确定如何运行它。这部分发生了很多神奇的事情，使得 Haskell 在并发性和并行性等任务上特别强大。它也是我们处理像垃圾收集这样的机制的地方。

我们会尽量把大部分时间花在`compiler`部分。编译管道有许多阶段，像类型检查和去糖。这将让我们专注于一个特定的阶段，并做出一个小小的改变。此外，运行时系统大部分是 C 代码，而大部分编译器是在 Haskell 本身！

# 结论

下周我们将会看到更多修改编译器的方法。在那之后，我们将开始研究 GHC 的实际问题，看看我们能做些什么来尝试解决它们！我们最终将在 Github 和 Phabricator 的提交过程中达到顶峰。

如果你想开始你的 Haskell 之旅，你应该阅读我们的[发射系列](https://www.mmhaskell.com/liftoff)！它将帮助你学习这种令人敬畏的语言的基础。更多更新，你也可以[订阅](https://www.mmhaskell.com/subscribe)我们的每月简讯！