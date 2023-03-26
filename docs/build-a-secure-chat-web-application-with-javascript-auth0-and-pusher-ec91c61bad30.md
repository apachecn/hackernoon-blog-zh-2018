# 用 JavaScript、Auth0 和 Pusher 构建一个安全的聊天 Web 应用程序

> 原文：<https://medium.com/hackernoon/build-a-secure-chat-web-application-with-javascript-auth0-and-pusher-ec91c61bad30>

安保很难。通常，当我们构建应用程序时，我们希望只允许注册用户访问应用程序。我们希望能够管理用户帐户，查看他们上次登录的时间，能够禁用可疑的帐户，并有一个仪表板来查看和管理所有这些数据。我们也可能决定支持多因素认证和社交登录。

但是安全不仅仅是困难，还需要一段时间来实现。如果有一种服务可以帮你解决这部分开发问题，那会怎么样？为什么要花几周或几个月的时间滚动你自己的授权呢？这就是 Auth0 大放异彩的地方。在本教程中，我将向您展示如何使用 Pusher 构建一个聊天应用程序，使用 Auth0 Lock 添加用户认证，以及从 Auth0 仪表板管理用户。

# **auth 0 和 Pusher 的介绍**

[Auth0](https://auth0.com/) 是一家专注于封装用户认证和管理的认证即服务(或身份即服务)提供商，它提供了一个 SDK，允许开发人员轻松添加认证和管理用户。其用户管理控制面板支持违规检测和多因素身份认证，以及无密码登录。

[Pusher](https://pusher.com/) 的 API 和托管基础设施允许我们构建可扩展的、可靠的实时应用。Pusher 对渠道和事件有一个基本的概念。通道提供了一种过滤数据和控制对不同信息流的访问的方式，而事件是在推送系统中打包消息的主要方法，并形成所有通信的基础。

# **构建应用**

我们将建立一个聊天应用程序，允许用户互相交流，每个人都能看到其他人的消息。它的工作方式类似于 Slack 中的通道工作方式:每个人只有一个通道进行交流。

这是我们将要建造的:

![](img/a521a5d8ee86de364a79529c4ddd4b06.png)

Final App

## **设置后端**

我们将从构建后端开始，这将有助于接收和广播聊天消息，提供静态文件，并设置 Auth0 和 Pusher。

首先，你需要注册一个 Pusher 和 Auth0 账户。去[pusher.com](https://pusher.com/)和[auth0.com](https://auth0.com)注册一个账户。要使用 Pusher API，我们必须从仪表板注册并创建一个 Pusher 应用程序。我们可以创建尽可能多的应用程序，每个应用程序都将获得一个应用程序 id 和密钥，我们将使用它们在客户端或服务器端代码上初始化 Pusher 实例。

## **创建新的推销商账户**

要创建新的 Pusher 应用程序，请单击**您的应用程序**侧菜单，然后单击抽屉下方的**创建新应用程序**按钮。这将打开设置向导。

1.输入应用程序的名称。在这种情况下，我称之为“聊天”。
2。选择一个集群。
3。如果您希望拥有不同的开发、试运行和生产实例，请选择“为多个环境创建应用程序”选项。
4。选择 **Vanilla JS** 作为前端，选择 **NodeJS** 作为后端。
5。点击“创建应用程序”按钮设置应用程序实例，完成整个过程。

![](img/97b5c581afcbb281deb0b531a9863386.png)

Create new app on Pusher

因为我们正在使用 Express 在 Node 中构建后端，所以让我们初始化一个新的 Node 应用程序并安装所需的依赖项。运行以下命令:

1. **npm init** 并选择默认选项
2。 **npm i —保存 body-parser express pusher** 安装 express 和 pusher 节点包

添加一个名为 *server.js* 的新文件，该文件将包含认证 Pusher 客户端的逻辑，并呈现我们稍后将添加的静态文件。该文件将包含以下内容:

```
var express = require(‘express’);
var bodyParser = require(‘body-parser’);
var Pusher = require(‘pusher’);var app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));var pusher = new Pusher({ appId: APP_ID, key: APP_KEY, secret: APP_SECRET, cluster: eu });app.post(‘/pusher/auth’, function(req, res) {
    var socketId = req.body.socket_id;
    var channel = req.body.channel_name;
    var auth = pusher.authenticate(socketId, channel);
    res.send(auth);
});app.post(‘/message’, function(req, res) {
    var message = req.body.message;
    var name = req.body.name;
    pusher.trigger( ‘private-chat’, ‘message-added’, { message, name });
    res.sendStatus(200);
});app.get(‘/’,function(req,res){
    res.sendFile(‘/public/index.html’, {root: __dirname });
});app.use(express.static(__dirname + ‘/public’));var port = process.env.PORT || 5000;
app.listen(port, function () {
    console.log(`app listening on port ${port}!`)
});
```

我们通过传入一个包含我们的应用 ID 和密钥的详细信息的对象来实例化 Pusher，这些信息可以在 Pusher 仪表盘的**应用密钥**选项卡中找到。Pusher 还提供了一种机制，用于在订阅时向通道认证用户。为此，我们在服务器上公开一个端点，该端点将验证请求并以成功或失败做出响应。这个端点将被 Pusher 客户端库调用，并且可以被命名为任何名称。我们在 Pusher 上使用这个端点的默认名称，即 */pusher/auth* 。第*行 var auth = pusher . authenticate(socket id，channel)；*Pusher 对客户端进行认证，并向主叫客户端返回一个认证码。

为了允许这个文件在我们启动 npm 时运行，我们用以下值更新了 **package.json** :

```
“scripts”: {
 “start”: “node server.js”,
 “test”: “echo \”Error: no test specified\” && exit 1"
}
```

**创建认证 0 客户端**
创建认证 0 客户端

1.从侧面菜单中选择**客户端**。
2。在新页面上，点击**创建客户端**按钮
3。输入应用程序的名称并选择**单页应用程序**作为选项
4。点击**创建**按钮创建客户端。

![](img/e309846babb463ddf36a841f44cd8756.png)

Auth0 客户端为我们提供了客户端 Id 和密码，我们将使用它们从代码中与 Auth0 进行交互。在“设置”选项卡上，我们可以看到名称、客户端 Id、密码、客户端类型等等。我想为我的域 [*启用 CORS http://localhost:5000*](http://localhost:5000)，设置注销 URL 和用户通过 Auth0 认证后重定向到的 URL。用[T5 更新以下设置 http://localhost:5000](http://localhost:5000**)

1.允许的回调 URL
2。允许的注销网址
3。允许原产地(CORS)

## **构建前端**

随着后端一切就绪，我们构建将促进消息传递的网页。创建一个名为 **public** 的文件夹，其中包含 html 和 javascript 文件。创建两个新文件 **style.css** 和**index.html**，内容如下:

**style.css**

```
[@import](http://twitter.com/import) url("[http://netdna.bootstrapcdn.com/font-awesome/4.0.3/css/font-awesome.css](http://netdna.bootstrapcdn.com/font-awesome/4.0.3/css/font-awesome.css)");
    .chat
    {
        list-style: none;
        margin: 0;
        padding: 0;
    }
    .chat li
    {
        margin-bottom: 10px;
        padding-bottom: 5px;
        border-bottom: 1px dotted #B3A9A9;
    }
    .chat li.left .chat-body
    {
        margin-left: 60px;
    }
    .chat li.right .chat-body
    {
        margin-right: 60px;
    }

    .chat li .chat-body p
    {
        margin: 0;
        color: #777777;
    }
    .panel .slidedown .glyphicon, .chat .glyphicon
    {
        margin-right: 5px;
    }
    .body-panel
    {
        overflow-y: scroll;
        height: 250px;
    }
    ::-webkit-scrollbar-track
    {
        -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
        background-color: #F5F5F5;
    }
    ::-webkit-scrollbar
    {
        width: 12px;
        background-color: #F5F5F5;
    }
    ::-webkit-scrollbar-thumb
    {
        -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,.3);
        background-color: #555;
    }
```

**index.html**

```
<!-- template from [http://bootsnipp.com/snippets/6eWd](http://bootsnipp.com/snippets/6eWd) -->
    <!DOCTYPE html>
    <html>
    <head>
        <!-- Latest compiled and minified CSS -->
        <link rel="stylesheet" href="[https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css](https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css)" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">
        <!-- Optional theme -->
        <link rel="stylesheet" href="[https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap-theme.min.css](https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap-theme.min.css)" integrity="sha384-rHyoN1iRsVXV4nD0JutlnGaslCJuC7uwjduW9SVrLvRYooPp2bWYgmgJQIXwl/Sp" crossorigin="anonymous">
        <script
            src="[https://code.jquery.com/jquery-2.2.4.min.js](https://code.jquery.com/jquery-2.2.4.min.js)"
            integrity="sha256-BbhdlvQf/xTY9gja0Dq3HiwQF8LaCRTXxZKRutelT44="
            crossorigin="anonymous"></script>
        <!-- Latest compiled and minified JavaScript -->
        <script src="[https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js](https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js)" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
        <link rel="stylesheet" href="style.css">
        <script src="[https://cdn.auth0.com/js/lock/10.18.0/lock.min.js](https://cdn.auth0.com/js/lock/10.18.0/lock.min.js)"></script>
        <script src="[https://js.pusher.com/4.0/pusher.min.js](https://js.pusher.com/4.0/pusher.min.js)"></script>
        <script src="index.js"></script>
    </head>
    <body>
        <div class="container">
        <div class="row form-group">
            <div class="col-xs-12 col-md-offset-2 col-md-8 col-lg-8 col-lg-offset-2">
                <div class="panel panel-primary">
                    <div class="panel-heading">
                        <span class="glyphicon glyphicon-comment"></span> <span id="username"></span>
                        <div class="btn-group pull-right">
                            <button type="button" class="btn btn-default btn-xs dropdown-toggle" data-toggle="dropdown">
                                <span class="glyphicon glyphicon-chevron-down"></span>
                            </button>
                            <ul class="dropdown-menu slidedown">
                                <li><a><span class="glyphicon glyphicon-refresh">
                                </span>Refresh</a></li>
                                <li><a><span class="glyphicon glyphicon-ok-sign">
                                </span>Available</a></li>
                                <li><a><span class="glyphicon glyphicon-remove">
                                </span>Busy</a></li>
                                <li><a><span class="glyphicon glyphicon-time"></span>
                                    Away</a></li>
                                <li class="divider"></li>
                                <li><a id="logout"><span class="glyphicon glyphicon-off"></span>
                                    Sign Out</a></li>
                            </ul>
                        </div>
                    </div>
                    <div class="panel-body body-panel">
                        <ul class="chat">

                        </ul>
                    </div>
                    <div class="panel-footer clearfix">
                        <textarea id="message" class="form-control" rows="3"></textarea>
                        <span class="col-lg-6 col-lg-offset-3 col-md-6 col-md-offset-3 col-xs-12" style="margin-top: 10px">
                            <button class="btn btn-warning btn-lg btn-block" id="btn-chat">Send</button>
                        </span>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <script id="new-message" type="text/template">
        <li id="{{id}}" class="right clearfix">
            <div class="chat-body clearfix">
                <div class="header">
                    <small class="text-muted">{{name}}</small>
                </div>
                <p>
                    {{body}}
                </p>
            </div>
        </li>
    </script>
    </body>
    </html>
```

该文件使用了来自 [bootsnip](http://bootsnipp.com/snippets/6eWd) 的模板，还包含了一个对 auth 0 Lock*<script src = "*[*https://cdn.auth0.com/js/lock/10.18.0/lock.min.js*](https://cdn.auth0.com/js/lock/10.18.0/lock.min.js)*"></script>*的脚本引用。 [Lock](https://auth0.com/docs/libraries#lock-login-signup-widgets) 是一个嵌入式身份验证小工具，提供登录所需的一组标准行为和一个可定制的用户界面。它提供了一种简单的方式，以非常少的配置与 Auth0 集成。

我们希望允许用户在进入应用程序时登录，并在通过身份验证后发送消息。添加一个新文件 **index.js** ，内容如下:

```
$(document).ready(function(){
 // Initiating our Auth0Lock
 let lock = new Auth0Lock(
 ‘CLIENT_ID’,
 ‘CLIENT_DOMAIN’,//example: lotus.auth0.com
 {
 auth: {
 params: {
 scope: ‘openid profile’
 } 
 },
 autoclose: true,
 closable: false,
 rememberLastLogin: true
 }
 );

 let profile = JSON.parse(localStorage.getItem(‘profile’));
 let isAuthenticated = localStorage.getItem(‘isAuthenticated’);

 function updateValues(userProfile, authStatus) {
 profile = userProfile;
 isAuthenticated = authStatus;
 }

 if(!isAuthenticated && !window.location.hash){
 lock.show();//show Lock widget
 }

 // Listening for the authenticated event
 lock.on(“authenticated”, function(authResult) {
 // Use the token in authResult to getUserInfo() and save it to localStorage
 lock.getUserInfo(authResult.accessToken, function(error, profile) {
 if (error) {
 // Handle error
 return;
 }

 localStorage.setItem(‘accessToken’, authResult.accessToken);
 localStorage.setItem(‘profile’, JSON.stringify(profile));
 localStorage.setItem(‘isAuthenticated’, true);
 updateValues(profile, true);
 $(“#username”).html(profile.name);
 });
 });
 });
```

我们通过传递应用程序的客户端 Id 来初始化锁，你的用户域以你的用户名开始，后跟 *.auth0.com* 或*{你的 _ 选择的 _ 区域}.auth0.com* ，例如*lotus.eu.auth0.com*。小部件是可配置的，我们可以发送配置选项，如**可关闭**、**自动关闭**和**授权**。在 **auth** 选项中，我们告诉它返回 **openid** 和 **profile** 声明。

我们检查用户是否通过了身份验证，如果没有，就显示小部件。一旦用户通过身份验证，Lock 就会发出我们已经订阅的“通过身份验证”事件。当它被引发时，我们将用户配置文件和其他凭证存储到 localStorage，并设置要在页面上显示的用户名。一旦用户通过身份验证，我们希望连接到 Pusher 并发送消息。使用以下代码更新 index.js:

```
if(!isAuthenticated && !window.location.hash){
  lock.show();
 }
 else{

 // Enable pusher logging — don’t include this in production
 Pusher.logToConsole = true;

 var pusher = new Pusher(‘APP_SECRET’, {
  cluster: ‘e.g eu’,
  encrypted: false
 });

 var channel = pusher.subscribe(‘private-chat’);
  channel.bind(‘message-added’, onMessageAdded); 
 }

 function onMessageAdded(data) {
  let template = $(“#new-message”).html();
  template = template.replace(“{{body}}”, data.message);
  template = template.replace(“{{name}}”, data.name);

  $(“.chat”).append(template);
 }
```

Pusher 使用 **APP_SECRET** 和**集群**进行初始化，您可以从 Pusher 上的 APP 仪表板中获得这些信息。我们订阅了一个叫做*私人聊天*的频道。Pusher 有 3 种类型的渠道:公共渠道、私人渠道和在线渠道。私有和在线频道让您的服务器控制对您正在广播的数据的访问。存在通道进一步强制订阅者在订阅时注册用户信息。私有频道以 *private-* 开始命名，并在订阅时在服务器中进行认证。

最后，我们希望在用户单击发送时向他们发送消息，并在他们选择注销时注销他们。用下面的代码更新 **index.js**

```
$(‘#btn-chat’).click(function(){
  const message = $(“#message”).val();
  $(“#message”).val(“”);
  //send message
  $.post( “[http://localhost:5000/message](http://localhost:5000/message)”, { message, name: profile.name } );
 }); 

  $(“#logout”).click((e) => {
  e.preventDefault();
  logout();
 });

 function logout(){
   localStorage.clear();
   isAuthenticated = false;
   lock.logout({ 
   returnTo: “[http://localhost:5000](http://localhost:5000)” 
  });
 }
```

当用户单击 send 按钮时，我们获取消息并将其放入一个具有用户概要名称的对象中，然后将其发送到服务器上的 */message* 端点。当点击 logout 按钮时，它调用 logout 函数来清除存储在 localStorage 中的数据，并调用 *lock.logout()* 让用户在 Auth0 上注销，并将他们重定向回我们的网站。通过所有这些添加，index.js 应该包含以下内容:

```
$(document).ready(function(){
   // Initiating our Auth0Lock
   let lock = new Auth0Lock(
    ‘CLIENT_ID’,
    ‘CLIENT_DOMAIN’,
    {
      auth: {
      params: {
       scope: ‘openid profile’
      } 
    },
   autoclose: true,
   closable: false,
   rememberLastLogin: true
  });

  // Listening for the authenticated event
  lock.on(“authenticated”, function(authResult) {
  // Use the token in authResult to getUserInfo() and save it to   localStorage
  lock.getUserInfo(authResult.accessToken, function(error, profile) {
  if (error) {
   // Handle error
   console.log(error);
   return;
  }

   localStorage.setItem(‘accessToken’, authResult.accessToken);
   localStorage.setItem(‘profile’, JSON.stringify(profile));
   localStorage.setItem(‘isAuthenticated’, true);
   updateAuthenticationValues(profile, true);
   $(“#username”).html(profile.name);
  });
 });

 let profile = JSON.parse(localStorage.getItem(‘profile’));
 let isAuthenticated = localStorage.getItem(‘isAuthenticated’);

 function updateAuthenticationValues(userProfile, authStatus) {
  profile = userProfile;
  isAuthenticated = authStatus;
 }

 $(“#logout”).click((e) => {
  e.preventDefault();
  logout();
 });

 function logout(){
  localStorage.clear();
  isAuthenticated = false;
  lock.logout({ 
   returnTo: “[http://localhost:5000](http://localhost:5000)” 
  });
 }

 function onMessageAdded(data) {
  let template = $(“#new-message”).html();
  template = template.replace(“{{body}}”, data.message);
  template = template.replace(“{{name}}”, data.name);

  $(“.chat”).append(template);
 }

 if(!isAuthenticated && !window.location.hash){
  lock.show();
 }
 else{
  if(profile){
  $(“#username”).html(profile.name);
 }

 // Enable pusher logging — don’t include this in production
 Pusher.logToConsole = true;

 var pusher = new Pusher(‘APP_SECRET’, {
  cluster: ‘eu’,
  encrypted: false
 });

 var channel = pusher.subscribe(‘private-chat');
 channel.bind(‘message-added’, onMessageAdded);

 $(‘#btn-chat’).click(function(){
  const message = $(“#message”).val();
  $(“#message”).val(“”);
  //send message
  $.post( “[http://localhost:5000/message](http://localhost:5000/message)”, { message, name: profile.name } );
  }); 
  }
 });
```

要测试该应用程序，请在终端上运行“npm start ”,并在两个不同的浏览器上打开[*http://localhost:5000*](http://localhost:5000`)*。下面是它的运行过程:*

*![](img/a521a5d8ee86de364a79529c4ddd4b06.png)*

# *包裹*

*这是一个应用程序，展示如何使用 Pusher 实时发送消息并保护通道，使用 Auth0 添加用户验证和帐户管理，并使用 Auth0 Lock 轻松集成到 Auth0。在你的控制台上，你可以看到用户、登录和新注册的总数。*

*![](img/8af9a31b886c85f054beea0a8deda257.png)**![](img/8e6a9045e654799fd7c7659e03ab7b83.png)*

*当您单击**用户* *侧菜单时，您还可以看到您的所有用户。在此页面上，您可以看到您的用户列表及其登录模式。*

*![](img/cadc8dee64ea36fe5b16bb9d1e39877a.png)*

*选择一个用户会将您带到一个更详细的页面，在这里您可以对帐户采取各种操作，例如，阻止帐户或发送验证电子邮件。*

*![](img/c0be4b3afc1604e8ecf1bed450e01b20.png)*

*同样在 Pusher 上，你可以进入你的应用仪表板，在 **Stats** 下，在那里你会看到关于你的应用的统计数据，比如连接频率和通过该应用发送的消息数量。这两种技术的结合使得构建实时安全的应用程序变得更快更容易。你可以在 [GitHub](https://github.com/pmbanugo/Pusher-Auth0-ChatApp) 上找到代码。*

**此文最初发表于* [*推手*](https://blog.pusher.com/build-secure-chat-web-app-javascript-auth0-pusher/)*