# 使用 SuperTest 的 API 测试

> 原文：<https://medium.com/hackernoon/api-testing-using-supertest-1f830ce838f1>

![](img/05912fe5be375e61ad84a71a80cd00ca.png)

想知道如何为你的 API 编写测试？你可以使用超级测试来测试 HTTP 端点。

在开始**超级测试**之前，让我们使用 node + express 创建一个简单的 API。让我们利用他们给我们的路线来学习**超级测试**库。本文的重点不是使用 express framework 创建 rest API。

让我们转到/routes/users.js 文件并创建一些端点。

```
**var** express = require('express');
**var** router = express.Router();

*/**
 * get all users
 */* router.get('/', **function** (req, res, next) {
    **return** res.json('all users sent');
});

*/**
 * Get a specific user
 */* router.get('/:id', **function** (req, res, next) {
    **if** (req.params.id === 'U001') { // just to demo
        **return** res.json("user U001 found");
    }
    **return** res.status(404).json('user not found');
});

*/**
 * Add a user
 */* router.post('/', **function** (req, res, next) {
    **let** content = req.body;
    **if** (content.id) { //just to demo
        **return** res.status(201).json("user created");
    }
    **return** res.status(400).json('user not created');
});

module.exports = router;
```

我创建了两个简单的获取用户数据的 get 方法和一个保存用户的 post 方法。哈哈，我是开玩笑的，没有真正的数据库集成或保存功能，只是基于一些虚拟逻辑发送一些 JSON 输出😹和状态代码来测试我们的 API。您可以创建一个真正的工作 API。但是记得发送相关的 [HTTP 状态代码](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)，这样以后编写单元测试时就容易了。

现在让我们关注使用 **SuperTest** 编写 API 测试。首先，您需要将依赖项安装为 devDependancy。

```
npm install supertest --save-dev
```

这也包括 mocha，因为 super test 使用 mocha 测试框架。但是你不需要安装它，它带有超级测试。

然后，让我们在项目的根级别创建一个名为 *test* 的目录，并创建一个名为 *apiTest.js 的文件。*这个文件是我们编写 API 测试的文件。

我们首先需要的是超级测试模块。

```
//apiTest.js
const request = require('supertest');
```

然后我们需要通过 http。服务器调用 supertest 的 request()方法。要做到这一点，让我们包括我们的快速应用程序如下。

```
//apiTest.js
const request = require('supertest');
constapp = require('../app'); //reference to you app.js file
```

让我们编写第一个 API 测试来测试[http://localhost:3000/users](http://localhost:3000/users)端点。如果此端点正常工作，它应该返回状态代码 200。用户路由文件就是这样实现的。参见下面的代码 res.json()会自动设置状态码 200。所以不用担心。

```
*//from users.js route file
/**
 * get all users
 */* router.get('/', **function** (req, res, next) {
    **return** res.json('all users sent');
});
```

好了，我们如何使用 supertest 在 out apiTest.js 中为这条路由编写测试。

```
//apiTest.js **const** request = require('supertest');
**const** app = require('../app');

//==================== user API test ====================

*/**
 * Testing get all user endpoint
 */* describe('GET /users', **function** () {
    it('respond with json containing a list of all users', **function** (done) {
        request(app)
            .get('/users')
            .set('Accept', 'application/json')
            .expect('Content-Type', /json/)
            .expect(200, done);
    });
});
```

这里**描述**方法来自于 mocha 测试框架。我们简单地指定端点名称作为第一个参数，然后指定函数来编写测试用例。这里摩卡**描述**()是用于对测试用例进行分组，而 **it** ()是用于编写真实的测试用例。我们给出一个简单的描述，作为 it()函数中的第一个参数，我们将要测试什么。然后是要调用的回调函数。

**请求**()需要 HTTP.server，因此我们传递我们的 express app 引用。然后在 **get** ()中我们指定路线终点。这里我们在 app 中所以可以省略 [http://localhost:3000/](http://localhost:3000/) 部分。只需给出路线终点。

在 **set** ()中，我们设置 HTTP 头属性。然后在 **expect** ()中，我们检查返回值，包括头值和正文值。

在这个例子中，我们检查返回的内容类型是否是 JSON。因为我们使用 res.json()方法将响应作为 JSON 发送。然后 expect(200)检查返回的状态代码是否等于 200。然后我们通过调用回调函数 *done* 来结束测试。

目前为止一切顺利。但是我们如何进行测试呢？你必须在你的 *package.json* 文件中做这个改变。

```
"scripts": {
  "start": "node ./bin/www",
  "test": "mocha 'test/apiTest.js'" //path to your test file
}
```

现在，您可以通过简单地使用

```
> npm test
```

运行测试文件后，您将看到以下输出。

```
> mocha 'test/apiTest.js'GET /users
GET /users 200 5.289 ms - 16
    ✓ respond with json containing a list of all users1 passing (38ms)
```

让我们继续在 users.js 文件中编写所有端点的更多测试用例。

```
**const** request = require('supertest');
**const** app = require('../app');

//==================== user API test ====================

*/**
 * Testing get all user endpoint
 */* describe('GET /users', **function** () {
    it('respond with json containing a list of all users', **function** (done) {
        request(app)
            .get('/users')
            .set('Accept', 'application/json')
            .expect('Content-Type', /json/)
            .expect(200, done);
    });
});

*/**
 * Testing get a user endpoint by giving an existing user
 */* describe('GET /user/:id', **function** () {
    it('respond with json containing a single user', **function** (done) {
        request(app)
            .get('/users/U001')
            .set('Accept', 'application/json')
            .expect('Content-Type', /json/)
            .expect(200, done);
    });
});

*/**
 * Testing get a user endpoint by giving a non-existing user
 */* describe('GET /user/:id', **function** () {
    it('respond with json user not found', **function** (done) {
        request(app)
            .get('/users/idisnonexisting')
            .set('Accept', 'application/json')
            .expect('Content-Type', /json/)
            .expect(404) //expecting HTTP status code
            .expect('"user not found"') // expecting content value
            .end((err) => {
                **if** (err) **return** done(err);
                done();
            });
    });
});

*/**
 * Testing post user endpoint
 */* describe('POST /users', **function** () {
    **let** data = {
        "id": "1",
        "name": "dummy",
        "contact": "dummy",
        "address": "dummy"
    }
    it('respond with 201 created', **function** (done) {
        request(app)
            .post('/users')
            .send(data)
            .set('Accept', 'application/json')
            .expect('Content-Type', /json/)
            .expect(201)
            .end((err) => {
                **if** (err) **return** done(err);
                done();
            });
    });
});

*/**
 * Testing post user endpoint
 */* describe('POST /users', **function** () {
    **let** data = {
        //no id
        "name": "dummy",
        "contact": "dummy",
        "address": "dummy"
    }
    it('respond with 400 not created', **function** (done) {
        request(app)
            .post('/users')
            .send(data)
            .set('Accept', 'application/json')
            .expect('Content-Type', /json/)
            .expect(400)
            .expect('"user not created"')
            .end((err) => {
                **if** (err) **return** done(err);
                done();
            });
    });
});
```

如果你仔细观察 *apiTest.js* 文件中的代码，你会发现其中的规律。我只展示了 GET 和 POST 方法，但是您可以使用真正的工作 API 尝试 PUT 和 DELETE 方法。

如果您使用 *npm test* 命令运行这个测试文件，最后您会看到如下输出。

```
> mocha 'test/apiTest.js'
GET /users
GET /users 200 5.610 ms - 16
    ✓ respond with json containing a list of all usersGET /user/:id
GET /users/U001 200 0.870 ms - 17
    ✓ respond with json containing a single userGET /user/:id
GET /users/idisnonexisting 404 0.458 ms - 16
    ✓ respond with json user not foundPOST /users
POST /users 201 10.196 ms - 14
    ✓ respond with 201 createdPOST /users
POST /users 400 0.607 ms - 18
    ✓ respond with 400 not created5 passing (62ms)
```

最好向 https://github.com/visionmedia/supertest 寻求更多的知识。