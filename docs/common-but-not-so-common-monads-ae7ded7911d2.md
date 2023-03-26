# 常见(但不太常见)的单子

> 原文：<https://medium.com/hackernoon/common-but-not-so-common-monads-ae7ded7911d2>

![](img/aa59e878f4e939995d33bf6ef4c085d3.png)

[上周](https://james-bowen-c4sg.squarespace.com/blog/2018/8/27/TODO)我们看了单子如何帮助你在 Haskell 开发中进行下一次跳跃。我们回顾了`runXXXT`模式，以及它是如何成为我们从其余代码中使用某些单子的一个常见途径。但有时回到基础也会有所帮助。事实上，我花了很长时间都没有真正掌握如何使用一些基本的单子。或者最起码，我不明白怎么把它们当单子用。

在本文中，我们将看看如何使用列表单子和函数单子。列表和函数是任何 Haskeller 从一开始就知道的核心概念。但是列表数据结构和函数应用也是单子！理解它们是如何工作的可以让我们更多地了解单子是如何工作的。

关于单子的深入讨论，请查看我们的[函数数据结构系列](https://www.mmhaskell.com/monads)！

# Do 语法的一般模式

使用`do`语法是理解如何实际使用单子的关键之一。bind 操作符使得很难跟踪你的参数在哪里。Do 语法保持了结构的整洁，并允许您轻松地传递结果。让我们看看这是如何与`IO`一起工作的，它是许多 Haskellers 学习的第一个单子。这里有一个例子，我们从一个文件中读取第二行:

```
readLineFromFile :: IO String
readLineFromFile = do
  handle <- openFile “myFile.txt” ReadMode
  nextLine <- hGetLine handle
  secondLine <- hGetLine handle
  _ <- hClose handle
  return secondLine
```

通过记住所有`IO`函数的类型签名，我们可以开始看到 do 语法的一般模式。让我们将每个表达式替换为它的类型:

```
openFile :: FilePath -> IOMode -> IO Handle
hGetLine :: Handle -> IO String
hClose :: Handle -> IO ()
return :: a -> IO areadLineFromFile :: IO String
readLineFromFile = do
  (Handle) <- (IO Handle)
  (String) <- (IO String)
  (String) <- (IO String)
  () <- (IO ())
  IO String
```

do 表达式中的每一行(除了最后一行)都使用赋值操作符`<-`。然后它在右边有一个表达式`IO a`，它在左边给它赋值`a`。最后一行的类型匹配这个函数的最终返回值。现在重要的是认识到我们可以将这种结构推广到任何单子:

```
monadicFunction :: m c
monadicFunction = do
  (_ :: a) <- (_ :: m a)
  (_ :: b) <- (_ :: m b)
  (_ :: m c)
```

例如，如果我们在`Maybe`单子中有一个函数，我们可以使用它并为上面的`m`插入它:

```
myMaybeFunction :: a -> Maybe amonadicMaybe :: a -> Maybe a
monadicMaybe x = do
  (y :: a) <- myMaybeFunction x
  (z :: a) <- myMaybeFunction y
  (Just z :: Maybe a)
```

需要记住的重要一点是，单子捕获的是计算上下文。对于`IO`，这种情况是计算可能与终端或网络交互。对于`Maybe`，上下文是计算可能失败。

# 单子单子单子

现在要绘制单子，我们需要知道它的计算环境。我们可以将任何返回列表的函数视为**不确定的**。它可以有许多不同的值。因此，如果我们将这些计算串联起来，我们的最终结果是每一种可能的组合。也就是说，我们的第一个计算可以返回一个值列表。然后我们想检查我们从这些不同的结果中得到什么，作为下一个函数的输入。然后我们会得到所有的结果。诸如此类。

为了看到这一点，让我们想象我们有一个游戏。我们可以用一个特定的数字`x`开始游戏。在每一轮，我们可以减一，加一，或者保持数字不变。我们想知道 5 轮后所有可能的结果，以及可能性的分布。所以我们从写非确定性函数开始。它接受一个输入并返回可能的游戏输出:

```
runTurn :: Int -> [Int]
runTurn x = [x - 1, x, x + 1]
```

下面是我们如何在这个 5 回合游戏中应用。我们将添加类型签名，以便您可以看到一元结构:

```
runGame :: Int -> [Int]
runGame x = do
  (m1 :: Int) <- (runTurn x :: [Int])
  (m2 :: Int) <- (runTurn m1 :: [Int])
  (m3 :: Int) <- (runTurn m2 :: [Int])
  (m4 :: Int) <- (runTurn m3 :: [Int])
  (m5 :: Int) <- (runTurn m4 :: [Int])
  return m5
```

在右边，每个表达式都有类型`[Int]`。然后在左边，我们把我们的`Int`拿出来。所以每一个`m`表情代表**我们将从`runTurn`那里得到的众多解决方案中的一个**。然后我们运行剩下的函数，想象我们只使用其中一个。但实际上，我们将运行它们，因为 list monad 是如何定义其绑定操作符的。这个心理跳跃有点棘手。当我们做列表计算时，坚持使用`where`表达式通常更直观。但是看到像这样的模式在意想不到的地方出现是很酷的。

# 函数单子

函数 monad 是另一个我花了很长时间才理解的函数。在某些方面，它和`Reader`单子是一样的。它封装了我们可以传递给不同函数的单个参数的上下文。但是和`Reader`的定义不一样。当我试图寻找定义时，它对我来说没有多大意义:

```
instance Monad ((->) r) where
  return x = \_ -> x
  h >>= f = \w -> f (h w) w
```

`return`的定义很有道理。我们将有一个函数，它接受一些参数，忽略那个参数，并给出值作为输出。绑定操作符稍微复杂一些。当我们将两个函数绑定在一起时，我们将得到一个带参数的新函数`w`。我们将对我们的第一个函数(`(h w)`)应用这个论点。然后我们将得到结果，应用到`f`，然后再次应用参数`w`。有点难以理解。

但是让我们在 do 语法的上下文中考虑这个问题。右边的每一个表达式都将是一个以我们的类型为唯一参数的函数。

```
myFunctionMonad :: a -> (x, y, z)
myFunctionMonad = do
  x <- :: a -> b
  y <- :: a -> c
  z <- :: a -> d
  return (x, y, z)
```

现在让我们想象我们将传递一个`Int`并使用几个不同的可以接受一个`Int`的函数。这是我们将得到的:

```
myFunctionMonad :: Int -> (Int, Int, String)
myFunctionMonad = do
  x <- (1 +)
  y <- (2 *)
  z <- show
  return (x, y, z)
```

现在我们有了有效的 do 语法！那么当我们运行这个函数时会发生什么呢？我们将在同一个输入上调用不同的函数。

```
>> myFunctionMonad 3
(4, 6, "3")
>> myFunctionMonad (-1)
(0, -2, "-1")
```

当我们在第一个例子中传递 3 时，我们在第一行加 1，在第二行乘以 2，在第三行`show`它。我们做这些都没有明确地陈述论点！棘手的是，所有的函数都必须将输入参数作为它们的最后一个参数。所以你可能要做一点争论。

# 结论

在本文中，我们探讨了列表和函数，这是 Haskell 中最常见的两个概念。我们一般不用这些做单子。但是我们看到它们仍然符合一元结构。我们可以在 do 语法中使用它们，并遵循我们已经知道的模式来使事情工作。

也许你以前尝试过学习 Haskell，但是发现单子有点太复杂了。希望这篇文章有助于阐明单子的结构。如果你想重新开始你的 Haskell 之旅，下载我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)！或者从头开始学习单子，阅读我们关于[函数数据结构](https://www.mmhaskell.com/monads)的系列文章！