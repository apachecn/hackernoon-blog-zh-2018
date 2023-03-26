# Beam:没有模板的数据库能力 Haskell！

> 原文：<https://medium.com/hackernoon/beam-database-power-without-template-haskell-77a2df12fa24>

![](img/e6d75990952e16b8b37a9fb321caefc3.png)

作为我们的 [Haskell Web 系列](https://www.mmhaskell.com/haskell-web)的一部分，我们检查了[持久性](http://hackage.haskell.org/package/persistent)和 [Esqueleto](http://hackage.haskell.org/package/esqueleto) 库。第一种方法允许您以特殊的语法创建数据库模式。然后，您可以使用模板 Haskell 为您的类型生成所有必需的 Haskell 数据类型和实例。更好的是，您可以编写类似 SQL 的 Haskell 代码来查询这些内容。这些查询是类型安全的，这很棒。然而，用模板 Haskell 指定我们的模式的需求带来了一些缺点。例如，代码需要更长的时间来编译，对于初学者来说不容易理解。

本周在博客上，我们将探索另一个名为 [Beam](https://tathougies.github.io/beam/) 的数据库。这个库允许我们使用模板 Haskell 指定我们的数据库模式*,而不需要*。有一些样板涉及，但它一点也不坏！和 Persistent 一样，Beam 支持很多后端，比如 SQLite 和 PostgresQL。与 Persistent 不同，Beam 还支持连接查询作为其系统的内置部分。

想了解更多关于高级库的想法，请务必查看我们的[制作清单](https://www.mmhaskell.com/production-checklist)！它包括几个不同的数据库选项。

# 指定我们的类型

首先要注意的是，虽然 Beam 不需要模板 Haskell，但是它需要很多其他的编译器扩展。你可以看看下面附录中的那些，或者看看 Github 上的[示例代码](https://www.github.com/jhb563/BeamExample)。现在让我们回想一下在使用 Persistent 时我们是如何指定模式的:

```
import qualified Database.Persist.TH as PTHPTH.share [PTH.mkPersist PTH.sqlSettings, PTH.mkMigrate "migrateAll"] [PTH.persistLowerCase|
  User sql=users
    name Text
    email Text
    age Int
    occupation Text
    UniqueEmail email
    deriving Show Read Eq Article sql=articles
    title Text
    body Text
    publishedTime UTCTime
    authorId UserId
    UniqueTitle title
    deriving Show Read Eq
```

对于 Beam，我们不会使用模板 Haskell，所以我们实际上将创建普通的 Haskell 数据类型。尽管如此，还是会有一些奇怪的事情。首先，按照惯例，我们将在末尾用额外的字符`T`来指定我们的类型。这是不必要的，但是约定帮助我们记住什么类型与表相关。我们还必须提供一个额外的类型参数`f`，稍后我们会详细讨论:

```
data UserT f =
  …data ArticleT f =
  ...
```

我们的下一个约定是在我们的字段名前面使用下划线。与 Persistent 不同，我们还将在字段名称中指定类型名称。有了这些约定，我遵从了图书馆创建者 Travis 的建议。

```
data UserT f =
  { _userId :: ...
  , _userName :: …
  , _userEmail :: …
  , _userAge :: …
  , _userOccupation :: …
  }data ArticleT f =
  { _articleId :: …
  , _articleTitle :: …
  , _articleBody :: …
  , _articlePublishedTime :: …
  }
```

因此，当我们指定每个字段的实际类型时，我们只需放入相关的数据类型，如`Int`、`Text`或其他，对吗？不完全是。为了完成我们的类型，我们将用我们想要的类型填充每个字段，除了通过`Columnar f`指定的。此外，我们将在这两种类型上派生`Generic`，这将允许 Beam 发挥它的魔力:

```
data UserT f =
  { _userId :: Columnar f Int64
  , _userName :: Columnar f Text
  , _userEmail :: Columnar f Text
  , _userAge :: Columnar f Int
  , _userOccupation :: Columnar f Text
  } deriving (Generic)data ArticleT f =
  { _articleId :: Columnar f Int64
  , _articleTitle :: Columnar f Text
  , _articleBody :: Columnar f Text
  , _articlePublishedTime :: Columnar f Int64 -- Unix Epoch
  } deriving (Generic)
```

现在这个和我们之前的模式有一些小的不同。首先，我们将主键作为我们类型的显式字段。对于持久化，我们使用`Entity`抽象将其分离。我们将在下面看到如何处理不知道密钥的情况。第二个区别是(目前)，我们在文章中省略了`userId`字段。我们将在处理主键时添加这一点。

# 圆柱的

那么这个`Columnar`到底是关于什么的呢？在大多数情况下，我们希望用原始字段类型指定一个`User`。但是在某些情况下，我们不得不对 SQL 表达式使用更复杂的类型。我们先从简单的案例开始。

幸运的是，`Columnar`的工作方式是，如果我们使用`Identity`来表示`f`，我们可以使用原始类型来填充字段值。我们将专门为这种身份情况创建一个类型同义词。然后我们可以举一些例子:

```
type User = UserT Identity
type Article = ArticleT Identityuser1 :: User
user1 = User 1 "James" "james@example.com" 25 "programmer"user2 :: User
user2 = User 2 "Katie" "katie@example.com " 25 "engineer"users :: [User]
users = [ user1, user2 ]
```

注意，如果你觉得重复`Columnar`关键字很麻烦，你可以把它缩短为`C`:

```
data UserT f =
  { _userId :: C f Int64
  , _userName :: C f Text
  , _userEmail :: C f Text
  , _userAge :: C f Int
  , _userOccupation :: C f Text
  } deriving (Generic)
```

现在，我们的初始示例将为所有字段分配原始值。因此，除了`Identity`之外，我们最初不需要为`f`参数使用任何东西。再往下，我们将处理自动递增主键的情况。在这种情况下，我们将使用`default_`函数，它的类型实际上是一个 SQL 表达式的束形式。在这种情况下，我们将为`f`使用不同的类型，但是灵活性将允许我们继续使用我们的`User`构造函数！

# 我们类型的实例

现在我们已经指定了我们的类型，我们可以使用`Beamable`和`Table`类型类告诉 Beam 更多关于我们的类型的信息。在我们将这些类型中的任何一个设为`Table`之前，我们需要指定它的主键类型。所以让我们再做几个类型同义词来表示这些:

```
type UserId = PrimaryKey UserT Identity
type ArticleId = PrimaryKey ArticleT Identity
```

现在，让我们将外键添加到我们的`Article`类型中:

```
data ArticleT f =
  { _articleId :: Columnar f Int64
  , _articleTitle :: Columnar f Text
  , _articleBody :: Columnar f Text
  , _articlePublishedTime :: Columnar f Int64
  , _articleUserId :: PrimaryKey UserT f
  } deriving (Generic)
```

我们现在可以在主类型和主键类型上为`Beamable`生成实例。我们还将派生出`Show`和`Eq`的实例:

```
data UserT f =
  …deriving instance Show User
deriving instance Eq Userinstance Beamable UserT
instance Beamable (PrimaryKey UserT)data ArticleT f =
  …deriving instance Show Article
deriving instance Eq Articleinstance Beamable ArticleT
instance Beamable (PrimaryKey ArticleT)
```

现在我们将为`Table`类创建一个实例。这将涉及一些类型族语法。我们将指定`UserId`和`ArticleId`作为我们的主键数据类型。然后我们可以填充`primaryKey`函数来匹配正确的字段。

```
instance Table UserT where
  data PrimaryKey UserT f = UserId (Columnar f Int64) deriving Generic
  primaryKey = UserId . _userIdinstance Table ArticleT where
  data PrimaryKey ArticleT f = ArticleId (Columnar f Int64) deriving Generic
  primaryKey = ArticleId . _articleId
```

# 存取透镜

我们将再做一件事来模仿持久性。模板 Haskell 自动为我们生成镜片。我们可以在进行数据库查询时使用它们。下面，我们将使用类似的东西。但是我们将使用一个特殊的函数`tableLenses`，来制作这些而不是模板 Haskell。如果你还记得我们如何使用 [Servant Client](https://hackage.haskell.org/package/servant-client) 库，我们可以通过使用`client`并将其与模式匹配来创建客户端函数。我们将对`tableLenses`做一些类似的事情。我们将在表的每个字段上使用`LensFor`,并创建一个构造项目的模式。

```
User
  (LensFor userId)
  (LensFor userName)
  (LensFor userEmail)
  (LensFor userAge)
  (LensFor userOccupation) = tableLensesArticle
  (LensFor articleId)
  (LensFor articleTitle)
  (LensFor articleBody)
  (LensFor articlePublishedTime)
  (UserId (LensFor articuleUserId)) = tableLenses
```

注意我们必须在`UserId`中包装外键镜头。

# 创建我们的数据库

与 Persistent 不同，我们将创建一个额外的类型来表示我们的数据库。我们的两个表在这个数据库中都有一个字段:

```
data BlogDB f = BlogDB
  { _blogUsers :: f (TableEntity UserT)
  , _blogArticles :: f (TableEntity ArticleT)
  } deriving (Generic)
```

我们需要让我们的数据库类型成为`Database`类的一个实例。我们还将指定一组可以在数据库中使用的默认设置。这两项都将涉及一个参数`be`，它代表后端(例如 SQLite、Postgres)。我们暂时保留这个通用参数。

```
instance Database be BlogDBblogDb :: DatabaseSettings be BlogDB
blogDb = defaultDbSettings
```

# 插入我们的数据库

现在，用 Beam 迁移我们的数据库比用 Persistent 稍微复杂一些。我们可能会在以后的文章中讨论这个问题。现在，我们将保持简单，使用 SQLite 数据库并自己迁移它。因此，让我们首先创建我们的表。在这里，我们必须遵循 Beam 的约定，特别是在外键的`user_id__id`字段上:

```
CREATE TABLE users \
  ( id INTEGER PRIMARY KEY AUTOINCREMENT\
  , name VARCHAR NOT NULL \
  , email VARCHAR NOT NULL \
  , age INTEGER NOT NULL \
  , occupation VARCHAR NOT NULL \
  );
CREATE TABLE articles \
  ( id INTEGER PRIMARY KEY AUTOINCREMENT \
  , title VARCHAR NOT NULL \
  , body VARCHAR NOT NULL \
  , published_time INTEGER NOT NULL \
  , user_id__id INTEGER NOT NULL \
  );
```

现在我们想写几个可以与数据库交互的查询。让我们从插入原始用户开始。我们首先打开一个 SQLite 连接，我们将编写一个使用该连接的函数:

```
import Database.SQLite.Simple (open, Connection)main :: IO ()
main = do
  conn <- open "blogdb1.db"
  insertUsers conninsertUsers :: Connection -> IO ()
insertUsers = ...
```

我们通过使用`runBeamSqlite`并传递连接来开始我们的表达式。然后，我们使用`runInsert`向 Beam 指定我们希望做一个 insert 语句。

```
import Database.Beam
import Database.Beam.SQLiteinsertUsers :: Connection -> IO ()
insertUsers conn = runBeamSqlite conn $ runInsert $
  ...
```

现在我们将使用`insert`函数，并发出信号通知我们希望从数据库中删除哪一个表:

```
insertUsers :: Connection -> IO ()
insertUsers conn = runBeamSqlite conn $ runInsert $
  insert (_blogUsers blogDb) $ ...
```

最后，由于我们正在插入原始值(`UserT Identity`，我们使用`insertValues`函数来完成这个调用:

```
insertUsers :: Connection -> IO ()
insertUsers conn = runBeamSqlite conn $ runInsert $
  insert (_blogUsers blogDb) $ insertValues users
```

现在我们可以检查并验证我们的用户是否存在！

```
SELECT * FROM users;
1|James|james@example.com|25|programmer
2|Katie|katie@example.com|25|engineer
```

让我们对文章做同样的事情。我们将使用`pk`函数来访问特定`User`的主键:

```
article1 :: Article
article1 = Article 1 "First article" 
  "A great article" 1531193221 (pk user1)article2 :: Article
article2 = Article 2 "Second article" 
  "A better article" 1531199221 (pk user2)article3 :: Article
article3 = Article 3 "Third article" 
  "The best article" 1531200221 (pk user1)articles :: [Article]
articles = [ article1, article2, article3]insertArticles :: Connection -> IO ()
insertArticles conn = runBeamSqlite conn $ runInsert $
  insert (_blogArticles blogDb) $ insertValues articles
```

# 选择查询

现在我们已经插入了一些元素，让我们运行一些基本的 select 语句。一般来说，对于 select，我们需要`runSelectReturningList`函数。如果需要，我们还可以查询具有不同功能的单个元素:

```
findUsers :: Connection -> IO ()
findUsers conn = runBeamSqlite conn $ do
  users <- runSelectReturningList $ ...
```

现在我们将使用`select`而不是上一个查询中的`insert`。我们还将在数据库中的用户字段上使用函数`all_`来表示我们想要所有用户。这就是我们所需要的！：

```
findUsers :: Connection -> IO ()
findUsers conn = runBeamSqlite conn $ do
  users <- runSelectReturningList $ select (all_ (_blogUsers blogDb))
  mapM_ (liftIO . putStrLn . show) users
```

要进行过滤查询，我们将从相同的框架开始。但是现在我们需要将我们的`select`语句增强为一元表达式。我们将从从所有用户中选择`user`开始:

```
findUsers :: Connection -> IO ()
findUsers conn = runBeamSqlite conn $ do
  users <- runSelectReturningList $ select $ do
   user <- (all_ (_blogUsers blogDb))
    ...
  mapM_ (liftIO . putStrLn . show) users
```

我们现在将通过使用`guard_`和应用我们的一个镜头来过滤。我们使用一个`==.`操作符来表示等式，就像持久化一样。我们还必须用`val`包装我们的原始比较值:

```
findUsers :: Connection -> IO ()
findUsers conn = runBeamSqlite conn $ do
  users <- runSelectReturningList $ select $ do
    user <- (all_ (_blogUsers blogDb))
    guard_ (user ^. userName ==. (val_ "James"))
    return user
  mapM_ (liftIO . putStrLn . show) users
```

这就是我们所需要的！Beam 将为我们生成 SQL！现在让我们试着做一个连接。这在 Beam 中实际上比在 Persistent/Esqueleto 中简单得多。我们所需要的只是在文章的“选择”中添加一些陈述。我们将按用户 ID 过滤它们！

```
findUsersAndArticles :: Connection -> IO ()
findUsersAndArticles conn = runBeamSqlite conn $ do
  users <- runSelectReturningList $ select $ do
    user <- (all_ (_blogUsers blogDb))
    guard_ (user ^. userName ==. (val_ "James"))
    articles <- (all_ (_blogArticles blogDb))
    guard_ (article ^. articleUserId ==. user ^. userId)
    return user
  mapM_ (liftIO . putStrLn . show) users
```

这就是全部了！

# 自动递增主键

在上面的例子中，我们硬编码了所有的 id。但是这通常不是您想要的。我们应该让数据库通过某种规则分配 ID，在我们的例子中是自动递增的。在这种情况下，我们将创建一个“表达式”，而不是创建一个`User`“值”。这可以通过我们类型中的多态参数`f`来实现。我们将省略类型签名，因为它有点令人困惑。但是下面是我们要创建的表达式:

```
user1' = User
  default_ 
  (val_ "James")
  (val_ "james@example.com")
  (val_ 25)
  (val_ "programmer")
```

我们使用`default_`来表示一个告诉 SQL 使用默认值的表达式。然后我们用`val_`提升我们所有的其他值。最后，我们将在 Haskell 表达式中使用`insertExpressions`而不是`insertValues`。

```
insertUsers :: Connection -> IO ()
insertUsers conn = runBeamSqlite conn $ runInsert $
  insert (_blogUsers blogDb) $ insertExpressions [ user1' ]
```

然后，我们将有我们的自动递增键！

# 结论

我们对`Beam`图书馆的介绍到此结束。正如我们所看到的，Beam 是一个很棒的库，它允许您在不使用任何模板 Haskell 的情况下指定数据库模式。更多细节，请务必查看[文档](https://tathougies.github.io/beam/)！

要更深入地了解如何使用 Haskell 库制作 web 应用程序，请务必阅读我们的 [Haskell Web 系列](https://www.mmhaskell.com/haskell-web)。它讲述了一些数据库机制以及创建 API 和测试。作为一个额外的挑战，尝试重写该系列中的代码以使用 Beam 而不是 Persistent。看看需要修改多少`Servant`代码来适应这种情况。

更多酷库的例子，请下载我们的[生产清单](https://www.mmhaskell.com/production-checklist)！您可以查看更多的数据库和 API 库！

# 附录:编译器扩展

```
{-# LANGUAGE DeriveGeneric #-}
{-# LANGUAGE GADTs #-}
{-# LANGUAGE OverloadedStrings #-}
{-# LANGUAGE FlexibleContexts #-}
{-# LANGUAGE FlexibleInstances #-}
{-# LANGUAGE TypeFamilies #-}
{-# LANGUAGE TypeApplications #-}
{-# LANGUAGE StandaloneDeriving #-}
{-# LANGUAGE TypeSynonymInstances #-}
{-# LANGUAGE NoMonoMorphismRestriction #-}
```