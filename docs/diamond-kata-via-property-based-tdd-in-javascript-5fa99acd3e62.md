# 通过 JavaScript 中基于属性的 TDD 实现钻石形

> 原文：<https://medium.com/hackernoon/diamond-kata-via-property-based-tdd-in-javascript-5fa99acd3e62>

![](img/f3405ebdeba94b7e48bda8a8836cbc75.png)

在之前的文章中，我已经介绍了基于属性的测试背后的基本思想。在这里，我将使用这种技术来 TDD 钻石形。

该帖子受到了大量的启发(即公然抄袭)。所以一定要去和做同样练习的 Nat Pryce 和 Mark Seemann 打招呼[1][2](参考资料底部的链接)。幸运的是，我将使用 JavaScript 和 [JSVerify](https://github.com/jsverify/jsverify) 。这样我就可以把自己藏在“但是我使用了不同的堆栈”的借口后面。

此外，我会尽量减少代码片段。如果你对更多细节感兴趣，请随时查看[回购](https://github.com/3v0k4/diamond-kata)。

## 钻石形

以及 Seb Rose 描述的[，问题陈述如下:](http://claysnow.co.uk/recycling-tests-in-tdd/)

> 给定一个字母，打印一个以“A”开始的菱形，所提供的字母在最宽处。

几个例子是

```
Input: A
Output: A

Input: B
Output:   A
         B B
          A

Input: C
Output:   A
         B B
        C   C
         B B
          A
```

## 准备，开始，摇滚

在`init`提交中，我想检查接线。这就是为什么我使用一个总是返回`5`的生成器来检查`isFive`属性。

```
// index.test.jsconst jsc = require('jsverify')
const mocha = require('mocha')
const isFive = require('./index')describe('TODO', () => {
  jsc.property('TODO', jsc.constant(5), isFive)
}) // index.jsconst isFive = number => number === 5
module.exports = isFive
```

这当然是绿色的

```
$ mocha index.test.jsTODO
    ✓ TODO1 passing (12ms)✨  Done in 0.52s.
```

## 发电机

一切正常，因此我可以创建钻石形的发电机。特别是，我需要生成`A..Z`范围内的字符。

由于我不确定使用什么，我决定检查一下`jsc.asciichar`生成器返回什么

```
const debug = x => {
  console.log(x)
  return true
}

describe('diamond', () => {
  jsc.property('TODO', jsc.asciichar, debug)
})
```

注意`return true`。这样,“property”`debug`永远不会失败，我可以检查所有生成的 asciichar。因为默认情况下，JSVerify 通过从生成器生成 100 个输入来检查属性 100 次，我明白了

```
$ mocha index.test.jsdiamond
T
K
.
EB
<
// ... up to 100 asciichars ✓ TODO1 passing (16ms)✨  Done in 0.52s.
```

不完全正确，事实上，我只需要在`A..Z`范围内生成字符。不幸的是，JSVerify 没有提供任何满足该约束的现成生成器。因此，我创建了一个自定义的

```
const alphabet = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'.split('')
const char = jsc.suchthat(jsc.asciichar, c => alphabet.includes(c))

describe('diamond', () => {
  jsc.property('TODO', char, debug)
})
```

这次我们得到了正确的值

```
$ mocha index.test.jsdiamond
B
L
X
B
Q
V
X
B
J
S
C
P
I
// ... up to 100 chars in A..Z ✓ TODO1 passing (19ms)✨  Done in 0.52s.
```

请注意，我本可以将支票移到房产内部

```
const property = c => {
  if (!alphabet.includes(c)) return true
  // ... test the property
}describe('diamond', () => {
  jsc.property('TODO', jsc.asciichar, property)
})
```

但是我会犯一个错误。事实上，在这种情况下，JSVerify 会用随机的`jsc.asciichar`调用`property` 100 次，因此，只有一部分生成的输入会通过`if`。换句话说，我会失去测试覆盖率。

## 属性:钻石不是空的

开始练习的属性只是检查菱形对于任何字符的长度不为 0。

```
jsc.property('is not empty', char, c => make(c).length !== 0)
```

我用它来变绿

```
const make = char => 'whatever'
```

来自 REPL

```
make(c) // for any c
// => 'whatever'
```

## 属性:第一行包含一个

```
jsc.property(
  'first row contains A',
  char,
  c => firstRow(make(c)).trim() === 'A'
)
```

我用它来变绿

```
const make = char => '        A       ' // padding is asymmetric
```

来自 REPL

```
make(c) // for any c
// => '        A       '
```

## 属性:最后一行包含一个

```
jsc.property(
  'last row contains A',
  char,
  c => lastRow(make(c)).trim() === 'A'
)
```

已经是绿色的了。

## 属性:第一行具有对称轮廓

```
const firstRowHasSymmetricalContour = diamond => {
  const leadingElements = leading('A', firstRow(diamond)).length
  const trailingElements = trailing('A', firstRow(diamond)).length
  return leadingElements === trailingElements
}jsc.property(
  ‘first row has symmetrical contour’,
  char,
  c => firstRowHasSymmetricalContour(make(c))
)
```

我用它来变绿

```
const make = char => '       A       ' // padding is symmetric
```

来自 REPL

```
make(c) // for any c
// => '       A       '
```

## 属性:行具有对称的轮廓

不仅第一排有对称的轮廓。让我们修改属性，以便检查所有的行

```
const rowsHaveSymmetricalContour = diamond =>
  diamond
  .split('\n')
  .map(rowHasSymmetricalContour)
  .reduce((acc, x) => acc && x) // [].every would be better herejsc.property(
  'rows have symmetrical contour',
  char,
  c => rowsHaveSymmetricalContour(make(c))
)
```

已经是绿色的了。

## 属性:行包含正确的字母

```
const rowsContainsCorrectLetters = (char, diamond) => {
  const pre = alphabetUntilBefore(char)
  const post = pre.slice().reverse()
  const expected = pre.concat([char]).concat(post)
  const actual = diamond.split('\n').map(row => row.trim())
  return expected.join() === actual.join()
}jsc.property(
  ‘rows contains the correct letters’,
  char,
  c => rowsContainsCorrectLetters(c, make(c))
)
```

我用它来变绿

```
const make = char => {
  const pre = alphabetUntilBefore(char)
  const post = pre.slice().reverse()
  const chars = pre.concat([char]).concat(post)
  return chars.join('\n')
}
```

测试和生产代码之间的重复是一个不好的味道。但是我决定把它留在那里。

来自 REPL

```
make('C')
// => 'A\nB\nC\nB\nA'
```

## 属性:行的宽度与高度相同

```
const rowsAreAsWideAsHigh = diamond => {
  const height = rows(diamond).length
  return all(rows(diamond).map(hasLength(height)))
}jsc.property(
  'rows are as wide as high',
  char,
  c => rowsAreAsWideAsHigh(make(c))
)
```

我用它来变绿

```
const makeRow = width => char => {
  if (char === 'A') {
    const padding = ' '.repeat(width / 2)
    return `${padding}A${padding}`
  } else {
    return char.repeat(width)
  }
}const make = char => {
  const pre = alphabetUntilBefore(char)
  const post = pre.slice().reverse()
  const chars = pre.concat([char]).concat(post)
  return chars.map(makeRow(chars.length)).join('\n')
}
```

还有来自 REPL 的

```
make('C')
// => '  A  \nBBBBB\nCCCCC\nBBBBB\n  A  '
```

## 属性:除了顶部和底部的行有两个相同的字母

```
jsc.property(
  'rows except top and bottom have two identical letters',
  char,
  c => internalRowsHaveTwoIdenticalLetters(make(c))
)
```

我用它来变绿

```
const makeRow = width => char => {
  if (char === 'A') {
   const padding = ' '.repeat(width / 2)
   return `${padding}A${padding}`
  } else {
    const padding = ' '.repeat(width - 2)
    return `${char}${padding}${char}`
  }
}const make = char => {
  const pre = alphabetUntilBefore(char)
  const post = pre.slice().reverse()
  const chars = pre.concat([char]).concat(post)
  return chars.map(makeRow(chars.length)).join('\n')
}
```

还有来自 REPL 的

```
make('C')
// => '  A  \nB   B\nC   C\nB   B\n  A  '
```

## 属性:行具有正确的内部空间量

```
jsc.property(
  'rows have the correct amount of internal spaces',
  char,
  c => rowsHaveCorrectAmountOfInternalSpaces(make(c))
)
```

我用它来变绿

```
const internalPaddingFor = char => {
  const index = alphabet.indexOf(char)
  return Math.max((index * 2) - 1, 0)
}const makeRow = width => char => {
   if (char === 'A') {
     const padding = ' '.repeat(width / 2)
     return `${padding}A${padding}`
   } else {
     const internalSpaces = internalPaddingFor(char)
     const internalPadding = ' '.repeat(internalSpaces)
     const externalSpaces = width - 2 - internalSpaces
     const externalPadding = ' '.repeat(externalSpaces / 2)
     return `${externalPadding}${char}${internalPadding}${char}${externalPadding}`
   }
}const make = char => {
  const pre = alphabetUntilBefore(char)
  const post = pre.slice().reverse()
  const chars = pre.concat([char]).concat(post)
  return chars.map(makeRow(chars.length)).join('\n')
}
```

还有来自 REPL 的

```
make('C')
'  A  \n B B \nC   C\n B B \n  A  '
```

不幸的是，`rowsHaveCorrectAmountOfInternalSpaces`在测试中使用了以下内容

```
const index = alphabet.indexOf(char)
return Math.max((index * 2) - 1, 0)
```

我不喜欢这种重复。因此，我决定测试外部空间(而不是内部空间)。

## 属性:行具有正确数量的外部空格

```
jsc.property(
  'rows have the correct amount of external spaces',
  char,
  c => rowsHaveCorrectAmountOfExternalSpaces(make(c))
)
```

这次`rowsHaveCorrectAmountOfExternalSpaces`在内部使用了不同的计算方法:

```
const index = alphabet.indexOf(char)
return ((width - 1) / 2 - index) * 2
```

这意味着我已经删除了重复。此外，测试已经是绿色的，因为内部空间的生产代码也考虑到了外部空间。

## 和..我们完了

如上所示，上次 REPL 测试给了我们

```
make('C')
// => '  A  \n B B \nC   C\n B B \n  A  '
```

这意味着

```
 A  
 B B
C   C
 B B
  A 
```

这些都是我发现的特性:

*   `is not empty`
*   `first row contains A`
*   `last row contains A`
*   `rows have symmetrical contour`
*   `rows contain the correct letters`
*   `rows are as wide as high`
*   `rows except top and bottom have two identical letters`
*   `rows have the correct amount of external spaces`

## 结尾部分

我注意到的第一件事是基于属性的 TDD 让你思考起来有多困难。事实上，想出这个形的例子真的很容易。但是不变量就不一样了。

同时，知道你的问题空间有什么性质，也就意味着对它有了深入的了解。对于基于属性的 TDD，有必要在编写实际的产品代码之前发现它们。

不仅如此，我发现自己写了一个和以前冲突的属性。事实上，使它变成绿色的代码，也使一些现有的代码变成红色。钻石形是一个简单的练习，但这在我们日常工作中经常发生。

此外，我已经从一般属性开始建立了自己的方式，然后是专门化的(即`diamond is not empty`到`rows have the correct amount of external spaces`)。这与基于实例的 TDD 的情况相反:从特定到一般[3]。

不幸的是，我不能和基于例子的 TDD 相比，因为我没有那样尝试过形。如果你对此感兴趣，请查阅参考资料。

## 参考

1.  [带 FsCheck 的钻石形](http://blog.ploeh.dk/2015/01/10/diamond-kata-with-fscheck/)作者马克·西曼
2.  [钻石卡塔—仅基于属性测试的 TDD](http://natpryce.com/articles/000807.html)
3.  [钻石形——对渐进式发展的思考](http://natpryce.com/articles/000809.html)作者:纳特·普莱斯

## 更多指针

*   [Nat pry ce 在 SPA 2013](http://natpryce.com/articles/000802.html) 举办的基于酒店的 TDD

从我个人的电子邮件中获取最新内容。用你的想法回复。让我们互相学习。订阅我的 [PinkLetter](https://odone.io#newsletter) ！