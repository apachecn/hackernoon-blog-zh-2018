# 为 GHC 3 做贡献:破解语法和解析

> 原文：<https://medium.com/hackernoon/contributing-to-ghc-3-hacking-syntax-and-parsing-4c8dea6658d8>

在上周的文章中，我们对 GHC 有了更多的了解。我们完成了基本的开发周期，并探索了代码库的一般结构。我们还通过更新一条错误消息进行了最简单的更改。本周，我们将对编译器进行一些更复杂的修改，展示你可以调整语言的方法。你不太可能做出这样的改变来解决现存的问题。但它会帮助我们更好地理解正在发生的事情。

和往常一样，您可以通过查看我们的其他资源来了解更多关于 Haskell 的基础知识。看看我们的[升空系列](https://www.mmhaskell.com/liftoff)或者下载我们的[初学者清单](https://www.mmhaskell.com/beginners-checklist)！

# 注释和更改词法分析器

让我们用一个简单的改变来热身。我们将添加一些新的语法来允许不同种类的注释。首先，我们必须稍微熟悉一下在`parser/Lexer.x`中定义的 Lexer。让我们试着定义它，这样我们就可以用四个撇号来表示一个注释。下面是我们的代码中可能出现的情况，以及如果我们现在尝试这样做，将会得到的错误消息。

```
module Main where'''' This is our main function
main :: IO ()
main = putStrLn "Hello World!"…Parser error on `''`
Character literals may not be empty
  |
5 | '''' This is our main function
  | ^^
```

现在，很容易添加新的一行来描述如何使用这个令牌。我们可以遵循 Lexer 文件中的示例。GHC 在这里定义了一个普通的单行注释:

```
"-- " ~$docsym .* { lineCommentToken }
"--" [^$symbol \ ] . * { lineCommentToken }
```

它需要两种情况，因为黑线鳕评论。但是我们不需要担心这个。我们可以在一行中指定我们的符号，如下所示:

```
"''''" .* { lineCommentToken }
```

现在我们可以将上面的注释添加到我们的代码中，它可以编译了！

# 添加新关键字

现在让我们看看如何给语言添加一个新的关键字。我们将从一个简单的替换开始。假设我们想像使用`if`一样使用`iffy`这个词。下面是代码片段的样子，以及我们最初得到的编译器错误:

```
main :: IO ()
main = do
  i <- read <$> getLine
  iffy i `mod` 2 == 0
    then putStrLn "Hello"
    else putStrLn "World"…Main.hs:11:5: error: parse error on input 'then'
   |
11 |     then putStrLn "Hello"
   |     ^^^^
```

让我们快速搜索一下关键字“if”在解析器部分中已经存在的位置。我们会找到两个地方。第一个是语言中所有保留字的列表。我们可以通过在列表中添加新的关键字来更新它。我们就找`basicTypes/Lexeme.hs`里的`reservedIds`集，可以添加一下:

```
reservedIds :: Set.Set String
reservedIds = Set.fromList [ …
  , "_", "iffy" ]
```

现在我们还必须解析它，以便它映射到一个特定的令牌。我们可以在`Lexer.x`中看到发生这种情况的一行:

```
( "if", ITif, 0)
```

我们可以在它的正下方添加另一行，将它匹配到同一个`ITif`令牌:

```
( "iffy", ITif, 0)
```

现在，一旦我们开始将语言放在一起，lexer 就会将它与相同的标记进行匹配。现在我们的代码编译并产生预期的结果！

```
lghc Main.hs
./prog.exe
5
World
```

# 反转 If

现在让我们给这个过程增加一点曲折。我们将添加另一个“if”关键字，并将其命名为`reverseif`。这将改变 if 语句的顺序。因此，当布尔值为假时，我们的代码将执行第一个分支，而不是第二个分支。我们需要往上游走一点。我们希望尽可能多地重复使用现有的机器，并在适当的时候颠倒我们的两种表达方式。让我们使用与上面相同的代码，除了 reverse 关键字。那么如果我们输入`5`，我们应该得到`Hello`而不是`World`。

```
main :: IO ()
main = do
  i <- read <$> getLine
  reverseif i `mod` 2 == 0
    then putStrLn "Hello"
    else putStrLn "World"
```

所以我们必须从在 lexer 中当前的`if`标记下给我们的`Token`类型添加一个新的构造函数开始。

```
data Token =
  …
  | ITif
  | ITreverseif
  ...
```

现在我们必须添加一行代码来将我们的关键字转换成这种令牌。

```
...
("if", ITif, 0),
("reverseif", ITreverseif, 0),
...
```

和以前一样，我们也将它添加到我们的关键字列表中:

```
reservedIds :: Set.Set String
reservedIds = Set.fromList [ …
  , "_", "iffy", "reverseif" ]
```

现在让我们看看使用`ITif`构造函数的不同地方。然后我们也可以将它们应用到`ITreverseif`中。我们可以在`Lexer.x`中找到另外两个例子。首先，有一个函数`maybe_layout`，它决定了一个语法结构是否需要一个左括号。然后是`isALRopen`函数，它告诉我们是否可以开始某种其他的缩进。在这两种情况下，我们都将以`ITif`为例:

```
maybe_layout :: Token -> P ()
…
  where
    f ITif = pushLexState layout_if
    f ITreverseif = pushLexState layout_if...
isALRopen ITif = True
isALRopen ITreverseif = True
...
```

在`Parser.y`中还有一点我们需要解析我们的新令牌:

```
%token
 …
 'if' { L _ ITif }
 'reverseif' { L _ ITreverseif }
```

现在我们需要弄清楚这些标记是如何创建语法结构的。这似乎也发生在`Parser.y`中。例如，我们可以看看构造基本 if 语句的部分:

```
| 'if' exp optSemi 'then' exp optSemi 'else' exp
    {% checkDoAndIfThenElse $2 (snd $3) $5 (snd $6) $8 >>
      Ams (sLL $1 $> $ mkHsIf $2 $5 $8)
        (mj AnnIf $1:mj AnnThen $4
          :mj AnnElse $7
          :(map (\l -> mj AnnSemi l) (fst $3))
         ++(map (\l -> mj AnnSemi l) (fst $6))) }
```

这里发生了很多事情，我们现在不打算去理解它！但是我们只需要改变两件事来为`reverseif`制定新的规则。首先，我们显然需要在第一行使用这个标记而不是`if`。

第二，看到第三行的那个`mkHsIf`语句了吗？这是我们在语法树中创建实际 Haskell“If”表达式的地方。`$5`指的是令牌列表中`exp`的第二个实例，`$8`指的是第三个也是最后一个表达式。这些分别是我们的“If”语句的`True`和`False`分支表达式。因此，为了颠倒我们的“如果”，我们需要做的就是将这个论点翻转到第三行！

```
| 'reverseif' exp optSemi 'then' exp optSemi 'else' exp
    {% checkDoAndIfThenElse $2 (snd $3) $5 (snd $6) $8 >>
      Ams (sLL $1 $> $ mkHsIf $2 $8 $5)
        (mj AnnIf $1:mj AnnThen $4
          :mj AnnElse $7
          :(map (\l -> mj AnnSemi l) (fst $3))
         ++(map (\l -> mj AnnSemi l) (fst $6))) }
```

最后，我们还需要做一个改变。添加这一行将在我们的语法中引入一些新的转换/减少冲突。已经有 233 个了，所以我们现在不用太担心。我们需要做的只是改变断言中冲突数量的计数:

```
%expect 235 -- shift/reduce conflicts
```

现在，当我们编译并运行我们的简单程序时，我们确实会看到它像预期的那样工作！

```
lghc Main.hs
./prog.exe
5
Hello
```

# 结论

因此，本周我们看到 GHC 发生了一些更复杂的变化，这些变化产生了切实的影响。下周，我们将通过查看捐献过程来结束对 GHC 的讨论。我们先来看看 Github 的“简单”方式。然后，我们还将使用 Arc 和 Phabricator 等工具完成更复杂的过程。

要了解关于 Haskell 的更多信息，您应该查阅我们的一些基本资料！如果你是这门语言的初学者，请阅读我们的[发射系列](https://www.mmhaskell.com/liftoff)。它会教你如何从头开始使用 Haskell。也可以看看我们的 [Haskell Web 系列](https://www.mmhaskell.com/haskell-web)看看更高级实用的技巧！