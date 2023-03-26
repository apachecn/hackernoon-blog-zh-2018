# 将 graphqlgen 用于我们的云堆栈

> 原文：<https://medium.com/hackernoon/using-graphqlgen-for-our-cloud-stack-ae30c4b017a1>

## TL；灾难恢复— Apollo 服务器和类型脚本 graphqlgen

在 Meeshkan，我们为 GraphQL 模式的 JS 对象表示编写自己的 typescript 接口，这被证明是容易出错和费力的。

Prisma 的新`[graphqlgen](https://github.com/prisma/graphqlgen)`包让这个疯狂的过程变得简单，但是如果你还不习惯 GraphQL 模式，它就像是在一个已经无法理解的堆栈中的另一层不理解。我想花一点时间来分享使用这一新工具的一些重要收获，以防您难以入门:

*   Apollo 服务器中的解析器实际上是如何工作的。
*   为什么`graphqlgen`牛逼。
*   为什么*永远不要*使用 prisma 类型作为服务器模式的类型。
*   奖金:如何单元您的测试解析器。

# 解析器如何在 Apollo 服务器中工作

在您能够有效地使用`graphqlgen`之前，您需要了解解析器在 Apollo Server 中如何工作的基础知识。

帮助我寻找解析器的主要事情是了解它们是如何级联的，就像`redux`减速器一样。来自 Apollo Server 的[文档](https://www.apollographql.com/docs/apollo-server/essentials/data.html)是一个有益的开始，但是我不太明白所有的事情是如何联系在一起的，我编造了一个糟糕的例子来展示他们试图解决的问题。

让我们假设您的模式是:

```
type User {
  id: ID! @unique
  posts: [Post!]!
}type Post {
  id: ID! @unique
  author: User!
}type Query {
  user(id): User
}
```

编写解析器的一种(不好的)方式是这样的:

```
export const Query: MyQueryResolverType = {
  user: async (_, { id }, __) => {
    const user = await getUserFromDatabase(id); // fetch user
    const posts = await getUserPostsFromDatabase(id); // fetch posts
    return {
       ...user,
       posts,
    }
  }
}export const User: MyUserResolverType {
  id: ({ id }, _, __) => id,
  posts: ({ posts }, _, __) => posts,
}export const Post: MyPostResolverType {
  id: ( { id }, _, __) => id,
  author: ({ author }, _, __) => author,
}
```

上面的代码有很多问题:我们不得不为像`MyQueryResolverType`、`MyUserResolverType`和`MyPostResolverType`这样的解析器编写自己的类型。`graphqlgen`将为我们处理这些问题，稍后我们会看到这一点，但目前我们仍受困于这种脆弱性。

这一部分的主要问题是`user`查询做的工作太少又太多。我们不知道传入的 graphql 查询是否会要求发布，那么我们为什么要在这里获取它们呢？此外，如果这个人做了如下事情:

```
{
  user(id) {
    posts {
      author {
        posts {
          author {
            id
          }
        }
      }
    }
  }
}
```

…这将会断开，因为它不会沿树向下递归。所以让我们用合适的解决方案来解决它。

```
export const Query: MyQueryResolverType = {
  user: (_, { id }, __) => getUserFromDatabase(id); // a promise
}export const User: MyUserResolverType {
  id: ({ id }, _, __) => id,
  posts: ({ id }, _, __) => getPostsFromUser(id), // a promise
}export const Post: MyPostResolverType {
  id: ({ id }, _, __) => id,
  author: ({ id }, _, __) => getUserFromPost(id); // a promise
}
```

不会再有破损。ApolloServer 沿树向下遍历，根据需要调用正确的解析器。这是因为传递给解析器函数的第一个参数是从上一个解析器返回的对象的表示，我们用它来获取关于对象的重要信息，如`id`。在 ApolloServer 文档中，这个对象被称为`parent`，我认为这是一个糟糕的名字选择。我会选择`partial_obj`或者`obj_hint`或者`obj_with_stuff_missing`。

# 为什么`graphqlgen`很牛逼

上面这个“好”的例子(至少)有两个讨厌的反模式:解析器类型需要手写，还有像`Post.id`这样基本上是身份函数的函数。`graphqlgen`修复这个问题。它将自动生成解析器类型的类型脚本接口*和解析器的默认解析器*。再见样板！

要使用`graphqlgen`，只需在您的根目录中包含一个`graphqlgen.yml`文件，然后在您的构建中的某个位置包含`yarn add --dev graphqlgen`和`npx graphqlgen`来生成商品。在撰写本文时，`graphqlgen.yml`文件有六个我们使用的键，所有这些键都是不言自明的:

```
# The target programming language for the generated code
language: typescript# The file path pointing to your GraphQL schema
schema: ./src/schema.graphql# Type definition for the resolver context object
# The context object is passed into ApolloServer.
# It usually contains stuff like a database connection
# or user id or whatever.
context: ./src/types.ts:IContext# Find TS models that correspond to the graphql schema and map them
# NOTE: this part is tricky and is the subject of the next section,
# so read on to find out more.
models:
  files:
    - path: path/to/my/resolver/types.ts
      defaultName: 'I${typeName}' # my convention
    - path: path/to/my/generated/prisma/types
      defaultName: '${typeName}Node' # prisma convention# Where all of this boilerplate code is written
output: ./src/generated/graphqlgen.ts# In case you need inspiration for your resolvers, this generates
# stubs that you can fill in
resolver-scaffolding:
  output: ./src/generated/tmp-resolvers/
  layout: file-per-type # one file per type in the 
```

现在，如果我们重温之前的例子，事情就变得简单多了:

```
export const Query: QueryResolvers.Type = {
  user: (_, { id }, __) => getUserFromDatabase(id); // a promise
}export const User: UserResolvers.Type {
  ...UserResolvers.defaultResolvers,
  posts: ({ id }, _, __) => getPostsFromUser(id), // a promise
}export const Post: PostResolvers.Type {
  ...PostResolvers.defaultResolvers,
  author: ({ id }, _, __) => getUserFromPost(id); // a promise
}
```

所有类型的解析器都在`src/generated/graphqlgen`中出现，我们不再需要推出自己的解析器。此外，为每种类型都生成了默认的解析器，因此我们不再需要编写所有的样板文件。太棒了。

# 为什么不应该使用 prisma 类型作为服务器模式的类型

Prisma 非常有效率。效率如此之高，以至于您只想使用 GraphQL 和 Apollo Server 的 dispense 从客户端 ping 您的数据库。这相当于直接从网页上读写数据库，简而言之，这是一个非常糟糕的想法。

现在`graphqlgen`已经出现了，以创纪录的速度创建一个 Apollo 服务器更加方便，在这样做的时候，您可能会尝试使用 Prisma 生成的接口作为表示模式中类型的接口。这也是个馊主意。例如，假设您的棱镜`datamodel.prisma`包含:

```
type UserNode {
  id: ID! @unique
  email: String!
  internalFieldUsedByDevs: String!
  anotherInternalField: String!
}
```

鉴于我们的`schema.graphql`包含:

```
type IUserNode {
  id: ID! @unique
  email: String
}
```

如果您不希望`internalFieldUsedByDevs`和`anotherInternalField`被您的客户端访问，您可能不希望与 prisma 生成的类型相同。更明智的选择是编写自己的类型并使用它。没问题，除了`graphqlgen`怎么知道怎么用你的而不是 prisma 的？让我们回到`graphqlgen.yml`。

```
models:
  files:
    - path: path/to/my/resolver/types.ts
      defaultName: 'I${typeName}' # my convention
    - path: path/to/my/generated/prisma/types
      defaultName: '${typeName}Node' # prisma convention
```

这里，**顺序事项**。`graphqlgen`将首先使用`types.ts`中的类型，然后尝试从生成的 prisma 类型中获取它没有找到的任何内容。这样，`Query.me`将被正确地记录和类型检查为我们编写的`IUserNode`,而不是 prisma 生成的`UserNode`。

虽然这乍一看似乎微不足道，但是当您的服务器模式和 prisma 模式开始出现分歧时，您最终会发现您的服务器模式并不是 prisma 模式的严格子集。因此，将两者分开将变得至关重要，最好尽早养成习惯。

# 额外收获:如何对解析器进行单元测试

与`graphqlgen`没有任何关系，但是，嘿，既然我们在讨论编写解析器，那就别忘了测试它们！

```
import * as request from "supertest";
import app from "../../path/to/my/express/app";const ENDPOINT = "/graphql";test("test hello", async () => {
  const { text } = await request(app)
      .post(ENDPOINT)
      .send({query: "{ hello }"});
  expect(JSON.parse(text).data.hello).toBe("Hello!");
});
```

平淡无奇，我知道，但最干净的东西往往是。因为我在网上找不到任何简洁明了的东西，所以我想把它放在这里。

这假设您已经在服务器的单独文件中设置了一个 express 应用程序，这样您就不必在每次测试时启动和停止服务器，这可能会导致各种与端口相关的奇怪错误。

我们在 Meeshkan 使用 Jest，但摩卡或其他任何东西都可以。在某些时候，您将需要模拟您的数据库通信对象(在我们的例子中，是由`prisma generate`生成的`prisma`实例)，这在这些平台上都非常简单。

所以你有它！几乎没有痛苦的类型检查、默认解析器搭建和单元测试。w00t！

![](img/a2cf0d1db3947d990d8c6919c3ee502c.png)