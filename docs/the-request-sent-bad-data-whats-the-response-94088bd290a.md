# 请求发送了错误数据；有什么回应？

> 原文：<https://medium.com/hackernoon/the-request-sent-bad-data-whats-the-response-94088bd290a>

*看看 HTTP 4xx 状态码 RFC 是如何发展的*

![](img/512bb42d64aa6c52054e1a1f26152210.png)

阅读 4xx 身份代码可能看起来很无聊，但它一直是([宗教](https://www.bennadel.com/blog/2434-http-status-codes-for-invalid-data-400-vs-422.htm))的来源？)多年来的争论。互联网工程任务组(IETF)已经澄清了[模糊性](https://www.quora.com/Which-HTTP-code-is-best-suited-for-validation-errors-400-or-422)的一个[lot](https://www.quora.com/Is-HTTP-Error-Code-422-WebDAV-specific)(*改变了*可能是一个更好的词)，所以我将看看它过去是什么，现在是什么，以及对你的后果，我的开发伙伴。

# 选择你的毒药

这里有一个伪测试用例，使用了一个看似合理但不明确的请求库:

```
const body = {
    title: "Ethel the Aardvark goes Quantity Surveying",
    ISBN: "no clue"
};request(url, body, {"Content-type":"application/json"})
.then(success)
.catch(failure => {...})
```

一切都是正确的:标题是正确的，数据是格式良好的。但是，其中一个值无效(ISBN)，服务器无法处理该请求。有哪些选择？

1.  返回 [400 错误请求](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.4.1)状态
2.  返回一些其他的 [4xx 状态](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.4)
3.  返回 [200 OK](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) ，并在响应体中编码一个错误

# 关于 400 状态码的标准曾经说过什么

```
The request could not be understood by the server due to malformed syntax. The client SHOULD NOT repeat the request without modifications.
```

大约在 1999 年。注意，它指定了“格式错误的语法”。在所示的 ISBN 示例中，没有格式错误的语法…问题只是一个错误的数据值。出于这个原因，人们反对因为坏数据而退回 400，因为标准对 400 的含义非常明确。

后来，添加了状态代码[422](https://tools.ietf.org/html/rfc4918#page-78)。这个状态代码是关于请求的语义，而不是它的语法。如果请求数据无法解释，那么 422 响应似乎更符合标准。

唉，时代变了，发送一个 422 来回应糟糕的数据是[似乎不赞成](https://www.keycdn.com/support/422-unprocessable-entity)。

# 现在的标准是什么

大约 2014 年。下面是[最新正文](https://tools.ietf.org/html/rfc7231#section-6.5.1):

```
The 400 (Bad Request) status code indicates that the server cannot or will not process the request due to something that is perceived to be a client error (e.g., malformed request syntax, invalid request message framing, or deceptive request routing).
```

这是一个比原来更广泛的定义，不限于语法错误，但它没有具体提到数据错误。这是怎么回事？通过查看[中关于自定义状态代码的内容](https://tools.ietf.org/html/rfc7231#page-47)可以找到一条线索:

```
HTTP status codes are extensible. HTTP clients are not required to
 understand the meaning of all registered status codes, though such
 understanding is obviously desirable. However, a client MUST
 understand the class of any status code, as indicated by the first
 digit, and treat an unrecognized status code as being equivalent to
 the x00 status code of that class, with the exception that a
 recipient MUST NOT cache a response with an unrecognized status code.
```

因此，您的客户端代码现在不必处理每个状态代码，只需处理状态代码的类(第一个数字)。好吧，但是你怎么处理第一个数字？

```
For example, if an unrecognized **status code of 471** is received by a client, the client can assume that there was something wrong with its request and treat the response **as if it had received a 400** (Bad
Request) status code.  The response message will usually contain a
representation that explains the status.
```

这是相关的线索，我已经标出来了。第一个是[没有在互联网号码分配机构(IANA)注册的 471 状态码](https://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml)。因此，该规范并没有提到“标准”状态代码。它继续说，如果一个状态代码是不可识别的，那么处理它就像一个 400 状态代码。

这里的含义是，您的 400 状态代码处理逻辑与用于未识别状态代码的*逻辑相同。因此，客户端的 400 状态代码处理程序逻辑是您的*后备、通用、通用处理程序。* **发生的任何** 4xx 错误都应该能够被您的 400 状态代码处理逻辑处理(即使不太理想)。*

# 那么，我可以定义我自己的 4xx 状态代码吗？

你*可以，*但是这样做应该有理由。不用说，所有的错误状态，不管是自定义的还是其他的，都应该**记录在 API 中**。

使用非标准代码的唯一好理由([并且有很多这样的代码](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#Unofficial_codes))，是因为你希望它的处理方式与 400 错误不同。换句话说，您正在使用一个特殊的状态代码(希望如此)来触发客户端中更好的处理行为。

## 如果我后来发现我的自定义状态代码与其他地方定义的其他自定义状态代码冲突，该怎么办？

**您**定义您的服务器状态代码的含义，并且您**为您支持的每个请求记录它们**。你永远不要让你的服务代理另一个服务的状态码！那就糟了。如果您需要将信息从出于其他原因使用状态代码的另一个服务中继到客户端，那么在错误消息中传递该信息(使用任何适当的记录状态代码)，或者记录它。

# 选项#3: 200 OK with error body 怎么样？

我给你我的看法，基于个人的挫败感:

请求**真的**可以吗？如果不是，为什么你说它是通过传回 200 OK，然后，在响应的主体中，说它不是？你这样做自相矛盾，让客户做额外的工作。

另一个问题是:你的错误响应的**形状**是什么？是简单的字符串吗？一个物体？期望客户端有自定义代码来解析和显示你的错误体，以及 B 的、C 的和 D 的服务的错误体是合理的吗？这不是不规范吗？你不使用 4xx 代码来坚持标准(就像你解释的那样)的全部意义不就在于此吗？

# 在大多数情况下，400 英镑是可以的

400 状态代码处理程序是后备处理程序。按照标准，它必须处理其他地方没有处理的所有状态代码。这些可能是注册的状态码，或自定义。

一个例外可能是优先顺序。通常的与数据相关的 400 状态可能是由于数据验证错误，被服务器捕获，但被客户端错过。需要修理的东西，但可能不紧急。另一方面，服务器上可能会出现一些情况(例如，SQL 语法错误)，需要数据库管理员立即注意。发送到客户端并由客户端处理的更具体的状态代码可能会提醒用户呼叫帮助台(或者让服务器发送 5xx 代码)。

请记住，标准一直在更新和完善，因此不时阅读它们会有所帮助，不要依赖他人(可能已经过时)的解释。这也适用于这里所说的内容。