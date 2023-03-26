# 从零开始的 Web 应用程序，第一部分

> 原文：<https://medium.com/hackernoon/web-application-from-scratch-part-i-314ea8203e64>

![](img/bb9d0d4dad1ea654d6700931e4d36e46.png)

Yes, this is how all servers start out.

这是我将从头开始用 Python 构建一个 web 应用程序(及其 web 服务器)的过程的系列文章中的第一篇。出于本系列的目的，我将只依赖 Python 标准库，而忽略 WSGI 标准。

事不宜迟，我们开始吧！

# 网络服务器

首先，我们将编写支持我们的 web 应用程序的 HTTP 服务器。但是首先，我们需要花一点时间了解 HTTP 协议是如何工作的。

## HTTP 如何工作

简单地说，HTTP 客户端通过网络连接到 HTTP 服务器，并向它们发送一串表示请求的数据。然后，服务器解释该请求，并向客户端发回响应。在 [RFC2616](https://tools.ietf.org/html/rfc2616) 中描述了整个协议以及这些请求和响应的格式，但是我将在下面非正式地描述它们，这样您就不必阅读全部内容。

## 请求格式

请求由一系列用`\r\n`分隔的行表示，第一行称为“请求行”。请求行由一个 HTTP 方法、一个空格、被请求文件的路径、另一个空格、客户端使用的 HTTP 协议版本以及回车符(`\r`)和换行符(`\n`)组成:

```
GET /some-path HTTP/1.1\r\n
```

在请求行之后是零个或多个标题行。每个标题行由标题名、冒号、可选值和`\r\n`组成:

```
Host: example.com\r\n
Accept: text/html\r\n
```

标题部分的结尾由一个空行表示:

```
\r\n
```

最后，请求可能包含一个“主体”——随请求发送到服务器的任意有效负载。

综上所述，这里有一个简单的`GET`请求:

```
GET / HTTP/1.1\r\n
Host: example.com\r\n
Accept: text/html\r\n
\r\n
```

这里有一个简单的`POST`请求:

```
POST / HTTP/1.1\r\n
Host: example.com\r\n
Accept: application/json\r\n
Content-type: application/json\r\n
Content-length: 2\r\n
\r\n
{}
```

## 响应格式

像请求一样，响应由一系列用`\r\n`分隔的行组成。响应中的第一行称为“状态行”，它由 HTTP 协议版本、后跟空格、响应状态代码、另一个空格、状态代码原因和`\r\n`组成:

```
HTTP/1.1 200 OK\r\n
```

在状态行之后是响应标题，然后是一个空行，然后是可选的响应正文:

```
HTTP/1.1 200 OK\r\n
Content-type: text/html\r\n
Content-length: 15\r\n
\r\n
<h1>Hello!</h1>
```

# 简单的服务器

基于我们目前对该协议的了解，让我们编写一个服务器，不管传入的请求是什么，它都发送相同的响应。

首先，我们需要创建一个套接字，将其绑定到一个地址，然后开始监听连接。

如果你现在尝试运行这段代码，它会打印出它正在监听`127.0.0.1:9000`的标准输出，然后退出。为了实际处理传入的连接，我们需要在套接字上调用`accept`方法。这样做将阻塞进程，直到客户端连接到我们的服务器。

一旦我们有了到客户机的套接字连接，我们就可以开始与它通信了。使用`sendall`方法，让我们向连接客户端发送一个示例响应:

如果您现在运行代码，然后在您最喜欢的浏览器中访问 [http://127.0.0.1:9000](http://127.0.0.1:9000/) ，它应该会呈现字符串“Hello！”。不幸的是，服务器将在发送响应后退出，因此刷新页面将失败。让我们来解决这个问题:

此时，我们有了一个 web 服务器，它可以为每个请求提供一个简单的 HTML 网页，全部代码只有 25 行。那还不算太糟！

# 文件服务器

让我们扩展 HTTP 服务器，使它能够提供磁盘外的文件。

## 请求抽象

在这样做之前，我们必须能够读取和解析来自客户端的请求数据。因为我们知道请求数据由一系列行表示，每一行由`\r\n`字符分隔，所以让我们编写一个从套接字读取数据并生成每一行的生成器函数:

这看起来可能有点令人畏惧，但本质上它所做的是从套接字中读取尽可能多的数据(以`bufsize`块的形式)，在一个缓冲区中将这些数据结合在一起(`buff`)，并不断地将缓冲区分割成单独的行，一次产生一行。一旦它发现一个空行，它就返回它读取的额外数据。

使用`iter_lines`，我们可以开始打印我们从客户那里得到的请求:

如果您现在运行服务器并访问 [http://127.0.0.1:9000](http://127.0.0.1:9000/) ，您应该会在控制台中看到如下内容:

```
Received connection from ('127.0.0.1', 62086)...
b'GET / HTTP/1.1'
b'Host: localhost:9000'
b'Connection: keep-alive'
b'Cache-Control: max-age=0'
b'Upgrade-Insecure-Requests: 1'
b'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.167 Safari/537.36'
b'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8'
b'Accept-Encoding: gzip, deflate, br'
b'Accept-Language: en-US,en;q=0.9,ro;q=0.8'
```

相当整洁！让我们通过定义一个`Request`类来抽象这些数据:

现在，请求类只知道方法、路径和请求头。我们将把解析查询字符串参数和读取请求体留到以后。

为了封装构建请求所需的逻辑，我们将向`Request`添加一个名为`from_socket`的类方法:

它使用我们之前定义的`iter_lines`函数来读取请求行。这就是它获取`method`和`path`的地方，然后它读取每个单独的标题行并解析它们。最后，它构建`Request`对象并返回它。如果我们将它插入到我们的服务器循环中，它应该是这样的:

如果您现在连接到服务器，您应该会看到这样的行被打印出来:

```
Request(method='GET', path='/', headers={'host': 'localhost:9000', 'user-agent': 'curl/7.54.0', 'accept': '*/*'})
```

因为`from_socket`可以在某些情况下引发异常，如果现在给出一个无效的请求，服务器可能会崩溃。为了模拟这种情况，您可以使用 telnet 连接到服务器，并向其发送一些虚假数据:

```
~> telnet 127.0.0.1 9000
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
hello
Connection closed by foreign host.
```

果然，服务器崩溃了:

```
Received connection from ('127.0.0.1', 62404)...
Traceback (most recent call last):
  File "server.py", line 53, in parse
    request_line = next(lines).decode("ascii")
ValueError: not enough values to unpack (expected 3, got 1)During handling of the above exception, another exception occurred:Traceback (most recent call last):
  File "server.py", line 82, in <module>
    with client_sock:
  File "server.py", line 55, in parse
    raise ValueError("Request line missing.")
ValueError: Malformed request line 'hello'.
```

为了更好地处理这类问题，让我们将对`from_socket`的调用封装在一个 try-except 块中，当我们收到一个格式错误的请求时，向客户端发送一个“400 Bad Request”响应:

如果我们现在尝试破坏它，我们的客户端将得到一个响应，服务器将保持运行:

```
~> telnet 127.0.0.1 9000
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
hello
HTTP/1.1 400 Bad Request
Content-type: text/plain
Content-length: 11Bad RequestConnection closed by foreign host.
```

此时，我们准备开始实现文件服务部分，但首先让我们将默认响应设为“404 Not Found ”:

此外，让我们添加一个“不允许 405 方法”响应。除了`GET`请求之外，我们还需要它。

让我们定义一个`SERVER_ROOT`常量来表示服务器应该从哪里提供文件，以及一个`serve_file`函数。

`serve_file`获取客户端套接字和文件路径。然后，它尝试将该路径解析为`SERVER_ROOT`内部的一个真实文件，如果该文件在服务器根目录之外解析，则返回“not found”响应。然后，它尝试打开文件并计算出它的 mime 类型和大小(使用`os.fstat`)，然后构造响应头并使用`sendfile`系统调用将文件写入套接字。如果它在磁盘上找不到该文件，那么它会发送一个“未找到”响应。

如果我们将`serve_file`加入到这个组合中，我们的服务器循环应该是这样的:

如果您在您的`server.py`文件旁边添加一个名为`www/index.html`的文件，并访问 [http://localhost:9000](http://localhost:9000/) ，您应该会看到该文件的内容。酷吧。

# 逐渐减少

第 1 部分到此为止。在第 2 部分中，我们将讨论提取`Server`和`Response`抽象，以及让服务器处理多个并发连接。如果你想查看完整的源代码并跟随，你可以在这里找到它[。](https://github.com/Bogdanp/web-app-from-scratch/tree/part-01)

下次见！

*感谢阅读！如果你喜欢这篇文章，给它一个掌声！你也可以在我的网站*[*https://defn . io*](https://defn.io)*，在*[*GitHub*](https://github.com/Bogdanp)*和*[*Twitter*](https://twitter.com/Bogdanp)*上找到我。*

[](https://twitter.com/bogdanp) [## 波格丹一世·波帕(@波格丹)|推特

### 波格丹一世·波帕的最新推文。程序员，https://t.co/FFd6cPhKk5 和…

twitter.com](https://twitter.com/bogdanp)