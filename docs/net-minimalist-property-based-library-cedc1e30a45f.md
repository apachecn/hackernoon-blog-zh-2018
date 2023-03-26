# 。基于. NET 极简属性的库

> 原文：<https://medium.com/hackernoon/net-minimalist-property-based-library-cedc1e30a45f>

在过去的不同帖子中，我们探讨了为什么测试对我们如此重要。此外，我们还看了 ScalaCheck，它是 Scala 编程语言的一个基于属性的测试框架。

因为我们是探险家，我们已经开始了一个新的冒险，创建一个小的，但功能和易于使用的基于属性的库。基于 NET 的语言。

# 动机

我们想要一个与我们当前的测试框架一起工作的库，在我们的例子中，就是 xUnit。通过快速浏览 NuGet，我们可以找到一些与我们目标相同的框架，但是它们需要学习整个框架。此外，为了使用它，您可能需要放弃您最喜欢的测试框架。由此产生了许多其他问题，例如为您的 IDE 提供一个测试运行程序，等等。

我们的库并不打算改变我们今天所做的任何事情，而是允许我们在常规测试中使用基于属性的测试。

# 核心概念

最重要的部分是我们所谓的 ***Gen < T >*** ，它代表了一种生成 T 类型的 ***有界*** 数据的方法

最简单的生成器可以通过以下方式创建:

```
**var** gen = Gen<**int**>.Empty();Assert.Empty(gen.Generate());
```

注意，我们使用的是 ***xUnit。断言*** 所以我们根本没有引入任何断言 API。

现在，让我们看看创建生成器的其他一些方法。

## 单一项目生成器

```
**var** once = Gen<**int**>.Once(5)Assert.Single(Gen<**int**>.Once(5).Generate());
```

## 重复生成器

```
**var** repeat = Gen<**int**>.Repeat(5, 20);

Assert.True(repeat.Generate().Count() == 20);
Assert.True(repeat.Generate().ToList().TrueForAll(x => x == 5));
```

在这里，我们说的是我们要生成 ***5*** 多达 ***20*** 次的值。

需要注意的是，调用`.Generate()`将总是返回相同的数据集。

## 可枚举生成器

一个更有趣的生成器是从***IEnumerable<T>***创建的。

```
**var** enumerableGen = Gen<**int**>.FromEnumerable(Enumerable.Range(1, 100));

Assert.True(enumerableGen.Generate().Count() == 100);
```

通过执行以下操作可以生成一个简单的版本:

```
**var** enumerableGen = Enumerable.Range(1, 100).ToGen();

Assert.True(enumerableGen.Generate().Count() == 100);
```

发电机本身不能做太多。但是，我们的想法是将它们用作值生成器，以便测试它们的一些属性。

## 生产者

很多时候我们需要一种更健壮的方法来生成数据。一部 ***制片人*** 的本意正是如此。

```
**public interface** IProducer<**out** T> : IEnumerable<T>
{

}
```

可以看到， ***IProducer*** 只是一种生成同类型*值序列的方式。*

*用于生成 ***int*** s 的简单 ***生产者*** 可以是:*

```
***public class** IntProducer : IProducer<**int**>
{
    **public** IEnumerator<**int**> GetEnumerator()
    {
        **while** (**true**)
        {
            **yield return new** Random().Next();
        }
    }

    IEnumerator IEnumerable.GetEnumerator()
    {
        **return** GetEnumerator();
    }
}*
```

*需要注意的是 ***IntProducer*** 会生成一个*无界的 ***int*** s 的集合，我们需要从 ***Producer*** 中获取一个生成器(***Gen<T>***)，这样我们就可以对其进行强制绑定。**

**为此，我们可以使用以下方法:**

```
****var** intGenerator = (**new** IntProducer()).ChooseFrom()**
```

**通过将`.ChooseFrom(howMany)`应用于任何 ***生产者*** ，我们创建了一个***Gen<>****，它将 ***生产者*** 强制上界。***

**另一个例子可能是***string producer***:**

```
****public class** StringProducer : IProducer<**string**>
{
    **public** IEnumerator<**string**> GetEnumerator()
    {
        **while** (**true**)
        {
            **yield return new** Random().Next().ToString();
        }
    }

    IEnumerator IEnumerable.GetEnumerator()
    {
        **return** GetEnumerator();
    }
}**
```

**同样，我们可以从它得到一个***Gen<T>***通过做:**

```
****var** stringGenerator = (**new** StringProducer()).ChooseFrom(20)**
```

**我们还包含了一种通过使用 ***TupleProducer < T，K >*** 来生成 ***(T，K)*** 形式的元组的方法。**

```
****public class** TupleProducer<T, K> : IProducer<(T, K)>
{
    **private readonly** IProducer<T> **_tProducer**;
    **private readonly** IProducer<K> **_kproducer**;

    **public** TupleProducer(IProducer<T> tProducer, IProducer<K> kproducer)
    {
        **_tProducer** = tProducer;
        **_kproducer** = kproducer;
    }

    **public** IEnumerator<(T, K)> GetEnumerator()
    {
        **var** tenum = **_tProducer**.GetEnumerator();
        **var** kenum = **_kproducer**.GetEnumerator();

        **while** (tenum.MoveNext() && kenum.MoveNext())
        {
            **yield return** (tenum.**Current**, kenum.**Current**);
        }
    }

    IEnumerator IEnumerable.GetEnumerator()
    {
        **return** GetEnumerator();
    }
}**
```

**同样，我们可以使用`.ChooseFrom`从它那里得到一个生成器。**

## **ForAll & Any**

**一旦我们创建了一个生成器，我们就可以使用`.ForAll`和`.Any`作为存在量化来证明一个属性对于所生成的值是成立的。**

**让我们通过测试以下函数来看一些例子:**

```
****T** id<**T**>(**T** x) => x;**int** sqrt(**int** x) => x * x;**string** concat(**string** a, **string** b) => a + b;**
```

**注意这是完全有效的 **C#** 代码，这里我们只使用方法体语法，在更传统的 **C#** 中等价的将是:**

```
****T** id<**T**>(**T** x) {    **return** x;
}
...**
```

**让我们继续测试这些功能。**

```
**[Fact]
**public void** Identity()
{
    Assert.True(
       (**new** IntProducer())
         .ChooseFrom()
         .ForAll(x => id(x) == x));
}**
```

**`.ForAll`收到我们要陈述的关于价值观的陈述。**

**在 ***恒等式*** 的情况下，我们要证明`for all x, id(x) == x`。**

***将为生成器上的每个单个值评估属性。***

**现在，我们来测试一下 ***sqrt:*****

```
**[Fact]
**public void** SomeSquares()
{
    Assert.True(
      Gen<**int**>
         .FromEnumerable(Enumerable.Range(1, 100))
         .Any(x => sqrt(x) == x));
}**
```

**在这种情况下，属性`sqrt(x) == x`仅对值 ***1*** 有效。因为我们使用的是`.Any`，所以这个测试也是一个通过测试。当我们使用`.Any`时，属性至少保持一个值就足够了。**

**最后可以测试 ***concat*** 。**

```
**[Fact]
**public void** Concat()
{
   **var** stringProducer = **new** StringProducer(); 
   **var** producer = **new** TupleProducer<**string**, **string**>(stringProducer, stringProducer);

   producer
       .ChooseFrom()
       .ForAll(t => 
          concat(t.**Item1**, t.**Item2**).StartsWith(t.**Item1**) 
          && 
          concat(t.**Item1**, t.**Item2**).EndsWith(t.**Item2**))
       .Should()
       .BeTrue();
}**
```

**这个非常有趣，因为我们一起使用了 ***字符串生成器*** 和 ***字符串生成器*** 。**

**在这里，我们生成随机字符串，将它们连接在一起，并证明属性:`for all a, b that are string, a + b == a* && a + b == *b`。我们的库将选择由 ***StringProducer*** 产生的 100 个字符串(有界集合或 ***Gen < T >*** )，并测试该属性对它们都成立。**

***注意我们是通过****fluent assertion*******API****来做断言，而不是简单的* ***断言*** *。然而，这只是为了表明我们的库并不局限于一种测试框架或断言风格。****

# **结局**

**尽管这是一个非常小的库，但它很好地完成了它的工作，并允许我们为*带来一个简化的基于属性的测试。网*。**

**您可以从 NuGet Gallery 获得它，从今天开始使用它。**

```
**PM> Install-Package NetChecker -Version 1.0.0**
```

**在 [***GitHub NetChecker 项目***](https://github.com/anicolaspp/NetChecker) ***上随时欢迎投稿。*****

***如果你想了解更多关于基于属性的测试，你可以阅读* [***用 ScalaCheck 探索基于属性的测试(简单例子)***](https://hackernoon.com/exploring-property-based-testing-with-scalacheck-simple-examples-bcdc34600810)**