# 关于 Python 生成器的一个常见误解

> 原文：<https://medium.com/hackernoon/a-common-misunderstanding-about-python-generators-dbc622914d33>

几天前我收到了以下邮件:

> *杰夫，*
> 
> 看来你知道迭代器。也许你能解释一些奇怪的行为。如果你运行下面的代码，你会发现这个函数被区别对待，仅仅是因为它在某个地方有一个“yield ”,即使它完全不可及。

```
def func(): 
    print("> Why doesn't this line print?") 
    exit() # Within this function, nothing should matter after this point. The program should exit 
    yield "> The exit line above will exit ONLY if you comment out this line."x = func()
print(x)
```

当我运行代码时，我从`print()`调用中得到以下输出:`<generator object func at 0x10e968a50>`。

这是怎么回事？为什么*不在`func()`印那行字？即使`yield`完全不可达，它*似乎*会影响函数执行的方式。*

# *`yield`如何影响一项功能*

*为了解释为什么会出现这种现象，让我们回顾一下`yield`。任何包含`yield`关键字的函数都会自动转换为 [*生成器*](https://hackernoon.com/tagged/generator) 。它返回的是一个*生成器迭代器*。我们的打印输出实际上暗示了这一点:*

*`$ python yield.py` `<generator object func at 0x10e968a50>`*

*当`x = func()`被执行时，*我们实际上并没有执行* `*func()*`内的任何代码。相反，因为`func()`是一个*生成器*，所以返回一个*生成器迭代器*。因此，虽然这看起来像一个函数调用，但它实际上给了我们*生成器迭代器*，我们将使用它来生成由*生成器*生成的值。*

*那么我们实际上如何“调用”一个*发生器*？*通过在生成器迭代器*上调用 `*next()*` *。在上面的代码中，这将执行对由`func()`返回并绑定到`x`的*生成器迭代器*的“下一个”调用。**

*如果我们想看到这个神秘的信息被打印出来，只需将代码的最后一行改为`print(next(x))`。*

*当然，在应该被视为迭代器的东西上反复调用`next()`有点麻烦。幸运的是，`for`循环支持对*生成器迭代器*的迭代。想象一个玩具发电机，实现如下:*

```
*def one_to_ten(): 
    """Return the integers between one and ten, inclusive.""" 
    value = 1
    while value <= 10:
        yield value value += 1*
```

*我们可以通过以下方式在 for 循环中调用它:*

```
*for element in one_to_ten(): 
    print(element)*
```

*当然，我们可以写得更冗长些:*

```
*iterator = one_to_ten()
for element in iterator:
    print(element)*
```

*这类似于原始代码所做的。它只是没有使用`x`来实际执行*生成器*中的代码。*

# *摘要*

*我希望这能澄清一些关于 [Python](https://hackernoon.com/tagged/python) 中`yield`和`generators`的常见问题。有关该主题的更深入的教程，请查看[改进您的 Python:“yield”和解释的生成器](https://jeffknupp.com/blog/2013/04/07/improve-your-python-yield-and-generators-explained/)。*

*Jeff Knupp 于 2018 年 6 月 4 日发布*

**原载于 2018 年 6 月 4 日*[*jeffknupp.com*](https://jeffknupp.com/blog/2018/06/04/a-common-misunderstanding-about-python-generators/)*。**