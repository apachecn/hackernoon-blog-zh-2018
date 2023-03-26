# 跳跃 II:使用更多的单子

> 原文：<https://medium.com/hackernoon/making-the-jump-ii-using-more-monads-bce44a7c3550>

![](img/34c1eefc2f4e7db343789be3602a1880.png)

答[几周前](https://www.mmhaskell.com/blog/2018/7/23/making-the-jump-advancing-past-beginner-haskell)，我们讨论了一些重要的步骤，以超越 Haskell 的“初学者”阶段。我们学习了如何组织您的项目以及如何找到相关的文档。本周，我们将继续关注另一个我们可以取得巨大进步的地方。我们将探索如何扩大我们的单子用法的词汇。

单子是 Haskell 的重要组成部分。除非你知道如何合并它们的一元函数，否则你不能使用很多库。这些函数通常涉及到该库定制的 monad。当你第一次开始时，可能很难知道如何将这些单子合并到程序的其余部分。

在本文中，我们将重点关注一种许多单子和库使用的特定模式。我把这种模式称为“运行”模式。通常，你会使用一个名字类似于`runXXX`或`runXXXT`的函数，其中`XXX`是单子的名字。这些函数总是将一元表达式作为它们的第一个参数。然后它们还会获取一些其他的初始化信息，最后返回一些输出。这个输出可以是纯形式的，也可以是你已经在使用的不同的单子，比如`IO`。我们将首先看看这是如何与`State`单子一起工作的，然后再看看其他一些库。

一旦抓住了这个话题，看起来就很简单了。但是我们中的很多人最初学习单子时都有一个不好的心理模型。例如，我了解到的关于单子的第一件事是它们有副作用。因此，你只能从有相同副作用的地方调用它们。这适用于木卫一，但不适用于其他单子。所以即使现在看起来很明显，我一开始也很难理解这个想法。但是让我们开始看看这种模式的一些例子。

要更深入地了解单子，请查看我们关于[函数数据结构](https://www.mmhaskell.com/monads)的系列文章！我们从学习更简单的东西开始，比如函子。然后我们最终发展到单子，甚至单子变形金刚！

# “运行”的基础:状态单子

先来回忆一下`State`单子。这个 monad 只有一个类型参数，我们可以将这个类型作为一个全局读/写状态来访问。下面是一个用`State`单子编写的函数示例:

```
stateExample :: State Int (Int, Int, Int)
stateExample = do
  a <- get
  modify (+1)
  b <- get
  put 5
  c <- get
  return (a, b, c)
```

如果这个函数令人困惑，你应该看看 `[State](http://hackage.haskell.org/package/mtl-2.2.2/docs/Control-Monad-State-Lazy.html)`的[文档。它至少会显示相关的类型签名。首先我们读取初始状态。然后我们用一些函数修改它。最后我们彻底改变它。](http://hackage.haskell.org/package/mtl-2.2.2/docs/Control-Monad-State-Lazy.html)

在上面的例子中，如果我们的初始状态是 1，我们将返回`(1,2,5)`作为结果。如果初始状态是 2，我们将返回`(2,3,5)`。但是假设我们有一个纯函数。我们如何调用我们的状态函数？

```
pureFunction :: Int -> Int
pureFunction = ???
```

答案是`runState`功能。我们可以检查文档并找到它的类型:

```
runState :: State s a -> s -> (a, s)
```

这个函数有两个参数。首先是一次`State`行动。我们将把上面的函数作为这个参数来传递！第二个是初始状态，这是我们将如何配置它。那么结果就是纯粹的。它包含我们的结果，以及状态的最终值。这里有一个例子，我们可以在纯函数中给出这个一元表达式。我们将从一个`where`子句调用它，并丢弃最终状态:

```
pureFunction :: Int -> Int
pureFunction input = a + b + c
  where
    ((a,b,c), _) = runState stateExample input
```

这是我们如何使用`runXXX`模式的最简单的例子。

# 升级到变压器

现在，假设我们的`State`函数不太纯。它现在想要打印它的一些输出，所以它需要`IO`单子。这意味着它将使用`StateT` monad 变压器，而不是`IO`:

```
stateTExample :: StateT Int IO (Int, Int, Int)
stateTExample = do
  a <- get
  lift $ print “Initial Value:”
  lift $ print a
  modify (+1)
  b <- get
  lift $ putStrLn “After adding 1:”
  lift $ print b
  put 5
  c <- get
  lift $ putStrLn “After setting as 5:”
  lift $ print c
  return (a, b, c)
```

现在，我们需要从一个`IO`函数中调用它，而不是从一个纯格式中调用这个函数。但是我们将再次使用一个`runXXX`函数。现在，虽然，因为我们使用的是 monad 转换器，我们不会得到一个纯粹的结果。相反，我们将在底层单子中得到我们的结果。这意味着我们可以从`IO`调用这个函数。所以让我们检查一下`runStateT`函数的类型。我们用`IO`代替了一般的单子参数`m`:

```
runStateT :: StateT s IO a -> s -> IO (a, s)
```

它看起来很像`runState`，除了额外的`IO`参数！它不是返回结果的纯元组，而是返回包含该结果的`IO`动作。因此我们可以从`IO`单子上调用它。

```
main :: IO ()
main = do
  putStrLn “Please enter a number.”
  input <- read <$> getLine
  results <- runStateT stateTExample input
  print results
```

我们将得到以下输出结果:

```
Please enter a number.
10
Initial Value:
10
After adding 1
11
After setting as 5
5
(10, 11, 5)
```

# 对库使用“运行”

这种模式通常会扩展到您使用的库中。例如，在我们关于解析的[系列中，我们研究了](https://www.mmhaskell.com/parsing) [Megaparsec](http://hackage.haskell.org/package/megaparsec) 库。该库中的许多解析器组合子存在于`Parsec`或`ParsecT`单子中。所以我们可以把一堆不同的解析器组合成一个函数。

但是要从普通的`IO`代码(或另一个单子)中运行该函数，您需要使用`runParserT`函数。让我们看看它的类型签名:

```
runParserT
  :: Monad m
  -> ParsecT e s m a
  -> String -- Name of source file
  -> s -- Input for parser
  -> m (Either (ParseError (Token s) e) a)
```

那里有很多类型参数，你不需要理解。但是结构是一样的。我们的`run`函数的第一个参数是一元动作。然后我们会提供一些我们需要的其他输入。然后我们得到一些结果，包裹在一个外部单子里(比如`IO`)。

如果我们使用`servant-client`库进行客户端 API 调用，我们可以看到相同的模式。您对 API 的任何调用都将在`ClientM`单子中。下面是`runClientM`函数的类型签名:

```
runClientM :: ClientM a -> ClientEnv -> IO (Either ServantError a)
```

同样的模式再次出现。我们将编写一元操作，并将其作为第一个参数传递。然后我们将提供一些初始状态，在这个例子中是一个`ClientEnv`。最后，我们将得到包裹在外部单子(`IO`)中的结果(`Either ServantError a`)。

# 表达式中的单子

同样重要的是要记住，许多基本的单子甚至不需要`runXXX`函数就可以工作！例如，您可以使用`Maybe`或`Either`单子来提取一些错误处理逻辑:

```
divideIfEven :: Int -> Maybe Int
divideIfEven x = if x `mod` 2 == 0
  then Just (x `quot` 2)
  else NothingdividesBy8 :: Int -> Bool
dividesBy8 = case thirdResult of
  Just _ -> True
  Nothing -> False
  where
    thirdResult :: Maybe Int
    thirdResult = do
      y <- divideIfEven x
      z <- divideIfEven y
      divideIfEven z
```

# 结论

单子是使用许多不同 Haskell 库的关键。但是当你第一次开始的时候，如何从你的代码中调用这些函数是非常混乱的。这同样适用于一些常见的单子变压器，如`Reader`和`State`。最常见的模式是`runXXXT`模式。掌握了这种模式，你就能更好地理解单子，写出更好的 Haskell！

为了更深入地了解单子和类似的结构，请务必阅读我们关于[函数数据结构](https://www.mmhaskell.com/monads)的系列文章。如果本文中的代码令人困惑，您绝对应该检查一下！如果你从未编写过 Haskell，但想开始，下载我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)！