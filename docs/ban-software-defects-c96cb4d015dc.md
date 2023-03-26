# 禁止软件缺陷

> 原文：<https://medium.com/hackernoon/ban-software-defects-c96cb4d015dc>

## 被认为有害的测试

![](img/b051307a115ebfea0451f2971b459ec8.png)

## 前端 JavaScript 生态系统充满了神奇的工具。🔮

有一系列的 UI 框架可供使用，从内置解决方案的框架到以模块化方式构建所需内容的轻量级库。您可以选择将您的视图编写为模板语言，用于为称为虚拟 DOM 的普通 [JavaScript](https://hackernoon.com/tagged/javascript) 对象插入 HTML 或语法糖。从使用隐式/命令性`Proxy`对象到显式/声明性单向数据流方法，状态管理有不同的理念。应用捆绑器有不同的种类，从带有大量 automagic 的零配置到高度可定制的程度。

单元测试库的可用选项从根本上来说不够多样。您的选择就像隐式全局变量或显式库导出一样普通。针对不同类型的数据编写测试断言并等待异步结果的流行方式有很多。这些只是同一主题的变体。编写命令式代码来组织和命名您的测试，然后在回调中使用更多命令式代码，该回调抛出异常来通知测试失败。编写这种风格的测试代码通常需要使用模拟、间谍和秘密代理。看不到一个纯粹的功能。

现在是 21 世纪，我们生活在声明式复兴的中期，但是我们的前端测试工具[仍然停留在过去的强制 JavaScript 思维模式中。我认为这是阻碍先测试实践的最大因素之一，在某些情况下，甚至是对避免后测试方法的公然敌视。最重要的是，看看我们为安装这些测试工具中最受欢迎的一个所付出的代价:](https://hackernoon.com/tagged/tools)

```
$ npm i -D jest> node install+ jest@23.4.2
added **583** packages in 29.795s$ du -hd 0 node_modules
 **59M** node_modules
```

当我看到这个的时候，我确信我错过了一些东西。当我四处询问时，普遍的共识是“事情就是这样”。人们已经默认了他们测试软件的膨胀，就像对他们没有犯下的罪行的错误的监禁判决。我写了很多样板文件，使用了不到 1%的功能。

我对这种情况感到如此震惊和失望，以至于我违背自己的判断，选择编写一个新型的测试库。这个库被称为 T5 Tead T6。我从头开始构建了这个工具，并且只包含了测试纯声明性代码所需的几个部分。最终的封装更小一些:

```
$ npm i -D tead+ tead@0.4.4
added **2** packages in 0.772s$ du -hd 0 node_modules
**284K** node_modules
```

# 将测试作为数据输入

事实证明，我描述我的测试所需要的只是用描述将它们组合在一起，并为测试断言本身提供两个比较值。我看到的唯一正确的社区是在 JavaScript 之外。[Elm](http://elm-lang.org)——例如，已经推出了一种纯粹的函数式方法，并为[提供了一个测试库](https://github.com/elm-community/elm-test)，这更符合我在这里的理念，其中每个测试都必须返回一个`Expectation`，对其进行评估以确定测试是否通过。

Elm 还提倡将 [*效果作为数据*](https://youtu.be/6EdXaWfoslc) ，因为他们的方法是从应用程序代码中移除副作用，并返回描述您希望对您执行什么效果以及如何处理结果(如果有的话)的数据。在下一个主要版本的 [Hyperapp](https://github.com/hyperapp/hyperapp/pull/726) web app 微框架中将会提供一个本地 JavaScript 方法，并且今天可以在我的`[@hyperapp/fx](https://github.com/hyperapp/fx)`库中获得。我决定将这一切进行到底，并以数据的形式提供*测试。*

## *我们来看看👀*

Tead 测试被写成表示测试断言及其命名分组的数据结构。下面是这样一个值的例子:

```
// sum is a function that takes two arguments and adds them
{
  "sum can add": {
    zeros: [sum(0, 0), 0],
    "positive numbers": [sum(1, 2), 3],
    "negative numbers": [sum(-1, -2), -3],
    "mixed sign numbers": [sum(1, -2), -1]
  }
}// If the sum functions works correctly the above will be equal to:
{
  "sum can add": {
    zeros: [0, 0],
    "positive numbers": [3, 3],
    "negative numbers": [-3, -3],
    "mixed sign numbers": [-1, -1]
  }
}
```

注意，唯一被调用的函数是在没有任何测试样板的情况下被测试的函数。对象键用于分组和描述测试。数组值表示测试预期的 2 元组。期望中元素的顺序是`[actual, expected]`，差异将被报告为测试失败。你可以把这个表达式想象成纯粹使用数据的`expect(action).toEqual(expected)`的一个更紧凑的版本。任何人如果告诉你 Tead 实际上是 TEAD，代表**T**est**E**everything**A**s**D**ATA……比我给它起名字的时候想得还多。

## 可测试代码

除了文件和控制台部分，我使用函数式风格编写了 Tead 本身的大部分内容。但是我保留了大部分其他代码，以便将您的*测试作为数据*由测试文件导出，评估数据中描述的断言，并将结果转换成可以很好地打印到控制台的值。另外，它的运行速度比我见过的任何其他解决方案都快。在观看模式下，它闪烁得太快以至于让人难以相信测试再次运行，即使项目中有数百个测试。这几乎造成了测试在我保存我的更改之前运行的错觉。

如果到目前为止，您还不知道如何用 Tead 测试异步代码或其他副作用，那么您还不知道。这个工具是专门为测试纯函数而优化的，这些纯函数总是对相同的输入输出相同的结果，而不会影响该函数范围之外的任何东西。Tead 在与作为数据的*效果的运行时相匹配时工作得最好(如上所述)，这样业务逻辑返回的值在相等性方面是可比较的，这保持了代码的纯净和可测试性。如果你能接受这种限制作为一种特性，而不是一个错误，那么你的回报将是你能找到的最易测试的应用程序代码。*

坚持这一信条也许说起来容易做起来难。这对于编写解决方案将肮脏不纯的逻辑推到软件外部边缘的库作者(比如我自己)来说不起作用，但它将保持使用它们的应用程序代码的纯净。作者仍然需要编写支持这种编程风格的运行时和效果。Tead 并不是为了这个目的，因为您将需要 mock/stub/spies 来测试这些构建块所执行的脏工作。这是一个用例，现有的测试工具有很大的优势，应该继续使用。幸运的是，您只需要这样做一次，从那时起，就把这个实现当作一个您不关心的黑盒。您的应用程序逻辑保持简单，并专注于您的业务问题。

![](img/d8854880ee189e7bcd0469788e4c17fc.png)

# 可供选择的事物

也许现在我已经向你推销了作为数据的*测试的价值*，但不是我选择用来表示这些数据的实现。让我们考虑一些可用于编码测试的数据值的替代类型，以及为什么没有选择它们。

## 标记的模板文字🏷

多亏了像`[styled-components](https://www.styled-components.com)`、`[emotion](https://emotion.sh)`和`[hyperx](https://github.com/choojs/hyperx#virtual-dom-node-example)`这样的库，这些现在风靡一时。它们定义了一个解析和插值多行字符串值的函数。以这种方式编写测试的一个可能的 API 可能如下所示:

```
test`
sum can add
  zeros: ${sum(0, 0)} equals ${0}
  positive numbers: ${sum(1, 2)} equals ${3}
  negative numbers: ${sum(-1, -2)} equals ${-3}
  mixed sign numbers: ${sum(1, -2)} equals ${-1}
`
```

这实质上是为整个特定领域语言实现一个解释器。有新的语法需要学习，测试分组/标签的概念与测试断言的定义在一个应该被分解的复合值中纠缠在一起。最重要的是，我懒得实现和记录这个庞然大物的复杂性。

## 一路向下排列🐢

如果你确信测试数据应该被分解，那么也许你不同意我选择的方式。也许测试定义的圣杯在于将 Lisp 类比到它的逻辑结论中，并且除了编写断言之外，还使用列表/数组对测试进行分组。这可能是这样的:

```
[
  "sum can add",
  ["zeros", sum(0, 0), 0],
  ["positive numbers", sum(1, 2), 3],
  ["negative numbers", sum(-1, -2), -3],
  ["mixed sign numbers", sum(1, -2), -1]
]
```

我认为这种方法实际上太简单了。这也给 API 带来了模糊性，因为很难区分比较两个数组的测试和带有一个标签和两个子测试的测试分组之间的区别。经过足够多的层次后，嵌套数组将变得比嵌套对象更难阅读。考虑了另一个方案，但被拒绝。

## 面向对象的测试👩‍🔬

一个不同的选择是将对象用于测试分组和断言，如下所示:

```
{
  "sum can add": {
    zeros: {
      expect: sum(0, 0),
      toEqual: 0
    },
    "positive numbers": {
      expect: sum(1, 2),
      toEqual: 3
    },
    "negative numbers": {
      expect: sum(-1, -2),
      toEqual: -3
    },
    "mixed sign numbers": {
      expect: sum(1, -2),
      toEqual: -1
    }
  }
}
```

无论为测试断言对象选择什么属性，这些属性都将成为用户必须记录和理解的保留名称。在这里使用对象有点重量级，因为它基本上是一个二元组值，用于验证实际结果与预期结果是否匹配。我承认不走这条路主要是风格和偏好的问题，而不是意识形态的问题。

如果你喜欢这篇文章，请阅读第一部分，它涵盖了一些与测试简单性相关的背景材料。

[](https://hackernoon.com/test-code-not-sanity-1e4c0ee51d06) [## 测试代码，而不是健全

### 简单的测试变得简单

hackernoon.com](https://hackernoon.com/test-code-not-sanity-1e4c0ee51d06) 

也请阅读我以前的一篇关于 Hyperapp 的文章，以及它如何支持可以用 Tead 之类的工具测试的函数式编程。

[](/hyperapp/hyperapp-for-redux-refugees-2507c9dd1ddc) [## Redux 难民超级援助方案

### 我是如何学会不再担心并爱上这个功能的

medium.com](/hyperapp/hyperapp-for-redux-refugees-2507c9dd1ddc) 

欢迎给我留言或者发牢骚给 [@okwolf](http://twitter.com/okwolf) ！

# 🐺