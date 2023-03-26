# Megaparsec:相同的语法，更多的特性！

> 原文：<https://medium.com/hackernoon/megaparsec-same-syntax-more-features-b8f9b844bb02>

![](img/60da3e5292db825cdece747e2f9a5001.png)

上周，我们通过学习 Attoparsec 库，向一元解析世界迈进了一步。与应用解析相比，它为我们提供了更清晰的语法。本周，我们将探索最后一个库: [Megaparsec](https://hackage.haskell.org/package/megaparsec) 。

这个库和 Attoparsec 有很多共同之处。事实上，这两者在设计上有很多兼容性。最终，我们会发现我们不需要改变太多的语法。但是 Megaparsec 确实有一些额外的功能，可以让我们的生活更简单。

要了解这里的代码示例，请前往 Github 上的`[megaparsec](https://github.com/jhb563/GherkinParsing/tree/megaparsec)` [分支](https://github.com/jhb563/GherkinParsing/tree/megaparsec)！要了解更多可以在生产中使用的优秀库，请务必下载我们的[生产清单](https://www.mmhaskell.com/production-checklist)！但是，如果您是 Haskell 的新手，请不要担心！只要看看我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)，你就会知道从哪里开始！

# 不同的解析器类型

首先，Megaparsec 的基本解析类型稍微复杂一些。它有两个类型参数，`e`和`s`，还带有一个内置的单子变压器`ParsecT`。

```
data ParsecT e s m atype Parsec e s = ParsecT e s Identity
```

`e`类型允许我们向解析器提供一些定制的错误数据。`s`类型是指我们的解析器的输入类型，通常是 String 的某种变体。这个参数也存在于 Attoparsec 的引擎盖下。但是我们通过使用`Text`模块避开了这个问题。现在，我们将设置自己的类型别名，将这些参数隐藏起来:

```
type MParser = Parsec Void Text
```

# 尽最大努力

让我们开始填充我们的解析器。Attoparsec 和 Megaparsec 有一个结构上的区别。当解析器在 Attoparsec 中失败时，它的默认行为是回溯。这意味着它好像没有消耗任何输入。Megaparsec 就不是这样！重复我们的`nullParser`代码的幼稚尝试在某些方面可能会失败:

```
nullParser :: MParser Value
nullParser = nullWordParser >> return ValueNull
  where
    nullWordParser = string "Null" <|> string "NULL" <|> string "null"
```

假设我们得到这个解析器的输入“NULL”。我们的程序将尝试选择第一个解析器，它将解析`N`标记。然后会在`U`失败。它将移动到第二个解析器，但是它已经消耗了`N`！因此，第二个和第三个解析器也会失败！

我们通过使用`try`组合子来解决这个问题。如果我们的解析器失败，使用`try`给我们提供了回溯的 Attoparsec 行为。以下内容可以正常工作:

```
nullParser :: MParser Value
nullParser = nullWordParser >> return ValueNull
  where
    nullWordParser = 
      try (string "Null") <|> 
      try (string "NULL") <|> 
      try (string "null")
```

更好的是，Megaparsec 还有一个方便的函数`string’`用于不区分大小写的解析。所以我们的 null 和 boolean 解析器变得更加简单:

```
nullParser :: MParser Value
nullParser = M.string' "null" >> return ValueNullboolParser :: MParser Value
boolParser = 
  (trueParser >> return (ValueBool True)) <|> 
  (falseParser >> return (ValueBool False))
    where
      trueParser = M.string' "true"
      falseParser = M.string' "false"
```

与 Attoparsec 不同，我们没有方便的科学数字解析器。我们将不得不从应用解析回到我们的逻辑，只是这次是一元语法。

```
numberParser :: MParser Value
numberParser = (ValueNumber . read) <$>
  (negativeParser <|> decimalParser <|> integerParser)
  where
    integerParser :: MParser String
    integerParser = M.try (some M.digitChar) decimalParser :: MParser String
    decimalParser = M.try $ do
      front <- many M.digitChar
      M.char '.'
      back <- some M.digitChar
      return $ front ++ ('.' : back) negativeParser :: MParser String
    negativeParser = M.try $ do
      M.char '-'
      num <- decimalParser <|> integerParser
      return $ '-' : num
```

注意，我们的前两个解析器都使用`try`来允许适当的回溯。为了解析字符串，我们将使用`satisfy`组合符读取所有内容，直到一个小节或换行符:

```
stringParser :: MParser Value
stringParser = (ValueString . trim) <$>
  many (M.satisfy (not . barOrNewline))
```

然后填充我们的值解析器就像以前一样简单了:

```
valueParser :: MParser Value
valueParser =
  nullParser <|>
  boolParser <|>
  numberParser <|>
  stringParser
```

# 填写细节

除了一些琐碎的改动，我们解析示例表的方式没有任何变化。当我们获取对时,`Statement`解析器需要添加另一个`try`调用:

```
parseStatementLine :: Text -> MParser Statement
parseStatementLine signal = do
  M.string signal
  M.char ' '
  pairs <- many $ M.try ((,) <$> nonBrackets <*> insideBrackets)
  finalString <- nonBrackets
  let (fullString, keys) = buildStatement pairs finalString
  return $ Statement fullString keys
  where
    buildStatement  = ...
```

否则，如果我们在语句中不使用任何关键字，我们就会失败！但其他方面都是一样的。当然，我们首先还需要改变调用解析器的方式。我们将使用`runParser`函数，而不是 Attoparsec 的`parseOnly`。这为我们的解析器的源文件增加了一个额外的参数，以提供更好的消息。

```
parseFeatureFromFile :: FilePath -> IO Feature
parseFeatureFromFile inputFile = do
  …
  case runParser featureParser finalString inputFile of
    Left s -> error (show s)
    Right feature -> return feature
```

但是我们的解析器的结构没有任何变化。获取 Attoparsec 代码和 Megaparsec 代码并在另一个库上重用它非常容易！

# 添加一些状态

我们从 Megaparsec 得到的一个好处是，它的 monad transformer 使我们更容易使用其他 monad 功能。我们的语句行解析器总是有点笨拙。让我们稍微清理一下，允许我们自己存储一个字符串列表作为一个状态对象。下面是我们改变解析器类型的方法:

```
type MParser = ParsecT Void Text (State [String])
```

现在，每当我们使用括号解析器解析一个键时，我们可以使用`modify`将该键添加到现有列表中。我们还将连同字符串一起返回括号，而不仅仅是关键字:

```
insideBrackets :: MParser String
insideBrackets = do
  M.char '<'
  key <- many M.letterChar
  M.char '>'
  modify (++ [key]) -- Store the key in the state!
  return $ ('<' : key) ++ ['>']
```

现在，我们可以将解析的字符串连接起来，而不是形成元组！

```
parseStatementLine :: Text -> MParser Statement
parseStatementLine signal = do
  M.string signal
  M.char ' '
  pairs <- many $ M.try ((++) <$> nonBrackets <*> insideBrackets)
  finalString <- nonBrackets
  let fullString = concat pairs ++ finalString
  …
```

现在我们如何得到最终的密钥列表呢？简单！我们`get`我们的状态值，重置它，并返回一切。不需要我们乱七八糟的`buildStatement`函数！

```
parseStatementLine :: Text -> MParser Statement
parseStatementLine signal = do
  M.string signal
  M.char ' '
  pairs <- many $ M.try ((++) <$> nonBrackets <*> insideBrackets)
  finalString <- nonBrackets
  let fullString = concat pairs ++ finalString
  keys <- get
  put []
  return $ Statement fullString keys
```

当我们开始运行这个解析器时，我们现在必须使用`runParserT`而不是`runParser`。这返回给我们一个`State`单子中的动作，意味着我们必须使用`evalState`来获得最终结果:

```
parseFeatureFromFile :: FilePath -> IO Feature
parseFeatureFromFile inputFile = do
  …
  case evalState (stateAction finalString) [] of
    Left s -> error (show s)
    Right feature -> return feature
  where
    stateAction s = runParserT featureParser inputFile s
```

# 百万奖金 c

作为最后的奖励，让我们看看 Megaparsec 中的错误消息。当我们在 Attoparsec 中有错误时，`parseOnly`函数会给我们一个错误字符串。但没那么有帮助。它只告诉我们系统内部的单个解析器出了什么问题:

```
>> parseOnly nullParser "true"
Left "string"
>> parseOnly "numberParser" "hello"
Left "Failed reading: takeWhile1"
```

这些消息没有告诉我们它在输入中的什么地方失败了，或者我们所期望的是什么。让我们来比较一下 Megaparsec 和`runParser`:

```
>> runParser nullParser "true" ""
Left (TrivialError 
  (SourcePos {sourceName = "true", sourceLine = Pos 1, sourceColumn = Pos 1} :| []) 
  (Just EndOfInput) 
  (fromList [Tokens ('n' :| "ull")]))
>> runParser numberParser "hello" ""
Left (TrivialError 
  (SourcePos {sourceName = "hello", sourceLine = Pos 1, sourceColumn = Pos 1} :| []) 
    (Just EndOfInput) 
    (fromList [Tokens ('-' :| ""),Tokens ('.' :| ""),Label ('d' :| "igit")]))
```

这给了我们更多的信息！我们可以看到我们试图解析的字符串。我们还可以看到它失败的确切位置。它甚至会给我们一张它试图使用的解析器的图片。在一个更大的系统中，这有很大的不同。我们可以追踪出我们在开发我们的语法或者使我们的输入符合语法时哪里出错了。如果我们自定义`e`参数类型，我们甚至可以将我们自己的详细信息添加到错误消息中，以提供更多帮助！

# 结论

这就结束了我们对 Haskell 中解析库的探索！在过去的几周里，我们学习了[应用性](https://hackage.haskell.org/package/regex-applicative)解析、 [Attoparsec](https://hackage.haskell.org/package/attoparsec) 和 [Megaparsec](https://hackage.haskell.org/package/megaparsec) 。第一个为我们的语言是正则的时候提供了有用和直观的组合子。它允许我们避免使用单子进行解析，以及可能带来的负担。在 Attoparsec 中，我们看到了一元风格解析的介绍。这为我们提供了一个更容易理解的语法，并且我们可以看到发生了什么。最后，本周，我们探索了 Megaparsec。这个库在语法上与 Attoparsec 有很多共同之处。但是它提供了一些额外的功能，可以使许多任务变得更容易。

准备好探索 Haskell develop 的更多领域了吗？想获得一些新图书馆学习的想法？下载我们的[生产清单](https://www.mmhaskell.com/production-checklist)！它将为您提供从数据结构到 web APIs 等领域的一些工具的快速总结！

从来没有用 Haskell 编程过？想开始吗？查看我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)！它拥有您开始 Haskell 之旅所需的所有工具！