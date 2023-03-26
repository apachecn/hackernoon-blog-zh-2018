# 用于算法竞争编程的 JavaScript

> 原文：<https://medium.com/hackernoon/javascript-for-algorithms-competitive-programming-45cf723cd16f>

去年，我花了一些时间参加竞争性编程竞赛，你必须在有限的时间内解决算法问题。不过，我仍然处于开始阶段，这已经很有趣了，并给了我一些好处。

我将把我的故事分成三部分:

*   动机
*   JavaScript 特性
*   对初学者的一些观察和建议

# 动机

我经常听到算法和数据结构的良好知识以及在竞争性编程中的成功在实际工作中并没有真正的帮助。

他们帮助很大，这就是为什么。

你在途中学习你的语言。我学到了很多关于 JavaScript 的东西，这些东西可能会在以后的生产中对我产生影响，因为这些问题相对较少，所以我几乎不会提前考虑它们(比如大量的数据)。

你解决有趣的问题。是的，一些自然的问题，比如做一些任务的最佳方式是什么，并且花费最少的时间。

你获得的知识是最基本的。如果你像我一样是一名前端人员，并且对你不得不经常扔掉你昨天才学到的东西感到厌倦，这可以为你节省港口——这是你多年来学习的东西。如果不是量子计算机我甚至会说几百年呵呵。

编程肌肉——半年前，我经常理解解决方案，但很难将它写入代码。现在，情况往往相反——一旦我理解了编码算法，它就像是在用母语说话，这肯定会提高我的工作效率。

这就引出了另一项重要的技能——学会如何把事情分成更小的部分，这样你就不会在任何特定的时刻把整个画面留在你的大脑里。这来自一些算法设计，如分治或动态编程，然后了解如何准备从算法的一个步骤到另一个步骤的数据，以便您保持低时间复杂度，但仍具有便于处理的数据格式。

# JavaScript 特性

我选择 JavaScript 是因为我比其他人更了解这种语言。

虽然与 C、Python 和 Java 相比，它并不是很受欢迎的竞争性编程语言，我可以猜到一些历史原因，但在我自己尝试之后，我认为我可能也会看到一些非历史原因(但我仍然热爱 JS)。

## 大数字

乍一看，Javascript 中允许的最大整数相当大:

```
Number.MAX_SAFE_INTEGER === 9007199254740991
```

直到你碰到一个问题，最大允许输入已经比那个大了([谷歌代码堵塞 2017](https://code.google.com/codejam/contest/3264486/dashboard#s=p2) 和[谷歌代码堵塞 2016](https://code.google.com/codejam/contest/6254486/dashboard#s=p3&a=2) 等等)。).

或者，有时问题甚至可能被隐藏起来——当您执行数十亿次乘法时，或者当您在生产中使用数字作为 id 时，在某一点上，所有大于`MAX_SAFE_INTEGER`的 id 将被 Javascript 视为相等:

```
Number.MAX_SAFE_INTEGER + 1 === Number.MAX_SAFE_INTEGER + 2 // true
```

有一些处理大数字的库，也有 Chrome 团队的[倡议，所以我们期待一个更光明的未来，但谁知道它会多快到来。现在我们必须处理它。](https://developers.google.com/web/updates/2018/05/bigint)

## 浮点数

下面的陈述是众所周知的，您可以在整个社区的文章中经常看到:

```
0.1 + 0.2 === 0.3 // false
```

这不是 JavaScript 特有的，对于这种现象有很多很好的解释。然而，我从未想过有一天我会想到这一点，直到我试图解决[谷歌代码堵塞 2017 年 1A 回合](https://code.google.com/codejam/contest/5304486/dashboard#s=p1)问题，我不得不计算一个人可以用每种成分的数量作为输入的最大数量，以及将一份数量作为常数所需的偏差。

考虑下面我用来求解的函数:

```
function getMinMaxWrong(total, serving) {
  var min = Math.ceil(total **/ (1.1** * serving));
  var max = Math.floor(total **/ (0.9** * serving));
  return min > max ? [0, 0] : [min, max];
}console.log(getMinMaxWrong(2376, 3)); // [720, **879**]
```

似乎是正确的，但是让我们用整数重写它:

```
function getMinMaxRight(total, serving) {
  var min = Math.ceil(total *** 10 / (11** * serving));
  var max = Math.floor(total *** 10 / (9** * serving));
  return min > max ? [0, 0] : [min, max];
}console.log(getMinMaxRight(2376, 3)); // [720, **880**]
```

数学上这两个公式是一样的。但是当您在 NodeJS 环境中执行它们时，您会得到两个不同的结果，第一个解决方案不会通过。

## 阵列方法纯度

我很快就习惯了函数式方法和不可变数组方法，比如`map`、`filter`、`slice`和`reduce`。即使简单的 for 循环更有表现力，我也使用它们，只是因为我觉得很酷😎。

后来，它让我相信所有其他数组方法都是浅拷贝，总是返回新数组而不是原始数组。

我想在执行了下面的代码后，我仍然使用未排序的`arr`:

```
arrSorted = arr.sort((a, b) => a — b)
```

但是我错了，因为`sort`和`reverse`改变了数组对象的位置。

确保你知道哪些方法会改变你的数组，哪些不会。

对此要小心的另一个原因是，当你使用递归时，你很容易在每个递归函数调用中使用浅层拷贝来增加空间复杂度。

## 最大调用堆栈

关于递归的另一点是最大调用堆栈大小。

我认为每个 JavaScript 开发人员一生中至少会看到一次由于无限`while`或`for`循环执行而产生的消息:

```
Maximum call stack size exceeded.
```

问题是——这不仅仅是无限循环。它也防止你用大量的迭代进行递归。

如果您曾经实现过深度优先搜索树遍历，那么您很可能是这样做的:

```
1  **procedure** DFS(*G*,*v*):
2    label *v* as discovered
3    **for all** edges from *v* to *w* **in** *G*.adjacentEdges(*v*) **do**
4      **if** vertex *w* is not labeled as discovered **then**
5        recursively call DFS(*G*,*w*)
```

我也解决了一个关于 Codeforces 的问题。同样适用于`C++`的解决方案在`Node`中并不适用，因为在一次测试中确实超过了最大调用堆栈大小。

通过使用[非递归深度优先搜索](https://stackoverflow.com/questions/5278580/non-recursive-depth-first-search-algorithm)有一个很好的方法，它有一些限制，但在特定问题中工作良好。同样的问题和走来走去适用于[洪水填充](https://en.wikipedia.org/wiki/Flood_fill)算法，我相信其他人也一样。

如果您仍然想使用递归解决方案，并且知道输入限制(这是经常发生的情况)，您可以通过执行摘自[Axel Rauschmayer 博士博客文章](http://2ality.com/2014/04/call-stack-size.html)的以下代码片段来计算 JavaScript 引擎的最大调用堆栈:

```
function computeMaxCallStackSize() {
  try {
    return 1 + computeMaxCallStackSize();
  } catch (e) {
    // Call stack overflow
    return 1;
  }
}
```

看看最坏情况下递归迭代的次数是否不超过这个数。

## 数组方法复杂性

Codeforces 上的同一个问题让我学到了关于 JavaScript 数组方法的另一件事:即使它们看起来做了同样的事情，它们也有不同的时间复杂度。

让我们在长度为 O(N)的数组上使用`push/pop` vs `shift/unshift`方法。

前两种方法复杂度为 O(1 ),因为它们

*   读取数组`length`的属性— O(1)
*   向数组对象再添加一个属性— O(1)
*   更新数组`length`的属性— O(1)

结束。

另外两个方法不仅要更新数组长度，还要更新所有 O(N)属性的值，因为它们的值向前或向后移动，这使得 O(N)复杂度几乎与 O(1)相同。

## 字符串是不可变的

我经常忘记的一件小事，也不是 JS 特有的，就是你可以用下面的方法更新一个数组:

```
arr = ['n', 'o']
arr[0] = 'y'
print(arr.join('')) // 'yo'
```

但是您不能像这样改变字符串:

```
str = 'no'
str[0] = 'y'
print(str)) // 'no'
```

因此，我经常使用技巧将字符串转换为数组，对其进行处理，然后再转换回字符串:

```
// str -> arr
arr = str.split('')// arr -> str
str = arr.join('')
```

从空间复杂性的角度来看，这并不是最佳的，但是在许多情况下，这使得代码更易读并且更容易实现。

## JS 还是不是 JS

竞赛引擎通常将 NodeJS 版本限制为某个旧版本，例如 Google Code Jam 支持 v4.8.2，这意味着[不能使用 classes、let/const 或 destructuring，这有时会在试图弄清楚你现在是在参加竞赛还是在从事 web 项目的过程中分裂你的思维。](https://node.green/)

大数字问题显然阻碍了 JavaScript 中某些问题的解决，最大调用栈问题也带来了一些不便。

我很乐意使用 JS 来解决限定问题，尤其是处理数组的方式，但是对于进一步的步骤，我可能会使用 Python。它没有一些限制，广泛用于机器学习，是仅有的两种允许参加著名的 [Google foobar 挑战赛](https://www.quora.com/What-is-Google%E2%80%99s-Foobar)的语言之一🙃。

# 如何准备资格赛

如果你想弄脏自己的手，为未来的比赛做准备，无论你想加强哪种语言，我建议你复习前几年的问题，积累一些经验，当你无法窥视解决方案时，这种自信是真正需要的。

## 小型与大型数据集

通常在 Code Jam 中，你会得到大小不同的数据集。虽然大数据集解决方案几乎总是考虑时间复杂度限制，但是小数据集通常可以用蛮力来解决。

这为您带来了以下好处:

*   您可以观察小数据集结果中的模式，并获得一些优化算法的见解，特别是当它是一个数学问题时
*   您可以在从强力解决方案获得的小数据集结果上验证您的大数据集解决方案
*   有时强力甚至对大型数据集也有效(只需注意输入限制和算法复杂性)

## 数学

有很多数学题考虑二进制表示、[被不同数整除](https://en.wikipedia.org/wiki/Divisibility_rule)、质数等。我猜这很难准备，所以我个人只是依靠我在学校参加数学竞赛的经验，在解决前几年的竞赛练习时积累一些知识。

## 概率论

令人惊讶的是，概率问题经常发生。

根据我的观察，通常足以知道，例如如果你有 p *1* ，p *2* ，..，p *n* 不同事件发生的概率，则概率为:

*   至少有一个事件发生是:p *1 +* p *2 +。*..+ p *n*
*   至少一个没有发生的事件是:(1—p*1)+*(1—p*2)+*……+(1—p*n)*
*   所有发生的事件是:p*1 **p*2 **……* p*n*
*   没有事件发生是:(1—p*1)**(1—p*2)**……*(1—p*n)*

例:[第 1C 轮 2017](https://codejam.withgoogle.com/codejam/contest/3274486/dashboard#s=p2&a=1)

## 位运算

整个编程世界都是基于信号的二进制性质，因此肯定存在需要了解位运算的问题。

示例:[2011 年资格赛](https://codejam.withgoogle.com/codejam/contest/975485/dashboard#s=a&a=2)

## 贪婪，散列，DP

几乎所有资格赛的算法任务都可以用[贪婪](https://en.wikipedia.org/wiki/Greedy_algorithm)方法、[散列](https://en.wikipedia.org/wiki/Hash_function)或[动态规划](https://en.wikipedia.org/wiki/Dynamic_programming)或它们的组合来解决。它只是需要一些练习来建立在哪里使用它们的直觉。

![](img/686080e2b91d83bc3b35b297eb461652.png)

# 下一步是什么

尽管 JavaScript 在竞争对手中并不流行，但有一些很棒的项目填补了这一空白:

[](https://github.com/trekhleb/javascript-algorithms) [## trekhleb/JavaScript-算法

### javascript-算法-用 JavaScript 实现的算法和数据结构，带有解释和链接，可以进一步…

github.com](https://github.com/trekhleb/javascript-algorithms) [](https://github.com/mgechev/javascript-algorithms) [## mgechev/JavaScript-算法

### 不同计算机科学算法的 javascript 实现。

github.com](https://github.com/mgechev/javascript-algorithms) 

这是我的知识库，里面有一些关于代码阻塞资格的解决方案:

[](https://github.com/romanovma/google-codejam) [## romanovma/google-codejam

### GitHub 是人们构建软件的地方。超过 2800 万人使用 GitHub 来发现、分享和贡献超过…

github.com](https://github.com/romanovma/google-codejam) 

祝你玩得开心，感谢你的阅读。

## 给我一些爱，❤️❤️❤️

如果你喜欢这篇文章，请做一些👏因此，我进一步写作的动力上升了。