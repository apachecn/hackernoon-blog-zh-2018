# 如何用 Node.js 和 Pusher 构建 Twitter 的实时赞功能

> 原文：<https://medium.com/hackernoon/how-to-build-twitters-real-time-likes-feature-with-node-js-and-pusher-fd8f97bee7cb>

如果你使用 Twitter，你可能已经注意到一个已经存在一段时间的漂亮的小功能:实时推文统计。这基本上意味着，随着世界各地的人喜欢或转发一条推文，你可以看到该推文的喜欢或转发数量增加(或减少)，而不必刷新页面。酷吧。

在本文中，我将带您在一个简单的 Node.js 应用程序中实现您自己的实时帖子统计(我们将仅限于赞)。下面是该应用的运行演示(请原谅我平淡无奇的用户界面):

![](img/1e5c336d7978623437db2159b8bbcbab.png)

Liking a post in one window immediately reflects in the other

## 设置项目

本教程假设您已经安装了 Node.js(版本 6 或更高)和 MongoDB(版本 3.2 或更高)。我们将使用 Express，一个流行的轻量级 Node.js 框架。让我们使用[快速应用生成器](https://expressjs.com/en/starter/generator.html)快速设置我们的应用:

```
# if you don't already have it installed
npm install express-generator -g# create a new express app with view engine set to Handlebars (hbs)
express --view=hbs poster
cd poster && npm install
```

然后，我们将添加我们需要的依赖项:

`npm install --save dotenv faker mongoose pusher`

以下是每个模块的用途。

*   我们使用 MongoDB 作为我们的数据存储，所以我们将使用**mongose**将我们的 MongoDB 文档映射到模型(JavaScript 对象)。
*   **Faker** 将帮助我们为我们的应用程序生成虚假数据，因为我们只想展示喜欢功能。
*   我们需要**推送器**与推送器的 API 对话。
*   最后， **dotenv** 是一个小软件包，它帮助我们从一个`.env`文件中加载我们的私有配置变量(比如我们的 Pusher 应用凭证)。

## 基本数据和视图

首先，让我们定义我们的数据结构。我们将把这个演示的范围限制在两个实体上:**用户**和**帖子**。对于用户来说。我们将只存储他们的名字。对于帖子，我们将存储:

*   正文
*   张贴的日期
*   发布它的用户(作者)，以及
*   它获得的赞数

因为我们需要的关于用户的唯一细节是他们的名字，所以我们不会建立一个用户模型；我们将直接从我们的 Post 模型中引用用户名。所以，让我们创建一个文件，`models/post.js`:

现在，我们将编写一个小脚本来将一些假数据放入我们的数据库。在`bin`目录下创建一个名为`seed.js`的文件，内容如下:

使用 node 运行种子(记住首先通过运行`sudo mongod`来启动 MongoDB 服务器):

`node bin/seed.js`

让我们为我们的主页设置路线和视图。

我们要做的第一件事是添加我们的 MongoDB 连接设置，这样当我们的应用程序启动时就创建了连接。在靠近`app.js`顶部的地方添加这一行:

`require('mongoose').connect('mongodb://localhost/poster');`

接下来，我们从数据库中检索所有帖子并将它们传递给视图。将`routes/index.js`中的代码替换为:

```
let router = require('express').Router();
let Post = require('./../models/post');router.get('/', (*req*, *res*, *next*) => {
  Post.find().exec((*err*, *posts*) => {
    *res*.render('index', { posts: *posts* });
  });
});module.exports = router;
```

最后，我们渲染帖子的视图(`views/index.hbs`)。我们将使用 Bootstrap 进行一些快速的造型。

一些注意事项:

*   我们将一个 **data-post-id** 属性附加到每个 Like 按钮上，这样我们可以很容易地识别它指向哪个帖子。这很快就会有用的。
*   我们给每个`likes_count`字段一个包含文章 ID 的`id`，所以我们可以直接引用只有文章 ID 的正确的`likes_count`。
*   我们在 Like 按钮上有一个点击处理程序(`**actOnPost**`)。我们将在这里切换按钮文本(喜欢→不喜欢)并增加`likes_count`。(如果是“不像”按钮，则相反)。我们一会儿会实现它。

## 喜欢和不喜欢逻辑

当用户点击“喜欢”时，我们希望发生以下情况:

1.  按钮上的文本从“喜欢”变为“不喜欢”。
2.  帖子旁边显示的赞数会增加 1。
3.  向服务器发出一个 AJAX 请求，将数据库中的`likes_count`加 1。
4.  在页面打开的所有其他标签页/窗口中，帖子旁边显示的赞数增加 1。(这就是 Pusher 的用武之地。)

对于不喜欢:

1.  按钮上的文字从“不像”变成“像”。
2.  帖子旁边显示的赞数会减少 1。
3.  向服务器发出一个 AJAX 请求，将数据库中的`likes_count`减 1。
4.  在页面打开的所有其他标签页/窗口中，帖子旁边显示的赞数会减少 1。(再次通过 Pusher。)

我们将喜欢和不喜欢都归类为可以在帖子上执行的**操作**，这样我们就可以一起处理它们。

让我们为`actOnPost`方法添加一些 JavaScript 到我们的主页。我们将引入 [Axios](https://github.com/axios/axios) 来实现简单的 HTTP 请求。将以下内容添加到您的`views/index.hbs`中:

```
<**script** src=**"https://unpkg.com/axios/dist/axios.min.js"**></**script**><**script**>
  var *updatePostStats* = {
    Like: function (*postId*) {
      document.querySelector('#likes-count-' + *postId*).textContent++;
    },
    Unlike: function(*postId*) {
      document.querySelector('#likes-count-' + *postId*).textContent--;
    }
  }; var *toggleButtonText* = {
    Like: function(*button*) {
      *button*.textContent = "Unlike";
    },
    Unlike: function(*button*) {
      *button*.textContent = "Like";
    }
  }; var actOnPost = function (*event*) {
    var postId = *event*.target.dataset.postId;
    var action = *event*.target.textContent.trim();
    *toggleButtonText*[action](*event*.target);
    *updatePostStats*[action](postId);
    axios.post('/posts/' + postId + '/act', { action: action });
  };</**script**>
```

然后我们在`routes/index.js:`中定义**行为**路线

```
router.post('/posts/:id/act', (*req*, *res*, *next*) => {
  const action = *req*.body.action;
  const counter = action === 'Like' ? 1 : -1;
  Post.update({_id: *req*.params.id}, {$inc: {likes_count: counter}}, {}, (*err*, *numberAffected*) => {
    *res*.send('');
  });
});
```

这里，我们使用 MongoDB 内置的`$inc`操作符来更改 likes_count 的更新操作。

## 用推送器通知其他客户

在这一点上，我们已经有了我们通常喜欢和不喜欢的特征。现在是时候通知其他客户端发生这样的行为了。

让我们来设置我们的推动器集成。[如果您还没有免费的推销者帐户](https://pusher.com/signup)，请创建一个。然后访问[您的仪表盘](http://dashboard.pusher.com)并创建一个新应用程序，记下您的应用程序凭证。因为我们使用 dotenv 包，所以我们可以将 Pusher 凭证放在项目根目录下的. env 文件中:

```
PUSHER_APP_ID=WWWWWWWWW
PUSHER_APP_KEY=XXXXXXXXX
PUSHER_APP_SECRET=YYYYYYYY
PUSHER_APP_CLUSTER=ZZZZZZZZ
```

将上面的存根替换为您 Pusher 仪表盘中的应用凭据。然后将下面一行添加到 app.js 的顶部:

`require('dotenv').config();`

接下来，我们将修改我们的路由处理程序，以便每当一个动作更新数据库中的`likes_count`时触发 Pusher 消息。我们将初始化 Pusher 客户端的一个实例，并通过调用`pusher.trigger`用它来发送消息。

触发方法有四个参数:

*   发送此消息的**通道**的名称
*   **消息的名称**
*   **有效载荷**(您希望随消息发送的任何数据)
*   **插座 ID** 。如果提供了该 ID，Pusher 将向除具有该 ID 的客户之外的每个客户发送该消息。这很有用，因此我们可以排除导致该操作的客户端，使其不再收到通知。

这是我们希望我们的有效载荷在类似动作的情况下看起来的样子:

```
{
  "action": "Like",
  "postId": 1234
}
```

因此，让我们将这个逻辑添加到我们的路由处理程序中:

```
let Pusher = require('pusher');let pusher = new Pusher({
  appId: process.env.PUSHER_APP_ID,
  key: process.env.PUSHER_APP_KEY,
  secret: process.env.PUSHER_APP_SECRET,
  cluster: process.env.PUSHER_APP_CLUSTER
});router.post('/posts/:id/act', (*req*, *res*, *next*) => {
  const action = *req*.body.action;
  const counter = action === 'Like' ? 1 : -1;
  Post.update({_id: *req*.params.id}, {$inc: {likes_count: counter}}, {}, (*err*, *numberAffected*) => {
    pusher.trigger('post-events', 'postAction', { action: action, postId: *req*.params.id }, *req*.body.socketId);
    *res*.send('');
  });
});
```

在客户端(`index.hbs`)我们需要处理两件事:

*   为每位客户订阅`post-events`频道
*   。将客户机的套接字 ID 添加到我们的`act` API 请求中，这样服务器就可以用它来排除客户机

我们将引入 Pusher SDK:

```
<**script** src=**"https://js.pusher.com/4.1/pusher.min.js"**></**script**><**script**>
  var *pusher* = new Pusher('your-app-id', {
    cluster: 'your-app-cluster'
  });
  var *socketId*;

  // retrieve the socket ID on successful connection
  *pusher*.connection.bind('connected', function() {
    *socketId* = *pusher*.connection.socket_id;
  }); var *channel* = *pusher*.subscribe('post-events');
  *channel*.bind('postAction', function(*data*) {
    // log message data to console - for debugging purposes
    *console*.log(*data*);
    var action = *data*.action;
    updatePostStats[action](*data*.postId);
  });</**script**>
```

全部完成！运行以下命令启动您的应用:

`npm start`

现在，如果你在浏览器的两个(或更多)标签中打开 [http://localhost:3000](http://localhost:3000) ，你应该会看到喜欢一个标签中的帖子会立即反映在另一个标签中。此外，由于我们前面放置了 console.log 语句，您将看到记录了该事件:

![](img/3e98e714a2d650d2ef059f317105ab60.png)

## 结论

在本文中，我们看到了 Pusher 的发布-订阅消息传递系统如何简单地实现特定帖子活动的实时视图。当然，这只是一个起点；我期待着看到你建造的所有伟大的东西。你可以在 Github 上查看完整应用[的源代码。](https://github.com/shalvah/poster)

*本文原载作者于* [*推手的博客*](https://blog.pusher.com/build-twitters-realtime-likes-feature-node-js-pusher/) *。*