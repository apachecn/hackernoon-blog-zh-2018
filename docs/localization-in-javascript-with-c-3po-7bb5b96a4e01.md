# 使用 c-3po 的 javascript 本地化

> 原文：<https://medium.com/hackernoon/localization-in-javascript-with-c-3po-7bb5b96a4e01>

> 项目被重命名为 **ttag** 。跟随新博士——[https://ttag.js.org/](https://ttag.js.org/)。由于可能的法律侵权，该名称已被更改。(详见本期[第](https://github.com/ttag-org/ttag/issues/104)期)

大家好！

这篇文章是关于新的 **javascript 本地化**工具— [**c-3po**](https://c-3po.js.org/) 。我们在生产中使用 c-3po 已经将近一年了，我们对此很满意。c-3po 被设计成在 GNU gettext 格式之上提供一个智能和可靠的翻译工作流程。总的来说，c-3po 是一个库加上一组[工具](https://hackernoon.com/tagged/tools)，可以让你用最少的努力来本地化你的项目。

恐惧问题:"**为什么** **又多了一个汉化库**？"。有几个成熟的本地化解决方案可以让您满意，但我们决定构建自己的解决方案的主要原因是:

*   我们不想对字符串使用 [**sprintf**](https://www.tutorialspoint.com/c_standard_library/c_function_sprintf.htm) 格式。 **es6** 引入了 [**模板字符串**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) ，我们只是想用它们来进行字符串格式化。
*   我们正在寻找一种智能工具，它可以从源代码(js，jsx)中提取翻译字符串，并且能够为提取的字符串添加某种验证。
*   我们一直在寻找一种解决方案，允许我们在构建步骤和脚本执行时预编译翻译。
*   与框架无关的解决方案，几乎可以集成到任何 [javascript](https://hackernoon.com/tagged/javascript) 框架中，并且可以用于任何构建工具。
*   希望确保在 CI 构建期间翻译所有字符串。

# 欢迎 c-3po！

以下是如何使用该库的一些示例:

复数形式支持:

> 关于为什么我们应该使用`t`标签作为第一个带有 **msgid** 的参数的更多细节在[这里](https://c-3po.js.org/why-use-msgid-for-ngettext.html)。

上下文支持:

# 。采购订单文件

根据 **GNU gettext** 格式，所有翻译都放在**里面。po** 文件。每个地区都有自己独立的**。po** 文件和所有翻译都添加在那里。c-3po 生态系统提供了可靠的工具集，可以创建和更新那些。po 文件。

因此，我们必须以某种方式解析我们的源代码，并提取所有用 c-3po 标签和函数包装的字符串。为此，我们创建了与 javascript AST 配合良好的[**babel-plugin-c-3po**](https://github.com/c-3po-org/babel-plugin-c-3po)。因此，它可以在 babel 工作的任何地方使用(jsx，ts，tsx，React Native e.t.c)。插件选项[在这里](https://c-3po.js.org/configuration.html)有一个单独的文档页面。只是为了减少一些样板工作，我们创建了 [**c-3po-cli**](https://github.com/c-3po-org/c-3po-cli) ，它工作在那个插件之上。

> `npm install -g c-3po`将把`c-3po`命令添加到您的环境中，这将在下面的例子中使用。

# 。采购订单文件创建

假设我们有一个`hello.js`文件，并想将其本地化为乌克兰语。

要开始，您需要设置**。po** 文件为乌克兰语。

```
c-3po init uk uk.po
```

> **uk** 是乌克兰语的 ISO 代码。(所有支持的代码可在[这里](http://docs.translatehouse.org/projects/localization-guide/en/latest/l10n/pluralforms.html)找到)

`init`命令将创建一个空的`po`文件，该文件包含所需文件头的最小集合。

# 将翻译提取并更新到。采购订单文件

要提取所有带标签的字符串，您需要执行一个简单的命令:

```
c-3po update uk.po hello.js
```

之后，我们的`Hello ${ name }`字符串必须出现在 **uk.po** 文件中:

```
#: hello.js:3
msgid "Hello ${ name }"
msgstr "
```

> **更新**命令将添加新的字符串，并删除那些不在源文件中的字符串。如果您的工作流程需要**。pot** 文件—您可以使用`extract`和`merge`命令。

# 构建本地化文件

如果您有能力为每种语言构建一个单独的包，建议您这样做，因为这将提高客户端性能(在客户端上没有额外的解析和替换+较小的包)。

```
c-3po replace uk.po hello-uk.js hello.js
```

该命令将创建原始文件`hello-uk.js`的本地化版本，并应用来自`uk.po`的所有翻译。

# 在运行时应用翻译

另一个选择是你可以应用适当的**。po** 文件上的一个脚本执行。

以下是如何为我们简单的`hello.js`实现这一点:

您还可以使用 webpack 和 [po-gettext-loader](https://github.com/cah4a/po-gettext-loader) 构建您的工作流，并使用动态导入功能导入适当的`.po`。

有了这个设置，`.po`文件中的每一个变化都会触发 webpack 构建。你可以点击阅读更多关于 webpack [不同设置的信息。](https://c-3po.js.org/localization-with-webpack-and-c-3po.html)

# 翻译验证和 CI

如果有一些未翻译的字符串或一些用标记包装的无效翻译，此命令将抛出:

```
c-3po check uk.po hello.js
```

关于**验证**—[https://c-3po.js.org/validation.html](https://c-3po.js.org/validation.html)的更多细节

# 下一步是什么？

尽管如此，我们仍在努力改进翻译过程，并打算向库和工具添加新功能。

*   简化 webpack 集成。
*   简化翻译设置。
*   打字稿。
*   更多文档和示例。

目前的翻译过程需要相当多的样板文件，我们可以减少，我们将解决这一问题。任何反馈将不胜感激，公关的欢迎！

# 更多链接

*   文件和示例—[https://c-3po.js.org/](https://c-3po.js.org/)
*   c-3po 库—[https://github.com/c-3po-org/c-3po](https://github.com/c-3po-org/c-3po)
*   巴别塔插件 c-3po—[https://github.com/c-3po-org/babel-plugin-c-3po](https://github.com/c-3po-org/babel-plugin-c-3po)
*   c-3po-CLI—【https://github.com/c-3po-org/c-3po-cli 