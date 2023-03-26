# 使用 Reason ml-第 2 部分

> 原文：<https://medium.com/hackernoon/get-reason-able-with-reasonml-part-2-65d3ab851570>

## 非法状态、选项类型、函数、元组、列表、数组和等式

![](img/3209d050a2d115e34d0a6afc4a3a4c2d.png)

这是 **3 部分系列**的第 2 部分，在这里我将帮助你理解 ReasonML 及其所有的语法和语义。我将介绍从基本的**数据类型**到在 ReasonML 中声明**函数**的所有内容。

如果你还没有读第一部分，我建议你先读一读，因为这个系列的所有文章都是相互关联的。

[](/@geeky_writer_/get-reason-able-with-reasonml-part-1-ac950188141b) [## 使用 Reason ml-第 1 部分

### RTOP、数据类型、字母绑定、词法范围、If-Else 和开关、记录和变量

medium.com](/@geeky_writer_/get-reason-able-with-reasonml-part-1-ac950188141b) 

# 用变体消除非法状态

将多个互斥选项表示为一个数据结构是 ReasonML 的强大概念之一。但是为了明智地使用它，我们需要重新思考如何对我们的数据建模。

首先，我将创建一个简单的应用程序来获取和呈现一些数据。对于这个应用程序，我首先需要定义 UI 状态。我希望应用程序在等待数据时打印一个加载文本，并在应用程序成功或不成功获取数据时打印一些文本。

我们首先宣布你的记录`type request`。我们添加了一个类型为`bool`的字段`loading`来指示请求是否正在加载。对`error`字段进行同样的操作。我还需要添加一个类型为`string`的`name`字段。`name`是我希望我的应用程序获取的数据。

逻辑很简单。如果请求当前正在加载，我们返回`loading`。万一失败，我们打印`Something went wrong`。当请求成功时，我们将打印`name`。

```
# type request = {
  loading: bool,
  error: bool,
  name: string,
};
# let myRequest = {
  loading: false,
  error: false,
  name: "Rajat"
};
- : string = "Rajat"
```

但是仍然有很多可能出错的地方。比如有人把`loading`和`error`都设为`true`怎么办？

`if/else`表达式呈现了`loading`，但是根据定义，这不应该发生。由于我们的数据结构的方式，它提出了一个问题，在这种情况下我们的 UI 代码应该做什么？也许显示的是`error`而不是`loading`？

我们可以使用变体来处理这个问题。我将用一个变量重新创建类型请求。每个标签代表一种状态，`loading`、`error`、`success`。成功是同样的一个额外的字符串。

```
# type request = Loading | Error | Success(string);
```

为要呈现的文本设置状态并编写开关表达式:

```
# let state = Loading;
# let ui = 
  | Loading => "Loading..."
  | Error => "Something went wrong"
  | Success => "Your name is " ++ name
};
```

我们可以改变状态并重新运行我们的开关表达式。所有情况都按预期工作。这样做的好处是，如果我们添加一个`request`变体，我们永远不会同时拥有`loading`、`error`和`success`。

使用带有变体的`switch`表达式将防止我们忘记一个状态。如果你错过了一个状态，Reason 会抛出一个错误。

这将解决我们的大部分问题。但是我们还有一个案子要处理。如果你的后端没有存储任何数据怎么办？然后应用程序将返回一个空字符串，输出类似于`Your name is` 。

为了处理这个损坏的 UI，我们将匹配一个空字符串并返回一个适当的案例。

```
# let ui =
  switch(state) {
  | Loading => "Loading..."
  | Error => "Something went wrong"
  | Success("") => "Your name is missing"
  | Success => "Your name is " ++ name
  };
```

在现实世界中，`Success`构造函数将包含另一个数据结构，如另一个变量，如`Record`或`List`。

```
# type userResponse = {
  id: int,
  name: string,
  age: int,
};
# type request = Loading | Error | Success(userResponse);
# let state = Success({id: 1, name: "Rajat", age: 22});
```

**注意:**类型系统不消除 bug。它只能指出未处理的情况，要求你覆盖。

# 选项类型

理智不做`null`。如果你试图将`null`与类型为`int`的`age`绑定，将无法工作。

```
# let age: int = null;
Error Unbound value null
```

有些人可能会说这很棒。`null`是我们应用程序中许多许多错误的原因。但是`null`确实有它的用途。例如，你想在你的应用程序中引用一些东西，但是你还不知道相应的值是否可用。

这就是为什么理性为我们提供了一个叫做`option`的变体。这个变体有两个标签:

*   **无** —这表示没有可用的值。
*   **有的** —这是表示有值。

```
# None;
- : option('a) = None
# Some(42);
- : option(int) = Some(42)
```

我们可以像这样使用`name`:

```
# let name = None;
let name: option('a) = None;
# let name = Some("Rajat");
let name: option(string) = Some("Rajat")
```

像任何其他变体一样，我们也可以将它与一个`switch`表达式一起使用。

```
let message = 
  switch (name) {
  | None => Sadly I don't know"
  | Some(value) => "The meaning of life is: " ++ value
  };
```

有了 option，我们就有了一个工具，它允许我们模拟一个新的值，同时仍然是类型安全的，这意味着纯推理程序没有空错误。

# 功能

在 Reason 中声明和使用函数非常简单。匿名函数看起来像这样:

```
# (x) => x + 1;
- : int => int = <fun>
```

Reason 中的函数由括号中的一个或多个参数定义。如果只有一个参数，可以省略括号。

```
# let plusOne = x => x + 1;
# plusOne(4);
- : int = 5
```

要编写一个将整数加到浮点数上的函数，我们可以这样做:

```
# let add = (x, y) => {
  let z = float_of_int(x);
  y + z;
};
```

这种语言的另一个特点是它对参数的局部应用。我们可以通过调用带有两个参数的函数将两个整数相加。

```
# let add = (x, y) => x + y;
# add(3, 2);
- : int = 5
```

提供一个参数也是可能的。它不是抛出一个错误，而是作为一个函数返回，其中只需要提供一个参数，在我们的例子中是第二个参数。让我们将函数绑定到一个名称并使用它。

```
# let addThree = add(3);
# addThree(2);
- : int = 5;
```

# 链接功能

简洁易读的代码是良好质量和可维护性的一个重要方面。反向应用操作符`|`允许我们将函数链接在一起，而没有创建中间绑定或复杂嵌套的麻烦。

我将创建一个函数列表，当一起使用时，将获取一个字符串，将其大写，然后将其转换为小写元素。

```
# let info = String.capitalize(String.lowercase("ALERT"));
```

使用反向应用操作符`|`，我们可以这样做:

```
# let info = "ALERT" |> String.lowercase |> String.capitalize;
```

结合参数的局部应用，这种语法会非常有用。

```
# [8, 3, 6, 1] 
  |> List.sort(compare)
  |> List.rev
  |> List.find(x => x < 4);
- : int = 3
```

# 递归函数

函数递归要求`let`绑定在它自己的局部范围内是可访问的。使用`rec`关键字，我们可以启用这种可见性来声明递归函数。

我正在创建一个函数，它会数到 10，并在每次递增后显示参数。

```
# let countUntilTen = x => {   
  if (x < 10) {     
    print_int(x)     
    countUntilTen(x + 1);   
  }; 
};Error: Unbound value countUntilTen
```

我们得到这个错误的原因是函数体不能访问函数指向的`let`绑定。但是包含`rec`关键字使之成为可能。这将允许函数看到并调用它们自己，从而为我们提供递归的能力。

```
# let rec countUntilTen = x => {
  if (x < 10) {
    print_int(x)
    countUntilTen(x + 1);
  };
};
let countUntilTen: int => unit = <fun>;# countUntilTen(6);
6789- : unit = ()
```

如果我们想要创建相互递归的函数，我们可以从使用`rec`关键字的单个递归函数开始，然后使用`and`关键字添加第二个递归函数。

```
# let rec add = x => ! even(x)
  and even = x => {
    if(x == 0) {
      true;
    } else {
      odd(x - 1);
    };
  };
  let odd: int => bool = <fun>;
  let even: int => bool = <fun>;
```

# 元组、列表和数组

## 元组

元组是一种数据结构，它允许我们存储任何类型的固定数量的值。

```
# ("Anna", 24);
- : (string, int) = ("Anna", 24)
```

每个元素都由其位置而不是其名称来标识。我们也可以嵌套一个元组。

```
# type point = (int, int);
# let myPoint: point = (4, 3);
```

为了访问一个元组的元素，Reason 为我们提供了两个方便的函数来访问前两个元素。`fst`将访问第一项，而`snd`将访问第二项。

```
# fst((0,2));
- : int = 0
# snd((0,2));
- : int = 2
```

让我们来看看另一个元组:

```
# let (a,b,c) = ("Rajat","Writer",23);
let a: string = "Rajat";
let b: string = "Writer";
let c: int = 23;
```

这里，如果您不想给每个位置分配一个值，您可以简单地用`_`替换该值。

每个元组都是不可变的。所以我们不能更新元组，只能创建新的元组。

## 列表

与元组不同，列表是同质的和不可变的。所以我们不能在一个列表中存储不同类型的条目。

```
# let List = ["Rajat", "Writer"];
```

但是，Reason 允许我们使用一个变量来保存不同的类型，并创建一个标签列表。

```
# type rajat = Name(string) | Age(int);
# [Name("Rajat"), Age(23)];
```

由于是不可变的，我们可以`append`、`prepend`，甚至替换列表中的一项。

```
# List.append(["Rajat"],["Writer"]);
- : list(string) = ["Rajat", "Writer"]
# ["Rajat"] @ ["Writer"]
- : list(string) = ["Rajat", "Writer"]
```

要将一个或多个元素添加到列表中，使用`...`操作符。注意`...`只能用于前置元素。

```
# [0, ...[1, 2, 3]];
- : list = [0, 1, 2, 3]
```

要访问一个列表，你需要使用`switch`表达式。

```
# let list = ["Rajat", "Writer"];
# let message = 
  switch(list) {
  | [] => "There is no data"
  | [head, ...rest] => "My name is " ++ head
};
let message: string = "My name is Rajat";
```

这可能会变得相当复杂。我们可以使用`List.nth`来访问列表中的元素。

```
# List.nth([2, 3], 0);
- : int = 2
```

但是同样，如果列表太小，这个方法会引发异常。

相反，我们可以使用`List.map`来操作列表中的每一项。`List.map`也可以接受一个函数作为它的第一个参数，本地列表作为第二个参数。

```
# List.map(x => x * 5, [2, 4]);
- : list(int) = [10, 20]
```

我们在 Reason 中还有其他的函数，比如`find`、`filter`、`sort`、`exist`、`split`来帮助列表做更多的事情。

## 数组

数组类似于列表，除了数组的内容被包装在管道和括号中，与列表不同，数组是可变的。

```
# let array = [|2, 3|];
# array[0];
- : int = 2
# let array[0] = 3;
```

# 平等

在任何编程语言中，通常有两种类型的等式:

*   **结构相等** —比较两个值的内容是否相同。
*   **引用等式** —检查两个值是否指向内存中的同一个表示。

## 结构平等

结构相等用于比较数据结构、布尔值、元组、列表和记录。

```
# "Rajat" == "Rajat";
- : bool = true
# true == false;
- : bool = false;
```

按理说，我们甚至可以使用结构等式来比较嵌套结构，并检查如下所示的不相等性:

```
# (false, [{name: "Rajat", job: "writer"}]) != (true, [(name: "Rajat", job: "writer"}]);
- : bool = false
```

## **参考等式**

与结构相等不同，引用相等不比较存储在值中的内容。相反，它检查这些值是否指向内存中的相同表示。

```
# let rajat = {name: "Rajat", job: "Writer"};
# rajat === rajat;
- : bool = true
# rajat === {name: "Rajat", job: "Writer"};
- : bool false
```

只有少数情况下你会用到引用相等。虽然这是一个非常快速的检查，但如果传入了具有相同内容的值，它也可能导致我们的应用程序错过。通过引用比较两个整数，总会发现它们是如何实现的。

```
# 24 === 24;
- : bool = true
```

# 未完待续…

本系列 **3** 的**部分 2** 到此结束。**第三部**马上就要出了，抓紧了:)

这里是**第一部**，如果你还没看的话。

[](/@geeky_writer_/get-reason-able-with-reasonml-part-1-ac950188141b) [## 使用 Reason ml-第 1 部分

### RTOP、数据类型、字母绑定、词法范围、If-Else 和开关、记录和变量

medium.com](/@geeky_writer_/get-reason-able-with-reasonml-part-1-ac950188141b) 

我是 Rajat S， [GeekyAnts](https://geekyants.com/?utm_source=medium&utm_medium=article&utm_campaign=blog) 的技术内容作者。有抱负的程序员还有很长的路要走。一个热爱漫威电影的铁杆 DC 漫画迷。以多任务处理著称。

感谢阅读，希望这是有帮助的！请👏如果你喜欢这篇文章，请在这里和/或在[推特](https://twitter.com/geeky_writer_)上关注我，了解我的新文章！