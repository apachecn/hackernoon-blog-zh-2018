# 保持整洁:Haskell 代码格式化程序

> 原文：<https://medium.com/hackernoon/keeping-it-clean-haskell-code-formatters-32ca25c59c70>

![](img/cba9195c33ccf857af2af435017b8d53.png)

很久以前，我们有一篇文章介绍了一些关于[如何组织你的进口陈述](https://www.mmhaskell.com/blog/2017/5/8/4-steps-to-a-better-imports-list)的技巧。就我记忆所及，那是我们在代码格式化方面做过的唯一一件事。但是当你和更多的人一起工作时，格式是你需要考虑的一件事，以使每个人都保持理智。您希望在整个代码库中保持一致的风格。这样，代码审查中的争议就少了，人们也不需要花太多心思来更新代码。他们不应该对遵循风格指南或代码片段中存在什么感到疑惑。

本周，我们将介绍三种不同的 Haskell 代码格式化工具。我们将考察[时髦的哈斯克尔](https://github.com/jaspervdj/stylish-haskell)、[欣登](https://github.com/commercialhaskell/hindent)和[布列塔尼](https://github.com/lspitzner/brittany)。正如我们将看到的，这些都有其优缺点。

关于 Haskell 项目的一些想法，请下载我们的[生产清单](https://www.mmhaskell.com/production-checklist)。您也可以参加我们免费的 [Stack 迷你课程](http://academy.mondaymorninghaskell.com/p/your-first-haskell-project)，学习如何使用 Stack 来组织您的代码！

# 时尚 Haskell

我们要看的第一个工具是[时尚的 Haskell](https://github.com/jaspervdj/stylish-haskell) 。这是一个简单易用的工具，因为它不需要任何配置就可以做一些很酷的事情。让我们来看看我们的 [Beam 文章](https://www.mmhaskell.com/blog/2018/7/30/beam-database-power-without-template-haskell)中一个格式不佳的代码版本。

```
{-# LANGUAGE DeriveGeneric #-}
{-# LANGUAGE FlexibleContexts #-}
{-# LANGUAGE FlexibleInstances #-}
{-# LANGUAGE GADTs #-}
{-# LANGUAGE MultiParamTypeClasses #-}
{-# LANGUAGE OverloadedStrings #-}
{-# LANGUAGE StandaloneDeriving #-}
{-# LANGUAGE TypeApplications #-}
{-# LANGUAGE TypeFamilies #-}
{-# LANGUAGE TypeSynonymInstances #-}
{-# LANGUAGE ImpredicativeTypes #-}module Schema whereimport Database.Beam
import Database.Beam.Backend
import Database.Beam.Migrate
import Database.Beam.Sqlite
import Database.SQLite.Simple (open, Connection)import Data.Int (Int64)
import Data.Text (Text)
import Data.Time (UTCTime)
import qualified Data.UUID as Udata UserT f = User
  { _userId :: Columnar f Int64
  , _userName :: Columnar f Text
  , _userEmail :: Columnar f Text
  , _userAge :: Columnar f Int
  , _userOccupation :: Columnar f Text
  } deriving (Generic)
```

这里有很多不可取的东西。我们的语言 pragmas 不会将它们的右括号排成一行。它们也没有任何可辨别的顺序。我们的导入也没有对齐，我们的数据类型中的字段也没有对齐。

时尚的 Haskell 可以解决这一切。首先，我们将在全球范围内安装它:

```
stack install stylish-haskell
```

(也可以用`cabal`代替`stack`)。然后我们可以在一个文件上调用`stylish-haskell`命令。默认情况下，它会将结果输出到终端。但是如果我们传递了`-i`标志，它将就地更新文件。这将使所有我们想要的改变在我们的文件中排列各种各样的陈述！

```
>> stylish-haskell -i Schema.hs--- Result:{-# LANGUAGE DeriveGeneric         #-}
{-# LANGUAGE FlexibleContexts      #-}
{-# LANGUAGE FlexibleInstances     #-}
{-# LANGUAGE GADTs                 #-}
{-# LANGUAGE ImpredicativeTypes    #-}
{-# LANGUAGE MultiParamTypeClasses #-}
{-# LANGUAGE OverloadedStrings     #-}
{-# LANGUAGE StandaloneDeriving    #-}
{-# LANGUAGE TypeApplications      #-}
{-# LANGUAGE TypeFamilies          #-}
{-# LANGUAGE TypeSynonymInstances  #-}module Schema whereimport           Database.Beam
import           Database.Beam.Backend
import           Database.Beam.Migrate
import           Database.Beam.Sqlite
import           Database.SQLite.Simple (Connection, open)import           Data.Int               (Int64)
import           Data.Text              (Text)
import           Data.Time              (UTCTime)
import qualified Data.UUID              as Udata UserT f = User
  { _userId         :: Columnar f Int64
  , _userName       :: Columnar f Text
  , _userEmail      :: Columnar f Text
  , _userAge        :: Columnar f Int
  , _userOccupation :: Columnar f Text
  } deriving (Generic)
```

时尚的 Haskell 与大多数常见的编辑器集成得很好。例如，如果您使用 Vim，您也可以在编辑器中运行该命令:

```
:%!stylish-haskell
```

我们无需任何配置即可获得所有这些功能。如果我们想改变一些事情，我们可以创建一个配置文件。我们将使用以下命令创建一个默认文件:

```
stylish-haskell --defaults > .stylish-haskell.yaml
```

如果我们愿意，我们可以稍微修改一下。举个例子，我们在全球范围内调整了我们的进口。这意味着它们都为`qualified`留出了空间。但是，如果没有合格的导入，我们可以决定不希望一组导入拥有该空间。在配置中有这方面的设置。默认情况下，它看起来像这样:

```
imports:
  align: global
```

我们可以将其更改为`group`,以确保我们的导入只在他们的分组内对齐。

```
imports:
  align: group
```

现在，当我们运行该命令时，我们将得到一个不同的结果:

```
module Schema whereimport Database.Beam
import Database.Beam.Backend
import Database.Beam.Migrate
import Database.Beam.Sqlite
import Database.SQLite.Simple (Connection, open)import           Data.Int  (Int64)
import           Data.Text (Text)
import           Data.Time (UTCTime)
import qualified Data.UUID as U
```

所以简而言之，时髦的 Haskell 对于有限的范围来说是一个伟大的工具。它对几个领域如进口和 pragmas 有无可争议的建议。它还删除了尾随空格，并合理地调整了 case 语句。也就是说，它不会影响您的主 Haskell 代码。让我们来看几个可以做到这一点的工具。

# 欣登

我们可以使用的另一个程序是`[hindent](https://github.com/commercialhaskell/hindent)`。顾名思义，它处理空白和缩进级别的更新。让我们看一个非常简单的例子。考虑这段代码，它改编自我们的 Beam 文章:

```
user1' = User default_  (val_ "James")  (val_ "james@example.com")  (val_ 25)  (val_ "programmer")findUsers :: Connection -> IO ()
findUsers conn = runBeamSqlite conn $ do
    users <- runSelectReturningList $ select $ do
        user <- (all_ (_blogUsers blogDb))
        article <- (all_ (_blogArticles blogDb))
        guard_ (user ^. userName ==. (val_ "James"))
        guard_ (article ^. articleUserId ==. user ^. userId) 
        return (user, article)
    mapM_ (liftIO . putStrLn . show) users
```

有些事情我们可以改变。首先，我们可能希望更新缩进级别，使其为 2 而不是 4。其次，让我们将行大小限制为 80。当我们在这个文件上运行`hindent`时，它会做出改变。

```
user1' =
  User
    default_
    (val_ "James")
    (val_ "james@example.com")
    (val_ 25)
    (val_ "programmer")findUsers :: Connection -> IO ()
findUsers conn =
  runBeamSqlite conn $ do
    users <-
      runSelectReturningList $
      select $ do
        user <- (all_ (_blogUsers blogDb))
        article <- (all_ (_blogArticles blogDb))
        guard_ (user ^. userName ==. (val_ "James"))
        guard_ (article ^. articleUserId ==. user ^. userId)
        return (user, article)
    mapM_ (liftIO . putStrLn . show) users
```

Hindent 也是可配置的。我们可以创建一个文件`.hindent.yaml`。默认情况下，我们将具有以下配置:

```
indent-size: 2
line-length: 80
force-trailing-newline: true
```

但是，如果我们愿意，我们可以更改它，使缩进级别为 3:

```
indent-size: 3
```

现在，当我们运行它时，我们实际上会看到它发生了变化，以反映:

```
findUsers :: Connection -> IO ()
findUsers conn =
   runBeamSqlite conn $ do
      users <-
         runSelectReturningList $
         select $ do
            user <- (all_ (_blogUsers blogDb))
            article <- (all_ (_blogArticles blogDb))
            guard_ (user ^. userName ==. (val_ "James"))
            guard_ (article ^. articleUserId ==. user ^. userId)
            return (user, article)
      mapM_ (liftIO . putStrLn . show) users
```

据我所知，Hindent 还有一些其他的效果是不可配置的。可以看到上面没有保留分割线。在另一个例子中，它分隔了我在另一个文件中分组的实例定义:

```
-- BEFORE
deriving instance Show User
deriving instance Eq User
deriving instance Show UserId
deriving instance Eq UserId-- AFTER
deriving instance Show Userderiving instance Eq Userderiving instance Show UserIdderiving instance Eq UserId
```

因此，在使用它之前，请确保您了解它的所有功能。像`stylish-haskell`，`hindent`与文本编辑器集成得很好。

# 布列塔尼

Brittany 是 Hindent 的替代方法，用于修改表达式定义。它主要关注在你的代码中水平空间的使用。据我所知，它不像`stylish-haskell`那样排列语言编译指令或改变导入语句。它也不涉及数据类型声明。相反，它试图重新格式化你的代码，以最大限度地利用空间，同时避免过长的代码行。作为一个例子，我们可以看看我们的`Beam`例子中的这一行:

```
insertArticles :: Connection -> IO ()
insertArticles conn = runBeamSqlite conn $ runInsert $ 
  insert (_blogArticles blogDb) $ insertValues articles
```

我们在哪里分割线的决定有点武断。但至少我们不会试图将所有内容都塞进一行。但是，如果我们有上面的方法或单行版本，Brittany 会将其更改为:

```
brittany --write-mode=inplace MyModule.hs--insertArticles :: Connection -> IO ()
insertArticles conn =
  runBeamSqlite conn $ runInsert $ insert (_blogArticles blogDb) $ insertValues
    articles
```

这使得水平空间得到“更好”的利用，因为我们在第一行得到了同样多的空间。也就是说，有人会说我们的第一种方法看起来更好。Brittany 还可以更改超出行限制的类型签名。假设我们有这个任意类型的签名，它对于一行来说太长了:

```
myReallyLongFunction :: State ComplexType Double -> Maybe Double -> Either Double ComplexType -> IO a -> StateT ComplexType IO a
```

Brittany 将对其进行修改，以便每个参数类型都在一行中:

```
myReallyLongFunction
  :: State ComplexType Double
  -> Maybe Double
  -> Either Double ComplexType
  -> IO a
  -> StateT ComplexType IO a
```

这在类型非常复杂的项目中非常有用。该结构使您更容易向各种参数添加 Haddock 注释。

# 危险

当然，使用这样的工具有(小)危险。如果你要使用它们，你要确保项目中的每个人都在使用它们。假设人 A 没有使用程序，并且提交了不是由程序格式化的代码。然后，人员 B 可能会仔细检查代码，他们的编辑器会更正文件。这将给他们留下与他们正在做的任何工作都不相关的文件本地更改。当他们提交代码进行评审时，这会导致很多混乱。无论是谁审查他们的代码，都必须仔细检查格式的变化，这减慢了审查的速度。

人们也可能对代码格式有(不合理的)强烈意见。因此，这通常是你希望在项目早期就确定下来，并避免事后改变的事情。对于本文中的例子，我认为在项目中使用时髦的 Haskell 是一件容易的事情。但是，在 H-Indent 和 Brittany 中做出的具体选择可能会更有争议。因此，在整个项目范围内建立这些机制可能会导致更多的问题，而不是解决更多的问题。

# 结论

代码格式化可能会损失大量的生产力。所以尽早和经常地确定标准是很重要的。代码格式化程序可以使特定标准的实施变得容易。使用`stack`和你选择的编辑器，它们也很容易被整合到你的项目中！

既然你已经知道了如何格式化你的代码，还需要一些关于下一步工作的建议吗？看看我们的[生产清单](https://www.mmhaskell.com/production-checklist)！它会给你一些很酷的库的想法，你可以用它来构建 Haskell web 应用程序等等！