# 如何让你的 Javascript 代码运行得更快

> 原文：<https://medium.com/hackernoon/how-to-make-your-javascript-code-faster-8989869d41af>

很久以前，在 2011 年，我写了我的第一个 DNI(西班牙 ID) [验证脚本](https://blog.singuerinc.com/javascript/actionscript3/validators/dni/nie/nif/2011/06/17/tip-validar-un-dninienif-de-espana-con-actionscript/)。我是用[的动作脚本](https://hackernoon.com/tagged/actionscript)完成的。

几年后，在 Flash 时代之后，我[用 Javascript 重写了它](https://blog.singuerinc.com/javascript/validation/spain/dni/nie/nif/regex/2014/08/06/code-day-006-spain-dni-validation/)。

最近，我进入了一个功能性很强的[编程](https://hackernoon.com/tagged/programming)学习过程，作为一个目标，我建议自己创建一个新的模块来应用我所学到的东西。

结果(我认为不是太遵循 FP 范式)是:

## 更好的 DNI

 [## 辛格莱公司/更好的 dni

### 更好-dni -最快的西班牙 dni(聂/ NIF)验证

github.com](https://github.com/singuerinc/better-dni/) 

很快我发现有很多模块在做同样的事情…

复制别人已经做的有点无聊，所以我把我的目标改为

> ”创建更快的模块来验证 DNI”

在这个过程中，我发现了很多提高代码速度的方法，虽然有时候看起来有点丑，其中一些方法让代码速度提高了一倍。

> 请注意，根据浏览器+CPU+等，您可能会得到不同的结果。我的发现基本基于 Chrome/Node。我会说，你应该在不同的环境中测试你的代码，看看哪里可以优化。

以下是我的发现:

# str.substr() vs str.slice()

```
'0123456789'.slice(-9); // => '123456789'
'0123456789'.substr(-9); // => '123456789'
```

胜者:**切** / x21 更快！/ [测试](https://jsperf.com/better-dni-slice-vs-substr/1)

# toLowerCase()与 toUpperCase()

我必须承认，我以前从未想过为什么一种方法会比另一种方法快。我正在研究 V8 代码，看起来 https://github.com/v8/v8/blob/master/src/string-case.cc#L16 是这个问题的答案。

```
'A'.toLowerCase(); // => 'a''a'.toUpperCase(); // => 'A'
```

如果需要比较两个字符串，这尤其有用。

在比较它们之前，最好把它们转换成小写。

获胜者: **toLowerCase** /稍快/ [测试](https://jsperf.com/better-dni-lower-case-vs-upper-case/1)

![](img/81d1626dc87afd45a17616db55ffef27.png)

Random image related to speed. You need one in all posts. Photo by [James Traf](https://unsplash.com/photos/ukTd6UiQbLQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/fast?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# indexOf()与对象属性

```
const y = 'xyz'.indexOf('y'); // => 1const y = {x: 0, y: 1, z: 2}['y'] // => 1
```

获胜者:**指标** /略快/ [测试](https://jsperf.com/better-dni-index-of-vs-obj-prop/1)

# 字符串连接与模板文字

我不知道为什么我认为模板文字比简单的字符串连接更复杂。

```
const x = 'x';'yyy' + x;`yyy${x}`; // faster
```

获胜者:**模板文字** /稍快/ [测试](https://jsperf.com/better-dni-str-concat-vs-str-template/1)

# 转换为数字

```
parseInt('1234', 10); // => 1234+'1234'; // => 1234
```

赢家:**+签** / x6.12 快！/ [测试](https://jsperf.com/better-dni-convert-to-number/1)

# Math.floor()与按位移位

我从这篇[博文](http://blog.blakesimpson.co.uk/read/58-fastest-alternative-to-math-floor-in-javascript)中获得了一些灵感。

```
Math.floor(1.123456789); // => 11.123456789 << 0; // => 1
```

赢家:**按位移位** /在 jsperf 中速度略快但 Math.floor()在我的 MacBook Pro 中表现要好得多。/ [测试](https://jsperf.com/better-dni-floor-vs-bitwise/1)

# 可以帮助您的代码运行得更快的其他技巧

## 提示 1

先做简单的验证，然后尽快返回。看看这些片段:

```
if(someComplexValidation(value) && value.length !== 9) return;
```

相对

```
if(value.length !== 9 && someComplexValidation(value)) return;
```

是同一个代码，对吗？首先进行“简单的”验证，这样如果第一次验证没有通过，您的代码就不会运行和返回。

## 提示 2

避免使用像“toUpperCase()”这样昂贵的方法，尝试使用聪明的替代方法:

```
const n = 'XYZ'.indexOf('y'.toUpperCase()); // => 2
```

相对

```
const n = 'XYZxyz'.indexOf('y') % 3; // => 2
```

## 提示 3

如果您知道用不同的方法可以达到相同的结果，请针对每个具体情况找到更好的方法:

```
const x1 = 'xyz'.substr(0, 1); // => 'x'const x2 = 'xyz'[0]; // => 'x'const x3 = 'xyz'.slice(0, 1); // => 'x'
```

我希望这能帮助你优化你的代码！

你想有所贡献，让它更快吗？打开一个拉式请求:

 [## 辛格莱公司/更好的 dni

### 更好-dni -最快的西班牙 dni(聂/ NIF)验证

github.com](https://github.com/singuerinc/better-dni/)