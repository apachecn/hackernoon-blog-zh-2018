# 用 Kotlin 为 Android 构建一个位置反馈应用

> 原文：<https://medium.com/hackernoon/build-a-location-feed-app-for-android-with-kotlin-742096e8f80e>

> [**推手**](http://bit.ly/2lCXscm) ，我们的每周赞助商，开发通信和协作 API，为世界各地的应用提供支持，由易于集成的 SDK 支持，用于 web、移动设备以及最受欢迎的后端堆栈。 [**入门。**](http://bit.ly/2lCXscm)

很多时候，我们喜欢在一个中心位置跟踪和可视化我们的应用程序。饲料是伟大的！在本教程中，我们将构建一个带有活动提要的 Android 应用程序，允许用户广播他们的位置，并与所有其他连接的用户实时共享。

我们将构建 Android 应用程序来监控一个 [Node.js](https://nodejs.org/) REST API 的活动。每次点击 API 的端点时，Pusher 会向一个通道发布一个带有一些信息(用户共享的位置)的事件。该事件将在所有连接的 Android 设备上实时接收。

这是正在运行的应用程序:

# 先决条件

本教程使用了以下技术

*   [推杆](https://pusher.com/)
*   [安卓工作室](https://developer.android.com/studio/index.html)
*   [节点](https://nodejs.org/en/download)

要跟进，您需要[注册 Pusher](https://pusher.com/signup.) 并访问您的仪表板以创建 Pusher 项目。您还需要安装 Android Studio v3+来构建这个应用程序的客户端部分。要构建我们的服务器端脚本，您需要下载并安装节点，如果您还没有安装的话。

# 客户端

既然你已经解决了这个问题，让我们开始构建我们的 Android 应用程序。启动 Android Studio 并创建一个新项目。一定要包括 Kotlin 支持。输入一个应用程序名称，在我们的例子中是推料器-位置-进料

选择应用程序的目标 SDK:

选择基本活动模板:

当项目构建完成时，打开您的应用程序级`build.gradle`文件并更新依赖项，如下所示:

接下来，通过点击 **Sync Now** 与 gradle 文件同步项目以安装添加的依赖项。

# 应用活动

# 登录活动

默认情况下，创建 Android 项目也会为您创建一个`MainActivity`类和一个关联的`activity_main.xml`文件。现在我们需要一个登录活动来收集用户的用户名。所以创建一个新活动，右键点击**main Activity>New>>Activity>>Empty Activity**，然后将其命名为`LoginActivity`。一旦这个活动被创建，它将在`res`下的`layout`文件夹中创建一个默认的布局文件`activity_login.xml`。布局将是一个相当简单的，它将有一个文本输入来收集用户的`username`和一个按钮来分享他们的位置。下面是`activity_login.xml`文件的一个片段:

这里我们有一个简单的带有两个视图对象的`LinearLayout`，一个用于收集用户的`username`的`EditText`输入和一个用于将位置发送到服务器的共享按钮。

Android 的默认风格并不总是吸引人，所以让我们添加一些自定义风格到我们的布局中，只是为了美观。在`res`文件夹下，打开`values`文件夹，导航到`colors.xml`文件，用以下代码更新:

其次，为了实现按钮和输入样式，我们创建了两个`drawable`文件。在`res`下右键点击**可绘制> >新建> >可绘制资源文件**，将其命名为`input_bg`并用以下代码更新:

这只是将圆边添加到 EditText 对象中。对于按钮样式，遵循与上面相同的步骤，创建一个新的可绘制文件，命名为`button`并如下设置:

最后更新`layout`目录下`values`文件夹中的`styles.xml`文件:

此时，xml 可视化工具中的输出应该如下所示:

接下来让我们创建一个名为`custom_view.xml`的新布局文件。我们将使用该文件在 recyclerview 对象上呈现用户的每个地图。在`res`下的`layout`文件夹中，创建新的布局资源文件，设置如下:

好了，我们完成了登录和 UI，让我们用它的 Java 文件来处理逻辑。打开`LoginActivity.kt`文件，设置如下:

这里我们只是简单地获取我们在布局文件中定义的输入值，并有目的地将其传递给`MainActivity`类。一旦用户在`Edittext`对象中输入了一个值(用户名),我们就在按钮上设置一个监听器，以便在单击时调用 intent 动作。只有当输入值不为空时，才会执行此操作。

# 主要活动

接下来，我们定义一个布局，当每个用户共享他们的位置时，我们将呈现他们的地图位置。我们将获得他们的纬度和经度坐标以及他们在`LoginActivity`中提供的用户名，并将其发送到我们的服务器，然后服务器会返回一张地图，在地图标记上显示用户名，并在屏幕上显示给所有用户。

在进入 MainActivity 之前，让我们首先用 RecyclerView 对象定义一个新的布局文件，以便在用户共享这些位置小部件时保存它们。在`res`下，右键点击**布局> >新建> >布局资源文件**，并将其命名为`content_main`(如果您在设置项目时选择了基本活动模板，那么默认情况下应该有这个文件)。打开这个文件，并像这样设置它:

正如所看到的，我们只是有一个 RecyclerView 对象，在其中我们将呈现每个用户的位置，这样他们就可以全部出现在一个列表中。最后，打开`activity_main.xml`并更新它:

# 应用逻辑

因为我们在布局文件中使用了 RecyclerView，所以我们需要一个适配器类。`RecyclerView`使用一个[适配器](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.Adapter.html)来管理其数据源的项目，使用一个[视图保持器](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ViewHolder.html)来保存表示单个列表项目的视图。在我们创建`Adapter`类之前，让我们首先创建一个`Model`类，它将在我们的远程数据和适配器之间接口。它将包含我们将数据传递给 recyclerview 的值。现在右击**main activity>>New>>****kot Lin 文件/类**，将其命名为 **Model，**种类**下拉框下的**，选择**类**，设置如下:

现在我们有了这个，让我们创建`Adapter`类。右键单击**main activity>>New>>kot Lin 文件/类**，将其命名为`Adapter`，在**种类**下拉菜单下，再次选择类，并用代码设置:

这里我们从模型类中定义了一个`arrayList`，适配器将使用它来填充`R``ecycler``V``iew`。在`onBindViewHolder()`方法中，我们将来自服务器的位置(经度和纬度)绑定到我们为其定义的视图容器。我们还将用户的用户名传递给地图标记。

然后在`onCreateViewHolder()`方法中，我们为列表中的单个项目定义布局设计。最后，`addItem()`方法将我们的模型类的一个新实例添加到`arrayList`中，并在每次添加新实例时刷新列表。

接下来，让我们使用我们在开始时安装的改进库来建立到节点服务器的连接。首先，我们创建一个新的 Kotlin 接口来定义我们将为此项目调用的 API 端点。右键点击**main activity>>New>>****kot Lin 文件/类**，在**种类**下拉菜单下，选择接口名称 it **Service** ，设置如下:

我们还需要一个类，它将为我们提供进行网络调用的改进实例。它也将是我们定义服务器 URL 和网络参数的类。所以按照前面的步骤，创建一个名为`Client.kt`的类，并像这样设置它:

> *用节点服务器的本地主机地址替换基本 URL。我们将*

我们在这里使用的`baseUrl`指向我们在你的机器上运行的本地节点服务器，如上所示，但是我们将在后面的教程中讨论它。现在让我们回到`MainActivity.kt`，初始化必要的对象并用我们上面创建的类更新它。

这里我们已经初始化了我们需要的对象，我们的适配器类、Pusher、位置请求和`fusedLocationClient`。

在`onCreate()`方法中，我们将使用适配器设置我们的 RecyclerView。我们还将调用带有浮动动作按钮的`setupPusher()`方法和`sendLocation()`动作:

> *在将这段代码添加到您的* `*onCreate()*` *方法中时，注意不要错过花括号*

所以我们调用了我们还没有定义的方法，这没问题，我们将在后面的教程中定义`setupPusher()`方法，但是首先，让我们这次定义并设置`sendLocation()`方法，在`onCreate()`之外:

通过我们之前初始化的`fusedLocationClient`对象，我们获得了用户的位置。如果我们成功地获得了位置，我们将把经度和纬度以及用户的`username`传递给我们的`body`对象。然后，我们使用它来构建 HTTP 请求，并将`jsonObjects`作为请求参数。

我们也在`onCreate()`方法中调用了`checkLocationPermission()`方法，但是我们还没有定义它。现在让我们创建这个方法，并像这样设置它:

当然，我们不能在没有得到用户许可的情况下就获取他们的位置，所以下面是我们如何设置请求访问他们位置许可的方法。就在`sendLocation()`方法之后，添加:x

现在让我们定义我们之前在`onCreate()`方法中调用的`setUpPusher()`方法:

这里，我们简单地将 Pusher 配置传递给 Pusher 对象，并订阅`feed`通道来监听`location`事件。然后，我们将从服务器返回的数据放入我们定义的变量中，并将它们传递给我们的模型类来更新适配器。

接下来，我们在应用程序中分别实现`onStart()`和`onStop()`方法来连接和断开推动器:

最后，在客户端，我们创建一个 Kotlin 数据类，它将定义我们将从服务器请求的有效负载。按照前面的步骤，创建一个名为 **RequestPayload** 的类，并像这样设置它:

# 服务器端

# 设置推动器

现在我们已经有了所有的客户端功能，让我们继续构建我们的服务器。但是首先，如果你还没有，现在是时候在这里创建一个免费账户了。首次登录时，系统会要求您输入一些配置选项:

输入名字，选择 *Android* 作为你的前端 [tech](https://hackernoon.com/tagged/tech) ，选择 *Node.js* 作为你的后端 tech。这将为您提供一些示例代码，帮助您了解项目 api 密钥:

然后转到 **App Keys** 选项卡，复制您的 app_id、密钥和秘密凭证，我们稍后会用到它们。

# 设置节点服务器

为此，我们将使用节点。因此，通过在命令提示符下运行以下命令，检查您的机器上是否安装了`node`和`npm`:

```
node --version
//should display version numbers

npm --version
//should display version numbers
```

如果不是这样，[下载并安装节点](https://nodejs.org/en/download/)。

接下来让我们开始构建我们的服务器端脚本。仍然在命令提示符下，运行:

```
mkdir pusherLocationFeeds
//this creates a project directory to host your project files

cd pusherLocationFeeds
// this navigates into the just created directory

npm init -y
//this creates a default package.json file to host our project dependencies
```

让我们安装这个项目需要的节点模块。基本上我们需要 Express、Pusher 和 body-parser。在项目目录中，运行:

```
install express, body-parser, pusher
```

您总是可以通过打开“package.json”文件来验证这些安装，此时依赖块应该如下所示:

```
"dependencies": {
        "body-parser": "^1.18.2",
        "express": "^4.16.3",
        "pusher": "^1.5.1"
      }
```

接下来在项目目录中创建一个 server.js 文件。首先，我们需要安装的节点模块:

```
var express = require("express")
var pusher = require("pusher")
var bodyParser = require("body-parser")
```

接下来，我们配置 Express:

```
var app = express();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
```

现在让我们通过传递配置对象来创建 Pusher 对象，该配置对象带有在 [Pusher 仪表板](https://dashboard.pusher.com/)中创建的应用程序的 id、密钥和密码:

```
var pusher = new Pusher({    
        appId: "pusher_app_id",
        key: "pusher_app_key",
        secret: "pusher_app_secret",
        cluster: "pusher_app_cluster"
      });
```

如前所述，我们将使用 Pusher 发布应用程序中发生的事件。这些事件有一个 **eventChannel** ，允许它们与一个特定的主题相关联，一个 **eventName** ，用于标识事件的类型，还有一个 **payload** ，您可以将任何附加信息附加到这个 payload 上并发送回客户端。

在我们的例子中，当 API 的端点被调用时，我们将发布一个事件到推送通道(“feed”)。然后将信息作为附件发送，这样我们就可以在客户端的活动提要中显示它。

下面是我们如何定义 API 的 REST 端点:

```
app.post('/location', (req, res,next)=>{

        var longitude = req.body.longitude;
        var latitude = req.body.latitude;
        var username = req.body.username;
      ...
```

在这里，当我们接收请求参数时，我们将从请求中提取发送者的经度、纬度和用户名，并像这样作为响应发送回客户端:

```
...  
        pusher.trigger('feed', 'location', {longitude, latitude,username});
        res.json({success: 200});
      });
```

现在，当用户键入用户名并点击“共享位置”按钮时，服务器会返回如下数据:

```
{
      "longitude" : "longitude_value"
      "latitude" : "latitude_value"
      "username" : "username_value"
 }
```

从这里，我们使用**适配器**将它传递到**取景框**并在屏幕上显示出来。完成后，您的 server.js 文件应该如下所示:

```
var pusher = require("pusher")
    var express = require("express")
    var Pusher = require("pusher")
    var bodyParser = require("body-parser")
    var pusher = new Pusher({
        appId: "app_id",
        key: "app_key",
        secret: "app_secrete",
        cluster: "app_cluster"
      });
    var app = express();
    app.use(bodyParser.json());
    app.use(bodyParser.urlencoded({ extended: false }));

    app.post('/location', (req, res,next)=>{

        var longitude = req.body.longitude;
        var latitude = req.body.latitude;
        var username = req.body.username;

        pusher.trigger('feed', 'location', {longitude, latitude,username});
        res.json({success: 200});
    });
    app.listen(4040, function () {
        console.log('Listening on 4040')
      })
```

现在导航到终端，cd 进入`server.js`文件。然后使用以下命令运行服务器:

```
node server.js
```

# 运行应用程序

一旦服务器启动，继续运行 Android 应用程序。若要运行该应用程序，请保持您的系统连接到互联网。回到 Android Studio，单击菜单栏上的绿色 play 图标运行应用程序，或者从菜单中选择**运行**，并从下拉菜单中单击**运行“应用程序”**。此操作将启动您的设备模式，让您看到所有连接的设备和模拟器。如果您使用的是物理设备，只需从显示的可用设备列表中选择您的设备，然后点击**确定**。

如果您在模拟器上运行，请从设备列表中选择您的首选模拟器(如果您有一个设置),或者按照以下说明设置新的模拟器:

在设备模式上，选择**创建新的虚拟设备。**这将启动一个硬件选择模式，您可以选择任何设备，例如 Nexus 5，然后单击**下一步。**这将启动另一个模式，您可以在其中选择想要在设备上运行的 API 级别。您可以选择任何可用的选项，或者坚持使用默认选项，选择 API level 25。再次点击**下一步**给你的仿真器一个自定义名称，然后点击**完成**完成设置。现在，当您再次运行该应用程序时，您将看到您的仿真器列在可用设备模式中。在系统仍连接到互联网的情况下，选择您的首选设备并单击**确定**运行。

# 结论

希望这篇教程以一种简单的方式向你展示了如何用 Pusher 为 Android 应用程序构建一个活动提要。随着你继续构建，也许你会发现实时更新是非常重要的。当你这样做时，Pusher 拥有你需要的一切。项目可在 Github 上获得，服务器端代码也可在 gist 上获得。

*原载于* [*推手的博客*](https://pusher.com/tutorials/location-feed-android-kotlin) *。*

> 我们的每周赞助商 [**Pusher**](http://bit.ly/2lCXscm) 开发通信和协作 API，为世界各地的应用提供支持，并由易于集成的 web、移动和最受欢迎的后端堆栈 SDK 提供支持。 [**入门。**](http://bit.ly/2lCXscm)