# 如何使用 Visual Studio 代码

> 原文：<https://medium.com/hackernoon/how-to-use-visual-studio-code-b0ffb26d33ea>

![](img/7ee36bb034babe7f0a1466e3edfb7110.png)

> 对学习 JavaScript 感兴趣？获取我的 [JavaScript 手册](https://flaviocopes.com/page/javascript-handbook)

编辑是一种奇怪的动物。有些人极力为他们的编辑选择辩护。在 Unix 世界里，你有那些`Emacs`对`vi`“战争”，我有点想象*为什么*这么多时间花在争论一个对另一个的优势上。

在过去的几年里，我使用了大量的编辑器和 ide。我能记住 TextMate，TextWrangler，Espresso，BBEdit，XCode，Coda，括号，Sublime Text，Atom，vim，PHPStorm。IDE 和编辑器的区别主要在于特性集和复杂性。

与 IDE 相比，我更喜欢编辑器，因为它速度更快，而且不太碍事。

在过去的 12 个月里，我一直在使用微软的开源编辑器 VS Code，它很快成为我最喜欢的编辑器。

# 该不该转 VS 代码？为什么呢？

如果你在寻找是否使用它的建议，让我说**是的**，你应该从你现在使用的任何其他编辑器切换到它。

这个编辑器建立在微软几十年的编辑经验之上。

编辑器的代码是完全开源的，使用它不需要付费。

它以[电子](https://electronjs.org/)为基础，这使得它可以跨平台，在 Mac、Windows 和 Linux 上工作。它是使用 [Node.js](https://hackernoon.com/tagged/nodejs) 构建的，你可以使用 [JavaScript](https://hackernoon.com/tagged/javascript) 扩展它(这使得它成为所有美国 JavaScript 开发者的胜利)。

这是**快**，很容易是我在 Sublime Text 之后用过的最快的编辑器。

它赢得了社区的热情:有数以千计的**扩展**，有些是官方的，有些是社区制作的，它是[获奖调查](https://insights.stackoverflow.com/survey/2018/)。

微软每月发布一次更新。频繁的更新促进了创新，微软正在倾听用户的声音，同时尽可能保持平台的稳定(我应该说，在一年的时间里，我几乎每天都在使用 VS 代码，从来没有遇到过问题)。

# 入门指南

Visual Studio 代码在互联网上的主页是[https://code.visualstudio.com/](https://code.visualstudio.com/)。

请访问该网站下载编辑器的最新稳定版本。

![](img/2c527c30433be857945184d6a864af91.png)

安装过程取决于平台，您应该已经习惯了。

首次启动编辑器时，您将看到欢迎屏幕:

![](img/ecb72fb978460a84021e1dd45384e444.png)

左侧有一个包含 5 个图标的工具栏。可以访问:

*   文件浏览器
*   搜索
*   源代码控制
*   调试器
*   扩展

# 探险家

让我们从探索者开始探索(双关语)。

![](img/7d846c66cca513f7ee706fb3af6b3797.png)

按下侧边栏中的“打开文件夹”按钮，或欢迎页面中的`Open folder...`链接。两者都会触发文件选择器视图。

选择一个有源代码的文件夹，或者仅仅是文本文件，然后打开它。

VS 代码将在视图中显示文件夹内容:

![](img/696938efaae528bbd5e36c7cb2347d31.png)

在右边，空视图显示了一些执行快速操作的命令，以及它们的键盘快捷键。

如果您选择左侧的文件，该文件将在主面板上打开:

![](img/be8475344b357703f231d34d89722373.png)

如果您开始编辑它，请注意标签中的文件名旁边会出现一个点，边栏中也会出现:

![](img/18b4103cda4eff4ff224de2d1d73cb4e.png)

按下`CMD+P`将向您展示一个快速文件拾取器，轻松移动大型项目中的文件:

![](img/fccc00dc830c8b43395cc0deb47086d1.png)

你可以使用快捷方式`CMD+B`隐藏文件所在的侧边栏。

> *注意:我使用的是 Mac 键盘快捷键。大多数时候，在 Windows 和 Linux 上，你只要把 CMT 改成 CTRL 就可以了，但并不总是这样。打印你的* [*快捷键参考*](https://code.visualstudio.com/docs/getstarted/keybindings#_keyboard-shortcuts-reference) *。*

# 搜索

工具栏中的第二个图标是“搜索”。点击显示搜索界面:

![](img/9923f0c250b34e5ad2a45e6e1356ffaf.png)

您可以单击图标使搜索区分大小写，匹配整个单词(而不是子字符串)，并对搜索字符串使用正则表达式。

要执行搜索，请按`enter`。

单击左侧的▷符号可启用搜索和替换工具。

单击这 3 个点会显示一个面板，让您只包括某些特定类型的文件，而排除其他文件:

![](img/348a176f82809f7cc2e05e57bdd73aa8.png)

# 源代码控制

通过单击工具栏中的第三个图标，可以启用“源代码管理”选项卡。

![](img/5e018cc6f61a8ff2eb82e07c0c60e31c.png)

VS 代码自带 Git 支持。在这种情况下，我们打开的文件夹没有初始化源代码管理。

单击顶部带有 Git 徽标的第一个图标，我们可以初始化 Git 存储库:

![](img/3b904ea7ecdf26308507946312a03f4c.png)

每个文件旁边的`U`意味着自从上次提交以来它已经被更新了(因为我们从来没有提交过，所以所有的文件都被更新了)。

通过编写文本消息并按下`Cmd-Enter`，或者点击顶部的✔︎图标，创建第一个提交。

![](img/3e308e5ba2ebc0b9b347c54a35cc3f62.png)

我通常将它设置为在提交更改时自动存放更改。

当点击 3 点图标时，它提供了许多与 Git 交互的选项:

![](img/fdd4132bdf073e8fc9174b57a247a41d.png)

# 调试器

工具栏中的第四个图标打开 JavaScript 调试器。这本身就值得写一篇文章。同时[查看官方文件](https://code.visualstudio.com/docs/editor/debugging)。

# 扩展ˌ扩张

第五个图标将我们带到扩展。

![](img/84d38133713edafa4846f5eec2358f75.png)

扩展是 VS 代码的一个杀手级特性。

它们能提供如此多的价值，以至于你最终肯定会大量使用它们。

我安装了很多扩展。

需要记住的一点是，你安装的每个扩展都会影响(或多或少)你的编辑器的性能。

您可以禁用安装的扩展，仅在需要时启用。

您还可以禁用特定工作区的扩展(我们稍后将讨论工作区)。例如，您不想在 Go 项目中启用 JavaScript 扩展。

这里有一个推荐的扩展列表，包括所有最流行的工具。

![](img/fef392d2eb41c2ddd4786ba7e240ca84.png)

由于我为我的博客编辑了很多 markdown 文件，VS Code 向我推荐了`markdownlint`扩展名，它为 Markdown 文件提供了林挺和语法检查。

举个例子，我们来安装一下。

首先，我检查视图的数量。1.2M，这么多！并且评论是正面的(4.55)。单击扩展名将在右侧打开详细信息。

![](img/f237b0dda466a94eb9047e130dc0fda9.png)

按绿色的安装按钮开始安装过程，这很简单。它会为你做一切，你只需要点击“重新加载”按钮来激活它，这基本上是重新启动编辑器窗口。

搞定了。让我们通过创建一个有错误的 markdown 文件来测试它，比如一个图像上缺少了一个`alt`属性。它成功地告诉我们:

![](img/a21b7e1af570b2750d41ac38781889cb.png)

下面我介绍一些你不想错过的流行扩展，也是我最常用的。

# 终点站

VS 代码有一个集成的终端。

你可以从菜单`View ➤ Integrated Terminal`中激活它，或者使用`CMD+\`，它会用你的默认外壳打开。

![](img/2cfa3edd90b3cd06945f8451dab16e00.png)

这非常方便，因为在现代 web 开发中，你几乎总是有一些`npm`或`yarn`进程在后台运行。

您可以创建多个终端标签，将它们一个接一个地显示，也可以将它们堆叠在窗口的右侧，而不是底部:

![](img/b4d1316a125120fbfb59fc4d3b8b5af0.png)

# 命令选项板

命令面板是一个非常强大的工具。您可以通过点击`View ➤ Command Palette`或使用`CMD+SHIFT+P`来启用它

一个模态窗口会出现在顶部，根据你安装的插件和你最后使用的命令，为你提供不同的选项。

我执行的常见操作有:

*   **扩展:安装扩展**
*   **首选项:颜色主题**改变颜色主题(我有时会从夜晚变到白天)
*   **格式化文档**，自动格式化代码
*   **运行 Code Runner 提供的代码**，执行高亮显示的 JavaScript 代码行

您可以通过开始键入来激活其中的任何一个，自动完成功能会显示您想要的那个。

还记得之前你输入`CMD+P`来查看文件列表吗？这是命令面板特定功能的快捷方式。还有其他的:

*   `Ctrl-Shift-Tab`显示活动文件
*   `Ctrl-G`打开命令调板，让您输入要转到的行号
*   `CMD+SHIFT+O`显示当前文件中找到的符号列表

是什么符号*取决于文件类型。在 JavaScript 中，这些可能是类或函数。在 Markdown 中，节标题。*

# 主题

您可以通过点击`CMD-k` + `CMD-t`，或者调用*首选项:颜色主题*命令来切换使用的颜色主题。

这将向您显示已安装的主题列表:

![](img/7f1609e6cc5437d737c822ae49e475c4.png)

你可以点击一个，或者用键盘移动，VS 代码会给你一个预览。单击 enter 应用主题:

![](img/7a3c65c4ada3efb13d0614b55e9c8155.png)

主题只是扩展。你可以去扩展管理器安装新的主题。

可能最好的发现方式是使用市场网站。

我最喜欢的主题是 [Ayu](https://marketplace.visualstudio.com/items?itemName=teabyii.ayu) ，它为一天中的任何时候提供了一个伟大的风格，晚上，早上/晚上和下午。

# 用户化

主题只是你可以做的一个定制。

分配给文件的侧边栏图标也是良好用户体验的重要组成部分。

您可以转到`Preferences ➤ File Icon Theme`来更改这些内容。Ayu 自带图标主题，与主题颜色完美匹配:

![](img/a3b58cc27a3dd7ca2f14123f8271ac9a.png)

到目前为止，我们所做的所有定制，主题和图标主题，都保存到用户首选项中。

转到`Preferences ➤ Settings`(也可以通过`CMD-,`到达)查看它们:

![](img/95eae0a0a15771c9103ccac0e275e97b.png)

为了便于参考，该视图在左侧显示默认设置，在右侧显示被覆盖的设置。你可以在`workbench.colorTheme`和`workbench.iconTheme`看到我们设置的主题名称和图标主题。

我用`CMD-+`放大，这个设置也被保存到`window.zoomLevel`，所以下次 VS 代码启动时，它会记住我的缩放选择。

您可以在**用户设置**中决定全局应用一些设置，或者在**工作区设置**中决定相对于工作区应用一些设置。

大多数情况下，这些设置是由扩展或 VS 代码本身自动添加的，但在某些情况下，您可以在这里直接编辑它们。

# 不错的配置选项

我在代码中设置了一些不错的配置选项:

*   `"editor.minimap.enabled": false`移除显示在编辑器右边的小地图
*   `"explorer.confirmDelete": false`当我想删除一个文件时不要再问我确认(我有源代码管理！)
*   `"explorer.confirmDragAndDrop": false`禁用拖放确认
*   `"editor.formatOnSave": true`保存时自动格式化代码
*   当我将代码粘贴到我的代码中时，自动格式化代码
*   `"javascript.format.enable": true`启用 JavaScript 代码的格式
*   `"files.trimTrailingWhitespace": true`修剪文件中的空白
*   `"editor.multiCursorModifier": "alt"`当点击 Alt 键并用鼠标点击时，我可以选择多行
*   `"editor.detectIndentation": true`适应文件缩进，在编辑别人代码时有用
*   `"editor.quickSuggestionsDelay": 0`立即显示代码建议，而不是几秒钟后

# 编码的最佳字体

我喜欢 [Fira 代码](https://github.com/tonsky/FiraCode)。它是免费的，并且有一些非常好的编程连字，可以将常见的结构如`!==`和`=>`转换成更好的符号:

![](img/52c4c6b072b991f4b419f422163a240c.png)

通过安装字体并将其添加到您的配置中来启用它:

```
"editor.fontFamily": "Fira Code", 
"editor.fontLigatures": true`
```

# 工作区

所有用户设置都可以在工作区设置中被覆盖。它们优先。例如，当您使用的项目的林挺规则不同于您使用的所有其他项目，并且您不想只为它编辑您最喜欢的设置时，它们非常有用。

点击`File ➤ Save Workspace as...`菜单，从现有项目创建一个工作空间。

当前打开的文件夹将被启用为工作区主文件夹。

下次打开 VS code 或者切换 project 时，不是打开一个文件夹，而是打开一个工作区，这会自动打开包含您的代码的文件夹，它会记住您为该工作区设置的所有设置。

除了拥有工作区级别的设置之外，您还可以禁用特定工作区的扩展。

你可以只处理文件夹，直到你有一个特定的理由需要一个工作空间。

一个很好的理由是拥有多个独立的根文件夹的能力。您可以使用`File ➤ Add Folder to Workspace`来添加一个新的根文件夹，它可以位于文件系统中的任何位置，但是会与您已有的其他文件夹一起显示。

# 编辑

# 智能感知

当你用一种支持的语言(JavaScript、JSON、HTML、CSS、Less、Sass、C#和 TypeScript)进行编辑时，VS Code 拥有 IntelliSense，这是一种在你键入函数和参数时提示它们自动完成的技术。

# 代码格式

在命令面板上有两个方便的命令(`Format Document`和`Format Selection`)可以用来自动格式化代码。默认情况下，VS 代码支持 HTML、JavaScript、TypeScript 和 JSON 的自动格式化。

# 错误和警告

当你打开一个文件时，你会在右边看到一个带有一些颜色的条。这些颜色表明您的代码中存在一些问题。举个例子，我现在看到的是:

![](img/a101a539ed21a42c831dc8a09764dfeb.png)

这些都是警告或错误。你可以试着在代码中找到它们，你可以看到用红色下划线标出的部分，或者你也可以按下`CMD-Shift-M`(或者选择`View ➤ Problems`)

![](img/7b75db50872077bd7c16dfbc9745c337.png)

# 快捷键

到目前为止，我已经向您展示了许多键盘快捷键。

记住它们开始变得复杂，但是它们是很好的生产力辅助工具。我建议打印官方快捷方式小抄，针对 [Mac](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf) 、 [Linux](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-linux.pdf) 和 [Windows](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf) 。

# 键盘映射

如果你习惯了其他编辑器的快捷键，也许是因为你在一个编辑器上工作了很长时间，你可以使用一个键映射。

VS 代码团队为最流行的编辑器提供了现成的键映射:vim、Sublime Text、Atom、IntelliJ、Eclipse 等等。它们以插件的形式提供。通过打开`Preferences ➤ Keymaps Extensions`菜单。

# 代码片段

片段很酷。

对于您可能使用的每种语言，都有一些扩展提供了现成的代码片段供您使用。

对于 JavaScript/React，一个流行的是 [VS 代码 ES7 React/Redux/React-Native/JS 片段](https://marketplace.visualstudio.com/items?itemName=dsznajder.es7-react-js-snippets)

你只需输入`rfe`，按 TAB 键，这就会出现在你的编辑器中:

```
import React from 'react'

const $1 = props => {
  return <div>$0</div>
}

export default $1
```

有很多这样的捷径，它们节省了很多时间。不仅仅是打字，还有查找正确的语法。

您也可以定义自己的代码片段。点击`Preferences ➤ User Snippets`并按照说明创建您自己的代码片段文件。

# 扩展展示

*   [**GitLens**](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) :想象谁对你的代码行做了最后一次修改，以及这是什么时候发生的
*   [**Git 历史**](https://marketplace.visualstudio.com/items?itemName=donjayamanne.githistory) 可视化并搜索 Git 历史
*   [**CSS Peek**](https://marketplace.visualstudio.com/items?itemName=pranaygp.vscode-css-peek) 让您通过检查 HTML 元素的类来查看和编辑 CSS 定义。非常方便。
*   [**Code Runner**](https://marketplace.visualstudio.com/items?itemName=formulahendry.code-runner) 让你运行你在编辑器中选择的代码，等等。支持多种语言。
*   [**Chrome 调试器**](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome) 允许您使用 VS 代码调试器调试浏览器中运行的 JavaScript 代码。
*   [**括号对着色器**](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer) 便于在代码中可视化括号结尾。
*   [**缩进-彩虹**](https://marketplace.visualstudio.com/items?itemName=oderwat.indent-rainbow) 给代码的缩进层次着色。
*   [**更漂亮**](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) 查看我的[更漂亮指南](https://flaviocopes.com/prettier/)
*   [**诚信通**](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 查看我的[诚信通指南](https://flaviocopes.com/eslint/)
*   [**CSS 智能感知**](https://marketplace.visualstudio.com/items?itemName=Zignd.html-css-class-completion) 根据您的工作区定义改进 CSS 的自动完成功能
*   [**npm**](https://marketplace.visualstudio.com/items?itemName=eg2.vscode-npm-script) 从命令面板启用 [npm](https://flaviocopes.com/npm/) 实用程序功能
*   [**自动关闭标签**](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag) 自动关闭 HTML/JSX/*标签

# VS 代码 CLI 命令

当您安装 VS 代码时，`code`命令在您的命令行中是全局可用的。

这对于启动编辑器，用`code .`打开一个包含当前文件夹内容的新窗口非常有用。

`code -n`将创建一个新窗口。

一个不为人知的有用之处是，VS 代码可以用`code --diff file1.js file2.js`快速显示两个文件之间的差异。

> 对学习 JavaScript 感兴趣？获取我的 [JavaScript 手册](https://flaviocopes.com/page/javascript-handbook)

*原载于*[*flaviocopes.com*](https://flaviocopes.com/vscode/)*。*