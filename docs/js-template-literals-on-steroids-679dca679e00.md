# 类固醇上的 JS 模板文字

> 原文：<https://medium.com/hackernoon/js-template-literals-on-steroids-679dca679e00>

模板文字就是我们过去称之为“模板字符串”的东西，用于嵌入表达式的字符串插值。这些特性在 python 和 ruby 等其他语言中已经存在很长时间了，最初是在 ECMAScript 6 中引入 javascript 的。使用模板文字，我们可以把难看的字符串串联在一起，当我们有嵌入的表达式和换行符时使用。

![](img/c2ecc8799e9967e49273ca344f8e9ca1.png)

# 基本用法

最常见的用法是构建一个由字符串和从表达式计算出的值组成的字符串。要在一个字符串中嵌入一个表达式，我们需要做的就是用像`${expression}`这样的花括号添加表达式。

例如，要生成带有文本的本地化日期，我们可以编写以下代码:

```
`Today is ${new Intl.DateTimeFormat('en-US').format(new Date())}`
```

# 高级用法

这个特性不仅仅是字符串和表达式的组合。我们可以通过实现一个**标签函数**来构建最终结果，给定表达式的字符串和值，该标签函数构成最终结果。

Tag functions 是另一个允许将构建字符串的逻辑提取到函数中的工具。标签功能可用于 HTML 转义、本地化、标记、翻译等。我们通过在模板文字表达式之前调用标签函数来使用它，例如`fn`Today is ${new Date()}``其中 fn 是标签函数。

## 实施

标签函数界面看起来像:

```
function fn(strings, ...values): string
```

其中`strings`是由表达式分隔的字符串数组，`values`是表达式值。例如`fn`Today is ${today} and Tomorrow is ${tomorrow}``执行 fn 作为`fn(['Today is ', ' and Tomorrow is '], '6/23/2018', '6/24/2018')`。

标签函数的实现应该总是返回一个字符串，例如考虑到`strings`和`values.`，在我们可以实现`fmtDate`标签函数之前，得到一个本地化的而不是本地化的:

```
function fmtDate(strings, date) {
  const str0 = strings[0];
  const str1 = strings[1];
  const strDate = new Intl.DateTimeFormat('en-US').format(date);
  return `${str0}${strDate}${str1}`;
}fmtDate`Today is ${new Date()}`; // “Today is 6/23/2018”
```

更进一步，将所有日期本地化:

```
function fmtVar(v) {
  return (v instanceof Date) ?
    new Intl.DateTimeFormat('en-US').format(v) : v;
}function fmtString(strings, ...values) {
  return values.reduce((acc, v, idx) => 
    acc + fmtVar(v) + strings[idx + 1], strings[0]);
}fmtString`Today is ${today} and tomorrow is ${tomorrow}`; 
// “Today is 6/23/2018 and tomorrow is 6/24/2018"
```

注意，在上面的例子中，`fmtStrings`是一个通用的格式化函数，它可以用来格式化任何使用`fmtVar`的东西，后者根据类型格式化一个参数。我们可以使用这种组合来格式化我们想要的任何变量。

## 现有原始数据实现

标签函数的常见用法是从模板字符串中获取原始数据。例如`first ${'\n'} second`实际结果为“第一\n 秒”而不换行。我们可以使用`String.raw`静态方法来做这件事。

```
String.raw`first ${'\n'} second`; // "first \n second"
```

![](img/9ecc2cc5a4548900d39d71a6b7a6051a.png)

# 最后

处理字符串时，模板文字可能是一把散弹枪，不仅用于表达式，还用于组成更复杂的字符串。翻译、本地化等等可以很好地利用这个工具。它甚至可以用作模板引擎，我知道 Polymer 在提供`html`<template>``功能的组件类中创建内嵌组件模板时使用了它。

另一方面，应该非常小心地使用模板文字。标签函数的使用和实现并不是那么直接，可能变得不可预测和难以理解。将它作为工具箱中的另一个工具。