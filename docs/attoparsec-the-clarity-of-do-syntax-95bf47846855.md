# attoparsec:Do 语法的清晰性

> 原文：<https://medium.com/hackernoon/attoparsec-the-clarity-of-do-syntax-95bf47846855>

![](img/570e9a3bafe1fd30084002beed6c9712.png)

在上周的文章[中，我们完成了对](https://www.mmhaskell.com/blog/2018/2/19/applicative-parsing-ii-putting-the-pieces-together)[应用解析](https://hackage.haskell.org/package/regex-applicative)库的研究。我们把所有较小的组合子放在一起，解析我们的小黄瓜语法。本周，我们将看看一个新的库: [Attoparsec](https://hackage.haskell.org/package/attoparsec) 。这个库使用一元的方法，而不是试图使用一个纯粹的应用结构做所有的事情。这种方法更常见。这使得语法更容易阅读和理解。这也将使我们更容易添加某些功能。

要了解本文的代码，请看一下 Github 上的`[attoparsec](https://github.com/jhb563/GherkinParsing/tree/attoparsec)`分支！关于有用库的一些更好的想法，下载我们的[产品清单](https://www.mmhaskell.com/production-checklist)！它包括从数据结构到机器学习的各种库资料！

如果你是 Haskell 的新手，一定要下载我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)！它将告诉您开始 Haskell 之旅所需的所有步骤！

# 分析器类型

在应用解析中，我们所有的解析器都有类型`RE Char`。这个类型属于`Applicative`类型类，但不是`Monad`。对于 Attoparsec，我们将使用`Parser`类型，一个完整的单子。所以一般来说，我们将编写以下类型的解析器:

```
featureParser :: Parser Feature
scenarioParser :: Parser Scenario
statementParser :: Parser Statement
exampleTableParser :: Parser ExampleTable
valueParser :: Parser Value
```

# 解析值

我们首先应该意识到的是，我们的解析器仍然是一个`Applicative`！所以不是一切都需要改变！我们仍然可以使用像`*>`和`<|>`这样的操作符。事实上，我们可以让我们的值解析代码几乎完全一样！例如，`valueParser`、`nullParser`和`boolParser`表达式可以保持不变:

```
valueParser :: Parser Value
valueParser =
  nullParser <|>
  boolParser <|>
  numberParser <|>
  stringParsernullParser :: Parser Value
nullParser =
  (string "null" <|>
  string "NULL" <|>
  string "Null") *> pure ValueNullboolParser :: Parser Value
boolParser = (trueParser *> pure (ValueBool True)) <|> (falseParser *> pure (ValueBool False))
  where
    trueParser = string "True" <|> string "true" <|> string "TRUE"
    falseParser = string "False" <|> string "false" <|> string "FALSE"
```

如果我们愿意，我们可以在不改变它们的结构的情况下使它们更“一元”。例如，我们可以用`return`代替`pure`(因为它们是相同的)。我们也可以用`>>`代替`*>`来执行一元操作，同时丢弃一个结果。我们的数值解析器有一点变化，但是变得更简单了！Attoparsec 的作者提供了一个方便的解析器来读取科学数字:

```
numberParser :: Parser Value
numberParser = ValueNumber <$> scientific
```

然后对于字符串值，我们将使用`takeTill`组合符读取所有字符，直到出现竖线或换行符。然后，我们将应用一些文本函数来删除空白，并将其返回给一个`String`。(我们使用的`Parser`单子将事物解析为`Text`而不是`String`。

```
stringParser :: Parser Value
stringParser = (ValueString . unpack . strip) <$> 
  takeTill (\c -> c == '|' || c == '\n')
```

# 解析示例

当我们解析示例表时，我们将通过使用 do-syntax 切换到一种更为单一的方法。首先，我们建立一个`cellParser`来读取单元格内的值。

```
cellParser = do
  skipWhile nonNewlineSpace
  val <- valueParser
  skipWhile (not . barOrNewline)
  char '|'
  return val
```

我们语句中的每一行都是指解析过程中的一个步骤。所以首先我们跳过所有的前导空格。然后我们解析我们的值。然后我们跳过剩余的空格，解析最后一个竖线来结束单元格。然后我们将返回解析后的值。

与应用语法相比，跟踪这里发生的事情要容易得多。不难看出我们丢弃了输入的哪些部分，使用了哪些部分。如果我们不在 do-syntax 中用`<-`赋值，我们就丢弃这个值。如果我们找回它，我们会用它。为了完成`exampleLineParser`，我们解析初始栏，获取许多值，关闭该行，然后返回它们:

```
exampleLineParser :: Parser [Value]
exampleLineParser = do
  char '|'
  cells <- many cellParser
  char '\n'
  return cells
  where
    cellParser = ...
```

读取表的键几乎是相同的。所改变的只是我们的`cellParser`用`many letter`代替了`valueParser`。所以现在我们可以把这些片段放在一起，为我们的`exampleTableParser`:

```
exampleTableParser :: Parser ExampleTable
exampleTableParser = do
  string "Examples:"
  consumeLine
  keys <- exampleColumnTitleLineParser
  valueLists <- many exampleLineParser
  return $ ExampleTable keys (map (zip keys) valueLists)
```

我们阅读信号字符串“Examples:”后，使用该行。然后我们得到我们的键和值，并用它们建立表。同样，这比在应用语法中映射像`buildExampleTable`这样的函数要简单得多。

# 声明

`Statement`解析器是我们可以提高代码清晰度的另一个领域。我们将再次定义两个助手解析器。这些函数将分别获取括号外的部分和括号内的部分:

```
nonBrackets :: Parser String
nonBrackets = many (satisfy (\c -> c /= '\n' && c /= '<'))insideBrackets :: Parser String
insideBrackets = do
  char '<'
  key <- many letter
  char '>'
  return key
```

现在，当我们把这些放在一起时，我们可以更清楚地看到 do 语法中概述的过程的步骤。首先我们解析“信号”这个词，然后是一个空格。然后我们得到“成对”的无括号和有括号的部分。最后，我们将得到最后一个不带括号的部分:

```
parseStatementLine :: Text -> Parser Statement
parseStatementLine signal = do
  string signal
  char ' '
  pairs <- many ((,) <$> nonBrackets <*> insideBrackets)
  finalString <- nonBrackets
  ...
```

现在我们可以定义我们的助手函数`buildStatement`并在 do-syntax 中在它自己的行上调用它。然后我们将返回结果`Statement`。这比跟踪我们将哪些函数映射到解析器的哪些部分要容易理解得多:

```
parseStatementLine :: Text -> Parser Statement
parseStatementLine signal = do
  string signal
  char ' '
  pairs <- many ((,) <$> nonBrackets <*> insideBrackets)
  finalString <- nonBrackets
  let (fullString, keys) = buildStatement pairs finalString
  return $ Statement fullString keys
  where
    buildStatement 
      :: [(String, String)] -> String -> (String, [String])
    buildStatement [] last = (last, [])
    buildStatement ((str, key) : rest) rem =
      let (str', keys) = buildStatement rest rem
      in (str <> "<" <> key <> ">" <> str', key : keys)
```

# 场景和功能

与应用解析一样，现在我们可以直接完成所有工作。为了解析一个场景，我们读取关键字，使用该行来读取标题，并读取语句和示例:

```
scenarioParser :: Parser Scenario
scenarioParser = do
  string "Scenario: "
  title <- consumeLine
  statements <- many (parseStatement <* char '\n')
  examples <- (exampleTableParser <|> return (ExampleTable [] []))
  return $ Scenario title statements examples
```

同样，如果没有例子，我们提供一个空的`ExampleTable`作为替代。背景的解析器看起来非常相似。唯一的区别是我们忽略了该行的结果，而是使用`Background`作为标题字符串。

```
backgroundParser :: Parser Scenario
backgroundParser = do
  string "Background:"
  consumeLine
  statements <- many (parseStatement <* char '\n')
  examples <- (exampleTableParser <|> return (ExampleTable [] []))
  return $ Scenario "Background" statements examples
```

最后，我们将把所有这些放在一起作为一个特性。我们阅读标题，获取背景(如果存在)，并阅读我们的场景:

```
featureParser :: Parser Feature
featureParser = do
  string "Feature: "
  title <- consumeLine
  maybeBackground <- optional backgroundParser
  scenarios <- many scenarioParser
  return $ Feature title maybeBackground scenarios
```

# 功能描述

我们现在要添加的一个额外特性是，我们可以更容易地解析特性的“描述”。我们在应用解析中省略了它们，因为实现起来很痛苦。当使用一元方法时，它变得简单得多。不过，我们必须采取的第一步是为我们特性的所有主要元素创建一个解析器。这种方法看起来像这样:

```
featureParser :: Parser Feature
featureParser = do
  string "Feature: "
  title <- consumeLine
  (description, maybeBackground, scenarios) <- parseRestOfFeature
  return $ Feature title description maybeBackground scenariosparseRestOfFeature :: Parser ([String], Maybe Scenario, [Scenario])
parseRestOfFeature = ...
```

现在我们将使用一个递归函数，一次读取一行描述，并添加到一个不断增长的列表中。诀窍是我们将使用 Attoparsec 提供的`choice`组合子。

我们将创建两个解析器。第一种假设没有进一步的描述。它试图解析背景和场景列表。第二个读取一行描述，将其添加到我们的增长列表中，并递归:

```
parseRestOfFeature :: Parser ([String], Maybe Scenario, [Scenario])
parseRestOfFeature = parseRestOfFeatureTail []
  where
    parseRestOfFeatureTail prevDesc = do
      (fullDesc, maybeBG, scenarios) <- choice [noDescriptionLine prevDesc, descriptionLine prevDesc]
      return (fullDesc, maybeBG, scenarios)
```

所以我们先试着运行这个`noDescriptionLineParser`。它将尝试读取背景，然后像我们一直做的那样读取场景。如果成功了，我们就知道我们完成了。我们传递的参数是完整的描述:

```
where
  noDescriptionLine prevDesc = do
    maybeBackground <- optional backgroundParser
    scenarios <- some scenarioParser
    return (prevDesc, maybeBackground, scenarios)
```

如果这个解析器失败了，我们知道这意味着下一行实际上是描述的一部分。所以我们将编写一个解析器来使用一整行，然后递归:

```
descriptionLine prevDesc = do
  nextLine <- consumeLine
  parseRestOfFeatureTail (prevDesc ++ [nextLine])
```

现在我们完成了！我们可以解析描述！

# 结论

这就结束了我们对 [Attoparsec](https://hackage.haskell.org/package/attoparsec) 的探索。下周回来，我们将通过了解[巨型炸弹](https://hackage.haskell.org/package/megaparsec)来结束这个系列。我们会发现，除了一些小的例外，它在语法上与 Attoparsec 非常相似。我们将看到如何使用一元解析的一些额外功能来丰富我们的语法。

要了解更多关于很酷的 Haskell 库的信息，请务必查看我们的[生产清单](https://www.mmhaskell.com/production-checklist)！它会告诉你一些关于各种领域的库的信息，比如数据库和 web APIs。

如果你从来没有写过 Haskell，下载我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)！它将为您提供开始 Haskell 之旅所需的所有资源！