# 如何轻松地将 cURL 用于 HTTP 请求

> 原文：<https://medium.com/hackernoon/how-to-easily-use-curl-for-http-requests-db3249c5d4e6>

![](img/355599ae544e94a001b146c302877ba9.png)

Photo by [Aron](https://unsplash.com/@aronunsplash?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

每个开发人员都需要知道一堆工具才能有效。在一个这样的工具中卷曲😄

在本文中，我将解释如何使用 **curl 发出 HTTP 请求**。

# 什么是卷曲

cURL 主要用于使用互联网协议为给定的 URL 传输数据。

Curl 是一个客户端程序。在名称 **cURL** 中， **c** 代表客户端， **URL** 表示 cURL 使用 URL。

curl 项目有一个 curl 命令行和一个 libcurl 库。在本文中，我们将关注 curl 命令行。

Curl 处理大量的互联网协议，比如 HTTP、FTP、SMTP、TELNET 等等。

在本文中，我们将只讨论从 Curl 发出 HTTP 请求。

# 先决条件

您可以使用命令检查您的系统中是否安装了 Curl。

```
curl --version
```

如果你的系统中没有 Curl，你可以从这个网址[https://curl.haxx.se/dlwiz/](https://curl.haxx.se/dlwiz/)安装它。

# 如何对 HTTP 请求使用 Curl

我已经使用 [NodeJS](https://hackernoon.com/tagged/nodejs) 构建了 2 个 REST [API](https://hackernoon.com/tagged/api) 端点。一个端点支持 GET 请求，另一个端点支持 POST 请求。

在本文中，我们将使用 Curl 调用 GET 和 POST 端点。

请从这个 [github repo](https://github.com/aditya-sridhar/simple-rest-apis-nodejs-without-frameworks) 中将 NodeJS 代码克隆到您的本地

repo 有关于如何克隆和运行 NodeJS 代码的说明。

克隆代码后，进入项目文件夹，使用下面的命令启动应用程序。

```
node server.js
```

该应用程序运行在本地主机端口 3000 上。

# 用 cURL 获取请求

应用程序有一个 GET 端点 **/sample** 。这个端点接受一个名为 **name** 的查询参数。

让我们使用 curl 来调用这个 API 端点。

转到新的命令提示符，键入以下命令

```
curl [http://localhost:3000/sample?name=aditya](http://localhost:3000/sample?name=aditya)
```

这将产生以下输出

```
**{**"text":"Hello aditya"**}**
```

使用 Curl 也可以得到详细的结果。运行以下命令

```
curl -v [http://localhost:3000/sample?name=aditya](http://localhost:3000/sample?name=aditya)
```

**-v** 用于获得详细的输出。

这将给出以下输出。

```
*****   Trying ::1...
***** TCP_NODELAY set
*****   Trying 127.0.0.1...
***** TCP_NODELAY set
***** Connected to localhost **(**127.0.0.1**)** port 3000 **(***#0)*
**>** GET /sample?name**=**aditya HTTP/1.1
**>** Host: localhost:3000
**>** User-Agent: curl/7.55.1
**>** Accept: *****/*****
**>**
< HTTP/1.1 200 OK
< Content-Type: application/json
< Date: Thu, 15 Nov 2018 12:36:21 GMT
< Connection: keep-alive
< Content-Length: 23
<
**{**"text":"Hello aditya"**}*** Connection *#0 to host localhost left intact*
```

详细结果包含状态代码、内容类型、内容长度等详细信息。它可以用来更好地了解 HTTP 请求期间发生了什么。

状态代码为 200，表示 HTTP 请求成功。

响应的内容类型是 JSON。

Content-Length 表示响应的大小。这里的响应大小是 23 字节。

# 使用 cURL 发布请求

应用程序有一个 POST 端点 **/test** 。此端点接受以下格式的 post 主体。

```
{
    "value":"nodejs"
}
```

为了进行 POST 调用，请键入以下命令。

```
curl --header "Content-Type: application/json" -d "{\"value\":\"node JS\"}" [http://localhost:3000/test](http://localhost:3000/test)
```

**–表头**表示文章主体的内容类型。这是 JSON。

**-d** 用于发送帖子正文内容。

该命令的输出如下所示。

```
{"text":"Post Request Value is node JS"}
```

要获得详细结果，请使用以下命令

```
curl -v --header "Content-Type: application/json" -d "{\"value\":\"node JS\"}" http://localhost:3000/test
```

输出如下所示。

```
*****   Trying ::1...
***** TCP_NODELAY set
*****   Trying 127.0.0.1...
***** TCP_NODELAY set
***** Connected to localhost **(**127.0.0.1**)** port 3000 **(***#0)*
**>** POST /test HTTP/1.1
**>** Host: localhost:3000
**>** User-Agent: curl/7.55.1
**>** Accept: *****/*****
**>** Content-Type: application/json
**>** Content-Length: 19
**>**
***** upload completely sent off: 19 out of 19 bytes
< HTTP/1.1 200 OK
< Content-Type: application/json
< Date: Thu, 15 Nov 2018 13:03:37 GMT
< Connection: keep-alive
< Content-Length: 41
```

# 命令行提供的附加选项

可使用 **-H** 选项修改现有的割台。

运行以下命令将**用户代理**头修改为**虚拟代理**

```
curl -v -H "User-Agent:Dummy Agent" [http://localhost:3000/sample?name=adi](http://localhost:3000/sample?name=adi)
```

上述命令的输出如下所示

```
*****   Trying ::1...
***** TCP_NODELAY set
*****   Trying 127.0.0.1...
***** TCP_NODELAY set
***** Connected to localhost **(**127.0.0.1**)** port 3000 **(***#0)*
**>** GET /sample?name**=**adi HTTP/1.1
**>** Host: localhost:3000
**>** Accept: *****/*****
**>** User-Agent:Dummy Agent
**>**
< HTTP/1.1 200 OK
< Content-Type: application/json
< Date: Thu, 15 Nov 2018 13:33:27 GMT
< Connection: keep-alive
< Content-Length: 20
<
**{**"text":"Hello adi"**}*** Connection *#0 to host localhost left intact*
```

在上面的输出中可以看到**用户代理**已经变成了**虚拟代理**。

现在，假设您想要删除**主机**标头。这可以通过运行以下命令来完成。

```
curl -v -H "Host:" [http://localhost:3000/sample?name=adi](http://localhost:3000/sample?name=adi)
```

上述命令的输出如下所示，可以看到**主机**不再存在

```
*****   Trying ::1...
***** TCP_NODELAY set
***** connect to ::1 port 3000 failed: Connection refused
*****   Trying 127.0.0.1...
***** TCP_NODELAY set
***** Connected to localhost **(**127.0.0.1**)** port 3000 **(***#0)*
**>** GET /sample?name**=**adi HTTP/1.1
**>** User-Agent: curl/7.55.1
**>** Accept: *****/*****
**>**
< HTTP/1.1 200 OK
< Content-Type: application/json
< Date: Thu, 15 Nov 2018 13:38:37 GMT
< Connection: keep-alive
< Content-Length: 20
<
**{**"text":"Hello adi"**}*** Connection *#0 to host localhost left intact*
```

**-H** 也可用于添加新的标题。这可以使用语法`curl -v -H "newheader: headervalue"`来完成

# 参考

科尔的文件:【https://ec.haxx.se/ 

关于使用 Curl 处理 HTTP 请求的更多信息[https://ec.haxx.se/http.html](https://ec.haxx.se/http.html)

# 祝贺😄

您现在知道了如何使用 cURL 处理基本的 HTTP 请求。本文只涉及了 curl 实际能做的很小一部分。

要了解更多关于 curl 的信息，你可以查看我上面提供的文档链接。

# 关于作者

我热爱技术，关注该领域的进步。

请随时联系我的 LinkedIn 账户[https://www.linkedin.com/in/aditya1811/](https://www.linkedin.com/in/aditya1811/)

你也可以在推特上关注我[https://twitter.com/adityasridhar18](https://twitter.com/adityasridhar18)

我的网站:[https://adityasridhar.com/](https://adityasridhar.com/)

*原载于*[*adityasridhar.com*](https://adityasridhar.com/posts/how-to-easily-use-curl-for-http-requests)*。*