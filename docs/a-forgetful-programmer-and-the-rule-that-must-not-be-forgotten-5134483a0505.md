# 健忘的程序员和不可忘记的规则

> 原文：<https://medium.com/hackernoon/a-forgetful-programmer-and-the-rule-that-must-not-be-forgotten-5134483a0505>

> 如果我有一个小时来拯救世界，我会花 55 分钟来定义这个问题

每次去我座位的路上，我都会路过一堵写着这句话的墙。每次我都会想起这句话

![](img/09c13c9b6203e977eeb8dda7b8a9f0b7.png)

By Karen Roe from Bury St Edmunds, Suffolk, UK [CC BY 2.0 ([https://creativecommons.org/licenses/by/2.0](https://creativecommons.org/licenses/by/2.0))], via Wikimedia Commons

在我忘记之前，我将向您展示我是如何解决一个致命的编程问题的，以说明如果我记得记住这句话，生活会变得多么美好。除非我是一头大象。唉！

附注:我并不想每天吃很多食物。不过，我承认，这并不坏。谁不想让烧烤国家破产呢！我想要大象的记忆

# 致命的编程问题

编写一个程序，将一个**数字作为输入**，并将**字数作为输出**。例如，123414 应该输出 10 万 23414

似乎很容易。下面是我如何着手解决它

# 基本原则思维

基本原则是任何事情的基本真理。也就是说我们不能再进一步分解了。仅仅用常识思考就能让我们很快到达这个阶段

在我看来，基本的事实是，我们从左到右阅读数字，从十位数到十位数和一位数。例外情况是 11-19，与其他两位数不同，11 被称为 11，而不是 10-1

在现实生活中，我把数字转换成文字就像呼吸一样容易。每个人都这样。我很满意这个逻辑不能被进一步分解。我继续写代码

Elon Musk figured out how to make batteries cheaper by using first principles thinking

# 不要重复你自己

我第一次尝试的时候有很多重复的代码。所以，我做得更好

Whoever said ‘Dont repeat yourself’ gets programming

# 我还能做什么？

我可以参数化断点，而不是硬编码。如果需要，用户可以提供数百万或数十亿的断点

> 我现在想起来了。他们总是说要避免硬编码

完全忘记了测试方面。我一向如此。参数化函数将需要无数的测试案例来测试它是否正确地输出百万、十亿、十亿和十万卢比！

> 他们还说先写测试用例。这样我就不会忘记，我想。哼哼！

如果我把功能分解成更小的组件，它们就不需要这么多测试用例了

> 啊！单一责任原则

如果我有更小的通用组件函数，那么我也可以在其他地方使用它们

> 模块化和可重用性问好

他们肯定还说了无数其他的事情。我甚至记不住所有这些编程实践

> 我健忘的记忆记得打招呼！也

我的代码会一直缺少一些技巧吗？我能写出别人能写出的最好的代码吗？我怎么知道？

# 如果我有一个小时来拯救世界，我会花 55 分钟来定义这个问题

看着我是怎么解决问题的，啊，我看到我已经在说解决问题了！我花了大约半个心跳的时间来思考这个问题，它就像呼吸一样简单记得吗？我没有花时间去定义问题，而是花了大部分时间去解决它

# 众所周知的 55 分钟

爱因斯坦说了 55 分钟的事情，他们说他是个天才。所以，让我花点时间来定义这个问题，看看会发生什么

## 问题定义:第一条:行动！

```
Numbers. Numbers are made up of crores, lakhs, thousands and so on. Or billions, millions, thousands and so on. Given a number, say how many crores and lakhs and so on are present in the given number
```

好吧。这些百万和十亿被称为数字系统单位。每个单位的“多少”？这些又是数字。盗梦空间！但是这些数字最多是三位数，对吗？比如 1234567。那是 100 万，234，000 和 567

## 问题定义:第二步:行动！

```
Given one number made up of number system units, split it into number system units. Then spell out the 3-digit quantity of each number system unit, spell out the name of the corresponding number system unit. Put this all together to make number-in-words and return it
```

读起来更好。我现在可以逐点写了

## 问题定义:第三招:行动！

```
Function to spell out any-digit number
-----
Split the number into number system units and their quantities
If quantity has more than 3-digits
 Call this same function to spell it out
Else
 Call the function to spell out a 3-digit number
For each number system unit, spell out its name
Put all these words together in the right orderFunction to spell out a 3-digit number
-----
Given a 3-digit number
Split it into hundreds and remainder
Spell out the quantity of hundreds
Spell out the remainder
Put those two words together in the right order
```

哈！现在就像我在用文字写代码。我把问题定义分解成简单明了的部分。到目前为止，这花了我大约 5 分钟

它帮助我看到，当数字大于 999 亿或 9990 亿时，数量可以超过 3 位数。我处理了。我还可以看到，我还没有考虑如何在任何地方处理零

再进行几次迭代，我就可以非常非常清楚地定义问题，处理特殊的场景，这将非常接近于用最简单的方式来描述这个问题

还记得编程最佳实践吗？在我看来，这个问题定义的特征将会是一个很好的编程最佳实践列表

我不知道这是否是最好的结果。但是，嘿，这就是为什么我们有同行评议

好吧爱因斯坦。你真是个天才。除非你在那 55 分钟里抽出 1 分钟来说一些关于如何记忆的事情。唉！