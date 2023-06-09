# 阿尔法、贝塔、伽马命名惯例

> 原文：<https://medium.com/hackernoon/alpha-beta-gamma-naming-convention-e735f16ed4c5>

![](img/66a936f5a5b0692316a8dfaa77269ad6.png)

几个月前，我开始使用阿尔法，贝塔，伽马命名惯例。它开始只是一个暂时的东西，但它最终坚持下来了，现在我一直在使用它。

# 阿尔法，贝塔，伽马

我每天都在使用 [BEM](http://getbem.com/) 。m 代表修饰词。一般来说，修饰语代表一个元素的变体。

有一天，我遇到了一个很大的编程问题:如何命名一个东西，在我的例子中，是一个修饰语。我想不出任何有建设性的东西，所以我只写了`alpha.`然后我创建了一个新元素修改，并将这个新的修饰符命名为`beta.`

当我回到那个特定的代码来更新修饰符的名称时，我意识到我喜欢这种方式。名字简洁明了，我知道我必须应用哪个类来修改原始元素。新的命名惯例诞生了。

# 命名变量

过了一段时间，我开始对 Sass 变量使用同样的方法。以下是我的网站的颜色变量的外观:

```
$color-alpha: #12e09f;
$color-beta: #e01258;
$color-gamma: #f5f5f5;
$color-psi: #1f1f1f;
$color-omega: #fff;
```

# 为什么有效

使用 Alpha，Beta，Gamma 命名约定的好处在于它将上下文从元素中分离出来。此外，[希腊字母](https://en.wikipedia.org/wiki/Greek_alphabet)被广泛使用，所以你已经知道一些字符的几率相当高。

假设我们有一个带有修饰符`list--red,` `list--green,`和`list--blue`的`list`组件，用于分别包含红色、绿色和蓝色元素的列表。在某个时候，我们的设计师决定将品牌颜色从红色改为粉红色。现在我们需要将红色更新为粉红色，这意味着我们的修饰符类名`list--red`不再有意义。我们需要将类名更新为`list-pink.`这种情况在 CSS 领域是一个经典问题。

如果我们使用α、β、γ的约定，我们会有这三个类:

*   `list--alpha,`
*   `list--beta,`还有
*   `list--gamma.`

现在我们可以随心所欲地修改样式，我们的类也不会再失去意义。

# 不要过度使用

这里存在潜在的大脑处理开销，因为您必须记住哪个字母代表元素的哪个版本。通过记录你的变量和修饰符，不要对一个组件使用太多的变体，尽量减少你的大脑使用。

> *用* `*psi*` *和* `*omega*` *来命名不同的或完全不同的变量或修饰语。*

给你一个提示:使用`psi`和`omega`字母(字母表的最后两个字母)来命名不同的或完全不同的变量或修饰语。例如，我用`color-psi`代表文字颜色，用`color-omega`代表白色。这些是`color-alpha,` `color-beta,`和`color-gamma`的反义词，在我的例子中是主题/品牌颜色。

# 结论

Alpha，Beta，Gamma 命名惯例对我有效，我在所有新项目中都使用它。我很确定这不是我发明的，我只是想和你分享这个方法，看看你是怎么想的。

你喜欢这种方法吗？您是否有不同的命名约定想与社区分享？我想听更多关于它的情况。💬

*最初发布于*[*www . silvestarbitrovic . from . HR*](https://www.silvestarbistrovic.from.hr/articles/alpha-beta-gamma-naming-convention/)*。*