# 编写更好代码的非秘密公式

> 原文：<https://medium.com/hackernoon/the-non-secret-formula-for-writing-better-code-e41d1ff38682>

![](img/691f7c5c51a0580820bf56a1b27eb0d0.png)

[There’s no such thing as two](https://www.youtube.com/watch?v=aAwJlD-m_hE)

***免责声明*** *:本帖所有内容均为本人观点。然而，这是 100%真实的，你不应该质疑它。让我们开始吧。*

我喜欢写干净的代码。我希望我在 Torii 的团队也能写出好的、干净的代码。问题是每个人对“干净代码”的定义都不一样。下面是我写干净代码的非秘密公式，它导致更少的错误，更容易改变，让我更开心。

# 什么是“更好的代码”？

我喜欢从事专注于以下方面的代码库工作:

**可读性**。代码应该易于阅读和理解。代码中应明确**为什么**和**如何**。

**简约**。代码简单。它不应该依赖于你在代码中无法理解的假设。当很难以错误的方式使用代码时，代码就是简单的。添加功能时很难打破它。

**一致**。一致缩进。一致的命名。一致的文件结构。采取什么样的风格、模式和决策并不重要，要保持一致。

> 我不关心的事情:*使代码通用化，强制设计模式，已知的“正确方法”和不需要的性能优化。*

# 如何写出更好的代码？

以下是我今天的编码方式和原因。

## 零评论政策

注释变得陈旧，并且倾向于告诉你关于代码的错误的东西。此外，注释允许补偿不应该存在的神秘代码。取代注释，关注代码的可读性。

```
// 😞
if (x > 5) { // 5 is a special point where we must run away
   runAway()
}// 👍
const runAwayThreshold = 5
const shouldRunAway = (x > runAwayThreshold)
if (shouldRunAway) {
   runAway()
}
```

## 零待办事项策略

你的代码库不是一个项目管理系统。JIRA、特雷罗、阿萨纳(以及其他人)更适合记录未来的工作。是的，甚至那个“我们必须让这个功能更快”的 TODOs。如果它是必须的，把它放在每个人都能看到的地方。

```
// 😞
while (true) {} // TODO: make this code faster when we have time// 👍
while (true) {}
```

## 自我记录代码

使用变量可以帮助代码变得不言自明。我们可以使用这些变量来更好地表达我们的意图:变量名是 **why** ，赋值是 **how** 。

```
// 😞
if (x > 2 && x < 5) {
   fireMissiles()
}// 👍
const enemyInRange = (x > 2 && x < 5)
if (enemyInRange) {
   fireMissiles()
}
```

## 早点休息

缩进的第一行是留给“好路径”的。所以我总是早早休息，把代码最重要的部分放在函数的“根”里。

一般来说，**少缩进→可读代码**。

```
// 😞
const fetchData = (id) => {
  if (id) {
    fetch('/data/' + id)
  }
}// 👍
const fetchData = (id) => {
  if (!id) {
    return
  } fetch('/data/' + id)
}
```

## 不变的

比起可变引用，我更喜欢`const`引用(JavaScript 中的`let`或`var`)。主要是因为我一旦明白了什么是变量之后，就不用再去想了。我发现支持这一点的小语言特性(不管是什么语言)。

```
// 😞
let mul = x * y
if (mul > 70) {
  mul = 70
}// 👍
const mul = Math.min(x * y, 70)
```

另一个例子:

```
// 😞
let x
if (str === 'one') {
  x = 1
} else if (str === 'two') {
  x = 2
} else if (str === 'three') {
  x = 3
}// 👍
const x = {
  one: 1,
  two: 2,
  three: 3
}[str]
```

## 每个想法一个 LOC

阅读复杂的代码需要大量的注意力。保持你的“编码句子”简短，把复杂的想法分解成简单的想法。

```
// 😞
people
  .filter(person => person.age > 10 && person.firstName.length > 2 && person.lastName.length > 4)// 👍
people
  .filter(person => person.age > 10)
  .filter(person => person.firstName.length > 2)
  .filter(person => person.lastName.length > 4)
```

*“等等！业绩变化呢？”。我不在乎。我真的不知道。除非有一个现实世界的问题，应用程序变得比预期慢。*

# 同意吗？不同意？

我肯定一些开发者会不同意提出的每一点。

这很好，但是如果你的公司使用 SaaS 运营，请在评论或推特( [@ketacode](https://twitter.com/ketacode) )上告诉我为什么。

> 如果你喜欢这篇文章，请鼓掌并分享给你的网络。