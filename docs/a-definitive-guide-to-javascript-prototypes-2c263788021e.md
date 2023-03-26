# JavaScript 原型权威指南

> 原文：<https://medium.com/hackernoon/a-definitive-guide-to-javascript-prototypes-2c263788021e>

有小代码片段和简单的解释。

# 介绍

许多开发人员和工程师对 JavaScript 原型感到困惑和陌生。

今天，是时候一劳永逸地去神秘化和掌握原型了。这样做可以让我们在 JavaScript 中不可避免地遇到和使用原型时，有信心处理它们。

## 部分

本指南分为以下几个部分。

*   创建函数的效果
*   将函数作为构造函数调用
*   原型遗传的演示
*   原型链的遍历
*   原型链的组件
*   函数的原型
*   没有构造函数的原型
*   一个原型为什么叫原型？

## 如何阅读本指南的提示

每一节都建立在前一节的基础上。因此，不要跳过某些章节，尤其是当你第一次阅读本指南的时候。

所有代码片段都是相关的，并且是跨部分累积的。代码片段应该可以很好地与最新版本的 JavaScript 兼容。

如果你浏览本指南的速度太快，你可能不会有太大的收获。试着仔细阅读本指南，你可能会对什么是原型以及如何使用它们深信不疑。

# 是时候一劳永逸地去神秘化和掌握原型了。

# 创建函数的效果

创建函数有两个效果。

1.  函数本身将被创建。
    注意，函数也是一个对象，因此它可以有额外的属性。
2.  第二个对象将被创建并作为`<Function>.prototype`附加到函数上。

为了演示这些效果，创建一个名为`Person`的函数，并观察它自动带有一个`Person.prototype`对象。

```
function Person(name) {
  this.name = name;
}typeof Person.prototype; // "object"
```

# 将函数作为构造函数调用

当我们用`new`关键字调用`Person`函数时，即作为一个构造函数，一个新的对象`this`被隐式创建，`this.name`被设置，最后，`this`被隐式返回。

```
function Person(name) {
  this.name = name;
}const alex = new Person("Alex");
typeof alex; // "object"
alex.name; // "Alex"
```

# 原型遗传的演示

重要的是，对象`alex`和从`Person`构造的任何其他对象将获得对`Person.prototype`的间接访问。

让我们给`Person.prototype`增加一个`greet`功能。注意，现有对象`alex`现在可以`greet`了，新创建的对象`tom`也可以这样做。这种形式的代码重用被称为原型继承。

```
Person.prototype.greet = function() {
  console.log(`Hi ${this.name}`);
}alex.hasOwnProperty("greet"); // false
alex.greet(); // "Hi Alex"const tom = new Person("Tom");
tom.greet(); // "Hi Tom"
```

尽管从`Person`构造的对象`alex`本身没有`greet`属性，但它能够访问`Person.prototype`并因此调用`Person.prototype.greet`，而`this`被隐式设置为`alex`，这导致“Hi Alex”被记录到控制台。

另一个对象`tom`以类似的方式获得对同一个 `Person.prototype`对象的访问。

# 原型链的遍历

当遍历算法在对象上找不到所需的属性时，它会查询对象的原型。如果找到原型的属性，遍历就会停止。否则，它将查询原型的原型，依此类推，直到找到属性或到达原型链的末端。

在每次遍历中，如果对象[不知道如何做，它会将做某事的责任委托给它的原型](https://en.wikipedia.org/wiki/Delegation_(object-oriented_programming))。`alex`不知道如何`greet`，所以`alex`向`Person.prototype`求助如何`greet`。

# 原型链的组件

让我们用`Object.getPrototypeOf`来检查`alex`的整个原型链。

```
Object.getPrototypeOf(alex) === Person.prototype; // true
```

上面那行告诉我们`alex`的原型是`Person.prototype`。因此，如果 JavaScript 在`alex`上找不到想要的属性，它将检查`Person.prototpe`。

换句话说，`alex`的原型链从`Person.prototype`开始。

如果 JavaScript 在`Person.prototype`上还是找不到想要的属性，就会查看`Person.prototype`的原型，也就是`Object.prototype`。

```
Object.getPrototypeOf(Person.prototype) === Object.prototype; // true
```

为什么`Object.prototype`是`Person.prototype`的原型？

假设`Person.prototype`是一个对象，由内置的`Object`构造函数构造而成(是否如此是一个实现细节)。

你可以观察到一个与我们目前所学一致的模式。

1.  `alex`由`Person`构造而成。
    `alex`的原型是`Person.prototype`。
2.  `Person.prototype`由`Object`建成。
    `Person.prototype`的原型是`Object.prototype`。

您可以先用`Object`替换`Person`，然后用`Person.prototype`替换`alex`，从语句 1 转到语句 2。

重述一下，我们已经看到`alex`的原型是`Person.prototype`，`Person.prototype`的原型是`Object.prototype`。因此，`alex`的原型链包含`Person.prototype`后跟`Object.prototype`。

`Object.prototype`是`alex`原型链中的最终原型吗？是的，因为`Object.prototype`没有原型(它是空的)。

```
Object.getPrototypeOf(Object.prototype) === null; // true
```

即使`Object.prototype`是一个对象，但它的原型不是`Object.prototype`，否则我们就会有一个无限的原型链。

JavaScript 中几乎所有其他对象都在原型链的末尾有`Object.prototype`。我们已经看到对象`alex`是如何从`Person`构造而来的。该属性也适用于从内置`Object`构造函数和对象文字语法创建的普通对象。

```
const constructedObject = new Object();
const objectLiteral = {};Object.getPrototypeOf(constructedObject) === Object.prototype; // true
Object.getPrototypeOf(objectLiteral) === Object.prototype; // true
```

事实上，几乎所有的对象在其原型链的末端都有`Object.prototype`，这具有实际意义，因为它们将可以访问`Object.prototype`提供的公共实用程序，如`toString`和`valueOf`。

```
alex.toString(); // "[object Object]"
alex.valueOf(); // Person { name: "Alex" }
```

# 函数的原型

前面，我们看到`alex`有一个`name`属性，可以使用`alex.name`来访问。语法没有说更多关于`alex`的东西，但是我们通常会给它添加更多的含义。我们认为`alex.name`不仅仅是碰巧在`alex.name`可以访问的任意名字，而是指**亚历克斯的名字**。

那`Person.prototype`呢？是指`Person` **的原型**？

没有。

```
Object.getPrototypeOf(Person) !== Person.prototype; // true
```

如果`Person.prototype`不是指`Person`的原型，那么它指的是谁的原型？

嗯，`Person.prototype`会成为由`Person`构造的对象的原型。我们已经看到了`alex`的这种行为。

把`Person.prototype`想象成圣诞老人存放在你家的礼物可能会有所帮助，但那份礼物是给你的孩子的，不是你的。

那么`Person`的实际原型是什么？是`Function.prototype`。

```
Object.getPrototypeOf(Person) === Function.prototype; // true
```

这是因为`Person`是一个`Function`，因此它有一个`Function.prototype`的原型。

`Function.prototype`提供`call`、`bind`、`apply`等常用工具，可通过`Person`等功能访问。

# 没有构造函数的原型

我们也可以创建没有构造函数的原型链。

```
const greeter = {
  greet() {
    console.log(`Hi ${this.name}`);
  }
};const bob = Object.create(greeter);
bob.name = "Bob";
bob.greet(); // "Hi Bob"Object.getPrototypeOf(bob) === greeter; // true
```

在上面的例子中，`Object.create`创建了一个以`greeter`为原型的新对象。该对象随后被分配给`bob`。虽然`bob`没有自己的`greet`功能，但它可以访问其原型`greeter`上的`greet`功能。

使用`Object.create`创建一个具有已定义原型的新对象比处理构造函数和`<Constructor>.prototype`对象更简单。

`Object.create`的使用可以结合工厂功能。与构造函数不同，工厂函数显式返回一个对象，并且不用`new`调用。这里有一个例子。

```
function createPerson(name, prototype) {
  const person = Object.create(prototype);
  person.name = name;
  return person;
}const ada = createPerson("Ada", greeter);
ada.greet(); // "Hi Ada"
```

鉴于工厂函数和`Object.create`的简单性和简洁性，它们往往是比构造函数和`<Constructor>.prototype`对象更受欢迎的方法。

# 一个原型为什么叫原型？

“因为有人想出来的”不是一个满意的答案。

单词 [prototype](https://hackernoon.com/tagged/prototype) 在 JavaScript 文献中经常被忽略。大多数人把它当作一个技术术语，并不解释为什么它被恰当地命名为“原型”。知道为什么原型被称为原型可以给我们一个更好的心理模型，无论何时我们遇到它。

也就是说，很难找到这个问题的确切答案。以下是我对这个问题的看法。

**现实生活中的原型**主要是指

1.  一个产品，
2.  尽管功能有限，但是
3.  产品的最终版本将共享其原型的一些特征。

用“对象”替换“产品”一词后，我们看到 JavaScript 原型指的是

1.  一个物体，
2.  尽管功能有限，但是
3.  对象的最终版本将共享其原型的一些特征。

第一点强调了与传统的基于类的继承相比，原型继承的独特之处。原型是一个可以独立使用的对象。然而，传统的类不是对象，因此不能像对象一样使用。

注意，尽管后来的 JavaScript 版本有一个`class`关键字，但它仍然使用原型继承。

第 2 点是有效的，因为与使用它作为原型的对象相比，原型通常具有较少的属性。比如`alex`和`Person.prototype`都能够`greet`(虽然`alex`是借助`Person.prototype`做到的)，但是`alex`多了一个`name`。

至于第三点，由于原型继承，JavaScript 对象确实共享其原型的一些特征。

由于对象是在原型链中链接的，如果您更改了原型，链接到该原型的现有和未来对象的行为可能会受到影响。

因此，请注意不要更改内置原型，除非您尝试聚合填充标准特征。如果每个人都擅自更改内置原型，就会出现冲突和代码崩溃。

# 摘要

您已经看到了什么是原型，JavaScript 如何遍历原型链来访问原型属性，以及代码如何在我们称之为原型继承的过程中被重用。您还看到了如何使用原型，不管有没有构造函数。

尽管原型的掌握让许多人感到困惑，但我希望这本权威指南能够帮助您掌握 JavaScript 中的原型，从而成为一名更好的软件开发人员和工程师。

## 祝贺你一路走到这里！

## 如果你觉得这个指南有用，把它发给你可能从中受益的同事和朋友。

# 为了进一步阅读

1.  Eric Elliott 撰写的关于 JavaScript 继承的常见误解
2.  [你不知道的 JS: *这个* &物体原型](https://github.com/getify/You-Dont-Know-JS/tree/master/this%20%26%20object%20prototypes)由凯尔·辛普森制作