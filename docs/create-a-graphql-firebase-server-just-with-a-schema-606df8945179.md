# 仅用一个模式创建一个 GraphQL — Firebase 服务器。🚀

> 原文：<https://medium.com/hackernoon/create-a-graphql-firebase-server-just-with-a-schema-606df8945179>

![](img/6fd7419db1a0f64109c62f010f297800.png)

现在您可以创建一个连接到 Firebase 的 GraphQL 服务器🔥数据库只是在你的终端上运行一个命令。

**如何使用:**

1.  安装 [easygraphql-firebase](https://github.com/EasyGraphQL/easygraphql-firebase) :

```
$ npm install easygraphql-firebase -g
```

2.用您想要的模型创建一个 GraphQL 模式文件:

```
type Student {
  key: ID!
  age: Int!
  name: String!
}

type School {
  key: ID!
  location: String!
  students: Int!
  name: String!
  student: Student!
}

input StudentInput {
  age: Int!
  name: String!
}

input SchoolInput {
  location: String!
  students: Int!
  name: String!
  phone: String!
  student: StudentInput!
}

type Query {
  getStudentByUsername(name: String!): Student!
  getStudents: [Student!]!
  getSchool(name: String!): School!
  getSchools: [School!]!
}

type Mutation {
  createStudent(input: StudentInput!): Student!
  createSchool(input: SchoolInput!): School!
}
```

3.在您的终端上运行:

```
$ easygraphql-firebase
```

就是这样， [easygraphql-firebase](https://github.com/EasyGraphQL/easygraphql-firebase) 将为您做一切事情，它将创建服务器、解析器、测试文件等等…查看视频以查看结果

[https://www.youtube.com/watch?v=e0IEEZ1kHDY](https://www.youtube.com/watch?v=e0IEEZ1kHDY)

如果您想现在部署，请确保已经安装了它，只需传递标志`--now`

```
$ easygraphql-firebase --now
```

如果你喜欢这个包，别忘了在 GitHub 上给个⭐️。

回购:[https://github.com/EasyGraphQL/easygraphql-firebase](https://github.com/EasyGraphQL/easygraphql-firebase)

国家预防机制:https://www.npmjs.com/package/easygraphql-firebase

**网址:**[https://easygraphql.com/](https://easygraphql.com/)