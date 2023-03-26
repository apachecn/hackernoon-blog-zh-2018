# 10 分钟学会函数 Python

> 原文：<https://medium.com/hackernoon/learn-functional-python-in-10-minutes-to-2d1651dece6f>

![](img/dee1275b71e6b0289d4623366bacb54f.png)

Photo by [Chris Ried](https://unsplash.com/photos/ieic5Tq8YMk?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/python-code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在本文中，您将了解什么是函数范式，以及如何在 Python 中使用函数编程。你还会学到列表理解和其他形式的理解。

# **功能范式**

在命令式范式中，你通过给计算机一系列任务来完成事情，然后它执行这些任务。在执行它们时，它可以改变状态。例如，假设您最初将 A 设置为 5，然后稍后您更改了 A 的值。您拥有变量，因为变量内部的值是变化的。

在函数范式中，你不告诉计算机做什么，而是告诉它东西是什么。一个数的最大公约数是多少，1 到 n 的乘积是多少等等。

正因为如此，变量不能变化。一旦你设置了一个变量，它将永远保持这个状态(注意，在纯函数式语言中，它们不被称为变量)。正因为如此，函数在函数范式中没有 T2 副作用。副作用是函数改变了外部的一些东西。让我们看一个典型 Python 代码的例子:

这段代码的输出是 5。在函数范式中，改变变量是一大禁忌，让函数影响其范围之外的事情也是一大禁忌。函数唯一能做的就是计算一些东西并返回结果。

现在你可能会想:“没有变量，没有副作用？这有什么好？”。好问题，奇怪的陌生人读到这里。

如果用相同的参数调用一个函数两次，它肯定会返回相同的结果。如果你已经了解了[数学函数](/brandons-computer-science-notes/a-primer-on-functions-9a51c1e9de80)，你就会知道欣赏这种好处。这被称为*参照透明*。因为函数没有副作用，如果你正在构建一个计算事物的程序，你可以加速程序。如果程序知道 func(2)等于 3，我们可以将它存储在一个表中。这可以防止程序在我们已经知道答案的情况下重复运行同一个函数。

通常，在函数式编程中，我们不使用循环。我们使用递归。递归是一个数学概念，通常，它意味着“馈入自身”。对于递归函数，该函数反复调用自己作为子函数。下面是 Python 中递归函数的一个很好的例子:

有些编程语言也**懒**。这意味着他们不到最后一秒不会计算或做任何事情。如果你写一些代码来执行 2 + 2，一个函数式程序只会在你实际需要使用结果的时候计算。我们将很快在 Python 中探索懒惰。

## **地图**

要理解 map，我们先来看看什么是 iterables。iterable 是任何你可以迭代的东西。通常这些是列表或数组，但是 Python 有许多不同类型的可迭代对象。你甚至可以通过实现神奇的方法来创建你自己的可迭代对象。一个神奇的方法就像一个 API，帮助你的对象变得更加 Pythonic 化。您需要实现两个神奇的方法来使一个对象成为可迭代的:

第一个神奇的方法，“__iter__”或 dunder iter(双下划线 iter)返回迭代对象，这通常在循环开始时使用。Dunder next 返回下一个对象是什么。

让我们进入一个简短的终端会话，看看这个:

这会打印出来

```
3
4
5
6
7
8
```

在 Python 中，迭代器是一个只有 __iter__ magic 方法的对象。这意味着您可以访问对象中的位置，但不能循环访问对象。有些对象会有魔术方法 __next__ 而没有 __iter__ 魔术方法，比如 sets(本文后面会谈到)。对于本文，我们将假设我们接触的所有东西都是可迭代的对象。

现在我们知道了什么是 iterable 对象，让我们回到 map 函数。map 函数允许我们将函数应用到 iterable 中的每一项。通常我们希望对列表中的每一项都应用一个函数，但是我们知道这对于大多数的可重复项都是可能的。Map 有两个输入，要应用的函数和 iterable 对象。

假设我们有一个数字列表，如下所示:

```
[1, 2, 3, 4, 5]
```

我们想对每个数字求平方，我们可以写这样的代码:

Python 中的函数是懒惰的。如果我们不包含“list()”，函数将存储 iterable 的定义，而不是列表本身。我们需要明确地告诉 Python“把它变成一个列表”来使用它。

在 Python 中突然从非懒求值变成懒求值有点奇怪。如果你更多地用功能性思维而不是命令性思维来思考，你最终会习惯的。

现在写一个像“square(num)”这样的普通函数很好但是看起来不对。我们必须定义一个完整的函数，仅仅是为了在地图中使用一次？嗯，我们可以使用 lambda(匿名)函数在 map 中定义一个函数。

# **λ表达式**

lambda 表达式是一个单行函数。例如，这个λ表达式对给定的一个数进行平方:

现在让我们运行这个:

```
>>> square(3)9
```

我听到了。“布兰登，论据在哪里？这是什么鬼东西？那看起来一点也不像函数？”

嗯，这有点令人困惑，但可以解释。所以我们给变量“square”赋值。这部分:

告诉 Python 这是一个 lambda 函数，输入名为 x。冒号后的任何内容都是您对输入所做的操作，它会自动返回结果。

为了将我们的 square 程序简化成一行，我们可以:

所以在 lambda 表达式中，所有的参数都在左边，你想用它们做的东西在右边。它变得有点乱，没人能否认。事实是，编写只有其他函数式程序员才能读懂的代码有某种乐趣。此外，把一个函数变成一行程序也非常酷。

# **减少**

Reduce 是一个把一个 iterable 变成一个东西的函数。通常，你对一个列表进行计算，**将它减少到一个数字。Reduce 看起来像这样:**

我们可以(并且经常会)使用 lambda 表达式作为函数。

列表的乘积是每个数字相乘的结果。为此，您需要编写:

但是使用 reduce，您可以只写:

才能得到同样的产品。代码更短，有了函数式编程的知识，代码更整洁。

# **滤镜**

filter 函数获取一个 iterable，并过滤掉该 iterable 中您不想要的所有内容。

通常过滤器接受一个函数和一个列表。它将该函数应用于列表中的每一项，如果该函数返回 True，则不执行任何操作。如果返回 False，则从列表中删除该项。

语法如下所示:

让我们看一个小例子，如果没有过滤器，我们将写:

有了过滤器，这就变成:

# **高阶函数**

高阶函数可以将函数作为参数和返回函数。一个非常简单的例子是这样的:

或者第二个定义“返回函数”的一个更简单的例子是:

你知道我之前说过纯函数式编程语言没有变量吗？嗯，高阶函数使这变得更容易。如果您所做的只是通过一个长长的函数隧道传递数据，那么您不需要在任何地方存储变量。

Python 中的所有函数都是一级类对象。第一类对象被定义为具有这些特征中的一个或多个:

*   在运行时创建
*   赋给数据结构中的一个变量或元素
*   作为参数传递给函数
*   作为函数的结果返回

所以 Python 中的所有函数都是一级的，可以作为高阶函数使用。

# **部分应用**

部分应用程序(也称为闭包)有点奇怪，但是非常酷。您可以调用一个函数，而无需提供它需要的所有参数。让我们看一个例子。我们想创建一个函数，它有两个参数，一个底数和一个指数，并返回底数的指数幂，如下所示:

现在，我们希望有一个专用的平方函数，使用幂函数计算一个数的平方:

这是可行的，但是如果我们想要一个立方函数呢？还是 4 次方的函数？我们能永远写下去吗？你可以。但是程序员很懒。如果你一遍又一遍地重复同样的事情，这是一个迹象，表明有一种更快的方法可以加快速度，并且可以让你不再重复。我们可以在这里使用部分应用程序。让我们看一个使用部分应用程序的 square 函数的示例:

是不是很酷！我们可以调用需要两个参数的函数，只使用一个参数，告诉 Python 第二个参数是什么。

我们也可以使用一个循环来生成一个幂函数，从立方一直到 1000 次方。

# **函数式编程不是 Pythonic 式的**

你可能已经注意到了，但是我们在函数式编程中想做的很多事情都是围绕着列表的。除了 reduce 函数& partial application，你见过的所有函数都生成列表。Guido(Python 的发明者)不喜欢 Python 中的函数性东西，因为 Python 已经有了自己生成列表的方法。

如果您将“import this”写入 Python 空闲会话，您将得到:

```
>>> import thisThe Zen of Python, by Tim PetersBeautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren’t special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one — and preferably only one — obvious way to do it.
Although that way may not be obvious at first unless you’re Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it’s a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea — let’s do more of those!
```

这就是 Python 的禅。这是一首关于蟒蛇意味着什么的诗。我们在这里要联系的部分是:

> *应该有一种——最好只有一种——显而易见的方法来做这件事。*

在 Python 中，map & filter 可以做与列表理解(接下来讨论)相同的事情。这打破了 Python 禅宗的一条规则，因此函数式编程的这些部分不被视为“Python 化的”。

另一个话题是 Lambda。在 Python 中，lambda 函数是一个普通函数。Lambda 是句法糖。这两者是等价的:

常规函数可以做 lambda 函数能做的一切，但反过来就不行了。lambda 函数不能做常规函数能做的所有事情。

这是一个关于为什么函数式编程不能很好地融入整个 Python 生态系统的简短争论。你可能已经注意到我之前提到了列表理解，我们现在将讨论它们。

# **列举理解**

之前，我提到过你可以用地图或过滤器做的任何事情，你都可以用列表理解来做。这是我们将了解他们的部分。

列表理解是用 Python 生成列表的一种方式。语法是:

举例来说，让我们对列表中的每个数字求平方:

好的，我们可以看看如何将一个函数应用于列表中的每一项。我们如何应用过滤器呢？好吧，看看前面的这段代码:

我们可以把它转换成如下的列表理解:

列表理解支持这样的 if 语句。你不再需要应用一百万个函数来得到你想要的东西。事实上，如果你想做一个清单，使用清单理解会看起来更简洁和容易。

如果我们想对列表中每个小于 0 的数字求平方呢？嗯，使用 lambda、map 和 filter，您将编写:

这看起来很长，也有点复杂。对于列表理解，它只是:

列表理解只对列表有用。映射和过滤可以在任何 iterable 上工作，那么这是怎么回事呢？嗯，你可以对你遇到的任何可重复的对象使用任何理解。

# 其他理解

你可以创造出对任何事物的理解

任何可重复项都可以通过理解产生。从 Python 2.7 开始，你甚至可以生成一个字典(hashmap)。

如果是可迭代的，就可以生成。让我们看看集合的最后一个例子。如果你不知道什么是 set，可以看看我写的这篇[其他文章](/brandons-computer-science-notes/a-primer-on-set-theory-746cd0b13d13)。TLDR 是:

*   集合是元素的列表，没有一个元素在列表中重复两次
*   集合中的顺序无关紧要。

你可能会注意到集合和字典有相同的花括号。Python 真的很聪明。它会根据你是否为字典提供了额外的价值来判断你是在写字典理解还是集合理解。如果你想了解更多关于理解的知识，请查看[这个](http://treyhunner.com/2015/12/python-list-comprehensions-now-in-color/)视觉指南。如果你想了解更多关于 comprehensions &生成器的知识，请查看这篇[文章](https://medium.freecodecamp.org/python-list-comprehensions-vs-generator-expressions-cef70ccb49db)。

# 结论

函数式编程很美很纯粹。功能代码可以是干净的，但也可能是杂乱的。一些铁杆 Python 程序员不喜欢 Python 中的函数范式。你应该用你想用的东西，用最好的工具。

## 你喜欢这篇文章吗？在社交媒体上与我联系，讨论所有与计算机科学相关的事情😁

[推特](https://twitter.com/brandon_skerrit)|[insta gram](http://instagram.com/brandon.codes)|[LinkedIn](https://www.linkedin.com/in/brandonls/)

别忘了点击那个👏拍手声👏按钮，以示感谢！

我写这篇文章没有得到报酬。如果你想支持我，请在下面给我买杯咖啡什么的😁

[](https://www.paypal.me/brandonskerritt) [## 用贝宝支付布兰登·斯凯里特。我

### 去 paypal.me/BrandonSkerritt 输入金额。既然是 PayPal，那就简单又安全。没有 PayPal…

www.paypal.me](https://www.paypal.me/brandonskerritt) [](https://monzo.me/brandonskerritt) [## 通过 Monzo.me 即时支付 Brandon

### 点击链接向布兰登付款。你不需要创建一个账户，而且完全免费。

monzo.me](https://monzo.me/brandonskerritt)