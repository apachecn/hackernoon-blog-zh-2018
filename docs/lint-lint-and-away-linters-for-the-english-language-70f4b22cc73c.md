# 棉绒，棉绒，扔掉！英语短绒

> 原文：<https://medium.com/hackernoon/lint-lint-and-away-linters-for-the-english-language-70f4b22cc73c>

# 亚历克斯

Alex 是一个单一用途的 linter，用于突出基于性别、种族、宗教或其他潜在不敏感语言的潜在不敏感单词。比如:

> *他启动了主节点，并连接了两个从节点。*

在这种情况下，亚历克斯会建议你用“他们”代替“他”，并对“主人”和“奴隶”使用不同的术语

Alex 适用于:

*   [npm](https://www.npmjs.com/package/alex) ，你也可以连接到 CI 系统。
*   [原子](https://github.com/wooorm/atom-linter-alex)
*   [崇高](https://github.com/sindresorhus/SublimeLinter-contrib-alex)
*   [Visual Studio 代码](https://github.com/shinnn/vscode-alex)
*   [一饮而尽](https://github.com/dustinspecker/gulp-alex)
*   [松弛](https://github.com/keoghpe/alex-slack)

# 就说不

另一个单一的目的，只是说不要突出所谓的“模糊词”，这往往会使你的写作听起来没有说服力，不自信，或者是不必要的噪音。比如:

> React 似乎很适合你的项目。

在这种情况下，linter 会建议你用更自信的词来代替“似乎”,比如:

> *React 很适合你的项目。*

只需说“不”即可:

*   [原子](https://github.com/lexicalunit/linter-just-say-no)

# 写得好

我最喜欢的 linters 之一， [write good](https://github.com/btford/write-good) 突出了你的文本中可能存在的问题，并带有你可以启用或禁用的检查。例如:

> *只需安装包，真的很简单。*

在这种情况下，linter 突出显示“just”和“really”作为削弱句子意思的单词。

Write good 适用于:

*   [国家预防机制](https://www.npmjs.com/package/write-good)
*   [原子](https://atom.io/packages/linter-write-good)
*   [草稿](https://agiletortoise.com/drafts/index.html)
*   [Visual Studio 代码](https://marketplace.visualstudio.com/items?itemName=travisthetechie.write-good-linter)
*   [升华](https://packagecontrol.io/packages/SublimeLinter-contrib-write-good)

# 散文

[Proselint](http://proselint.com/) 的用法比这个列表中的其他一些更模糊，它结合了从一批“伟大”作家那里学到的东西(用引号括起来，因为这是主观的)来帮助你写出更好的散文。还不清楚它进行了什么检查，但随着它的工作范围越来越广，分析整个句子而不是单个单词，它可以帮助你找到其他 linters 提到的问题。

例如:

> *在中心的中央坐着一群猫。他们在做什么。*

在这种情况下，散文将突出 center/centre 的不一致拼写，并将“bunch of cats”更正为“stalling of cats”这里是它检查的内容的完整列表。

散文棉绒适用于:

*   [Python](https://pypi.python.org/pypi/proselint) 可以连接到 CI 系统。
*   [在线](http://proselint.com/write)
*   [升华](https://github.com/amperser/proselint/tree/master/plugins/sublime/SublimeLinter-contrib-proselint)
*   [原子](https://github.com/smockle/linter-proselint)
*   [Emacs](https://github.com/amperser/proselint/tree/master/plugins/flycheck)
*   [带 Ale 的 Vim](https://github.com/w0rp/ale)和[带语法的 Vim](https://github.com/vim-syntastic/syntastic)
*   [造粒机](https://github.com/google/arc-proselint)
*   [危险](https://github.com/dbgrandi/danger-prose)
*   [Visual Studio 代码](https://github.com/ppeszko/vscode-proselint)
*   [煤炭](https://github.com/coala-analyzer/bear-docs/blob/master/docs/ProseLintBear.rst)
*   [IntelliJ](https://github.com/kropp/intellij-proselint)

# 淡水河谷

让我们进入更大、更可配置的棉绒， [Vale](https://valelint.github.io/docs/) 结合了来自其他棉绒、[的一系列检查，还允许您添加您的风格定义](https://valelint.github.io/docs/styles/)进行检查，这对内部风格指南很有用。由于这一功能，其他人已经为 write-good 和 proselint 提供了定制样式，这意味着您可以使用 Vale 为您管理一系列棉绒。您可以在项目的基础上创建全局或项目级的配置文件来启用特定的检查，并且它比这里的许多 linters 支持更多的文本格式。

由于这种可扩展性，很难提供 Vale 推荐的具体示例，但举例来说:

> *你还没到被出版的年龄。*

根据你启用了什么，Vale 会建议用“太年轻”(一个“Litotes”检查)替换“不够老”，以及“被发表”作为被动语态的一个例子。

淡水河谷可用于:

*   [原生包](https://github.com/ValeLint/vale#installation)
*   [原子](https://github.com/TimKam/atomic-vale)
*   [Emacs](https://github.com/abingham/flycheck-vale)
*   [升华](https://github.com/ValeLint/SubVale)
*   [Visual Studio 代码](https://marketplace.visualstudio.com/items?itemName=lunaryorn.vale)
*   [通过 ALE 进行 Vim](https://github.com/w0rp/ale)

# Textlint

与 Vale 类似， [Textlint](https://textlint.github.io/) 是可插拔、可配置的 linter，支持这里提到的许多其他 linter(或类似的)。默认情况下，什么都不启用，您需要为 Textlint 创建全局或项目级别的配置文件来检查任何东西。例如:

> *这很有用，但多少是 TBD。*

在这种情况下，linter 突出显示了“非常”和“TBD”(作为一个未展开的首字母缩略词)。

Textlint 适用于:

*   [国家预防机制](https://www.npmjs.com/package/textlint)
*   [netbeans](http://plugins.netbeans.org/plugin/73031/textlint-support)
*   [原子](https://atom.io/packages/linter-textlint)
*   [崇高](https://github.com/joeybaker/SublimeLinter-textlint)
*   [Visual Studio 代码](https://marketplace.visualstudio.com/items?itemName=taichi.vscode-textlint)
*   [维姆](https://github.com/heavenshell/vim-textlint)

# 科拉

再次 [Coala](https://www.coala.io/#/home) 结合了几个 linter，旨在创建一个一体化的 linter，可以用于你的整个项目、代码以及文档。[您可以通过添加“bears”](https://coala.io/#/languages)来实现这一点，bears 是与 Coala 一起打包的其他棉绒，其中包括这个列表中的许多棉绒。

Coala 适用于:

*   [Python](https://pypi.python.org/pypi/coala)
*   [码头工人](http://docs.coala.io/en/latest/Users/Install.html#using-coala-with-docker)
*   [Git 挂钩](http://docs.coala.io/en/latest/Users/coala_as_Git_Hook.html)
*   [原子](https://github.com/coala/coala-atom/)
*   [崇高](https://github.com/coala/coala-sublime/)
*   [括号](https://github.com/coala/coala-brackets/)
*   [月食](https://github.com/coala/coala-eclipse/)
*   [Vim](https://github.com/coala/coala-vim/)
*   [Visual Studio 代码](https://github.com/coala/coala-vs-code/)
*   [Emacs](https://github.com/coala/coala-emacs/)

# 而且还有更多！

上面的列表是我最喜欢和最常用的 linters，以及那些有很好的编辑器支持的，但自然，还有更多。

*   检查你是否使用了正确的不定冠词。
*   作为林挺过程的一部分，Aspell 用于拼写检查。
*   [美国](https://atom.io/packages/linter-american)纠正那些鬼鬼祟祟的英式拼写。
*   一个根据 IBM 写作风格指南检查你的文档的工具。

此外，我经常使用的工具，如[海明威](http://www.hemingwayapp.com/)、[语法](https://www.grammarly.com/)、[挑剔者](http://nitpickertool.com/)和[语言工具](https://languagetool.org/)也值得一提，但这些工具并没有针对技术写作进行优化，它们都是独立的工具，不能与 CI 挂钩，也不能与 API 等一起使用。

*最初发表于*[*dzone.com*](https://dzone.com/articles/lint-lint-and-away-linters-for-the-english-languag)*。*