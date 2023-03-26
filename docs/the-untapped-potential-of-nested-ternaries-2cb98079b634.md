# “嵌套”三角形未开发的潜力

> 原文：<https://medium.com/hackernoon/the-untapped-potential-of-nested-ternaries-2cb98079b634>

## 我们一直在错误地使用这些东西！

![](img/6da6a9915a99562c13e024d5072744dd.png)

Photo by Talles Alves ([https://unsplash.com/photos/HNiOq7eg8ck](https://unsplash.com/photos/HNiOq7eg8ck))

很快，我将向您展示 JavaScript 中尚未开发的“嵌套”术语的潜力。(在嵌套的周围使用引号*也将被解释)。不过先说一下*模式匹配*！*

模式匹配出现在许多[编程](https://hackernoon.com/tagged/programming)语言中，包括 Scala 和 Haskell。它用于识别模式，并为我们提供了一个清晰、简洁的方法来确定我们的代码将如何分支。

这里有一个 Scala 中模式匹配的例子，来自 Bruce Tate 的优秀[七周七种语言](https://pragprog.com/book/btlang/seven-languages-in-seven-weeks):

```
def doChore(chore: String): String = chore match {
 case “clean dishes” => “scrub, dry”
 case “cook dinner” => “chop, sizzle”
 case _ => “whine, complain”
}doChore(“clean dishes”) // -> "scrub, dry"
doChore(“mow lawn”) // -> "whine, complain"
```

遵循逻辑很简单。我们只需扫描列表中的第一个匹配项，并在那里找到要返回的值。如果我们到达列表的末尾，还没有遇到我们要找的案例，那么在末尾我们会找到我们的返回值，一个我们没有考虑的所有案例的集合。

下面是 Haskell 中计算阶乘的模式匹配:

```
factorial :: Integer -> Integer
factorial 0 = 1
factorial x = x * factorial (x - 1)-- factorial 0 -> 1
-- factorial 1 -> 1
-- factorial 17 -> 355687428096000
```

有趣的是，Prolog 中的斐波那契数列:

```
fib(0, 1).
fib(B, C) :- fib(A, B), C is A + B.% fib(0, X) -> X = 1.
% fib(1, X) -> X = 1, X = 2.
% fib(144, X) -> X = 233.
```

我们不能在 [JavaScript](https://hackernoon.com/tagged/javascript) 中使用模式匹配作为控制结构，但是如果你仔细看，`case "clean dishes"`(来自我们的 Scala 示例)开始看起来有点像布尔测试，我们确实有。继续眯着眼，这段 JavaScript 代码看起来有点像模式匹配:

```
let **result**;**if** (operator === ‘+’) {
  result = left + right;
} **else if** (operator === ‘*’) {
  result = left * right;
} **else if** (operator === ‘-’) {
  result = left - right;
} **else** {
  result = left / right;
}
```

但是我们可以做得更好！这段代码的一些问题:

*   尽管我们知道`const`是首选，但我们还是使用了`let`。
*   我们正在改变一个在我们的块范围之外声明的变量的值。
*   我们通过多次键入我们的任务`result =`来重复我们自己。

让我们看看我们能用嵌套的三元组做些什么，好吗？

```
const **result** =
  operator === ‘+’ ? left + right
  : operator === ‘*’ ? left * right
  : operator === ‘-’ ? left — right
  : left / right;
```

嘿，那还不算太糟！让我们回顾一下我们所说的模式匹配:

> 遵循逻辑很简单。我们只需扫描列表中的第一个匹配项，并在那里找到要返回的值。如果我们到达列表的末尾，还没有遇到我们要找的案例，那么在末尾我们会找到我们的返回值，一个我们没有考虑的所有案例的集合。

看起来我们已经有了一个工具，它的功能很像 JavaScript 中已经存在的模式匹配！

注意我们为什么能够做到这一点是很重要的——我们格式化了我们的“嵌套”三元组，使得每一行都有一个条件和一个结果，并在结尾包含所有内容。因此，我们可以读写它，就好像它**根本没有嵌套**。因此引用。😉没有必要担心记住每个嵌套的上下文。一旦你排除了某条线适用于你正在考虑的情况，你可以放心地忘记它。

在 JavaScript 中避免嵌套术语通常被认为是最佳实践，但这是因为我们通常编写如下所示的代码:

```
**var** thing **=** foo ? bar : baz **===** qux ? quxx : foobar;
```

如果你认为这看起来令人困惑，那是因为它是！ [ESLint](https://eslint.org/docs/rules/no-nested-ternary) 建议解决方案是编写这样的代码:

```
**var** thing;**if** (foo) {
  thing **=** bar;
} **else** **if** (baz **===** qux) {
  thing **=** quxx;
} **else** {
  thing **=** foobar;
}
```

但希望现在你意识到有更好的选择:

```
**var** thing =
  foo ? bar
  : baz === qux ? quxx
  : foobar;
```

可以在推特上关注我 [@okaybenji](https://twitter.com/okaybenji)