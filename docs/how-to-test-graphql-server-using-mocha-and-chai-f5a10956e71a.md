# 如何使用 Mocha 和 Chai 测试 GraphQL 服务器

> 原文：<https://medium.com/hackernoon/how-to-test-graphql-server-using-mocha-and-chai-f5a10956e71a>

当 Kolosek 团队第一次[开始在 NodeJS 项目](https://kolosek.com/sails-graphql-guide/)中使用 GraphQL 时，有一场编写测试的斗争:团队阅读了大量博客，寻找最佳方法。不幸的是，似乎没有，所以团队决定自己做一个，这就是。

本文将帮助您使用 [**【摩卡】**](https://mochajs.org/#getting-started)[**【柴】**](http://chaijs.com/api/)[**SuperTest**](https://github.com/visionmedia/supertest)和 [**GraphQL**](http://graphql.org/graphql-js/) 来测试您的 GraphQL 服务器。

首先，让我们设置运行测试所需的一切:

```
npm i --save-dev mocha chai supertest graphql
```

现在你已经安装好了所有的东西，接下来你可以继续**导入你将在 *User.test.js* 文件中使用的所有依赖项**:

```
const chai = require('chai');const expect = chai.expect;
const url = `http://localhost:3001/`;
const request = require('supertest')(url);describe('GraphQL', () => {
    // Tests
});
```

*设置甚至比* [*设置 RSpec*](https://kolosek.com/rails-rspec-setup/) *进行测试还要简单！*

在运行任何测试之前，您必须将一个命令添加到您的包中。

```
 "scripts": {
      "test": "mocha test/*.test.js"
  }
```

这将告诉**摩卡**用*测试所有文件。在你的测试文件夹中测试*扩展。要运行测试，只需键入:

```
npm test
```

它将显示 **0 通过**，因为您还没有编写任何测试用例。

让我们定义一个 graphql 模式，您将使用它来编写测试。例如，在这里，您将在 *UserType.js* 文件中创建一个包含用户基本信息的用户类型:

```
const graphql = require('graphql');export default new graphql.GraphQLObjectType({
name : 'UserType',
 fields : {
     id : {
         type : graphql.GraphQLInt
     },
     name : {
         type : graphql.GraphQLString
     },
     username:{
         type: graphql.GraphQLString
     },
     email : {
         type : graphql.GraphQLString
     }
});
```

*科洛塞克团队组织测试的方式与其组织* [*RSpec 控制器测试*](https://kolosek.com/rspec-controller-test/) *的方式相似！*

让我们定义查询字段，该字段将向用户返回您刚才定义的字段:

```
const graphql = require('graphql');
const UserType = require('./UserType').default;export default {
 user: {
   description: 'Returns information about user/s',
   type: new graphql.GraphQLList(UserType),
   args: {
     id: { type: graphql.GraphQLInt },
   },
   resolve: async (_, { id }) => {
       if (id) return User.find({ id })
       return User.find()
   }
 },
```

为了能够查询用户，您必须定义 GraphQL 模式:

```
const graphql = require('graphql');
const UserQuery = require('./UserQuery');export default new graphql.GraphQLSchema({
    name: 'Query',
    fields: UserQuery
})
```

在开始编写测试之前要做的最后一件事是创建一个通用的 GraphQL 控制器，它将为每个查询触发，这样客户端应用程序(您是使用 React 的[)和您的测试也有一个端点来发出请求。](https://kolosek.com/react-first-steps/)

```
const graphql = require('graphql');
const schema = require('./schema').default;module.exports = {
   graphql: async (req, res) => {
     try {
       const result = await graphql(schema, req.body.query, req);
       if (result.errors) throw (result.errors);
       return res.ok(result);
     } catch (err) {
       return res.badRequest(err);
     }
   },
```

现在您已经定义了用户类型，为它定义了查询对象，并将其包含在 GraphQLSchema 中，您已经准备好**编写测试**。

让我们假设在您的数据库中有一些用户，剩下唯一要做的事情就是向您的 *GraphQLController* 发送带有 *supertest* 的请求，并输出您的查询结果:

```
const chai = require('chai');const expect = chai.expect;
const url = `http://localhost:3001/`;
const request = require('supertest')(url);describe('GraphQL', () => {
    it('Returns user with id = 10', (done) => {
        request.post('/graphql')
        .send({ query: '{ user(id: 10) { id name username email } }'})
        .expect(200)
        .end((err,res) => {
            // res will contain array with one user
            if (err) return done(err);
            res.body.user.should.have.property('id')
            res.body.user.should.have.property('name')
            res.body.user.should.have.property('username')
            res.body.user.should.have.property('email')
            done();
          })
     }) it('Returns all users', (done) => {
         request.post('/graphql')
         .send({ query: '{ user { id name username email } }' })
         .expect(200)
         .end((err, res) => {
             // res will contain array of all users
             if (err) return done(err);
             // assume there are a 100 users in the database
             res.body.user.should.have.lengthOf(100);
          })
      })
});
```

当然，柴库提供了更多的选择。

就这样，我们结束了这篇文章。

希望你喜欢它，并发现它的信息！

*原载于 2018 年 4 月 23 日*[*kolosek.com*](https://kolosek.com/testing-graphql-server/?utm_source=hn)*。*