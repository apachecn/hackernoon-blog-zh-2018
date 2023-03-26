# 用理性变得理性——第 3 部分

> 原文：<https://medium.com/hackernoon/get-reason-able-with-reasonml-part-3-6e2f3f61f959>

## 模式匹配、类型参数、异常、循环、模块和 BuckleScript

![](img/3209d050a2d115e34d0a6afc4a3a4c2d.png)

这是 **3 部分系列**的**第 2 部分**，在这里我将帮助你理解 ReasonML 及其所有的语法和语义。我将介绍从基本的**数据类型**到在 ReasonML 中声明**函数**的所有内容。

在继续之前，请确保您已经阅读了本系列的 [**第 1 部分**](https://hackernoon.com/get-reason-able-with-reasonml-part-1-ac950188141b) 和 [**第 2 部分**](https://hackernoon.com/get-reason-able-with-reasonml-part-2-65d3ab851570) 。都是有联系的。

# 使用开关的模式匹配

模式匹配是这种语言最好的特性之一。它主要做两件事:

*   检查值的特定结构。
*   提取部分值。

有了模式匹配，我们可以做很多很棒的事情，比如将多个模式整合到一个案例中！

由于结构相等，模式匹配可以很好地处理元组、列表、变量和记录等数据结构。

## 模式匹配元组

```
# switch (rajat) {
  | ("Learn ReasonML", false) => "It's Awesome"
  | ("Learn ReasonML", true) => "It really is awesome"
};
```

但这将使我们的应用程序详尽无遗。我们需要使用`_`来匹配这个类型的不匹配值。使用名称代替下划线允许我们提取部分元组。

```
# switch (rajat) {
  | (_, true) => "Its Awesome"
  | (text, false) => "It really is awesome. " ++ text
};
- string = "It really is awesome. Learn ReasonML"
```

## 模式匹配列表

在列表的情况下，我们可以在精确的列表上执行模式匹配。

```
# switch (["a", "b", "c"]) {
  | ["a", "b", "c"] => true
  | _ => false
};
```

关于列表的模式匹配，最好的事情是`...`可以用来提取列表的第一个和最后一个元素。

```
# switch (["x", "y", "z"]) {
  | [head, ...tail] => print_endline(head)
  | [] => print_endline("Empty list")
};
```

## 模式匹配数组

当模式匹配数组时，我们只能对特定长度的数组这样做。数组中的值使用结构等式进行匹配。我们可以使用下划线，也可以使用名称提取数组元素。

```
# switch ([|"a", "b", "c"|]) {
  | [|"a", "b", _|] => print_endline("a, b and something")
  | [|_, "x", "y"|] => print_endline("something, " ++ x ++ y)
  | _ => print_endline("An Array")
};
```

## 模式匹配记录

首先，我将通过声明类型来创建一个记录，然后将它绑定到一个`name`。

```
# type todo = { text: string, checked: bool};
# let myTodo = { text: "Learn ReasonML", checked: true};
```

现在，我可以通过匹配精确的值来提取文本。但是相反，我将尝试其他方法，并使用名称描述提取文本。

```
# switch (myTodo) {
  | {text, checked: true} => "It is awesome: " ++ text
  | {text, checked: false} => "You won't regret it!" ++ text
};
- : string = "It is awesome: Learn ReasonML"
```

## 模式匹配变体

让我们创建一个名为`hero`的变体，标签为`DC`和`Marvel`。

```
# type hero = DC(string) | Marvel(string);
# let dc = DC("Batman");
# let marvel = Marvel("Iron Man");
```

使用模式匹配，我可以检查标签并提取变体的任何部分。

```
# switch (dc) {
  | DC(text) => "I am " ++ text
  | Marvel(text) => "I am " ++ text
};
- : string = "I am Batman"
```

如果您想匹配多个项目并返回结果，您可以这样做:

```
# switch ("Batman") {
  | "Superman" | "Batman" | "The Flash" => "DC Comics"
  | _ => "Marvel Comics"
};
- : string = "DC Comics"
```

# 类型参数

类型也可以接受参数。参数可以与其他语言中的泛型进行比较。创建列表时，首先需要创建一个接收数据类型的类型列表。这里，数据类型是一个类型参数。

```
# let rajat: list(string) = ["Batman", "Superman"];
```

编译器甚至可以反转列表的类型参数。

> 但是我们为什么需要这个呢？

使用类型参数，我们可以创建一个新的类型，它可以接受任意多的参数。

基本上，`type`可以变成一个接受参数并返回新类型的函数。

```
# type hero('a) = ('a, 'a);
# let heroOne: hero(string) = ("Superman", "Clark Kent"); 
```

这样我们可以在创建更多类型的同时避免重复。

还要注意，类型参数只有在以**“`”、**开头，后面跟一个字符或单词时才有效。

## 字母绑定可变性

`let`默认情况下，绑定是不可变的。一旦绑定引用了一个值，它就不能引用其他任何值。但是你可以通过创建一个新的绑定来避免这个问题，这个新的绑定有相同的名字并且*隐藏*以前的绑定。

```
# let person = "Clark Kent";
# print_endline(person); /* Prints "Clark Kent" */
# let person = "Superman";
# print_endline(person); /* Print "Superman" */
```

还有一种方法可以通过使用引用包装实际值来使`let`绑定变得可变。

```
# let foo = ref(5);
let foo: ref(int) = {contents: 5};
```

现在，如果我想更改`foo`中的数据，我会这样做:

```
# foo := 6;
- : unit = ()
# foo;
- : ref(int) = {contents: 6}
```

`foo`的值从 5 变为 6。为了检索引用的值，我需要使用`^`字符。

```
# foo^;
- : int = 6
```

# 例外

按理说，Exception 是一种变体，当您试图在空列表中查找内容时会遇到这种变体。

```
# List.find(x => x == "Rajat", []);
Exception: Not_found.
```

还没完呢！您可以使用`raise`功能创建自己的异常。要捕获应用程序中的异常，请使用模式匹配。

```
# raise(Not_found);
Exception: Not_found.
# try (raise(Not_found)) {
  | Not_found => "Oh Oh!"
};
= : string = "Oh Oh!"
```

我们可以使用`exception`直接匹配`switch`表达式中的异常。

```
# switch (List.find(x => x == "rajat", [])) {
  | name => "Obtained"
  | exception Not_Fount => "Not found"
  };
  - : string = "Not found"
# switch (List.find(x => x == "rajat", ["rajat"])) {
  | item => "Obtained"
  | exception Not_found => "Not found"
  };
- : string = "Obtained"
```

`exception`的用处不多。事实上，你可以用`option`来代替。

# 环

## For 循环

`for`循环用于从数据结构的第一个值迭代到最后一个值。

```
# for (x in 1 to 5) {
    print_int(x * 2);
    print_string(" ");
  };
2 4 6 8 10 - : unit = ()
```

该范围必须有效，并且从较低值到较高值。如果你试着用另一种方式去做，`for`什么都不会做。为了使`for`反向工作，将`to`换成`downto`。

## While 循环

只要给定的条件为`true`，则`while`回路将保持运行。

```
# let x = ref(0);
let x: ref(int) = { contents: 0};
# while (x^ < 5) {
  print_int(x^);
  x := x^ + 1;
};
01234- : unit = ()
```

# 模块

模块可以被描述为代码中的小块。它们允许我们将像`let`绑定和`types`这样的东西封装到逻辑实体中。

Reason 中的每个文件都是一个模块。这就是为什么`name`需要在每个项目中都是独一无二的。

使用`module`关键字在你的推理项目中创建一个模块。确保你的模块名是大写的。

```
# module Rajat = {};
`module Math` : { };
# module Math = {
  let name = "rajat";
  let age = 24;
};
`module Math`: { let name: string; let age: int };
```

要访问模块内部的任何东西，使用`.`符号。

```
# Rajat.name;
- : string = "rajat"
# Rajat.age;
- : int = 24;
```

`.`符号在这里非常有用，因为我们也可以访问存储在模块中的类型。这样，编译器将只查看当前模块或其父模块。

Reason 也允许我们打开一个模块的定义并引用它的内容，而不需要在名字前加前缀。

为此，我们使用了`open`关键字。这将全局打开该模块，并在当前范围内导入其定义。

# BuckleScript

回到本系列的第 1 部分 中，我说过可以使用一种叫做**的东西将 Reason 编译成 JavaScript。让我们看看这实际上是怎么做的。**

首先我们需要在我们的系统中安装 **BuckleScript** 。

```
npm install -g bs-platform
```

安装了这个包，我不仅可以将我的代码编译成 JavaScript，还可以编译成本地二进制代码。

现在我需要使用`bs-platform`建立一个新项目

```
bsb -init reason-project -theme basic-reason
```

这个命令将在你的系统中创建一个名为`reason-project`的新目录。如果您看一下这个目录的内容，您会注意到它与典型的 JavaScript 项目有一些相似之处。

这里唯一独特的是`bsconfig.json`文件。我们可以在这个文件中为这个项目配置 BuckleScript。

如果你查看这个项目的`src`目录，你会看到它包含了一个`Demo.re`文件。让我们删除它并创建一个名为`Main.re`的新文件。您可以在这里编写任何 ReasonML 代码，并使用 NPM/Yarn 运行`build`脚本。这将把任何扩展名为`.re`的文件编译成一个匹配的 JavaScript 文件。所以我们的`Main.re`文件会编译成`Main.bs.js`。

您可以使用`node`运行这个 JavaScript 文件。

```
node src/Main.bs.js
```

您也可以使用`start`脚本来手动构建它。这个脚本将监视文件的变化并相应地编译。

# 下一步是什么？

这个系列应该足以让你开始使用 ReasonML。但是如果你还想知道更多，看看 Reason 的官方文档:

 [## 快速入门原因

### 嘘

reasonml.github.io](https://reasonml.github.io/docs/en/quickstart-javascript.html) 

你也可以看看[尼克·格拉芙](https://medium.com/u/ff3f225e7f5c?source=post_page-----6e2f3f61f959--------------------------------)在 Egghead 上的课程。对我帮助很大！

[](https://egghead.io/courses/get-started-with-reason) [## 从理性开始

### 这门课程首先会教你关于编程语言的语法和语义的知识

蛋头](https://egghead.io/courses/get-started-with-reason) 

我是 Rajat S， [GeekyAnts](https://geekyants.com/?utm_source=medium&utm_medium=article&utm_campaign=blog) 的技术内容作者。有抱负的程序员还有很长的路要走。一个热爱漫威电影的铁杆 DC 漫画迷。以多任务处理著称。

感谢阅读，希望这是有帮助的！请👏如果你喜欢这篇文章，请在这里和/或在[推特](https://twitter.com/geeky_writer_)上关注我，了解我的新文章！