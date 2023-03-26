# 为什么我们选择 Kotlin 来创建 Android 应用

> 原文：<https://medium.com/hackernoon/why-we-choose-kotlin-for-creating-android-apps-46030b10d19c>

![](img/e2d84511126dcf1856e2309595794db6.png)

Me, trying to convince Android team to use Kotlin

大约两年前，我们的团队决定尝试一些新东西:完全用 JetBrains 的一种 T2 编程语言 Kotlin T1 开发一个商业应用程序。当时，我们有过 Kotlin 的经验，但规模要小得多:将应用程序的某些部分转换成一种新语言，或者在自己喜欢的项目上尝试。然而，用新的编程语言开发商业应用程序会带来一些困难:

*   我们深深扎根于基于 Java 的 Android 开发。切换到 Kotlin 相当困难，尤其是对于没有函数式编程经验的开发人员来说
*   有些东西就是不行。匕首在盒子外面并不好用

所有这些都可能导致错过交付日期和应用程序的稳定性问题。

一个人应该有强烈的动机去完成转变。我们的动机是相信**科特林将成为 Android 平台开发的游戏改变者。这很有趣。**

保持 Kotlin 参考开放，我们开始开发[选民应用程序。Kotlin 是一种具有 100% Java 互操作性的 JVM 语言，如果你熟悉 Java，学习 Kotlin 很容易。然而，如果你想充分利用这种语言，理解函数式编程的概念是必不可少的。](https://uptech.team/#portfolio)

> 学习函数式编程需要一段时间。所以要有耐心。

而函数式编程并不容易。至少在开始的时候。直到它变得有趣。我强烈建议参加 Martin Ordersky 的[系列课程“Scala 中的函数式编程”。Scala 有时让人不知所措，但它很好地概述了新的函数式编程思维。你可以把 Kotlin 看作是 Scala 的一个更简单的版本。](https://www.coursera.org/specializations/scala)

# 是什么让我们转向科特林一边

## 功能性编程风格

Kotlin 与 Java 100%互操作。此外，Kotlin 是一种函数式语言。后者允许编写更优雅、更有表现力的代码。

1.  **功能纯度**

纯函数的概念(一个没有副作用的函数)是最重要的函数概念，它允许我们极大地降低代码的复杂性并摆脱大多数可变状态。

> 在 Javascript、Java、C#等命令式编程语言中，副作用无处不在。这使得调试非常困难，因为变量可以在程序中的任何地方更改。那么当你因为一个变量在错误的时间被改成了错误的值而出现 bug 的时候，你去哪里找呢？到处都是？这可不好。

请注意我们是如何在不改变数据内容的情况下操作数据的。

**2。高阶函数**

> 高阶函数要么将函数作为参数，要么作为返回函数，要么两者都是。

高阶函数无处不在。您只需将函数传递给集合，使代码易于阅读。读起来像简单的英语。是不是很奇妙？

让我们设想一种情况，我们想要统计不同类型的未读消息的数量。典型的方法是:

如您所见，随着新需求的引入，代码变得不可读和不可管理。让我们看看如何用高阶函数解决这个问题:

我们可以想象这样的用例，我们想要参数化`fold`函数参数，比方说，用于计算未读消息的乘积。

使用高阶函数的另一个例子是用简单的高阶函数替换大量的监听器:

```
BillingView : LinearLayout {var billingChangeListener: (() -> Unit)? = null
...}... // in an activity far, far away
billingView.billingChangeListener { updateUI() }
```

**3。不变性** 不变性使代码更容易编写、使用和推理(类不变量建立一次，然后不变)。你的应用组件的内部状态会更加一致。Kotlin 通过引入`val`关键字和 Kotlin 集合来增强不变性，缺省情况下 kot Lin 集合是不可变的。一旦`val`或集合被初始化，你就可以确定它的有效性。(关于`val`关键词的更准确定义，见 UPD)。

```
data class Address(val line1: String, val city: String)**val** items = listOf(Address("242 5th St", "Los Angeles"),   Address("Dovzhenka St. 5", "Kiev"))
```

## 零安全

这个语言特性让我们仔细考虑模型类中字段的可空性。以前，您不能确定 DTO 中的字段是否已初始化，@Nullable 和@NotNull 注释会有所帮助，但作用不大。现在，有了 Kotlin，您可以精确地知道什么字段可以为空，什么字段稍后被初始化(例如由 Dagger 注入的字段)，并且您可以对这些字段进行严格的控制。结果？几乎没有`NullPointerExceptions`。(我们内部称`?.`为[鹅](https://pixabay.com/static/uploads/photo/2014/05/26/11/23/swan-354448_960_720.jpg)运算符，因为它看起来像鹅的脖子)

```
brand?.let { badge.enabled = brand.isNewBadge }
// Can also be written as 
badge.enabled = brand?.isNewBadge?:false
```

## 安科

Anko DSL 是一个很棒的库，它极大地简化了视图、线程和 android 生命周期的工作。Github 的描述称 Anko 是“令人愉快的 Android 应用程序开发”,事实也确实如此。

注意，当在 Activity 内部调用`uiThread`时，如果`isFinishing`是`true`，那么这个块将不会执行。我们实际上并不使用这个特性，因为 RxJava 处理我们应用程序中的所有线程，但这是一个很好的特性。

**用 Anko 代替 XML** 。虽然 Anko 还没有准备好取代标准的 Android UI 构建，但有时它非常方便。

如你所见，Anko DSL 允许你在 Android 内置视图旁边使用自定义视图。这就是它比标准 XML 更有优势的地方。

## Kotlin Android 扩展:移除 ButterKnife 依赖

**无聊了没？我打赌你没看就滚动了。在科特林，你不需要这些。您可以通过@id XML 参数引用视图属性，这些属性的名称将与 XML 文件中声明的名称相同。更多信息可在[官方文件](https://kotlinlang.org/docs/tutorials/android-plugin.html)中找到。**

## 其他简洁的功能

1.  **扩展功能&构建器**

`apply`、`let`和扩展函数可以很容易地用来创建优雅的构建器。

**2。在最初的几天里，你经常会遇到一个问题:你不知道如何用 Kotlin 编写一个相当简单的 Java 表达式。
一个简单的窍门是用 Java 写一段代码，然后粘贴到 Kotlin 文件中。多亏了 JetBrains 里的人，它被自动转换成 Kotlin。Hacky，但工作像一个魅力！**

**3。摆脱不必要的依赖** Kotlin 替代了很多第三方库，比如 ButterKnife、Google Autovalue、Retrolambda、Lombok 和一些 RxJava 代码。

# 摘要

作为一个软件开发团队，我们面临的主要挑战是交付优秀的产品和有效地完成工作。虽然要开始在 Kotlin 中有效地开发，您需要一些函数式编程的先决知识，但是投入时间学习它确实会有回报。我相信 Kotlin 是对传统 Android 开发的一个重大改进，它允许我们按时交付优秀的应用程序，并且错误少得多。

请随意提问，我们将乐意帮助其他人完成过渡。在下面分享你的想法/评论吧！

> 穿着衣服编程是你能得到的最大乐趣。
> 
> 约翰·古塔格

*UPD:* `*val*` *实际上并不是指‘不可变’，而是指‘只读’。详见* [*本文*](https://artemzin.com/blog/kotlin-val-does-not-mean-immutable-it-just-means-readonly-yeah/) *。*

> 这篇文章最初发表在 [UPTech 团队博客](http://blog.uptech.team)上。关注我们，获取更多关于如何构建优秀产品的文章💪

**参考文献**

1.  [科特林参考](https://kotlinlang.org/docs/reference/)
2.  [所以你想成为一名函数式程序员](/@cscalfani/so-you-want-to-be-a-functional-programmer-part-1-1f15e387e536#.8cmkitum2)
3.  [为什么函数式编程很重要](https://www.cs.kent.ac.uk/people/staff/dat/miranda/whyfp90.pdf)
4.  [用 Java 不可变对象编程的 6 个好处](https://www.linkedin.com/pulse/20140528113353-16837833-6-benefits-of-programming-with-immutable-objects-in-java)
5.  [Anko DSL vs Android XML-First](http://maximomussini.com/posts/anko-vs-android-xml/)
6.  [将应用程序转换成 100% Kotlin 的经验教训](/keepsafe-engineering/lessons-from-converting-an-app-to-100-kotlin-68984a05dcb6#.jkxs39qko)
7.  [结果:选民申请](https://play.google.com/store/apps/details?id=app.voter.xyz&hl=en)。99.8%无崩溃用户。
8.  Kotlin: val 并不意味着不可变，它只是意味着只读