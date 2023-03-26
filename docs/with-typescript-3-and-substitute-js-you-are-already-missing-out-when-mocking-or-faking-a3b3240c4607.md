# 使用 TypeScript 3，您已经错过了嘲笑或伪造的机会

> 原文：<https://medium.com/hackernoon/with-typescript-3-and-substitute-js-you-are-already-missing-out-when-mocking-or-faking-a3b3240c4607>

在我们开始之前，我知道以下几点:

*   我用了一个点击半点击的标题让你来到这里。
*   我无耻地分享了许多现有框架中的另一个[嘲讽](https://hackernoon.com/tagged/mocking)/作伪框架(`substitute.js`)。
*   我是这个框架的作者。
*   我不是脸书或谷歌，只是一个像你一样的开发者。
*   我甚至不是一个作家——你一定已经猜到了这一点。

所以为什么要注意，甚至给我多一分钟你的时间？

简单。我几乎可以肯定，我可以把那一分钟还给你，在未来，我还可以把更多的时间还给你。

我不会详细讨论什么是假装或嘲笑，或者为什么这是个好主意。这篇文章假设你已经知道了。

## 目录

*   [用代码样本比较框架](/p/a3b3240c4607#d827)
*   [限制](/p/a3b3240c4607#28f2)
*   [获取 substitute.js](/@mathiaslykkegaardlorenzen/with-typescript-3-and-substitute-js-you-are-already-missing-out-when-mocking-or-faking-a3b3240c4607#a048)

# 将框架与代码示例进行比较

下面所有的例子都假设有一个名为`RealCalculator`的类和一个支持它的名为`CalculatorInterface`的接口。这些分别定义如下。

有了这些类型的定义，让我们继续。我希望这些例子不言自明，并且您会同意以下观点:

*   那个`substitute.js`语法更容易记，更容易学，写起来更快。
*   由于对 TDD 的影响，伪造/模仿接口的能力是现有框架的一个巨大优势。

# 创建一个类的模拟

## ts-mockito

使用`ts-mockito`,你首先创建一个模仿的“类”或“构造函数”,然后你可以从这个模仿创建一个真正的非伪造的实例。

## 类型起订量

`typemoq`也是如此。

## 替代品. js

对于`substitute.js`，实例和赝品是一回事。

# 创建界面的模拟

从一个接口创建一个 mock 使得 [TDD](https://hackernoon.com/tagged/tdd) 变得更加容易，代码也少了很多，同时还能继续编译。使用界面模拟，您可以:

1.  从定义接口开始(没有实现)。
2.  依赖于代码中的那些接口。
3.  使用 fakes 为接口编写测试(您可以在不首先编写类的情况下伪造接口！).
4.  用真实的类实现代码。
5.  微笑。你能够拥有完整的强类型，以及实际上在整个过程中一直编译的东西。没有弯弯曲曲的线条，只有纯粹愉快的 TDD。

## ts-mockito

遗憾的是，这不受支持。

## 类型起订量

`typemoq`也是一样——不支持。

## 替代品. js

在`substitute.js`中，你可以从一个[接口](https://hackernoon.com/tagged/interface)伪造就可以了。由于 ES6 代理，这背后的魔力是可能的。

# 存根方法

在这个例子中，我们将比较三个框架如何处理方法的存根。

## ts-mockito

使用`ts-mockito`,您可以在包装函数中指定模拟对象的参数和返回值。

## 类型起订量

`typemoq`也是如此。

## 替代品. js

对于`substitute.js`，您可以在 mock 本身上定义返回值，并以流畅和强类型的方式使用它。

# 存根属性

在这个例子中，我们将比较三个框架如何处理属性的存根。

## ts-mockito

使用`ts-mockito`可以在包装函数中指定模拟对象的返回值。

## 类型起订量

`typemoq`也是如此。

## 替代品. js

对于`substitute.js`，您可以在 mock 本身上定义返回值，并以流畅和强类型的方式使用它。

# 投掷错误

在这个例子中，我们将比较三个框架如何处理抛出错误。所有框架中相同的语法也以类似于上面描述的方式应用于方法，所以我们不会再赘述。

## ts-mockito

使用`ts-mockito`,您可以在包装器函数中指定要在模拟对象上抛出的异常。

## 类型起订量

`typemoq`也是如此。

## 替代品. js

对于`substitute.js`，您使用`returns`函数并将其封装在 lambda 中以抛出错误。

# 呼叫计数验证

在这个例子中，我们将比较三个框架如何处理调用计数验证。所有框架中相同的语法也适用于属性，所以我们不会详细讨论。

*注意* `*substitute.js*` *不支持“至少* `*n*` *次”验证，也不支持“少于* `*n*` *次”验证，因为我个人认为这是不好的做法——你的测试应该总是确定性的。你应该总是知道在测试中预期的调用数量。*

## ts-mockito

使用`ts-mockito`,您可以在包装函数中指定要匹配的模拟对象的操作。

## 类型起订量

`typemoq`也是一样。

## 替代品. js

对于`substitute.js`，您使用`received`函数并对其返回值调用操作。

或者，你也可以使用`didNotReceive`,它完全符合你的预期。

# 代理呼叫

如果你想让你的假方法做别的事情，代理调用可能是有用的。

## ts-mockito

使用`ts-mockito`,您可以在包装器函数中指定匹配模拟对象的操作，然后使用另一个函数来指定代理调用哪个方法。

## 类型起订量

`typemoq`也是如此。

## 替代品. js

对于`substitute.js`，您使用`mimicks`函数，并指定将调用代理到哪个函数。

# 匹配参数

参数匹配器可以让你更明确地知道什么时候你想伪造什么，或者你希望收到什么样的呼叫。

## ts-mockito

使用`ts-mockito`，您可以使用像`anything`和`between`这样的函数来匹配参数。

*我还没有找到在* `*ts-mockito*` *中指定自定义 lambda 来表达自定义匹配器的方法——如果你知道的话，请在评论部分告诉我。*

## 类型起订量

几乎和`ts-mockito`一样，但是支持 lambdas。

## 替代品. js

在这里，语法清晰明了。一个很好的小细节是，即使在您键入时，也一直有强类型，有完整的类型推断(它甚至知道`Arg.is`函数中的`x`是什么类型)。

# 呼叫验证产生的错误

如果你想让你的假方法做别的事情，代理调用可能是有用的。

## ts-mockito

`ts-mockito`显示方法名称和参数以及预期的调用次数，但不显示记录的总调用次数。

上面的代码抛出:

> 应该至少调用 1 次“add(strictEqual(1)，strictEqual(2)”。但已被调用 0 次。

## 类型起订量

对于`typemoq`来说也是一样，除了它还显示一个电话记录列表

上面的代码抛出:

> 应至少调用 real calculator . add(it . is value(1)，it . is value(2))1 次，调用了 0 次
> 
> 已配置的设置:
> real calculator . add(it . is value(1)，It.isValue(2))
> 
> 执行的调用:
> RealCalculator.add(3，4)

## 替代品. js

对于`substitute.js`,您使用`mimicks`函数并指定将调用代理到哪个函数。

上面的代码会抛出:

> 应使用参数[1，2]对方法 c 进行一次或多次调用，但未收到任何此类调用。
> 收到的对方法 c 的所有调用:
> - > 1 个带有参数[3，4]的调用

# 限制

以上看起来都挺好的吧？但是像其他框架一样，`substitute.js`也有局限性。

下面列出了其他框架能做而`substitute.js`不能做的事情，以及不支持它的原因。

## 部分模拟

如果你仅仅依赖于几个伪造的方法，部分模仿通常是不好的。通常在测试时，你想要伪造所有的东西或者几乎所有的东西。

虽然`substitute.js`不支持部分模仿，但是您可以显式代理您想要从真实实例中“模仿”的每个方法。

## 呼叫订单验证

外界(使用你的单元的公共接口)不应该关心你的单元调用它的依赖项的方法的顺序。我认为这是一种反模式，这也是它不被支持的原因。

## 更多？

是否有更多的例子是`substitute.js`做不到而其他人能做到的？请在评论中告诉我，我会添加它或者解释为什么它没有被添加到框架中。

试图使你 99%情况下使用的伪装操作变得简单，但是如果你愿意牺牲好看的语法，它仍然应该足够灵活以覆盖复杂的情况。

# `Getting substitute.js`

下面是安装`substitute.js`所需的命令。注意，它需要 [TypeScript](https://hackernoon.com/tagged/typescript) 3 或更高版本。

`npm install @fluffy-spoon/substitute --save-dev`

**GitHub:**https://github.com/ffMathy/FluffySpoon.JavaScript.Testing .作伪
https://www.npmjs.com/package/@fluffy-spoon/substitute[T21](https://www.npmjs.com/package/@fluffy-spoon/substitute)