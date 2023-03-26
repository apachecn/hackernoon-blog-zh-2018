# 应用解析 II:将碎片放在一起

> 原文：<https://medium.com/hackernoon/applicative-parsing-ii-putting-the-pieces-together-fe998dbe4587>

![](img/bb3c1c284c507bdc772aa72b34cc4e54.png)

在上周的文章中，我们介绍了[应用解析库](https://hackage.haskell.org/package/regex-applicative)。我们学习了`RE`类型以及像`sym`和`string`这样的基本组合子。我们看到了如何将这些功能与类似`many`和`<*>`的应用功能结合起来，将字符串解析成数据结构。本周，我们将把这些片段放在一个实际的解析器中，用于我们的小黄瓜语法。要了解代码示例，请查看 Github 资源库上的 [Parser.hs](https://github.com/jhb563/GherkinParsing/blob/master/src/Parser.hs) 。

从下周开始，我们将探索一些其他的解析库，从 [Attoparsec](https://hackage.haskell.org/package/attoparsec) 开始。想了解更多关于这些库和其他库的信息，请下载我们的[产品清单！它总结了从数据库到 Web APIs 的许多库。](https://www.mmhaskell.com/production-checklist)

如果您从未编写过 Haskell，那就开始吧！下载我们免费的[初学者清单！](https://www.mmhaskell.com/beginners-checklist)。

# 值分析器

为了与上一篇文章中的方法保持一致，我们将从较小的语法元素开始。然后，我们可以使用这些来轻松地建立更大的。为此，让我们为我们的`Value`类型构建一个解析器，这是我们语法中最基本的数据结构。让我们回忆一下那看起来像什么:

```
data Value =
ValueNull |
ValueBool Bool |
ValueString String |
ValueNumber Scientific
```

由于我们有不同的构造函数，我们将为每个构造函数创建一个解析器。然后我们可以将它们与替代语法结合起来:

```
valueParser :: RE Char Value
valueParser =
  nullParser <|>
  boolParser <|>
  numberParser <|>
  stringParser
```

现在，我们的空值和布尔值解析器很容易。对于其中的每一个，我们将给出一些不同的选项，关于我们可以用什么字符串来表示这些元素。然后，与更大的解析器一样，我们将把它们与`<|>`结合起来。

```
nullParser :: RE Char Value
nullParser =
  (string “null” <|>
  string “NULL” <|>
  string “Null”) *> pure ValueNullboolParser :: RE Char Value
boolParser =
  trueParser *> pure (ValueBool True) <|> 
  falseParser *> pure (ValueBool False)
  where
    trueParser = string “True” <|> string “true” <|> string “TRUE”
    falseParser = string “False” <|> string “false” <|> string “FALSE”
```

注意，在这两种情况下，我们都用`*>`丢弃了实际的字符串，然后返回我们的构造函数。我们必须用`pure`包装期望的结果。

# 数字和字符串值

数字和字符串稍微复杂一些，因为我们不能依赖硬编码的格式。对于数字，我们将考虑整数、小数和负数。我们暂时忽略科学符号。整数很容易解析，因为我们有很多字符都是数字。我们使用`some`而不是`many`来强调至少有一个:

```
numberParser :: RE Char Value
numberPaser = …
  where
    integerParser = some (psym isNumber)
```

十进制解析器会读取一些数字，然后是一个小数点，然后是更多的数字。我们坚持小数点后至少有一个数字。

```
numberParser :: RE Char Value
numberPaser = …
  where
    integerParser = some (psym isNumber)
    decimalParser = 
      many (psym isNumber) <*> sym ‘.’ <*> some (psym isNumber)
```

最后，对于负数，我们将读取一个负符号，然后是另一个解析器:

```
numberParser :: RE Char Value
numberPaser = …
  where
    integerParser = some (psym isNumber)
    decimalParser = 
      many (psym isNumber) <*> sym ‘.’ <*> some (psym isNumber)
    negativeParser = sym ‘-’ <*> (decimalParser <|> integerParser)
```

但是，我们不能原样组合这些解析器！现在，它们都返回不同的结果！整数分析器返回单个字符串。十进制解析器返回两个字符串和十进制字符，依此类推。通常，我们希望将每个解析器的结果组合成一个字符串，然后将它们传递给`read`函数。这需要在最后两个解析器上映射几个函数:

```
numberParser :: RE Char Value
numberPaser = …
  where
    integerParser = some (psym isNumber)
    decimalParser = combineDecimal <$> 
      many (psym isNumber) <*> sym ‘.’ <*> some (psym isNumber)
    negativeParser = (:) <$> 
      sym ‘-’ <*> (decimalParser <|> integerParser) combineDecimal :: String -> Char -> String -> String
    combineDecimal base point decimal = base ++ (point : decimal)
```

现在我们所有的数字解析器都返回字符串，所以我们可以安全地组合它们。我们将把`ValueNumber`构造函数映射到我们从字符串中读取的值上。

```
numberParser :: RE Char Value
numberPaser = (ValueNumber . read) <$>
  (negativeParser <|> decimalParser <|> integerParser)
  where
    ...
```

注意顺序很重要！如果我们把整数解析器放在第一位，我们就有麻烦了！如果遇到小数点，整数解析器会贪婪地成功，解析小数点之前的一切。我们要么丢失小数点后的所有信息，要么更糟，出现解析失败。

我们需要做的最后一件事是读取一个字符串。我们需要读取示例单元格中的所有内容，直到碰到一个竖线，然后忽略任何空白。幸运的是，我们有合适的组合子，我们甚至已经写了一个`trim`函数！

```
stringParser :: RE Char Value
stringParser = (ValueString . trim) <$> readUntilBar
```

而现在我们的`valueParser`会按预期工作！

# 构建示例表

既然我们可以解析单个值，那么让我们来看看如何解析完整的示例表。我们可以使用我们自己的值解析器来解析一整行值！第一步是读取行首的竖线。

```
exampleLineParser :: RE Char [Value]
exampleLineParser = sym ‘|’ *> ...
```

接下来，我们将为每个单元格构建一个解析器。它将读取空白，然后是值，然后向上读取下一个条。

```
exampleLineParser :: RE Char [Value]
exampleLineParser = sym ‘|’ *> ...
  where
    cellParser = 
      many isNonNewlineSpace *> valueParser <* readThroughBarisNonNewlineSpace :: RE Char Char
isNonNewlineSpace = psym (\c -> isSpace c && c /= ‘\n’)
```

现在我们阅读其中的`many`，并通过阅读换行符来结束:

```
exampleLineParser :: RE Char [Value]
exampleLineParser = 
  sym ‘|’ *> many cellParser <* readThroughEndOfLine
  where
    cellParser = 
      many isNonNewlineSpace *> valueParser <* readThroughBar
```

现在，我们需要一个类似的解析器来读取示例的标题列。这将具有与值单元格相同的结构，只是它将读取正常的字母字符串而不是值。

```
exampleColumnTitleLineParser :: RE Char [String]
exampleColumnTitleLineParser = sym ‘|’ *> many cellParser <* readThroughEndOfLine
  where
    cellParser = 
      many isNonNewlineSpace *> many (psym isAlpha) <* readThroughBar
```

现在我们可以开始构建完整的示例解析器了。我们需要读取字符串、列标题，然后是值行。

```
exampleTableParser :: RE Char ExampleTable
exampleTableParser =
  (string “Examples:” *> readThroughEndOfLine) *>
  exampleColumnTitleLineParser <*>
  many exampleLineParser
```

我们还没完成。我们需要对这些结果应用一个函数来产生最终的`ExampleTable`。诀窍是我们想把示例键和它们的值对应起来。我们可以用一个简单的函数来完成这个任务。它将使用`map`返回每个值列表上的 zip 键:

```
exampleTableParser :: RE Char ExampleTable
exampleTableParser = buildExampleTable <$>
  (string “Examples:” *> readThroughEndOfLine) *>
  exampleColumnTitleLineParser <*>
  many exampleLineParser
  where
    buildExampleTable :: [String] -> [[Value]] -> ExampleTable
    buildExampleTable keys valueLists = ExampleTable keys (map (zip keys) valueLists)
```

# 声明

现在我们可以解析给定场景的示例，我们需要解析 Gherkin 语句。首先，让我们制作一个通用解析器，它将关键字作为参数。然后，我们的完整解析器将尝试每个不同的语句关键字:

```
parseStatementLine :: String -> RE Char Statement
parseStatementLine signal = …parseStatement :: RE Char Statement
parseStatement =
  parseStatementLine “Given” <|>
  parseStatementLine “When” <|>
  parseStatementLine “Then” <|>
  parseStatementLine “And”
```

现在我们将去掉信号词并解析语句行本身。

```
parseStatementLine :: String -> RE Char Statement
parseStatementLine signal = string signal *> sym ' ' *> ...
```

解析语句是很棘手的。我们希望解析括号内的键，并将它们作为键分开。但是我们也希望它们成为语句字符串的一部分。为此，我们将创建两个辅助解析器。首先，`nonBrackets`将通过括号(或换行符)解析字符串中的所有内容。

```
nonBrackets :: RE Char String
nonBrackets = many (psym (\c -> c /= ‘\n’ && c /= ‘<’))
```

我们还需要一个解析器来解析括号并返回里面的关键字:

```
insideBrackets :: RE Char String
insideBrackets = sym ‘<’ *> many (psym (/= ‘>’)) <* sym ‘>’
```

现在来读一个语句，我们从非括号开始，用括号中的键交替。让我们观察一下，我们以无括号开始和结束，因为它们可以是空的。因此，我们可以用一列非括号/括号对来表示一行，后面跟着最后一个非括号部分。为了组成一对，我们使用由`TupleSections`启用的`(,)`构造函数将解析器结果组合成一个元组:

```
parseStatementLine :: String -> RE Char Statement
parseStatementLine signal = string signal *> sym ‘ ‘ *>
  many ((,) <$> nonBrackets <*> insideBrackets) <*> nonBrackets
```

从这里开始，我们需要一个递归函数来构建最终的语句字符串和键列表。我们用`buildStatement`来做这个。

```
parseStatementLine :: String -> RE Char Statement
parseStatementLine signal = string signal *> sym ‘ ‘ *>
  (buildStatement <$> 
    many ((,) <$> nonBrackets <*> insideBrackets) <*> nonBrackets)
  where
    buildStatement :: 
      [(String, String)] -> String -> (String, [String])
    buildStatement [] last = (last, [])
    buildStatement ((str, key) : rest) rem =
      let (str', keys) = buildStatement rest rem
      in (str <> "<" <> key <> ">" <> str', key : keys)
```

我们最不需要的是一个 final helper，它将把`buildStatement`的结果转换成`Statement`。我们就叫这个`finalizeStatement`，然后就完事了！

```
parseStatementLine :: String -> RE Char Statement
parseStatementLine signal = string signal *> sym ‘ ‘ *>
  (finalizeStatement . buildStatement <$> 
    many ((,) <$> nonBrackets <*> insideBrackets) <*> nonBrackets)
  where
    buildStatement :: 
      [(String, String)] -> String -> (String, [String])
    buildStatement [] last = (last, [])
    buildStatement ((str, key) : rest) rem =
      let (str', keys) = buildStatement rest rem
      in (str <> "<" <> key <> ">" <> str', key : keys) finalizeStatement :: (String, [String]) -> Statement
    finalizeStatement (regex, variables) = Statement regex variables
```

# 情节

既然我们已经准备好了所有的部分，那么为场景编写解析器就很容易了！首先，我们通过读取关键字获得标题，然后是该行的其余部分:

```
scenarioParser :: RE Char Scenario
scenarioParser = string “Scenario: “ *> readThroughEndOfLine ...
```

之后我们看了很多语句，然后是例表。由于示例表可能不存在，我们将提供一个纯空表作为替代。我们可以通过映射`Scenario`构造函数将所有东西包装在一起。

```
scenarioParser :: RE Char Scenario
scenarioParser = Scenario <$>
  (string “Scenario: “ *> readThroughEndOfLine) <*>
  many (statementParser <* sym ‘\n’) <*>
  (exampleTableParser <|> pure (ExampleTable [] []))
```

我们还可以制作一个非常相似的“后台”解析器。所有的变化是，我们阅读字符串“背景”而不是一个标题。因为我们将标题硬编码为“背景”，所以我们可以将它包含在构造函数中，并将其映射到解析器上。

```
backgroundParser :: RE Char Scenario
backgroundParser = Scenario “Background” <$>
  (string “Background:” *> readThroughEndOfLine) *>
 many (statementParser <* sym ‘\n’) <*>
  (exampleTableParser <|> pure (ExampleTable [] []))
```

# 最后一个功能

我们快完成了！我们剩下的就是写`featureParser`本身了！与场景一样，我们将从关键字和标题行开始:

```
featureParser :: RE Char Feature
featureParser = Feature <$>
  (string “Feature: “ *> readThroughEndOfLine) <*>
  ...
```

现在我们将使用`optional`组合子来解析`Background`(如果它存在),如果不存在则返回`Nothing`。然后，我们将结束解析许多场景！

```
featureParser :: RE Char Feature
featureParser = Feature <$>
  (string “Feature: “ *> readThroughEndOfLine) <*>
  (optional backgroundParser) <*>
  (many scenarioParser)
```

注意，这里我们忽略了我们作为原始语法的一部分提出的特性的“描述”。因为没有相应的关键字，所以使用应用解析来处理它是很痛苦的。当我们从下周开始研究一元方法时，我们会发现这并不困难。

# 结论

这就结束了我们对应用解析的探索。我们可以看到 Haskell 非常适合解析。这种语言的功能性意味着很容易从像我们的第一个解析器这样的小构件开始。然后我们可以逐渐把它们组合起来，做成更大的东西。将我们的头脑包裹在所有不同的操作符和组合符中可能有点棘手。但是一旦你理解了这些让我们组合我们的解析器的方式，它们就很有意义并且易于使用。

为了加深你对有用的 Haskell 库的了解，下载我们免费的[生产清单](https://www.mmhaskell.com/production-checklist)！它会告诉你许多任务的库，从数据库到机器学习！

如果你以前从未写过一行 Haskell，不要害怕！下载我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)了解更多！