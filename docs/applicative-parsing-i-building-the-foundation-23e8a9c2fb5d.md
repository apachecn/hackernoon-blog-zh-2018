# 应用解析 I:构建基础

> 原文：<https://medium.com/hackernoon/applicative-parsing-i-building-the-foundation-23e8a9c2fb5d>

![](img/c54ac09306112bde11e9a997a6036490.png)

[上周](https://mmhaskell.com/blog/2018/2/5/parsing-primer-gherkin-syntax)我们复习了小黄瓜语法的基础知识，为解析做好了准备。在本文和下一篇文章中，我们将使用[应用解析](https://hackage.haskell.org/package/regex-applicative-0.3.3/docs/Text-Regex-Applicative.html#t:RE)库来解析该语法。本周，我们将集中讨论这个库的基础知识，并建立一个可以使用的组合元词汇。我们将大量使用`Applicative` typeclass。如果您需要对此进行复习，请查看[本文](https://mmhaskell.com/blog/2017/2/6/applicatives-one-step-further)。当我们开始编码的时候，你也可以跟随 [Github 上的例子！这里的大部分代码都在](https://github.com/jhb563/GherkinParsing) [Parser.hs](https://github.com/jhb563/GherkinParsing/blob/master/src/Parser.hs) 中。

在接下来的几周，我们还将看到其他几个解析库。如果您想了解更多信息，请下载我们的[生产检查表](https://www.mmhaskell.com/production-checklist)。它总结了许多其他有用的库来编写更高级别的 Haskell。

如果你从未开始写哈斯克尔，现在是你的机会了！获取我们的免费[初学者清单](https://www.mmhaskell.com/beginners-checklist)并学习开始的基础知识！

# 入门指南

因此，为了开始解析，让我们对我们的输入格式做一些笔记。首先，我们将把我们的输入特征文档当作一个字符串。我们将删除所有空行，然后修剪每一行的前导和尾随空格。

```
parseFeatureFromFile :: FilePath -> IO Feature
parseFeatureFromFile inputFile = do
  fileContents <- lines <$> readFile inputFile
  let nonEmptyLines = filter (not . isEmpty) fileContents
  let trimmedLines = map trim nonEmptyLines
  let finalString = unlines trimmedLines
  case parseFeature finalString of
    ...…
isEmpty :: String -> Bool
isEmpty = all isSpacetrim :: String -> String
trim input = reverse flippedTrimmed
  where
    trimStart = dropWhile isSpace input
    flipped = reverse trimStart
    flippedTrimmed = dropWhile isSpace flipped
```

对于我们的语法来说，这意味着一些事情。首先，我们不关心缩进。第二，我们忽略了额外的线路。这意味着我们的解析器可能允许某些我们不想要的格式。但这没关系，因为我们试图让事情变得简单。

# 稀土类型

对于基于应用的解析，我们将使用的主要数据类型被称为`RE`，用于正则表达式。这表示一个解析器，它由两种类型参数化:

```
data RE s a = ...
```

类型指的是我们将要解析的基本单元。因为我们将输入解析为单个的`String`，这将是`Char`。那么`a`类型就是解析元素的结果。这因解析器而异。我们可以使用的最基本的组合子是`sym`。这将解析您选择的单个符号:

```
sym :: s - > RE s sparseLowercaseA :: RE Char Char
parseLowercaseA = sym ‘a’
```

为了使用一个`RE`解析器，我们调用`match`函数或者它的中缀等价函数`=~`。如果我们可以匹配整个输入字符串，这些将返回一个`Just`值，否则返回`Nothing`:

```
>> match parseLowercaseA “a”
Just ‘a’
>> “b” =~ parseLowercaseA
Nothing
>> “ab” =~ parseLowercaseA
Nothing -- (Needs to parse entire input)
```

# 谓词和字符串

自然，我们会想要一些更复杂的功能。我们可以使用`psym`解析任何符合特定谓词的字符，而不是解析单个输入字符。因此，如果我们想读取任何不是换行符的字符，我们可以这样做:

```
parseNonNewline :: RE Char Char
parseNonNewline = psym (/= ‘\n’)
```

`string`组合子允许我们匹配特定的完整字符串，然后返回它:

```
readFeatureWord :: RE Char String
readFeatureWord = string “Feature”
```

我们将使用它来解析关键字，尽管我们最终常常会丢弃“结果”。

# 应用组合子

现在`RE`型适用。这意味着我们可以对它应用各种应用组合子。其中之一是`many`，它允许我们多次应用同一个解析器。这里有一个我们会经常用到的组合子。它允许我们读取所有内容，直到一个新行，并返回结果字符串:

```
readUntilEndOfLine :: RE Char String
readUntilEndOfLine = many (psym (/= '\n'))
```

除此之外，我们还想利用适用的`<*>`操作符来组合不同的解析器。我们也可以通过使用`<$>`在这些之上应用一个纯函数(或构造函数)。假设我们有一个存储两个字符的数据类型。下面是我们如何为它构建一个解析器:

```
data TwoChars = TwoChars Char CharparseTwoChars :: RE Char TwoChars
parseTwoChars = TwoChars <$> parseNonNewline <*> parseNonNewline...>> match parseTwoChars “ab”
Just (TwoChars ‘a’ ‘b’)
```

我们也可以使用`<*`和`*>`，它们是主应用操作符的表亲。第一个将被解析，但是**忽略**右边的解析结果。第二个丢弃左侧结果。

```
parseFirst :: RE Char Char
parseFirst = parseNonNewline <* parseNonNewlineparseSecond :: RE Char Char
parseSecond = parseNonNewline *> parseNonnewline…>> match parseFirst “ab”
Just ‘a’
>> match parseSecond “ab”
Just ‘b’
>> match parseFirst “a”
Nothing
```

注意最后一个失败了，因为解析器需要两个输入！我们一会儿会回到这个失败的概念。但是现在我们知道了这项技术，我们可以编写一些其他有用的解析器:

```
readThroughEndOfLine :: RE Char String
readThroughEndOfLine = readUntilEndOfLine <* sym '\n'readThroughBar :: RE Char String
readThroughBar = readUntilBar <* sym '|'readUntilBar :: RE Char String
readUntilBar = many (psym (\c -> c /= '|' && c /= '\n'))
```

第一个将解析该行的其余部分，然后使用换行符本身。其他解析器完成同样的任务，除了竖线字符。下周我们解析`Examples`部分时会用到这些。

# 备选方案:处理解析失败

我们在上面介绍了解析器“失败”的概念。当然，我们需要能够在解析器失败时提供替代方案！否则，我们的语言结构将非常有限。幸运的是，`RE`类型也实现了`Alternative`。这意味着当一个解析器失败时，我们可以使用`<|>`操作符来确定另一个解析器。让我们来看看实际情况:

```
parseFeatureTitle :: RE Char String
parseFeatureTitle = string “Feature: “ *> readThroughEndOfLineparseScenarioTitle :: RE Char String
parseScenarioTitle = string “Scenario: “ *> readThroughEndOfLineparseEither :: RE Char String
parseEither = parseFeatureTitle <|> parseScenarioTitle…>> match parseFeatureTitle “Feature: Login\n”
Just “Login”
>> match parseFeatureTitle “Scenario: Login\n”
Nothing
>> match parseEither “Scenario: Login\n”
Just “Login”
```

当然，如果所有选项都失败了，那么我们仍然会有一个失败的解析器！

```
>> match parseEither “Random: Login\n”
Nothing
```

我们将需要这个来为我们的解析系统引入某种程度的选择。例如，由用户决定他们是否想要包含一个`Background`作为他们特性的一部分。因此，我们需要能够读取背景，如果它在那里，或者继续分析一个场景。

# 结论

这就结束了我们对应用解析的基本组合子的介绍。下周，我们将把我们在这里开发的所有部分用于小黄瓜语法本身。到目前为止，一切似乎都很小。但是我们将会看到，一旦我们有了基本的部分，我们实际上可以非常快速地建立我们的结果！

如果你想看到更多对重要的 Haskell 任务有用的库，看看我们的[生产清单](https://www.mmhaskell.com/production-checklist)。它将向您介绍一些用于解析的库、数据库、API 等等！

如果您是 Haskell 的新手，现在是开始的最佳时机！下载我们免费的[初学者清单](https://www.mmhaskell.com/beginners-checklist)！它将帮助你下载正确的工具并开始学习这门语言。