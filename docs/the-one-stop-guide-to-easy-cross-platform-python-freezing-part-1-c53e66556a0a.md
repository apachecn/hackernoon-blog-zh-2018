# (简单的)跨平台 Python 冻结一站式指南:第 1 部分

> 原文：<https://medium.com/hackernoon/the-one-stop-guide-to-easy-cross-platform-python-freezing-part-1-c53e66556a0a>

![](img/44ae3cb812a363cadf33307fb1c3eb2d.png)

Image taken from Sqreen.io

我成为 MusicBrainz Picard 的维护者已经快一年了，这是一款跨平台多语言的桌面应用程序，允许你通过这个[非常酷的服务 MusicBrainz](http://musicbrainz.org) 来标记你的音乐文件。

我认为 Picard 是一个相当大的 python 应用，大约有 35k SLoC。对于这样规模的 python 应用程序，挑战来了。去年我面临的最大挑战之一是在我为我的 GSoC 项目将 Picard 移植到 Python 3/PyQt5 之后，为它支持的所有三个平台打包 Picard，Linux、macOS 和 Windows。你可以在这里阅读更多关于[的内容。](https://hackernoon.com/500-commits-of-summer-my-story-of-foss-and-gsoc-40bb8b325f65)

> “冻结”您的代码就是创建一个可执行文件分发给最终用户，其中包含您的所有应用程序代码以及 Python 解释器。
> 
> 以这种方式发布的好处是，即使用户没有安装所需的 Python 版本(或任何版本)，您的应用程序也会“正常工作”。在 Windows 上，甚至在许多 Linux 发行版和 OS X 上，还没有安装正确的 Python 版本。
> 
> -[Python 的搭便车指南](http://docs.python-guide.org/en/latest/shipping/freezing/)

## 测试我的选择

我们现有的设置分别使用 py2exe 和 py2app 来冻结 Windows 和 macOS 的 Picard。因为它们不完全支持 Python 3 和 PyQt5，所以我在寻找一个新的冻结工具。在用 [cx_Freeze](https://anthony-tuininga.github.io/cx_Freeze/) 测试了 waters 之后，我最终选定了 [PyInstaller](http://pyinstaller.org) 。

## py installer——一个快乐的惊喜

关于 PyInstaller，我想说的一件事是——冻结我的 python 应用程序是如此容易，而我只需付出最少的努力，就能获得神话般的依赖，这让我感到非常惊讶。它支持 Python 2 和 3 以及所有 3 个桌面操作系统，甚至允许您为每个操作系统创建可移植的一体化二进制文件。多酷啊！

我打算让您稍微了解一下 PyInstaller 是多么强大和简单，以及如何与 [AppVeyor](https://ci.appveyor.com) 和 [TravisCI](http://travis-ci.org) 结合使用，您甚至无需访问其中任何一个就可以为 Windows 和 macOS 打包 python 应用程序。

# 入门指南

在博客的这一部分，我们将创建一个 PyInstaller 规范文件并冻结我们的包。在下一部分中，我们将研究 TravisCI 和 AppVeyor 的持续交付。

## 安装 PyInstaller

你需要做的就是`pip install pyinstaller`。就这么简单。您可以在全局范围内安装它，也可以安装在您的项目所在的虚拟环境中。后者显然是更可取的。这将让你主要访问 2 个脚本，我们将在本教程的其余部分使用— `pyinstaller`和`pyi-makespec`。

## 项目信息和结构

让我们从一个非常基本的结构开始介绍 PyInstaller，然后根据我们的需要进行调整。

```
package_dir
├── package
│   ├── submodule_bar
│   ├── submodule_foo
│   ├── __init__.py
│   └── main.py
├── entry_point.py
└── setup.py
```

以上假设您有一个名为`entry_point.py`的入口点脚本，它启动您的应用程序。参见 [python-packaging](http://python-packaging.readthedocs.io/en/latest/index.html#) 获取如何打包应用程序的帮助。

现在神奇的部分来了。要冻结你的应用程序，你只需

`pyinstaller entry_point.py -n foobar`

就这么简单！PyInstaller 将自动计算出所有的依赖项，包括所有需要加载的动态库，并使用名为`**foobar**`的冻结应用创建一个`dist`目录。

输出应该如下所示

```
package_dir
├── dist
│   └── foobar
│       ├── ...
│       ├── ...
│       ├── ...
│       └── foobar
├── package
│   ├── submodule_bar
│   ├── submodule_foo
│   ├── __init__.py
│   └── main.py
├── entry_point.py
├── foobar.spec
└── setup.py
```

你可以通过启动`dist/foobar/foobar`来执行你的 app(当然在 Windows 和 macOS 上分别会是`foobar.exe`或者`foobar.app`)。

## 便携应用，这是什么魔力？

现在让我们更进一步。如果您希望将整个应用程序与其所有依赖项捆绑成一个可移植的可执行文件，该怎么办？很简单，只需将`--onefile`标志传递给 PyInstaller。

`pyinstaller entry_point.py -n foobar --onefile`

PyInstaller 将在名为`foobar`的 dist 文件夹中输出一个可移植的可执行文件，这个文件很容易启动。同样，PyInstaller 将自动查找并捆绑该文件中的所有依赖项！

# 不会这么简单吧？可以吗？如果我需要…

## 捆绑库

PyInstaller 支持许多现成的主要框架和库。这包括—

Babel，Django，IPython，matplotlib，numpy，pillow，PyGTK，PyQt4，PyQt5，scipy，sphinx，SQLAlchemy，wxPython 和[等等。](https://github.com/pyinstaller/pyinstaller/wiki/Supported-Packages)

如果你的应用程序依赖于以上任何一个库，你不需要担心包含依赖库、dll、隐藏导入、包或其他任何东西的麻烦。PyInstaller 为您打理一切。它递归地检查你的代码，找出所有的依赖关系。

## 添加和捆绑资源

资源可以是任何东西，图像、图标、文本数据、翻译字符串。捆绑和访问您的资源有一个非常简单的方法。为了简单起见，让我们假设您所有的资源都在一个名为`resources`的目录中，如下所示

```
package_dir
├── package
│   ├── submodule_bar
│   ├── submodule_foo
│   ├── __init__.py
│   └── main.py
├── resources
│   ├── bar.dat
│   └── foo.png
├── entry_point.py
└── setup.py
```

***捆绑资源***

运行`pyi-makespec entry_point.py -n foobar --onefile`。`pyi-makespec`脚本接受与`pyinstaller`相同的参数，但是它没有实际运行 PyInstaller，而是创建了一个`foobar.spec`规格文件供您定制，然后可以用`pyinstaller foobar.spec`调用。

您的`foobar.spec`文件应该是这样的——

spec 文件只是一个 python 脚本，尽管上面显示了一些特殊的调用。要添加资源，您只需创建一个数组，其中包含元组列表——

*   第一个字符串指定当前系统中的一个或多个文件。
*   第二个指定运行时包含文件的*文件夹*的名称。

一个简单的脚本可以做到这一点

您将把上面的代码添加到规范文件中，它现在看起来应该是这样的—

*注意* `*a.datas*` *中对* `*get_resources()*` *的调用。*

***访问捆绑资源***

引用自 [PyInstaller wiki](http://pyinstaller.readthedocs.io/en/stable/runtime-information.html) —

> 您可能需要在运行时了解应用程序是从源代码运行，还是被“冻结”(捆绑)。例如，您可能有通常基于模块的`__file__`属性找到的数据文件。当代码被捆绑时，这是行不通的。
> 
> PyInstaller 引导加载程序将名称`frozen`添加到`sys`模块中。所以测试“我们被捆绑了吗？”

总之，这是你需要做的，以访问你捆绑的任何资源—

将以下两个变量添加到您的实用程序部分—

然后，您可以在您的`entry_point.py`中使用它，如下所示—

您现在可以在`main.py`中加载您的资源，如下所示——

## 捆绑二进制文件

PyInstaller 应该通过检查 python 模块自动捆绑任何`.so`或`.dll`文件。但是如果[做不到，那么](http://pyinstaller.readthedocs.io/en/stable/spec-files.html#adding-binary-files)很容易添加它们。

捆绑应用程序依赖的二进制文件或库与捆绑数据文件的方式非常相似。

假设以下目录结构—

```
package_dir
├── bin
│   ├── bar.so
│   ├── bar.dll
│   └── bar.dylib
├── package
│   ├── submodule_bar
│   ├── submodule_foo
│   ├── __init__.py
│   └── main.py
├── resources
│   ├── bar.dat
│   └── foo.png
├── entry_point.py
└── setup.py
```

假设你的应用程序依赖于一个共享库`bar`，你有适用于所有 3 个操作系统的二进制文件。

你可能会把它们包括在内，如下所示

你可能会问，引用 PyInstaller Wiki 的话，将文件添加为数据文件或二进制文件有什么区别

> 二进制文件指的是 dll、动态库、共享目标文件等，PyInstaller 将在其中搜索进一步的二进制依赖项。图像和 pdf 等文件应放入`datas`

因此，请确保添加任何 dll 或 So 文件作为二进制文件，而不是数据文件。

## 冻结图形用户界面应用程序

你可能想要将`--windowed`标志传递给`pyinstaller`，以确保在打开应用程序时没有控制台。

## 冻结 macOS 应用程序

如果你正在冻结一个单文件窗口的 macOS 应用程序，你会想要添加一个额外的调用到你的规范文件中，就像这样

有关这些选项的更多信息，请参见 [PyInstaller-Wiki](http://pyinstaller.readthedocs.io/en/stable/spec-files.html#spec-file-options-for-a-mac-os-x-bundle) 。

> 注意:对于简单的情况，您也可以通过传递给`pyisntaller`或`pyi-makespec`脚本的标志来完成上述所有任务。更多信息见[使用规格文件](http://pyinstaller.readthedocs.io/en/stable/spec-files.html)。

# 下一步是什么？

以上食谱对于所有的一般用例应该是绰绰有余的。我希望以上提供了如何使用 PyInstaller 的基本指南。对于更高级的用例，您可以仔细阅读 [PyInstaller Wiki。](http://pyinstaller.readthedocs.io/en/stable/spec-files.html#)

如果你想看到上面的指南在起作用，你可以看看 [Picard github 回购](https://github.com/metabrainz/picard)。

在本博客的下一部分，我们将学习如何使用 AppVeyor 和 TravisCI 以及 PyInstaller 来捆绑我们的应用程序。

# 哈利普，我卡住了！

如果你发现自己无法理解指南的任何部分，或者有一个非常特殊的用例，请在下面留下评论，如果可以的话，我很乐意帮助你:)