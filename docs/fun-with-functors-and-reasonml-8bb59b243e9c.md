# Functors 和 ReasonML 的乐趣

> 原文：<https://medium.com/hackernoon/fun-with-functors-and-reasonml-8bb59b243e9c>

## 初学者对 ReasonML 类型和函子类型的简短介绍。

在阅读了[数组是一个包含多个值的仿函数。提供的函数用数组中的每个值来计算，作为回报，你得到一个新的数组！顺便说一句，从道理上讲，你不会对一个值调用方法——这就是 OOP。调用方法的方式是调用一个函数并将值传递给它:](https://medium.com/u/a3129fead82b#map</a></code>。这是因为<em class=)

```
let increment = x => x + 1;
map(Array[1], increment) // => Array[2]
```

[`option`类型，就像`Array`一样，也是一个函子。为了理解它是如何工作的，让我们把它分成两个构造函数:`Some`和`None`。](https://medium.com/u/a3129fead82b#map</a></code>。这是因为<em class=)

[如果我们`map`超过`None`会发生什么？我建议它应该具有与映射空数组相同的行为:它不应该做任何事情，它根本不应该执行所提供的函数——它应该只返回`None`。对于`Some`，我建议它应该具有与映射具有单个值的数组相同的行为。所以单个值将被应用到函数中，然后用`Some`再次打包:](https://medium.com/u/a3129fead82b#map</a></code>。这是因为<em class=)

```
let increment = x => x + 1;
map(None, increment); // => None
map(Some(1), increment); // => Some(2)
```

[实现这个`map`函数只需要几行代码:](https://medium.com/u/a3129fead82b#map</a></code>。这是因为<em class=)

```
let mapOption = (opt, fn) =>
  switch opt {
  | None => None
  | Some(value) => Some(fn(value))
  };
```

[Reason 惊人的类型系统确保了`fn`是一个接收提供的`option`值的函数，并返回一个新的选项。`mapOption`的完整类型声明是:](https://medium.com/u/a3129fead82b#map</a></code>。这是因为<em class=)

```
let mapOption = (opt: option('a), fn: 'a => 'b) : option('b)
```

[让我们一起回顾一下:我们首先声明一个名为 mapOption 的函数，它接收一个名为`opt`的参数，类型为`option(‘a)`。正如我们已经谈到的，`‘a`是一个类型参数，使得这个函数得到*任何类型的选项。*然后，我们接收一个函数，该函数接收`‘a`(我们在`opt`参数中的类型参数)，并返回`‘b`，这是或不是一个不同的类型。然后，函数返回`option(‘b)`，它与内部函数结果类型相同，但是被包装在一个`option`中。](https://medium.com/u/a3129fead82b#map</a></code>。这是因为<em class=)

[换句话说，我们接收一个`option(‘a)`，然后我们接收一个将值从类型`‘a`转换为`‘b`的函数，然后我们返回`option(‘b)`:我们支持内部类型之间的转换，而我们仍然在`option`的土地上:](https://medium.com/u/a3129fead82b#map</a></code>。这是因为<em class=)

```
let stringify = x => string_of_int(x); // int => string
mapOption(Some(1), stringify); // Some("1")
mapOption(None, stringify); // None
```

# [更多仿函数示例](https://medium.com/u/a3129fead82b#map</a></code>。这是因为<em class=)

[我们可以声明另一种类型——`result`函子。`result`仿函数几乎和`option`一样，但是它的负端也有一个值。你可以把它的值当作一个`reason`，或者`error`，或者仅仅是`failure`。当你返回`result`仿函数时，你基本上是在说“这个方法可能会失败”。您可以在验证和数据库查询中使用`result`,或者用于任何错误很重要的事情:](https://medium.com/u/a3129fead82b#map</a></code>。这是因为<em class=)

```
type result('s, 'f) = Success('s) | Failure('f)
```

[`map`行为几乎与`optionMap`行为相同。如果我们有`Success`，我们将获取它的数据并将其应用到所提供的函数中。如果我们有一个`Failure`，我们不会将值应用到函数中，只是让`Failure`保持原样。实现也是几行代码:](https://medium.com/u/a3129fead82b#map</a></code>。这是因为<em class=)

```
let mapResult = (res, fn) =>
  switch res {
  | Failure(failure) => Failure(failure)
  | Success(success) => Success(fn(success))
  };
```

[这种数据结构帮助我们将程序构建成一棵树。当我们遇到问题时，我们停止运用我们的逻辑去解决它。我们基本上停止了“快乐之路”,直到我们处理了失败。*当我们映射到* `*Failure*` *上时，我们停留在相同的失败上，所以一旦我们有了* `*Failure*` *就不会发生数据转换。*](https://medium.com/u/a3129fead82b#map</a></code>。这是因为<em class=)

[在下一篇文章中，我将写关于单子的内容，单子是](https://medium.com/u/a3129fead82b#map</a></code>。这是因为<em class=)[函子](https://hackernoon.com/tagged/functors)的一个特例，它在构建数据转换时可以给你很大的帮助。

> 在那之前，你对自定义函子有什么想法？

感谢阅读！