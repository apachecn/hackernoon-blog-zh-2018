# 编码自动化课程#1:请懒惰

> 原文：<https://medium.com/hackernoon/please-be-lazy-and-automate-your-coding-7a0d948324b3>

![](img/dca35e85cf54fc17da1b51b5159cd2a0.png)

当我开始进入代码世界时，我非常高兴能输入每一个字符。如今，我意识到我在一个普通的工作日重写了同样的句子。此外，当我从一个项目切换到另一个项目时，我意识到我正在解决类似的问题。有时我不记得解决方案，所以我被迫回到谷歌，访问那些紫色的结果来寻找相同问题的答案。

在这篇文章中，我将谈论几个自动化工具，它们将帮助你开始用更少的键盘输入进行更多的思考。

## 先说一些定义。

在开始解释与代码自动化相关的一切之前，需要澄清一些概念。这将允许我们说同一种语言。

**样板**，这个术语指的是一组可以作为起点的静态代码。这类似于从框架文档中复制并粘贴一些代码，然后开始创建新的特性。作为*样板*工具的例子，你有 Angular ( `ng new myApp`)、React ( `npx create-react-app my-app`)和 VueJS ( `vue create my-project`)的命令行工具。

**片段**，这个术语类似于*样板文件*，但是针对少量代码。它有助于开发人员减少输入。一些 ID 支持这个特性，检查这些有用的代码片段生成器:JetBrains 团队的 [visual studio 代码](https://code.visualstudio.com/docs/editor/userdefinedsnippets)代码片段插件和[实时模板](https://www.jetbrains.com/help/idea/using-live-templates.html)。

**Linter 或 lint** ，在软件中是指一套分析工具，在编码过程中提醒用户其源代码可能存在的问题。

**自动化**，它是指在没有人类干预的帮助下，生成执行任务的工具的过程。当我们创建代码时，这个过程应该使用 ID 插件或命令行工具自动完成。

## 如何开始

开始自动化流程的第一步是定义需要改进的地方。这篇文章是关于代码的，所以可以自动化的东西如下:

1.  *代码标准*。您是否厌倦了阅读关于`;`的项目文档，或者是否需要在每个`{`前换行。
2.  *新文件的自举*。当创建一个新的 javascript 类时，您是否厌倦了编写相同的行？。每个项目都有不同的架构，但是每个人都遵循设计模式。
3.  *同一段代码的重复输入*。用一个动态片段来实现这种很酷的对齐怎么样？。是否需要在 Angular 创建一个类似商店的服务？。如果你需要一个 javascript 闭包呢？。
4.  *命令行命令*。你写了多少次:`$ cd /documents/my-projects/javascript`、`$ git status`、`$ git checkout origin master`、`$ git pull origin master`？。另外，你在控制台写东西的时候打了多少次错别字？
5.  *寻找已知问题的解决方案。*你过去解决过什么现在忘记了的事情吗？

记住这些要点，让我们开始为每个要点寻找解决方案。

## 代码标准

这是我最喜欢的部分。哪个应该是每个项目的最佳风格指南？。大多数项目都喜欢遵循谷歌、Airbnb 或一些额外的自定义规则。因此，如何自动化这个过程呢？

这里的第一种方法是将所有这些规则写入 linter 工具的配置文件中。它允许我们在输入时直接在 ID 中看到一些错误。大多数开发者使用 ESLint 作为 linter 工具。

棉绒工具就足够了。但是，如果您想在所有项目中使用标准的格式规则，该怎么办呢？。[更漂亮](https://prettier.io/)是一个自动化工具，它根据配置文件中描述的格式规则重写所有代码。它支持大多数开发编辑器，并且可以随时执行。

为谷歌和 Airbnb 的开发团队定义的规则通过他们的 GitHub 仓库公开。如果你想看一看或者在你自己的项目中用作样板，看看这里的[和这里的](https://github.com/google/eslint-config-google)。

## 新文件的引导和同一段代码的重复输入

我合并了这两点，因为两者的解决方案可以用相同的工具创建。

让我们从每个框架的团队创建的命令行工具开始。这些工具允许开发人员根据提供商建议的模式生成新的 javascript 类。这可能已经足够好了，但是，谁知道一个项目完全遵循那个模式呢？。这就是为什么我认为每个开发人员都应该花一些时间为他们的项目自动生成代码。

开始自动化代码的一种方法是代码片断。让我解释一下片段是如何工作的。

1.  首先，您需要确定您每次都重复的一部分代码。
2.  其次，选择您喜欢的代码片段工具。
3.  第三，创建模板。

让我们创建一个:

假设您发现 reduce 方法是项目的一个常见任务。让我们使用 IntelliJ 的实时模板为这个任务创建一个代码片段。

正如您所看到的，您定义了所需的变量(在`$var$`中)，指定了静态文本，并在填写完所有变量后生成了所需的行为。酷！

作为这些定制模式的一个例子，我想参考我的朋友 Edwin 的工作，他为 Angular 应用程序的单元测试创建了一组实时代码模板。以同样的方式，John Papa 创建了一个不错的 visual studio 代码插件[,专注于角度开发。](https://github.com/johnpapa/vscode-angular-snippets)

## 命令行命令

所有开发人员都需要使用命令提示符。打开它后，您要做的第一件事是导航到所需的文件夹。使用别名可以很容易地实现自动化。

让我们来看看:

假设您想导航到`company/projects/my-project`文件夹。

在你的`.bash_profile`中添加下面几行。

```
alias goToProject="cd ~/company/projects/my-project"
```

下次打开 bash 并输入`goToProject`，它将导航到所需的文件夹。

你讨厌图形界面而喜欢在控制台上使用 Git 吗？，只需在你的`.bash_profile`中添加以下几行，你的爱就会增长(更多信息请点击[这里](http://kurtdowswell.com/software-development/git-bash-aliases/))。)

作为一个额外的特性，如果你想在文件路径旁边有分支的名字，你需要添加下面的代码到`.bash_profile`

最后，错别字怎么办？。写了几次:`git brnch`还是`$ phuton`。有一个很酷的插件叫做“ [theFuck](https://github.com/nvbn/thefuck) ”。通过输入**操**，它可以帮助你改正错别字。厉害！。

## 寻找已知问题的解决方案

有些人喜欢在每次遇到问题时浏览 StackOverflow 线程。我发现这是一个重复的任务。为了找到这个问题的解决方案，我将所有这些 URL 存储在一个带有小说明的电子表格中。我得说实话，前一百个链接之后那桌就乱了。

做了一点研究，我发现了不同的服务( [JsFiddle](https://jsfiddle.net/) 、 [JsBin](https://jsbin.com/?html,output) 、 [plunklr](https://plnkr.co/) 、 [Stackblitz](https://stackblitz.com/) 和 [Codepen](https://codepen.io/) )来生成代码并在浏览器上预可视化。我选择 Codepen 作为我的标准项目库，Stackblitz 作为我的 Angular 和 Typescript 项目。这些工具不仅帮助我重现了问题，还让我看到了可行的解决方案。比如我总是忘记 currentTarget 和 Target 的区别。所以我创造了[这个](https://codepen.io/xthecapx/pen/zqVOpv):

下次我需要记住它时，我只需在 Codepen 上打开我的仪表盘。

[在这里](https://codepen.io/xthecapx/pen/VaJZmP) [你](https://codepen.io/xthecapx/pen/KzLEba) [可以](https://codepen.io/xthecapx/pen/BjXBya) [找到我创建的](https://stackblitz.com/edit/thecap-formly) [不同的](https://stackblitz.com/edit/angular-autounsubscribe)项目作为我的个人库。🍻

## 结论

每次写东西写两遍，请偷懒，自动化代码。

感谢阅读，请在 Medium 和 [Twitter](https://twitter.com/thecapnews) 上关注我，或在 [LinkedIn](https://www.linkedin.com/in/cristian-marquez/) 上向我发送连接请求。