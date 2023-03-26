# TypeScript 中的基本类型

> 原文：<https://medium.com/hackernoon/basic-types-in-typescript-15faec3f7897>

## 扩展 JavaScript 数据类型

## 什么是类型？

本文的目的是构建关于基本 JavaScript 数据类型的现有知识，并向新开发人员展示 TypeScript 提供了什么。TypeScript 是 JavaScript 的超集，所以 JavaScript 的基础知识不是必须的，只会有所帮助。为了简洁起见，我假设读者了解 JavaScript 的基础知识，并省略 JavaScript 中已经存在的基本类型脚本数据类型。

![](img/3267aba639e901ade296a5c606b451e4.png)

Photo by [Steve Halama](https://unsplash.com/@steve3p_0?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 排列

我在关于数据类型的文章[中没有提到数组，因为从技术上讲，它们被认为是 JavaScript 中的对象。TypeScript 没有对这种数据类型做任何新的事情，它仍然是计算机科学的经典支柱，但是 TypeScript 确实使数组成为它自己的数据类型。数组保存一个*单个*数据类型/值，可以用两种方式声明:](/@jtearl188/javascript-types-8ff88a24c1d5)

```
let myArray: number[];
let myArray: Array<number>; //generic syntax
```

我最常看到的是第一个例子，在我看来，它比一般语法更简洁，并且遵循了许多其他编程语言中的语法。

## 元组

元组是 Python 和 C#等语言中常见的另一种编程数据结构。与数组不同，元组可以存储各种数据类型的集合。它们是基于索引的，这意味着像数组一样，存储在元组中的单个值可以使用它们的基于零的数字索引来选择。

```
let myTuple:[boolean, string];
```

对于元组有一个警告，如果我们试图添加一个已知长度之外的元组，会发生什么？假设我们尝试将第三个值推送到下面的元组:

```
let myTuple:[boolean, string] = [true, "sigma"];
```

这里会发生什么？TypeScript 发挥了一些神奇的作用，使用了所谓的联合。如果你需要复习一下联合，简单明了的解释是，我们添加到元组的第三个值可以是声明的数据类型。如果你想知道更多[的话，这篇 reddit 帖子](https://www.reddit.com/r/typescript/comments/96jh6f/how_do_union_types_affect_tuples/)详细阐述并涵盖了如何通过添加尾部来改善元组。

## 列举型别

TypeScript 中的枚举与其他语言相同，它们是一组预定义的相关常量。

```
enum Fish {Bass, Trout, Salmon}
let catch: Fish = Fish.Bass;
```

知道枚举在幕后映射到数值是很有帮助的。默认情况下，枚举的数值索引为 0，但它们也可以设置为开发人员选择的任何值，在下面的示例中，我将 Bass 的索引设置为 1，而不是默认的 0。现在知道了这一点，我可以在我的枚举中使用 Bass 的数值来设置 catch。我个人的观点是，这会降低代码的可读性，通常应该避免。

```
enum Fish {Bass = 1, Trout, Salmon}
let catch: Fish = Fish[1];
```

![](img/3140f789e0efcb0a868b1357aee11ab7.png)

Photo by [Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## 任何的

我把 any 看作是普通 JavaScript 中的 var。Any 告诉 TypeScript 该变量可能是动态内容，或者它可能会改变类型，并且应该在编译时检查中省略该变量。Any 的强大之处在于它可以让开发人员跳过编译检查，但是由于这个原因，它应该作为最后的手段而不是首选。TypeScript 的美妙之处在于它允许开发人员定义类型，因为它是一种不明确的数据类型，所以这一点无关紧要。

```
let idk: any = 4;
```

any 的一个很好的用例是处理您可能只知道部分存储类型的数据。在下面的例子中，我们有许多不同类型的数组，并且能够使用任何类型来保持数组完整。

```
let mixedArray: any[] = [1, "fish", false];
mixedArray[0] = "replace number with string";
```

## 空的

Void 是 any 的反义词，它表示类型的缺失。当函数不返回值时，这种情况很常见。虽然您可以将变量声明为 void，但这并不是最有用的，因为变量只能被赋值为 undefined 或 null。

```
function returnsNothing(): void {};
let voidVar: void = null;
```

## 从不

在 2.0 版中引入，never 表示永远不会出现的值。Never 用于从不返回的函数以及具有不可能类型的变量。

```
//function that never returns
function infinteLoop(): never{while(true){}}// impossible type
let impossible;
if (typeof impossible === "string" && 
typeof impossible === "number"){
     impossible //type is never
}
```

我很少见过显式类型为 never 的函数或变量，但是知道这种类型的存在是很重要的。一些我从未见过的更现实的例子是错误处理函数。

## 结论

类型在编程中并不新鲜，但是了解它们对于使用 TypeScript 是必不可少的。通过允许类型强制，TypeScript 从动态类型语言中提取 JavaScript，并通过静态类型方法增加了一个澄清层。知道如何充分利用这些类型将使您的代码对其他人更具可读性，并改进您代码的维护。