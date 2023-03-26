# 你不知道的 7 件事。网络代码

> 原文：<https://medium.com/hackernoon/7-things-you-didnt-know-about-net-code-a7acf4b2c455>

这是我在长时间的工作、与同事的讨论和啊哈时刻中发现的一些事情(没有特定的顺序)。我想和大家分享一下，如果你不知道其中的一些甚至全部，不要感到羞愧。在某些情况下，我花了很多年才了解他们。所以拥抱它们，它会让你变得更好。NET 开发者。

![](img/f18d06f09726b09ef9751dadcd04af94.png)

Geeky stuff

## 第一:输出参数总是改变值

下面是一些简单的代码和一个简单的问题:

```
int testParameter = -1;Int32.TryParse(“abc”, out testParameter);
```

TryParse 执行后， *testParameter* 的值会是多少？

很容易自己尝试，只需将它放入控制台应用程序并显示 testParameter 的值。如果你认为它是-1，那你就错了。即使 TryParse 不会成功，int 的值也会变成默认值，所以会是 0(零)。MSDN 是这样说 out 参数的:[这个参数是未初始化传递的；结果中最初提供的任何值都将被覆盖。](https://msdn.microsoft.com/en-us/library/f02979c7(v=vs.110).aspx)

## 第二:枚举可以有扩展方法

我们在代码中有一个很大的枚举，包含了变量的可能值。我不会详细说明为什么我们需要它，或者它是否是我们的最佳选择，但这里的情况并非如此。假设它看起来像这样(相信我，它更大，这只是一个更小的类似例子):

```
enum Day {Sat=1, Sun, Mon, Tue, Wed, Thu, Fri};
```

现在想象一下，如果您想要查看是否是周末，您的代码将类似于以下代码:

```
if (dayParameter == Day.Sun || dayParameter == Day.Sat) ….
```

所以我创建了一个叫 IsItWeekend(dayParameter)的方法。然后一个同事说，为什么我们不创建一个扩展方法，而不是在我们可能需要的每个类中都有上面的方法？

老实说，我认为不可能在 Enums 上创建一个扩展方法，但是两天后我回来对他说:‘对不起，我错了，这是可能的’。我发现它真的很强大，因为最常用的替代方法是创建一个助手类，而我一点也不喜欢它们。我不会详细介绍如何创建这样一个扩展方法，因为有一篇关于 MSDN 的非常好的文章。我相信更重要的是记住这是可以做到的。

## 第三:对不同的项目使用 HashSet

我在许多项目中发现了很多这样的代码:

```
List<string> uniqueItems = new List<string>();
if(!uniqueItems.Contains(parameter))
{
   uniqueItems.Add(parameter);
}
```

这有什么问题吗？它可以工作，但是 Contains 在列表上执行线性搜索，这是 O(n)。如果您最终有许多这样的问题(通常是大量的 CPU 使用)，这可能会转化为生产中的性能问题。直到。NET 3.0 人们创建字典时，将键集合用于不同的列表，并将值设置为 NULL。但是自从。NET Framework 3.5 我们有了一个新类叫做 [HashSet < T >](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.hashset-1?view=netframework-4.7.1) 。这是一个没有任何重复元素的集合，[包含的方法是 O(1)操作](https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.hashset-1.contains)。所以我们的代码很快就变成了:

```
HashSet<string> uniqueItems = new HashSet<string>();
if(!uniqueItems.Contains(parameter))
{
   uniqueItems.Add(parameter);
}
```

此外，我们可以删除 Contains 调用，因为如果元素已经存在，Add 不会添加它，它只返回 false(如果要添加，则返回 true)。所以代码又变成了这样:

```
HashSet<string> uniqueItems = new HashSet<string>();
uniqueItems.Add(parameter);
```

## 第四:许多值类型不存在于堆栈中

*值类型分配在堆栈上，引用类型分配在堆上。*这就是我们学到的，这就是我们在采访中说的，我们侥幸逃脱了。可能很多。针对初学者的 NET 书籍是这么说的，所以我们从 C#的第一步开始阅读。但这是错误的，这只是一个神话，有这么多的值类型保存在堆上，这里有两个重要的例子说明这种情况何时发生。

*   如果值类型是一个对象的实例变量，那么它被保存在堆上。简单是因为这是整个对象所在的地方，所以这也是值类型被分配的地方。
*   另一种情况是静态变量:在堆上分配一个值类型的静态变量。这是有意义的，因为这个变量只有一个实例。

你可能会问，值类型和引用类型有什么区别？这非常简单:值类型通过值传递(即使它是引用类型的实例变量)，而引用类型通过引用传递。这意味着下次面试时你可以给出更准确的答案。

关于值类型和引用类型的更多细节，这里有 Jon Skeet 的一篇很棒的文章。

## 第五:回应。重定向进程外会话

这是我的最爱之一，因为我在 2014 年左右发现了它，当时我们想更改现有的应用程序以使用进程外会话。我们的目标是支持多个服务器，但我们不想使用粘性会话——我们部署在 AWS 上，因此使用粘性会话意味着失去自动伸缩的所有优势。我们确实从脱离会议进程和使用响应中学到了很多。重定向是结果之一。假设我们有这样一些简单的代码:

```
Session[“FlowId”] = Guid.NewGuid();
//more code goes here
Response.Redirect(Url.Action(“Account”, “Login”));
```

当会话正在进行时，这可以很好地工作，但是当它退出进程时，会话["FlowId"]项将不会被保存。这是因为反应。Redirect 抛出一个 ThreadAbort 异常，因此线程永远不会到达将会话对象保存在外部存储中的部分。当在进程中使用会话时，所有的更改都是当场完成的，这意味着抛出异常不会影响保存。

那么，让它工作的解决方案是什么呢？简单，回应。Redirect 有一个接受第二个参数 endResponse 的重载，只需传递 false，就不会引发异常。[甚至因为性能原因建议设置为 false】。](https://msdn.microsoft.com/en-us/library/a8wa7sdt(v=vs.110).aspx)

```
Session[“FlowId”] = Guid.NewGuid();
//more code goes here
Response.Redirect(Url.Action(“Account”, “Login”), false);
```

## 第六:泛型类型创建不同的类

我们有以下课程:

```
public class GenericCache<T>
{
     public static int ctorCalls = 0;
     private List<T> list;
     public GenericCache()
     {
         ctorCalls++;
         list = new List<T>();
     }
 }
```

仅仅出于教育目的，这个实现是相当愚蠢的(所以不要问我为什么 ctorCalls 是一个公共字段)。但是在一个大的应用程序中，这样的类是很常见的。总要有一个缓存类(这样你就可以节省昂贵的 I/O 调用)，它变得通用只是时间问题(因为你会在很多类中使用它)。

```
GenericCache<string> genericCache = new GenericCache<string>();
GenericCache<int> genericCacheInt = new GenericCache<int>();
Console.WriteLine(GenericCache<DateTime>.ctorCalls);
```

在上面的几行中，通用缓存类的构造函数被调用了两次，然后我们希望将 ctorCalls 的值打印到控制台。

那么它会显示什么价值呢？如果你说 2，那你就错了。这是 generic 在幕后做的事情:GenericCache <string>将是与 GenericCache <int>和 GenericCache <datetime>不同的类型(类)。这意味着静态变量 ctorCalls 将有 3 个实例，每个泛型类型一个。因此显示在控制台上的值将是 0。</datetime></int></string>

## 第七:逻辑运算的顺序(与和或)

以一个简单而有力的结尾。如果处理不当，这会给你带来很多麻烦: *& &(和)比||(或)*更强大。让我们举几个例子来看看这意味着什么，以及它如何影响您的代码:

```
bool result;result = (true || true) && false; //this gives falseresult = true || (true && false); //this gives trueresult = true || true && false;// what will be the result of this?
```

l̵i̵k̵e̵̵i̵̵s̵a̵i̵d̵̵a̵b̵o̵v̵e̵̵a̵n̵d̵̵i̵s̵̵m̵o̵r̵e̵̵p̵o̵w̵e̵r̵f̵u̵l̵̵t̵h̵a̵n̵̵o̵r̵,̵̵w̵h̵i̵c̵h̵̵m̵e̵a̵n̵s̵̵i̵t̵̵w̵i̵l̵l̵̵b̵e̵̵r̵u̵n̵̵f̵i̵r̵s̵t̵,̵̵e̵v̵e̵n̵̵i̵f̵̵i̵t̵̵i̵s̵̵t̵h̵e̵̵s̵e̵c̵o̵n̵d̵̵o̵p̵e̵r̵a̵t̵i̵o̵n̵.̵̵s̵o̵̵t̵h̵e̵̵t̵h̵i̵r̵d̵̵c̵a̵s̵e̵̵a̵f̵t̵e̵r̵̵w̵e̵̵d̵o̵̵t̵h̵e̵̵a̵n̵d̵̵o̵p̵e̵r̵a̵t̵i̵o̵n̵̵w̵i̵l̵l̵̵b̵e̵c̵o̵m̵e̵:̵̵r̵e̵s̵u̵l̵t̵̵=̵̵t̵r̵u̵e̵̵|̵|̵̵f̵a̵l̵s̵e̵̵s̵o̵̵i̵t̵̵w̵i̵l̵l̵̵g̵i̵v̵e̵̵y̵o̵u̵̵t̵r̵u̵e̵.̵̵s̵o̵̵i̵t̵̵i̵s̵̵e̵x̵a̵c̵t̵l̵y̵̵t̵h̵e̵̵s̵a̵m̵e̵̵w̵i̵t̵h̵̵t̵h̵e̵̵s̵e̵c̵o̵n̵d̵̵o̵p̵e̵r̵a̵t̵i̵o̵n̵.̵̵n̵o̵w̵̵m̵y̵̵s̵u̵g̵g̵e̵s̵t̵i̵o̵n̵̵i̵s̵̵t̵o̵̵a̵l̵w̵a̵y̵s̵̵u̵s̵e̵̵p̵a̵r̵e̵n̵t̵h̵e̵s̵i̵s̵̵f̵o̵r̵̵t̵h̵e̵̵o̵r̵d̵e̵r̵̵o̵f̵̵o̵p̵e̵r̵a̵t̵i̵o̵n̵s̵̵t̵o̵̵l̵o̵o̵k̵̵m̵o̵r̵e̵̵c̵l̵e̵a̵r̵.̵

上述解释不正确，结果将为真，但不是因为运算符优先，而是因为应用了短路。它知道第一个值为真，所以不会执行||和&&操作符，它只是停止并返回真。

在我找到一个更好的例子之前，我会在这里放上与 C#操作符的链接，也就是说 AND 条件参数在 OR 之上。感谢 [@kabtoffe](http://twitter.com/kabtoffe) 指出这一点。

就是这些，希望对你有帮助。如果你知道其他有趣的事情，请留下你的评论。NET/C#代码。

*如果你喜欢这个并且想要更多的精神食粮 y* [*你也可以注册我的软件开发者时事通讯。NET 技术*](https://mailchi.mp/c6f9b64a7def/dotnet-newsletter) *。我计划从 2018 年 6 月开始，每月发送两次。*