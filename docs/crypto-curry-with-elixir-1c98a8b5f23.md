# 秘制咖喱配长生不老药

> 原文：<https://medium.com/hackernoon/crypto-curry-with-elixir-1c98a8b5f23>

## (更多)功能性酏剂— 1

![](img/0f6f523c7e0d3ae8d75799155f9fa224.png)

如果你(像我一样)从面向对象语言开始接触 Elixir，那么像**curry、函数组合和无指针函数**这样的概念可能不是很熟悉。其中一些默认情况下不适用于酏剂。但是它们是**通用函数式编程(FP)概念**的一部分，值得探索。

我从一本同事推荐的免费书籍开始:[弗里斯比教授的《函数式编程基本指南》](https://drboolean.gitbooks.io/mostly-adequate-guide/)。我会把它推荐给任何开始接触函数式编程的人。它使用了 [JavaScript](https://hackernoon.com/tagged/javascript) 和一些库来举例说明这些概念。然而，JS 并不是我的主要语言，所以我很想知道它们是如何应用于 Elixir 的。

如今，任何事情都与加密货币有关。为了跟上潮流，让我们建立一个小的加密价格检查器。然后我们将使用 FP 概念重构它。

# 初始实施

public 函数将英镑金额作为唯一的参数。它输出你可以购买的加密硬币的数量。

示例:

```
buy_crypto_with_gbp(2000) =>
[
  BTC: 0.18983617051918897,
  ETH: 1.9337893873932355,
  LTC: 10.66955239648603
]
```

`Support`模块的作用是与外部 API 通信并返回汇率。你可以在 [Github repo](https://github.com/iacobson/blog_crypto_curry/blob/master/lib/support.ex) 中找到实现。它公开了两个公共函数:

```
# exchange rate between 2 currencies
exchange_rate("USD", "GBP") => 0.7463 # exchange rate between crypto and USD
list_crypto_usd() => [BTC: 14256.67, ETH: 1371.76, LTC: 262.51]
```

上面的代码中有几个简单的动作:

*   获取 3 种加密货币的美元价格列表
*   获取两种货币之间的汇率
*   将加密价格列表从美元转换为英镑。
*   映射每个加密硬币的数量

挑战在于使用 currying、composing 和 pointfree 的功能概念重构上面的代码。

# 咖喱，作曲，免费

首先，我必须说，那些概念不是现成的灵丹妙药。我们可以自己实现其中的一些，但是使用这个神奇的包更安全也更容易: [Quark](https://github.com/expede/quark) 。你可能会说，我们已经可以用管道`|>`操作符在 Elixir 中组合函数了。听我说，我们要用的作曲略有不同。在 mix 文件中添加夸克包，并在模块顶部添加`use Quark`。

## Currying

是调用带多个参数的函数的能力，一次一个参数。每次它将返回一个新的函数，直到应用最后一个参数并返回结果。

Quark 为私有函数定义了与`defcurry`或`defcurryp`的 curry。

让我们从确定美元对英镑的汇率开始。在我们的`Support.exchange_rate/2`函数周围添加一个包装器:

```
defcurry exchange_rate(from, to) do
    Support.exchange_rate(from, to)
end
```

现在，您可以开始将参数一个接一个地应用到我们的新函数中。您可以将部分结果(这是一个函数)赋给变量。或者它可以成为一个新的函数定义:

```
# apply arguments
exchange_rate.("USD").("GBP") => 0.7463# assign resulting function to a variable
from_usd = exchange_rate.("USD") => #Function<5.65603100/1 in CryptoCurry.exchange_rate/0>from_usd.("GBP") => 0.7463# new function definition
def exchange_rate_from_usd do
  exchange_rate().("USD")
endexchange_rate_from_usd.("GBP") => 0.7463
```

下一步是将上述转换应用于我们收到的加密货币信息`{BTC: 14256.67}`。我们使用在初始实现中定义的相同的`apply_conversion/2`函数，但是我们只是对它进行了修改:

```
defcurry apply_conversion(rate, {crypto, value_usd}) do
    {crypto, value_usd * rate}
end
```

如果我们遵循与上述相同的逻辑，我们现在可以对以英镑为单位的比特币价值进行如下操作:

```
apply_conversion_gbp =
  apply_conversion.(exchange_rate_from_usd.("GBP")) => #Function<7.34465968/1 in CryptoCurry.apply_conversion/0>apply_conversion_gbp.({:BTC, 14265.67}) =>
{:BTC, 10646.469520999999}
```

变得有点困惑？这就是下一个概念可能派上用场的地方。

## 组成

Elixir 管道操作符`|>`从左侧的表达式中获取输出，并将其作为第一个参数传递给右侧的函数调用。

组合非常相似，只是它将一个函数作为参数传递给另一个函数。它可以是:

*   从右到左——数学方法——用`<|>`表示
*   从左到右——流动方式，如仙丹管——用`<~>`表示

让我们看看测试应用程序之外的一个小例子:

where rl is right to left, and lr is left to right

在这种情况下:

```
bazz_rl.("a").("b") == bazz_lr.("a").("b")
```

回到加密的例子，我们将保持接近 FP 的书，并使用数学的方式组成。我们希望定义一个函数，它将接受加密数据并将其转换为 GBP。

```
def apply_conversion_gbp do
  (apply_conversion() <|> exchange_rate_from_usd()).("GBP")
end
```

`apply_conversion`将部分应用`exchange_rate_from_usd`作为其第一个参数。将使用`"GBP"`属性调用结果函数。当然，结果是另一个可以用加密数据调用的函数:

```
apply_conversion_gbp.({:BTC, 14265.67}) =>
{:BTC, 10646.469520999999}
```

我们快到了。需要一个函数，将返回我们可以用英镑购买的硬币数量。从最初的实现开始，我们就已经有了这个功能。只需要咖喱。

```
defcurryp get_quantity({crypto, value_gbp}, amount) do
  {crypto, amount / value_gbp}
end
```

## 映射和合成

如果你还记得最初的例子，我们在加密数据列表上迭代了两次。一次应用汇率，然后获取数量:

```
Support.list_crypto_usd()
|> Enum.map(&apply_conversion(Support.exchange_rate("USD", "GBP"), &1))
|> Enum.map(&get_quantity(&1, amount))
```

这可能是低效的，但是数学在这里再次帮助了我们:

`map(f2) <|> map(f1) == map(f2 <|> f1)`

所以我们可以组合这两个函数，并且只映射一次。

```
def convert_gbp_and_get_quantity do
  get_quantity() <|> apply_conversion_gbp()
enddefcurry buy_crypto_with_gbp(amount) do
  Enum.map(
    Support.list_crypto_usd(),
    &convert_gbp_and_get_quantity().(&1).(amount)
  )
end
```

我们的应用程序现在处于工作状态。

```
buy_crypto_with_gbp.(2000) =>
[
  BTC: 0.19774571082071027,
  ETH: 2.0169243958209693,
  LTC: 11.090413196189877
]
```

## 自由点

这最后一步是我们情况下的表面变化。这是我们举办`buy_crypto_with_gbp/1`的唯一原因。

Pointfree 是声明函数而不提及参数的能力。我们使用`defx` Quark 包为我们的应用程序定义一个无点接口函数。

```
defx buy_crypto_with_gbp do
  buy_crypto_with_gbp().()
end
```

我们可以用`buy_crypto_with_gbp(2000).`来调用它

## 把所有的放在一起

相当长的旅程，但我们做到了。使用 FP 概念实现加密转换器。

# 回到灵丹妙药

然而，保持从上面的实现中学到的一些东西，我们可以重构我们的仙丹代码，而不需要库里或者额外的库。

# 结论

如果我需要在上面的`CryptoCurry`和`CryptoElixir`实现中做出选择，我会选择最后一个。我发现长生不老药的原生方式更加清晰明了。

Currying 和 composing 函数会使你的代码变得枯燥。可以帮助构建特殊功能和一般功能。此外，由于函数名更短，没有太多的参数，代码可能看起来更整洁。但是从长远来看，或者对于项目中的新人来说，这可能会令人困惑。

让我们举个例子。我看到用两个连续的参数调用`convert_gbp_and_get_quantity`。但我不能确定这是否只是部分应用。我不知道它是返回一个最终结果还是另一个等待更多参数的函数。

最后，重要的是要知道我们可以选择在 Elixir 中使用这些概念。特别是如果你有一个功能背景，你有时可能会错过他们。

我希望帖子至少给了你一些关于库里、作曲和《长生不老》中 freepoint 的基本概念。

我很好奇你对长生不老药和那些 FP 概念的看法。如果你在其他语言中使用它们，你还需要它们吗？如果没有，你会考虑使用它们吗？