# 像对待代码一样对待你的测试

> 原文：<https://medium.com/hackernoon/treat-your-tests-like-your-code-64a6e3841268>

![](img/4f7767e896c8c72c026491faf5eb0277.png)

作为一名多年的[开发者](https://hackernoon.com/tagged/developer)，我注意到了一个模式。这种模式与单元测试有关。尤其是那些写单元测试的人。我注意到的是，很多开发人员并不像对待他们的(生产)代码那样对待他们的单元测试。也就是说，他们低估了单元测试的工程化程度，最终得到了无法调试的不可读的测试，并且没有人理解什么是测试[测试](https://hackernoon.com/tagged/testing)。

# 使用构造函数

如果您在生产代码中使用 DI(依赖注入),您的测试代码通常需要大量的设置。我们都看到了这种混乱。new x()；new y()；new z()；最终，您得到的设置只需要半个页面。但是，幸运的是，有一些模式可以解决这个问题。我喜欢在尽可能少的地方使用构造函数的规则。这意味着，你应该钻研你的模式书，从使用构建器模式开始。

```
public class RepositoryBuilder
{
    public RepositoryBuilder()
    {
        DbConnection = new MockDbConnection();
    }public IDbConnection DbConnection { get; set; }public Repository Build()
    {
        return new Repository(DbConnection);
    }
}
```

你不必遵循上面所说的，但基本上，这将抽象出构造函数并为你设置一些默认值。如果需要测试 DbConnection 和存储库之间的特定行为，可以自己设置 DbConnection。现在您的测试将如下所示，您甚至可以更进一步。

```
[TestFixture]
public class RepositoryTests
{
    [Test]
    public void CanGetDataFromRepository()
    {
        var repository = new RepositoryBuilder().Build();
        var expectedData = CreateExpectedData();var data = repository.GetData();data.ShouldBe(expectedData);
    }
}
```

你可以看到代码是可读的。不再有构造函数的复制+粘贴，这意味着当你重构的时候——会很容易，因为你只需要改变一个地方。现在，我并不是说这个规则是 100%正确的，但是当你可以将你的测试从实现中分离出来一点点的时候，它会让你的生活变得更加容易。

# 保持简单愚蠢

不要试图在一个单元测试中测试多个东西。如果你的方法不止做一件事，那么也许是时候考虑重构这个方法了。但是不用担心！救援马上就到。您可以编写多个单元测试，在您的方法中测试不同的东西。也许你有一个装饰器，你想测试你添加的东西是否有效，你是否还在调用你正在装饰的类。没关系！分两次单元测试就行了。

# 嘲弄的

我喜欢嘲笑班级的行为。比起创建存根类，我更喜欢它。外面有成吨的嘲讽框架。对于你选择的语言，可能会有一个嘲讽的框架。对于 C#，我喜欢使用 Moq——但是如果有人能推荐一个更好的模拟框架——我会支持它。对于 JS——你可以从我之前的帖子中看到我喜欢 Jest。它能做我们需要的一切。

# 流畅的语法/方法链接

我喜欢断言和构建器的流畅语法。它有助于创建一个故事，任何阅读测试的人都可以跟随和理解，而不必查看代码。但是你可能会走得太远——有时候就是这样。我见过人们为“给定/何时/然后”现象而疯狂。一个例子:

```
[Test]
public void DocusignTest()
{
    Given.ADocuSignService
        .ThatReturnsInvalidTempResult().GetDocuSignURL(Given.AHotelId)
        .ShouldNotBeNullOrEmpty();
}
```

是的:它是可读的

*   是的:可以理解。
*   是的:很短。

然而，缺点是:

*   没有:它在测试什么？
*   否:我在哪里调试？
*   否:我无法浏览这个测试并了解其内部工作原理。我必须仔细检查每一种方法，找出每一种方法的作用。

我更喜欢“安排、行动、断言”的语法。你做你设置(安排)。您调用有问题的方法(act)。你决定真相(断言)。

```
[Test]
public void DocusignTest()
{
    // arrange
    var service = Given.ADocuSignService.ThatReturnsInvalidTempResult();// act  
    var result = service.GetDocuSignURL(Given.AHotelId);// assert
    result.ShouldNotBeNullOrEmpty();
}
```

我觉得这比第一个例子更容易理解。也许你不知道？让我知道你如何组织你的测试。

我希望你能从这篇简短的介绍中学到一点东西来改进你的测试。总而言之，使用构建器(或类似的东西)将你的测试从你测试的对象的变化中分离出来。这会让你的重构变得更加容易。保持你的测试简单。一次测试一件事。试试嘲讽！这将节省您创建存根。最后，尝试流畅的语法——只是不要太过火。

*原载于 2018 年 6 月 18 日*[*www . alexaitken . NZ*](https://www.alexaitken.nz/blog/treat-your-tests-like-your-code/)*。*