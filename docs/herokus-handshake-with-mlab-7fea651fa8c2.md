# Heroku 和 mlab 的握手

> 原文：<https://medium.com/hackernoon/herokus-handshake-with-mlab-7fea651fa8c2>

![](img/84d489f3f5c45f9db55f739007a6400f.png)

平台即服务(PaaS)节省了我们设置虚拟服务器所需的时间，使可扩展性更容易，并减少了设置服务器的成本。

最初，在云中设置服务器需要连接和配置所有类型的组件，但无服务器、Docker、Heroku 等的引入使我们可以只关注应用程序。

我们将把我们的 MongoDB 云服务(mlab)与我们的 PaaS(Heroku)合并。这很重要，因为在使用 PaaS 时，它允许我们专注于开发和管理我们的应用程序，而不会被维护基础设施的想法所干扰。

要成功完成这一过程，我们需要:

*   Heroku CLI
*   [饭桶痛击](https://git-scm.com/download/win)(当然)
*   [一个 mlab 账户](https://www.mlab.com/login/?r=%2Fdatabases%2Fheroku_3wwgk424#users)
*   [一个 Heroku 账号](https://id.heroku.com/login)

现在我们已经做好了一切准备，我们可以在我们的 Heroku 帐户上创建一个新的应用程序。为了这个项目，应用程序的名称是' mediumapp11 '。

然后，我们在 mlab 上创建一个名为“mediumapp11”的数据库。当我们的数据库启动并运行后，我们可以添加一个数据库用户，然后我们会在上面看到我们的 MongoDB URI。这将帮助我们从本地主机连接到数据库。

我们会有这种性质的东西:

```
mongodb://<dbuser>:<dbpassword>[@ds0*1*7](http://twitter.com/ds041678)*.mlab.com:41678/mediumapp11
```

其中,“dbuser”标记是您创建的用户的用户名,“dbpassword”是该用户的密码。

现在，在我们的本地主机上，我们可以创建一个“mediumapp11”文件夹，然后执行以下操作:

```
heroku addons:add mongolab --app mediumapp11
```

“mediumapp11”所在的位置会显示您的应用程序名称。当这个完成后，我们应该在 Heroku 项目中找到 mlab 插件。

![](img/aeecf2f8cba376c0c7210044b2a67ce5.png)

现在，我们终于可以创建一个“mediumapp11.js”文件了。我们将需要 mongoose 依赖来定义我们的模式和 http。

```
const mongoose = require('mongoose');const http = require('http');const Schema = mongoose.Schema;
```

现在，我们可以定义我们的模式，创建一个文档，它是我们模型的实例，然后指定我们从 mlab 获得的 URI。

```
let Team = mongoose.model('Team', TeamSchema);let db = mongoose.connection;let dbUrl = 'mongodb://mediumapp11:mediumapp11[@ds041678](http://twitter.com/ds041678).mlab.com:41678/mediumapp11';
```

然后，我们可以监听错误，如果没有错误，我们就连接到我们的 URI 并保存一个特定的文档。

```
db.on('error', function () {console.log('error');});mongoose.connect(dbUrl, function (err) {if (err) {  return console.log('there was a problem' + err);  }console.log('connected!');var team = new Team({ name: 'Jehovah'  });team.save(function (error, data) {if (error) {console.log(error);} db.close();process.exit();});});
```

现在，我们应该确保用“mediumapp11”替换了“<dbuser>”，用“mediumapp11”替换了“<dbpassword>”，然后我们就可以运行应用程序了</dbpassword></dbuser>

```
node mediumapp11.js
```

我们现在可以刷新我们的 mlab，然后转到文档，然后我们会发现我们的文档已保存。

![](img/3459e06b3dd632ff0105e8acbd155beb.png)

如果你知道了什么，请告诉我👏👏👏。