# JavaScript —回归基础:前缀与后缀

> 原文：<https://medium.com/hackernoon/javascript-back-to-basics-prefix-vs-postfix-8da5256223d2>

![](img/f396c9bd5c7916685c3632bda4b93408.png)

## 祝我好运吧，我正在钻研 JavaScript！

尽管我很想马上开始使用 [JavaScript](https://hackernoon.com/tagged/javascript) ，并创建应用程序，但我知道除非我理解了基础，否则我无法完全掌握这门语言。因此，我一直在关注来自 [https://javascript.info](https://javascript.info) 的章节，到目前为止这是一个很好的来源。

作为我自己的参考，我想我会写一些我正在[学习](https://hackernoon.com/tagged/learning)的有趣的花絮，或者一些我一直在努力解决的话题。我希望这对其他学习 JavaScript 的人也有所帮助。

## 递增/递减

这种数值运算将变量增加或减少 1。重要的是要记住，这只能应用于变量，对数值应用这个操作会返回一个错误。

Increment ++将变量增加 1

减量—:将变量减 1

++或--可以用在变量的前后。这就是事情变得有点棘手的地方。

***语法***
后缀形式:counter++

前缀形式:++计数器

虽然两种形式都将变量增加 1，但还是有区别的。后缀形式返回变量的原始值，在递增/递减之前，前缀形式返回递增/递减之后的值。如果我们使用增量/减量的返回值，就可以看出这种差异。

***举例***
前缀

```
let counter = 2;
alert(++counter); // *3 incremented value has been returned*
```

后缀

```
let counter = 2;
alert(counter++); // *2 Returns the original value prior to the increment*
```

然而，如果我们在稍后的时间点使用增量/减量的值，则两种形式之间没有区别。

***例***
前缀

```
let counter = 2; 
++counter; // *3 The incremented value* alert(counter); // *3 Incremented value has been returned*
```

后缀

```
let counter = 2;
counter++; // *2 The original value* alert(counter); // *3 Value has now been incremented and returns the new value*
```

我花了一点时间来理解这一点，所以我希望这是一个足够清楚的解释。

如果你喜欢这篇文章，给它几个掌声。我将不胜感激！