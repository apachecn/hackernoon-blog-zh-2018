# 开始测试你的 GraphQL 模式，查询和，突变！

> 原文：<https://medium.com/hackernoon/start-testing-your-graphql-schema-queries-and-mutations-b514911b1368>

![](img/a9beed70463316b5bbe5bcb4b8ab43d1.png)

[EasyGraphQL](https://github.com/EasyGraphQL): Free open source tools for GraphQL

对您的代码进行一些测试总是一个您应该实现的好习惯。你所做的测试将帮助你防止一些错误，也将确保你的应用程序如你所想的那样工作。

有时进行测试可能很困难，需要大量的代码，但大多数时候这取决于您用来测试代码的实现；有一些包可以帮助你用几行代码进行测试。

今天我将介绍`[easygraphql-tester](https://github.com/EasyGraphQL/easygraphql-tester)`，它是一个 npm 包，将帮助您在服务器端和客户端测试您的模式、查询和突变。

## **怎么用？**

使用`[easygraphql-tester](https://github.com/EasyGraphQL/easygraphql-tester)`不需要很多额外的代码来测试你的模式、查询和变异。

**第一步:**

*   导入包`[easygraphql-tester](https://github.com/EasyGraphQL/easygraphql-tester)`
*   读取 GraphQL 模式
*   初始化测试器并将 schemaCode 传递给它

```
const EasyGraphQLTester = require('easygraphql-tester')
const fs = require('fs')
const path = require('path')

const schemaCode = fs.readFileSync(path.join(__dirname, 'schema.gql'), 'utf8')

const tester = new EasyGraphQLTester(schemaCode)
```

**测试查询:**

*   将查询设置为`const`
*   如果测试应该通过，调用`tester` pass 中的`test`,作为第一个参数，作为第二个参数，作为第三个参数，输入期望的变量

```
const EasyGraphQLTester = require('easygraphql-tester')
const fs = require('fs')
const path = require('path')

const schemaCode = fs.readFileSync(path.join(__dirname, 'schema', 'schema.gql'), 'utf8')
const tester = new EasyGraphQLTester(schemaCode)

const query = `
  {
    getMe {
      id
      email
      familyInfo {
        father {
          email
        }
        mother {
          username
        }
      }
    }
  }
`tester.test(true, query)
```

如果查询成功，它将返回您请求的字段的模拟。测试人员还将验证参数，以防查询需要一些参数和参数类型。

**测试一个突变:**

*   将变异设为`const`
*   如果测试应该通过，从`tester` pass 调用`test`,作为第一个参数，作为第二个参数，作为第三个参数，输入期望的变量

```
const EasyGraphQLTester = require('easygraphql-tester')
const fs = require('fs')
const path = require('path')

const schemaCode = fs.readFileSync(path.join(__dirname, 'schema', 'schema.gql'), 'utf8')
const tester = new EasyGraphQLTester(schemaCode)

const mutation = `
  mutation CreateUser{
    createUser {
      email
    }
  }
`tester.test(true, mutation, {
  email: 'test@test.com',
  username: 'test',
  fullName: 'test',
  password: 'test'
})
```

如果查询成功，它将返回您在变异中请求的字段的模拟。

## 嘲弄查询和变异:

`[easygraphql-tester](https://github.com/EasyGraphQL/easygraphql-tester)`可以作为你的查询或变异的模仿者，使用它很简单。

调用方法`.mock()`并传递一个带有以下选项的对象:

*   查询:这将是要测试的查询/变异。
*   变量:如果是一个变异，这是必需的，它必须是一个带有输入字段的对象。
*   fixture:这是可选的，如果你想通过你的自定义 fixture。
*   saveFixture:默认为`false`，如果传递 Fixtures，当再次进行相同的查询时，将其设置为`true`，它将返回 fixture 值。

结果将具有顶级字段，这意味着结果将是一个具有属性的对象，该属性将是带有模拟结果的查询或别名的名称(顶级字段)。

## 模拟例子

```
'use strict'const EasyGraphQLTester = require('easygraphql-tester')
const fs = require('fs')
const path = require('path')const userSchema = fs.readFileSync(path.join(__dirname, 'schema', 'user.gql'), 'utf8')const tester = new EasyGraphQLTester(userSchema)const query = `
  {
    getUser(id: "1") {
      id
      name
      familyInfo {
        lastName
        email
      }
    }
  }
`const fixture = {
  id: '1',
  name: 'EasyGraphQL'
}const { getUser } = tester.mock({ query, fixture })// getUser
{ 
  id: '1',
  name: 'EasyGraphQL',
  familyInfo: [
    { 
      lastName: 'Bartoletti',
      email: 'YSjsYuV@wtnK.com'
    },
    { 
      lastName: 'Bartoletti',
      email: 'YSjsYuV@wtnK.com'
    },
    { 
      lastName: 'Bartoletti',
      email: 'YSjsYuV@wtnK.com'
    }
  ]
}
```

## 错误:

如果查询上有错误或突变`[easygraphql-tester](https://github.com/EasyGraphQL/easygraphql-tester)`会让你知道发生了什么。

**试图访问 getMe 上的无效字段 id->父亲**

```
const EasyGraphQLTester = require('easygraphql-tester')
const fs = require('fs')
const path = require('path')

const schemaCode = fs.readFileSync(path.join(__dirname, 'schema', 'schema.gql'), 'utf8')
const tester = new EasyGraphQLTester(schemaCode)

const query = `
  {
    getMe {
      id
      email
      familyInfo {
        father {
          id
          email
        }
      }
    }
  }
`
tester.test(true, query) // Error: Invalid field id on getMe
```

**查询上的无效参数**

```
const EasyGraphQLTester = require('easygraphql-tester')
const fs = require('fs')
const path = require('path')

const schemaCode = fs.readFileSync(path.join(__dirname, 'schema', 'schema.gql'), 'utf8')
const tester = new EasyGraphQLTester(schemaCode)

const getUserByUsername = `
  {
    getUserByUsername(username: 1, name: test) {
      email
    }
  }
`

tester.test(true, getUserByUsername) // Error: username argument is not type String
```

**输入**上缺少字段

```
const EasyGraphQLTester = require('easygraphql-tester')
const fs = require('fs')
const path = require('path')

const schemaCode = fs.readFileSync(path.join(__dirname, 'schema', 'schema.gql'), 'utf8')
const tester = new EasyGraphQLTester(schemaCode)

const mutation = `
  mutation CreateUser{
    createUser {
      email
    }
  }
`
const test = tester.test(true, mutation, {
  email: 'test@test.com',
  fullName: 'test',
  password: 'test'
})
// Error: username argument is missing on createUser
```

## **配合摩卡使用&柴:**

为了在测试中获得更好的结果，你可以将它与 Mocha & Chai 一起使用(也可以将它与你最喜欢的一起使用)来测试结果并验证返回的字段。

```
'use strict'

const fs = require('fs')
const path = require('path')
const { expect } = require('chai')
const EasyGraphQLTester = require('../lib')

const schemaCode = fs.readFileSync(path.join(__dirname, 'schema', 'schema.gql'), 'utf8')

describe('Mutation', () => {
  let tester

  before(() => {
    tester = new EasyGraphQLTester(schemaCode)
  })

  describe('Should throw an error if variables are missing', () => {
    it('Should throw an error if the variables are missing', () => {
      let error
      try {
        const mutation = `
          mutation CreateUser{
            createUser {
              email
            }
          }
        `
        tester.mock(mutation)
      } catch (err) {
        error = err
      }

      expect(error).to.be.an.instanceOf(Error)
      expect(error.message).to.be.eq('Variables are missing')
    })
  })

  describe('Should return selected fields', () => {
    it('Should return selected fields', () => {
      const mutation = `
        mutation CreateUser{
          createUser {
            email
          }
        }
      `
      const test = tester.mock(mutation, {
        email: 'test@test.com',
        username: 'test',
        fullName: 'test',
        password: 'test'
      })

      expect(test).to.exist
      expect(test.email).to.be.a('string')
    })
  })
})
```

如果你喜欢这个包，别忘了在 GitHub 上给个⭐️

模拟演示:[https://repl.it/@alejandroestrada/easygraphql-tester](https://repl.it/@alejandroestrada/easygraphql-tester)

带笑话的演示:[https://codesandbox.io/s/42m2rx71j4](https://codesandbox.io/s/42m2rx71j4)

回购:[https://github.com/EasyGraphQL/easygraphql-tester](https://github.com/EasyGraphQL/easygraphql-tester)

国家预防机制:[https://www.npmjs.com/package/easygraphql-tester](https://www.npmjs.com/package/easygraphql-tester)

【https://easygraphql.com/】网站: