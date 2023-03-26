# 基于属性的测试(少量 JavaScript)

> 原文：<https://medium.com/hackernoon/property-based-testing-4330e3e77381>

![](img/13eea14447461bbe1e9367da19062e0e.png)

如果你来自面向对象，并且曾经测试过任何一款软件，那么你很有可能使用过基于实例的测试。换句话说，用一些预定义的输入调用被测函数，并根据一些预定义的期望检查返回值。例如，用`1`和`2`调用函数`sum`，预期结果是`3`。

但是这种方法存在一些问题。首先，只能检查有限数量的例子。其次，决定验证什么样的输入输出是一个人的事。不幸的是，这反映了开发者的偏见。因此，很有可能一些关键的输入没有得到检查。

但是如果输入不是由开发人员选择的，怎么可能对函数的返回值有期望呢？

事实证明，无论输入是什么，从代码中导出不变量(属性)总是正确的是可能的。如果你把它和随机值生成器混合，你会得到基于属性的测试。

在`sum`的例子中，我们将使用一个数字生成器来产生输入。然后，我们将导出一些要测试的性质，如交换性(即`x + y == y + x`)和结合性(即`(x + y) + z == x + (y + z)`)。最后，基于属性的测试框架会生成一些集合`x`、`y`和`z`，并多次检查属性是否成立。

在出现故障时，我们将取回第一组`x`、`y`和`z`，对于它们，属性不成立。

`sum`是一个简单的例子。但是我们可以想象一些更复杂的东西，包括其他函数调用和不那么琐碎的输入。在这种情况下，仅仅获得失败的输入集是不够的。

这就是为什么大多数基于属性的测试框架都提供了一个叫做收缩的特性。换句话说，在失败后，框架试图通过删除或简化输入数据，将导致属性失败的输入缩小到最简单的形式。

## 给我看看代码

让我们将基于属性的测试应用到那些你在现实生活中永远不会看到的愚蠢例子中。我要用 JavaScript 和 [JSVerify](https://github.com/jsverify/jsverify) 。

假设我们有以下需要测试的代码:

```
const div = (dividend, divisor) => dividend / divisor
```

很简单，对吧？以下基于示例的测试是绿色的，所以我们可以到此为止了！

```
describe('div', () => {
  it('with natural numbers', () => {
    const expected = 2 const actual = div(6, 3) assert.strictEqual(actual, expected)
  }) it('with decimal numbers', () => {
    const expected = 2 const actual = div(6.3, 3.15) assert.strictEqual(actual, expected)
  })
})
```

不完全是。让我们看看基于属性的测试会发生什么。

首先，作为生成器，我们可以使用返回自然数的`jsc.nat`。

其次，作为属性，让我们只检查“右分配”的属性(即`(n1 + n2) / n3 == (n1 / n3) + (n2 / n3)`)。

```
describe('div', () => {
  const naturalNumber = jsc.nat jsc.property(
    'is right-distributive',
    naturalNumber, naturalNumber, naturalNumber,
    (n1, n2, n3) => div(n1 + n2, n3) === div(n1, n3) + div(n2, n3)
  )
})
```

嘣！

```
Error: Failed after 4 tests and 4 shrinks. rngState: 08b51479f83d6a20ec; Counterexample: 0; 0; 0;
```

有了`n1 = 0`、`n2 = 0`和`n3 = 0`就出事了。从节点 REPL

```
div(0 + 0, 0) === div(0, 0) + div(0, 0)
// falsediv(0 + 0, 0)
// NaNdiv(0, 0) + div(0, 0)
// NaNNaN === NaN
// false
```

JavaScript，对吧？如果我们再次运行基于属性的测试

```
Error: Failed after 4 tests and 4 shrinks. rngState: 08b51479f83d6a20ec; Counterexample: 2; 32; 3;
```

再次爆炸。但这一次是不同的失败。从节点 REPL

```
div(2 + 32, 3) === div(2, 3) + div(32, 3)
// falsediv(2 + 32, 3)
// 11.333333333333334div(2, 3) + div(32, 3)
// 11.333333333333332
```

JavaScript 和浮点运算，对吧？

现在，如果基于属性的测试在 2 次运行中发现 2 个错误

```
const div = (dividend, divisor) => dividend / divisor
```

想象一下它还能从更复杂的代码中发现什么。

## 结尾部分

基于属性的测试消除了偏见。这样就可以发现开发人员没有想到要测试的错误。

此外，它迫使从另一个角度考虑代码，这是一件好事。

同时，属性比例子更抽象一些(比如 sum vs `sum(1, 2) == 3`中的交换性)。这就是为什么混合两种风格是最好的主意。

还想吃吗？看看我在[后续文章](/@riccardoodone/diamond-kata-via-property-based-tdd-in-javascript-5fa99acd3e62)中使用了基于属性的 TDD。

## 两颗北极指极星

*   约翰·休斯的《不要写测试》
*   [视频] [编写 1000 个测试的懒惰程序员指南:基于属性的测试介绍](https://skillsmatter.com/skillscasts/6432-the-lazy-programmers-guide-to-writing-1000s-of-tests-an-introduction-to-property-based-testing#video)作者 Scott Wlaschin
*   [论文] [理论的实践:在“存在”测试中增加“为所有人”的陈述](http://shareandenjoy.saff.net/tdd-specifications.pdf) 

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！