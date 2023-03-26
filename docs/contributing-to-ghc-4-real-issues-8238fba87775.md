# 为 GHC 4 献计献策:现实问题

> 原文：<https://medium.com/hackernoon/contributing-to-ghc-4-real-issues-8238fba87775>

在过去的几周里，我们好好看看 GHC。[我们从](https://mmhaskell.com/blog/2018/6/11/contributing-to-ghc-1-preparation)开始，看看为 GHC 开发准备本地机器所需的步骤。这在 Windows 上是一个特别困难的过程，所以我们把重点放在那里。之后，我们看了一下[为我们自己创建一个开发周期](https://mmhaskell.com/blog/2018/6/18/contributing-to-ghc-2-basic-hacking-and-organization)的基本方法。我们通过改变一个错误消息并观察它在编译器中的表现来验证这一点。上周我们做了一些更复杂的改动。这一周，我们将通过看一些做贡献的基本方法来结束这个系列。

# 证明文件

在任何软件项目中，文档都是一件棘手的事情。在任何给定的时刻，大部分的努力都是为了确保程序正常运行。当你已经理解了代码，你就不需要看文档了。所以诱惑是不要改变任何评论。这意味着文档总是有可能过时。哈斯克尔更容易犯这种错误。我们通过修改、编译和查看哪里出了问题来寻找问题。文档永远不会中断！

有经验的开发人员会记得多修改文档。尽管如此，还是不可避免地会有一些东西从缝隙中溜走。但是对于 GHC 代码库的新手来说，有一个好消息！我们处于寻找文档漏洞的最佳位置，因为我们是最需要阅读它的人！这就是我如何发现我能做出的第一个贡献。

在探索 lexing 类型时，我发现了一个不太有意义的评论。在`compiler/basicTypes/BasicTypes.hs`的顶部，写着:

```
-- There is considerable overlap between the logic here and the logic
-- in Lexer.x, but sadly there seems to be way to merge them.
```

这读起来不太对。从上下文来看，似乎很清楚作者打算写“似乎没有**或**方法来合并它们”。太好了，让我们为此提交一个拉取请求！我们将派生存储库并打开一个拉取请求。因此，我们将创建[fork](https://github.com/jhb563/ghc)，克隆 repo，打开一个新分支，并针对 master 打开一个 pull 请求。

现在有一个有点恼人的问题，那就是 CI 构建现在似乎还没有通过。但是希望[这个公关](https://github.com/ghc/ghc/pull/153)会在某个时候被合并进去。

# 使用 Trac 跟踪问题

当然，GHC 还有更复杂的问题。这是我们想要添加到代码库中的真正特性，以及我们想要修复的错误！要了解那里发生了什么，您需要查看[问题跟踪器](https://ghc.haskell.org/trac/ghc)。GHC 为此使用 Trac，你可以观察列表上的所有问题。它们有标签，这些标签基于它们的版本以及它们的重要性。

这可能是一个很长的列表。我翻阅了许多不同的门票，不知道我能帮上什么忙。那么，你如何能找到一些东西开始呢？首先，你可以订阅 GHC 发展邮件列表。那里的对话会帮助你发现人们在做什么。其次，您可以登录到 [Freenode](https://freenode.net/) 并进入`#ghc`通道。你可以问任何人发生了什么事，你可以在哪里帮忙。幸运的是，在问题列表上还有一个“新来者”的标签。这些是 GHC 开发者强调的问题，对于代码新手来说应该很容易。让我们来看看其中的一个问题。

# 看看真正的问题:中缀模式

从这次狩猎中，我发现[这张票](https://ghc.haskell.org/trac/ghc/ticket/15235)，和`(->)`的中缀值有关。票声称声明的箭头操作符的中缀级别 0 实际上是不正确的。让我们来看看它们是什么意思。

提醒一下，中缀级别表示操作员在确定操作顺序时的优先级。例如，乘法运算符`(*)`比加法运算符`(+)`具有更高的中缀级别。我们可以在每台机器上使用`:info`命令，通过快速的 ghci 会话来确认这些信息。

```
>> :i (+)
…
infixl 6 +
>> :i (*)
…
infixl 7 *
>> 5 + 2 * 3
11 -- Would be 21 if addition were higher precedence
```

现在，当两个算子有相同的中缀水平时，那么我们参考中缀水平的方向。作为一个例子，我们可以比较减法和加法。我们会发现也是`infixl 6`。由于是`infixl`(相对于`infixr`)，我们给左侧操作优先权。这里有一个例子。

```
>> :i (-)
…
infixl 6 -
>> 5 - 2 + 18
21 -- Not (-15)
```

所以让我们看看我们的箭头操作符，我们在定义类型签名时使用它:

```
>> :i (->)
data (->) (a :: TYPE q) (b :: TYPE r) -- Defined . `GHC.Prim`
infixr 0 `(->)`
...
```

这表明这个操作符的中缀级别为 0，我们应该优先考虑右边的内容。然而，提交 bug 的人建议使用以下代码:

```
{-# LANGUAGE TypeOperators #-}module Bug whereimport Data.Type.Equalitytype (~>) = (->)
infixr 0 ~>f :: (a ~> b -> c) :~: (a ~> (b -> c))
f = Refl
```

这里有很多更高层次的概念，所以让我们把它们都分解一下。第一，`(->)`是一个类型运算符，意思是它本身其实就是一个类型。因此，我们可以为它创建一个名为`(~>)`的类型同义词。然后我们可以给这个新的操作符指定任何我们喜欢的中缀层次。在这种情况下，我们将选择与原始操作符`infixr 0`相同的中缀级别。

下一部分创建一个表达式`f`。它的类型签名使用`(:~:)`操作符来表示类型之间的关系相等。这个类型有一个`Refl`构造函数。你唯一需要理解的是我们的每个箭头模式(`(a ~> b -> c)`和`(a ~> (b -> c))`)都是一个类型。并且这些代码应该**只编译**如果那些类型是相同的。

从表面上看，这些类型*应该是相同的。毕竟，这两个操作符都声称是`infixr 0`，这意味着我们在`(:~:)`右边加括号的方式应该与它自然排序的方式相匹配。但是代码**不编译！***

```
>> ghci
>> :l Bug.hs
Bug.hs:11:5: error:
    * Couldn’t match type `a` with `a ~> b`
      `a` is a rigid type variable bound by
        f :: forall a b c. ((a ~> b) -> c) :~: (a ~> ( b -> c))
        At Bug.hs:10:1-38
      Expected type: ((a ~> b) -> c) :~: (a ~> (b -> c))
        Actual type: ((a ~> b) -> c) :~: ((a ~> b) -> c)
    * In the expression: Refl
      In an equation for `f’: f = Refl
    * Relevant bindings include
      f :: ((a ~> b) -> c) :~: (a ~> (b -> c))
        (bound at Bug.hs:11:1)
   |
11 | f = Refl
   |
```

我们可以在“实际类型”一行看到编译器是如何解释`(a ~> b -> c)`的。它优先考虑左派，而不是右派。事实上，如果我们更改类型签名以反映给予`(~>)`的优先级，我们的代码将编译为:

```
f :: (a ~> b -> c) :~: ((a ~> b) -> c)
f = Refl
…
>> ghci
>> :l Bug.hs
Ok, one module loaded.
```

# 修复

对我们来说幸运的是，票里已经提出了解决方案。编译器使用`Fixity`类型表示我们的操作符的中缀级别。我们可以看到我们为一些内置运算符定义了级别的特定位置:

```
negateFixity, funTyFixity :: Fixity
negateFixity = Fixity NoSourceText 6 InfixL -- Fixity of unary negate
funTyFixity = Fixity NoSourceText 0 InfixR -- Fixity of `->`
```

我们想改变函数类型操作符的固定性。我们应该让它看起来像是`-1`，而不是看起来像是 0，显示这个操作符的较低优先级。注意这个代码指的是我们的 we report。它最终具有较低优先级的实际原因更加复杂。但是让我们做出改变:

```
funTyFixity = Fixity NoSourceText (-1) InfixR
```

# 测试我们的变化

这似乎应该是一个简单的测试变化。首先，我们将再次`make`我们的代码。然后我们将启动 GHCI 并询问关于`(->)`的信息。但是当我们尝试时，这似乎不起作用！

```
> make
> ghci
...
>> :i (->)
data (->) (a :: TYPE q) (b :: TYPE r) -- Defined . `GHC.Prim`
infixr 0 `(->)`
...
```

这里的问题是，重新制作不会导致 GHCI 使用我们新的本地构建版本的 GHC。即使在从`ghc/inplace/bin`目录中使用`ghci.exe`时，它仍然不能解释这种变化。解决这个问题的方法是，不使用`ghci`，我们可以将`--interactive`标志传递给对`ghc`的普通调用。所以我们需要这样的东西:

```
~/ghc/inplace/bin/ghc-stage2.exe -o prog --interactive Main.hs
```

这将打开 GHCI 提示符，加载我们的主模块。现在，当我们继续前进并获得信息时，我们将看到它的工作！

```
> ~/ghc/inplace/bin/ghc-stage2.exe -o prog --interactive Main.hs
...
>> :i (->)
data (->) (a :: TYPE q) (b :: TYPE r) -- Defined . `GHC.Prim`
infixr -1 `(->)`
...
```

因此，我现在将发出一个简单的 pull 请求来解决这个问题。你可以在这里跟踪进度[。随着进展的深入，我会更新这篇文章。](https://github.com/ghc/ghc/pull/158)

# 结论

我们为 GHC 做贡献的系列报道到此结束！外面有很多 bug，所以不要害怕看一看任何标有`newcomer`的东西。只要确保看一看已经在票上发生的讨论就行了！

要了解更多关于 Haskell 的内容，你可以阅读我们的 [Liftoff 系列](https://www.mmhaskell.com/liftoff)(针对初学者)或者我们的 [Haskell Web 系列](https://www.mmhaskell.com/haskell-web)，如果你已经熟悉这种语言的话。你也可以下载我们的 [Haskell 初学者清单](https://www.mmhaskell.com/beginners-checklist)来开始！或者你可以看看我们的[生产清单](https://www.mmhaskell.com/production-checklist)如果你想要一些更先进的项目的想法。