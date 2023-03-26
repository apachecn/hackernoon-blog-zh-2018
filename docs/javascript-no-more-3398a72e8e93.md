# 不再使用 JavaScript

> 原文：<https://medium.com/hackernoon/javascript-no-more-3398a72e8e93>

![](img/72a6ec200ae623ec7bdf584279b12087.png)

> 通过解除大脑所有不必要的工作，一个好的符号可以让它专注于更高级的问题，并且实际上增加了……脑力——阿尔弗雷德·诺斯·怀特海

编程语言是给人用的，不是给计算机用的。除了机器代码，计算机不需要任何编程语言。好的编程语言有助于让人们更容易思考问题。这很重要，因为编写代码不仅仅是为了解决问题。从根本上讲，这也是关于你如何思考、交流和理解的问题。

在今天的网络上，我们有一种占主导地位的语言:JavaScript。JavaScript 之所以被创造出来，是因为马克·安德森“认为 HTML 需要一种‘粘合语言’，易于网页设计师和兼职程序员使用，以组装图像和插件等组件。”(1)

作为前端开发者，我们不应该接受这一点。我们不应该让自己局限于一种语言——尤其是一种著名的在 10 天内拼凑起来的语言，它只有一些“好的部分”。虽然 Axel Rauschmeyer(我非常尊敬他)可能认为对[来说 JavaScript 一团糟是件好事](http://2ality.com/2018/02/js-backward-compatibility.html)——我完全不同意。我们需要帮助我们解决问题的语言，而不是制造更多问题让我们解决的语言。

# 语言和思想

> “编程语言中用于表达编程思想的词语当然决定了你如何表达你的思想，甚至可能决定了你能表达什么思想。”—史蒂夫·麦康奈尔

有一个老笑话是这样说的。

*在天堂:厨师是法国人，情人是意大利人，银行家是瑞士人。地狱里:厨师是英国人，情人是瑞士人，银行家是意大利人。*

按照这个笑话的思路，如果我们想写好烹饪，我们应该用法语写。虽然我们可以用英语写烹饪，但我们会缺乏法语中的词汇和范例，而这些词汇和范例对烹饪来说是必不可少的。事实上，如果你了解烹饪，你就知道这是真的！烹饪中有许多专门的法语术语。即使你在写一本英语烹饪书，你也会使用这些法语单词(例如，mise en place、Bain Marie、Coulis 等)。

你使用的语言会影响你思考和解决问题的方式。为了说明这一点，让我们比较一下 currying 在 Haskell 中的工作方式和在 JavaScript 中的工作方式。我们将考虑一个简单的 currying 函数。

下面是 Haskell 代码:

在 Haskell 中，函数是自动生成的。奉承是 Haskell 范式的一部分。作为一名 Haskell 程序员，我知道这一点。它影响了我写的每一点代码。我思考如何创建一个功能的方式——即解决问题和沟通——在某种程度上涉及到奉承。

下面是一些或多或少做同样事情的 JavaScript 代码(2):

Currying 在 JavaScript 中不是隐式的。如果我想使用 currying，我必须实现它。那我就要显式使用了。并且有大量的实现——不算我自己选择实现的可能性。每种实现都有其成本和收益。

因为我没有本地 currying，所以我不得不处理在 Haskell 中不必做的决定。事实上，如果我试图用 JavaScript 进行函数式编程，我可能不得不做出*许多这样的*决定。每个决定都在几个方面影响我的问题解决和我的代码:

*   [决策疲劳](https://en.wikipedia.org/wiki/Decision_fatigue)
*   多种模式(拉姆达方式、洛达什方式、民间故事方式、一些随机的 NPM 模式……)
*   更多的代码需要管理(更多的代码需要阅读和理解)
*   等等。

正如您所看到的，即使是一个小例子，Haskell 和 JavaScript 处理相同任务的方式也有很大的不同。Haskell 中琐碎而固有的东西在 JavaScript 中变得更加复杂、冗长和陌生。

综上所述，我们可以陈述我们自己版本的怀特海:

> 一门好的编程语言，应用到适当的领域，可以增加你解决问题的能力。糟糕的语言——或者不合适的语言——会让事情变得更难。

# 语言和解决问题

知道了一种语言对解决问题和交流的影响，编程语言经常被创建来解决特定的问题就不足为奇了。几个例子:

*   Ada 是为嵌入式和实时系统而设计的。
*   Erlang 被设计用来编写电话应用程序。
*   ALGOL 被设计用来清晰地描述算法。
*   Pascal 是用来教学生结构化编程的。

现代 web 应用程序是为解决各种各样的问题而编写的。我们应该有多样化的编程语言。我们应该能够使用为我们的问题空间设计的语言，适合我们处理问题的方式的语言，或者我们想从中学习的语言。

简而言之:是时候脱离 JavaScript 了。

# 我们现在可以这样做

尽管 JavaScript 是现代浏览器支持的唯一语言，但我们可以使用多种语言来构建 web 应用程序。由于许多得到良好支持的项目的工作，现在有了大量可以编译成 JavaScript 的语言。甚至有一些项目支持在流行的框架中使用这些语言，比如 React 和 Vue——例如， [Reason-React](https://github.com/reasonml/reason-react) ， [PureScript-React](https://github.com/purescript-contrib/purescript-react) ， [Reason-Vue](https://github.com/aweary/reason-vue) 等等。

关于编译成 JavaScript 的语言的完整列表，请参见 [Jared Ashkenas 的 GitHub 列表](https://github.com/jashkenas/coffeescript/wiki/list-of-languages-that-compile-to-js)。该列表包括所有的。Net 语言，Scala，Haskell，Ruby，Elm，Python，Erlang…等等。Ashkenas 列表上的语言数量如此之大，以至于它暗示了阿特伍德定律的一个同伴:“如果一种编程语言存在，就会有人编写一个工具把它编译成 JavaScript。”

# 前进的道路

虽然有很多语言可以编译成 JavaScript，但这并不是一个完美的选择。编译成 JavaScript 会带来一些已知的问题:

*   调试[编译好的 JavaScript 可能很难](/@roman01la/debugging-clojurescript-6e4d903e831)。
*   如果有工具可以帮助调试，很多工具都不成熟(尽管 [Elm](http://debug.elm-lang.org/) 的调试器看起来很有前途)。
*   由于语言之间的概念差异，一种语言中的某些概念可能无法很好地翻译成 JavaScript。这可能会影响表现和理解。
*   对脚本进行调优和性能改进是一项挑战。
*   等等。

创新总是有代价的。停滞也是如此。停滞的成本更高。如果我们要在前端开发方面取得进展，我们必须有更多的多样性。需要有人带路。当有足够多的开发人员在 web 应用程序空间中使用给定的语言时，工具创建者、供应商和社区就会有动力给予他们支持。即使我们最终只有两三种主流语言，我们也会有更丰富的词汇和范例可以使用。

*注意事项*

1.  [https://en.wikipedia.org/wiki/JavaScript](https://en.wikipedia.org/wiki/JavaScript)
2.  这个代码示例摘自 Steven Syrek。他写过一篇关于使用 JS 学习 Haskell 的文章。可以在这里访问:[https://medium . com/@ sjsyrek/using-JavaScript-to-learn-haskell-f 57509015842](/@sjsyrek/using-javascript-to-learn-haskell-f57509015842)
3.  语言和思维是一个丰富的话题。如果你感兴趣，你可能会喜欢这篇文章:[https://www . languagesociety . org/resource/language-and-thought](https://www.linguisticsociety.org/resource/language-and-thought)