# 如何编写可伸缩的 Nodejs 服务[+代码示例]

> 原文：<https://medium.com/hackernoon/how-to-write-scalable-nodejs-services-code-examples-b48baa2d9eb9>

我最近有机会在 [Nodejs](https://hackernoon.com/tagged/nodejs) 环境中使用 OOP 模式，并且我花了很长时间这样做。

让我们看看这次我们要构建什么:

*   构建一个 [API](https://hackernoon.com/tagged/api) 服务，提供两个资源: **apps** 和 **developers** ，但是以后会添加更多的资源。
*   数据是从 NoSql 数据库中读取的，但是读取数据的位置将来可能会改变。
*   当提供应用程序时，我们需要为其关联的开发者提供一个特殊的属性“author_info”(每个模型可能包含不同的规则，这些规则定义了如何提供项目)。
*   现在这个服务将只负责读取数据，但是我们应该允许在每个模型上创建和更新操作。
*   我们需要能够改变特定模型的输出格式(我们有合作伙伴仍然使用 xml 格式)。

存储在数据库中的模型原始格式如下:

**开发商**

```
{
    "id": 23,
    "name": "AresGalaxy",
    "url": "[https://aresgalaxy.io/](https://aresgalaxy.io/)"
},
```

**app**

```
{
    "id": 21824,
    "developer_id": 23,
    "title": "Ares",
    "version": "2.4.0",
    "url": "[http://ares.en.softonic.com](http://ares.en.softonic.com)",
    "short_description": "Fast and unlimited P2P file sharing",
    "license": "Free (GPL)",
    "thumbnail": "[https://screenshots.en.sftcdn.net/en/scrn/21000/21824/ares-14-100x100.png](https://screenshots.en.sftcdn.net/en/scrn/21000/21824/ares-14-100x100.png)",
    "rating": 8,
    "total_downloads": "4741260",
    "compatible": [
      "Windows 2000",
      "Windows XP",
      "Windows Vista",
      "Windows 7",
      "Windows 8"
    ]
},
```

当获取开发者资源时，它应该保持原样。但是在获取**应用**时，我们需要像这样合并`developer`模型:

```
{
    "id": 21824,
    "developer_id": 23,
 **"author_info": {
        "id": 23,
        "name": "AresGalaxy",
        "url": "**[**https://aresgalaxy.io/**](https://aresgalaxy.io/)**"
    },**
    "title": "Ares",
    "version": "2.4.0",
    "url": "[http://ares.en.softonic.com](http://ares.en.softonic.com)",
    "short_description": "Fast and unlimited P2P file sharing",
    "license": "Free (GPL)",
    "thumbnail": "[https://screenshots.en.sftcdn.net/en/scrn/21000/21824/ares-14-100x100.png](https://screenshots.en.sftcdn.net/en/scrn/21000/21824/ares-14-100x100.png)",
    "rating": 8,
    "total_downloads": "4741260",
    "compatible": [
      "Windows 2000",
      "Windows XP",
      "Windows Vista",
      "Windows 7",
      "Windows 8"
    ]
},
```

以下是我对此的想法:

我们需要以一种非常简单的方式来声明资源，但是似乎每个资源在格式和输出上都是不同的。

所以我们需要从`Resource`概念中提取“公共”部分，并为每个`Model`构建不同且独立的实现。

什么是`Model`？在 REST 范式中，我们通常将资源调用到某个通过 URL ( `api.io/rest/employee`)表示的`domain item`，我们可以使用 HTTP 动词并提供几个参数来轻松地与它进行交互。

当编写可维护的 API 时，我们需要区别于描述每个资源的规则的代码和定义如何实现 HTTP 连接的代码。

所以我最后创建了两个基本实体，分别是`models`和`resources`。

*   资源是执行 HTTP 通信的类，现在我们只有一个类，因为应用程序和开发人员在这一层不包含差异。
*   模型是描述如何操作的类，比如从数据库读取数据、连接数据、格式化输出等。是为某个特定的领域实体做的，像开发者和 app，它们是不同的，应该是独立的。

所以我们有两个模型类`developer`和`app`以及一个单独的`resource`类。但是，在运行时，我们有两个资源实例，每个都有自己的模型实例，负责特定的域规则。

这是开始的脚本:

```
const { setConfig } = require("ritley");
setConfig(require("./ritley.conf"));const BasicResource = require("./resources/basic-resource");[
  require("./models/app"),
  require("./models/developer"),
].forEach(Model => **new BasicResource(new Model)**);
```

我们用的是[里特利](https://www.npmjs.com/package/ritley)。一个月前我为快速后端开发和 REST 服务做的轻量级包。

所以在前面的代码中，我们只需要我们的 ritley 配置，它基本上设置了 rest 路径、静态资产文件夹(如果需要)和监听端口。

然后我们只需遍历模型，并创建一个资源实例来绑定它的模型，我们就准备好了。

让我们看一下文件夹结构:

```
.
├── **adapters**
│ ├── low.conf.js
│ ├── low.js
│ └── low-provider.js
├── low.database.json
├── **models**
│ ├── app.js
│ ├── common.js
│ └── developer.js
├── package.json
├── README.md
├── **resources**
│ └── basic-resource.js
├── ritley.conf.js
├── run.js
├── **test** │ └── developers.test.js4 directories, 13 files
```

我们已经创建了 models/common.js 抽象类作为进一步模型的起点:

```
const { inject, createClass } = require("kaop")
const LowProvider = require("../adapters/low-provider");module.exports = CommonModel = createClass({
  adapter: null,
 **constructor: [inject.args(LowProvider), function(_db) {
    this.adapter = _db;
  }],**  read() {
    return new Promise(resolve => resolve("read not implemented"));
  },
  find() {
    return new Promise(resolve => resolve("find not implemented"));
  },
  toString(obj) {
    return JSON.stringify(obj);
  }
})
```

你可能注意到我没有使用和声 ES 类。那是因为我们需要像装饰者这样的东西，而我们现在不想使用任何代码转换器。相反，我们使用 [kaop](https://www.npmjs.com/package/kaop) 来轻松实现反射技术，例如**依赖注入**。

所以基本上前面的代码声明了一个**抽象模型**，它将包含一个访问数据库的 lowdb 实例适配器。如果我们改变我们的数据库服务，我们只需要关心导入另一个提供者。

以下代码代表 models/developer.js:

```
const { extend } = require("kaop");
const CommonModel = require("./common");module.exports = DeveloperModel = extend(CommonModel, {
 **path: "developer",**  read() {
    return new Promise(resolve =>
      resolve(this.adapter.getCollection("developers")));
  }
});
```

这只是在读取方法实现上不同于通用模型，所以我们只是用一个新的模型来替换它。

请注意，我们的 DeveloperModel 包含 path 属性，basic resource 将使用该属性来监听几个路径。以下是方法:

```
const { extend, override } = require("kaop");module.exports = BasicResource = extend(AbstractResource, {
  constructor: [override.implement, function(parent, _model) {
 **parent(_model.path);**
    this.model = _model;
  }], get(request, response) {
    let prom = null; if(request.query.id) {
      prom = this.model.find(request.query);
    } else {
      prom = this.model.read();
    }
    prom.then(result =>
      this.writeResponse(response, this.model.toString(result)));
  }, writeResponse(response, body) {
    body && response.write(body);
    response.statusCode = 200;
    response.end();
  }
})
```

BasicResource 从 AbstractResource 扩展而来，重写其构造函数以提供路径，正如您在突出显示的行中所看到的，这将为每个实例调用。正如我们在启动脚本中看到的，模型被传递给资源，以正确地构建我们的 HTTP 侦听器。BasicResource 的 get 方法将拦截所有指向每个路径的 HTTP GET 请求。用开发人员模型配置的一个实例将只在 **<主机>/rest/开发人员**路径上有效地监听，依此类推。

因此，请求**<host>/rest/developer**的客户机将由 BasicResource 实例来回答，该实例是用 DeveloperModel 实例创建的。

例如，如果我们希望允许 post 或 PUT 请求，我们需要在 BasicResource 上写下一个 POST 方法，ritley 允许我们简单地编写名为 HTTP 动词的方法，因此任何匹配的请求都将得到处理。如果我们需要仅在几个路径上允许 POST，我们可能需要将 BasicResource 扩展到 AdvancedResource 或允许更多 HTTP 动词的东西。这是正确分离关注点的最佳实践。

也许模型需要按照它们需要挂载的资源类型进行分组。

例如:

```
const { setConfig } = require("ritley");
setConfig(require("./ritley.conf"));const BasicResource = require("./resources/basic-resource");
**const AdvancedResource = require("./resources/advanced-resource");**[
  require("./models/app"),
  require("./models/developer"),
].forEach(Model => new BasicResource(new Model));**[
  require("./models/company")
].forEach(Model => new AdvancedResource(new Model));**
```

现在让我们看一下初始需求，看看这是不是一个好方法(*问题* — **回答**):

*   *构建一个 API 服务，提供两种模式:****apps****和****developers****，不过以后会增加更多的资源。—* **添加新模型非常简单，我们只需从普通模型扩展创建一个新模型，实现所需的方法，并在启动脚本上声明**
*   *数据是从 NoSql 数据库中读取的，但是读取数据的位置将来可能会改变。—* **负责访问数据服务的代码在** `**adapter**` **文件夹里，我们用的是一个牛逼的资源比如**[**lowdb**](https://www.npmjs.com/package/lowdb)**。我们有 3 个不同的文件:** `**low.conf.js**` **包含数据库路径，** `**low.js**` **将 lowdb 方法包装成与域相关的动作供模型使用，以及** `**low-provider.js**` **声明注入模型的单例依赖，这样我们可以快速切换不同的数据库服务:)**
*   *当提供应用程序时，我们需要为其相关开发者提供一个特殊属性“author_info”(每个模型可能包含不同的规则，这些规则定义了如何提供项目)*。— **每个模型都有自己的方法来检索数据，直到资源，因此我们可以根据自己的需要构建数据。对于这个特殊的例子，我们在 db 适配器上创建了一个方法，因为嵌套模型在这里是一个非常常见的例子** `**adapters/low.js**` **:**

```
**getMappedCollection(uid, joinuid, joinkey, newkey) {
**  const joincollection = this.instance.get(joinuid);
  return this.instance
    .get(uid)
    .map(app => this.mergePredicate(
      app,
      joincollection.find({ "id": app[joinkey]}),
      newkey)
    )
    .value();
},
mergePredicate(app, subject, newkey) {
  return { ...app, { [newkey]: ...subject } };
},
```

**然后，因为 app model 是唯一一个提供嵌套项目的，所以我们利用它** `**models/app.js**` **:**

```
read() {
  return new Promise(resolve =>
 **resolve(this.adapter.getMappedCollection(**      "apps", "developers", "developer_id", "author_info")
    ));
},
```

*   *现在这个服务将只负责读取数据，但是我们应该允许在每个模型上创建和更新操作。—* **已经解决，但要展示一个例子:**

```
const { extend, override } = require("kaop");
const BasicResource = require("./basic-resource");// we only need to implement a new method since this class inherits
// from BasicResource
module.exports = AdvancedResource = extend(BasicResource, { post(request, response) {
    // create entry logic
  }
});
```

*   *我们需要能够改变特定模型的输出格式(我们有合作伙伴仍然使用 xml 格式)*。— **如果一个特定的模型需要不同于其他模型的输出，那么我们需要从** `**models/common.js**` **中覆盖** `**toString()**` **方法。假设 DeveloperModel 需要以 XML 格式输出，因为我们的一些合作伙伴目前仍在使用 2008 SQL Server..**

```
const { extend } = require("kaop");
const CommonModel = require("./common");
**const xmlconverter = require("awesomexmlparser");**module.exports = DeveloperModel = extend(CommonModel, {
  path: "developer",
  read() {
    return new Promise(resolve =>
      resolve(this.adapter.getCollection("developers")));
  },
 **toString(obj) {
    return xmlconverter.stringify(obj);
  }** })
```

# 今天就到这里吧！非常感谢:)

你可以在这里查看代码[https://github.com/k1r0s/micro-ritley-lowdb-example](https://github.com/k1r0s/micro-ritley-lowdb-example)