# Angular 7 — HTTP 和身份验证

> 原文：<https://medium.com/hackernoon/angular-7-http-exploration-ffadddc8d745>

![](img/0d450e371bb2293b9dae59309c87f6b8.png)

很长一段时间没有发帖，空闲时间和无聊激发了我的灵感，所以让我们分享一点我在过去几个月里一直在学习的东西。这个帖子将是关于 *Angular 7* 中的 [Http](https://hackernoon.com/tagged/http) 请求。

*Angular 7* 应用程序中的 Http 请求与我们习惯的第一版 Angular 略有不同，一个根本的区别是 *Angular 7* 中的 *HttpClient* 返回可观察的对象。

公平地说，大多数 *Angular 7* 看起来与 *Angular 1.x* 完全不同，Http [API](https://hackernoon.com/tagged/api) 也不例外。 *Angular 1.x* 为我们提供的 *$http* 服务在大多数情况下都非常有效，并且易于使用。在 Angular 7 *中，HttpClient* 要求我们学习一些新概念，包括如何处理*可观察对象。*

# Angular 1.x $http 和 Angular 7 HttpClient 的区别

在 *Angular 7* 中，Http 实现提供了一种简单的处理请求的方式，但是有一些重要的区别。

首先， *Angular 7* 中的 HTTP 调用默认通过 *RxJS* 返回对象可观察，而 *Angular 1.x* 中的 *$http* 返回承诺。当处理来自 Http 请求的响应时，可观察流的使用为我们提供了更大的灵活性。

例如，如何修复失败的 Http 请求是自动转发的，这对于用户网络通信缓慢或断断续续的情况很有用。

我们将在本文后面看到如何在我们的 observables 中实现 *RxJS* 操作符。

在 *Angular 7、* *中，HttpClient* 已经被用作一个可注入类，就像其他类一样，当我们想要在我们的组件中使用它时，我们会导入它，并且还附带了一组 Http 可注入拦截器，它们是通过 HTTP_INTERCEPTORS 导入的。

在 *Angular 1.x* 中，我们将通过为我们的 *$http* 选项提供 *transformRequest* 函数或 *transformResponse* 来实现这一点，我们可以在应用程序配置中全局转换请求。

在 *Angular 7* 中，我们将实现 *HttpInterceptor* 接口。

# 探索 HttpClient

在本文的过程中，我们将看到如何处理 POST 和 GET 请求，这将要求我们检索和存储一个 JWT(令牌)，因此我们将实现一个基本的身份验证。

## 获取请求

让我们从一个简单的 GET 请求开始，该请求针对 ***/api/clients*** 路由，不需要认证。

getAll 方法看起来很熟悉——我们发出一个 http.get 请求，将一个 URL 作为参数传递。记住，在 1.x Angular $http 案例中，这是我们消费我们开始的承诺的地方，并且将使用。然后，但正如你在这里看到的，我们正在做一些相当不同的事情。

如前所述， *Angular 7* 中的 Http 调用返回可观察对象，所以我们需要使用 *RxJS* 方法对它们进行操作。

## 发布请求

我们的身份验证方法需要向后端发出 POST 请求，并指定内容类型，这样我们就可以提交凭据。为此，我们将使用标题。

身份验证方法接受提交表单时从视图传输的数据。我们正在获取他的用户名和密码，并以服务器等待的方式制作一串凭证。

请求发生了，从这里返回的可观察对象以我们在 GET 请求中看到的相同方式被处理。随后，我们将收到的令牌存储在本地存储中。

有关在 *Angular 7* 中实现 Http 请求的更多细节，请访问[完整文档](https://angular.io/guide/http)。

# 结论

与在 *Angular 1.x* 中所做的相比，在 *Angular 7* 中的 Http 请求无疑是一种不同的方法，但是随着这种改变，在能力上有了很大的提高。返回是可观察对象的请求非常好，因为我们可以对它们使用 *RxJS* 操作符，并且返回的流将按照我们喜欢的方式运行。

在本文中，我们已经看到了如何使用 HTTP 让 *GET* 和 *POST* 请求进行身份验证，但是我们也可以使用其他类型的 Http 请求，比如 *PUT、DELETE、*和 *PATCH* 。我们还看到了如何使用 *Headers* 类定制我们发送的消息头。

![](img/75d1b418f18e43379eb833623525f304.png)

好好学习，留下你的评论，下次见！！！

*本帖是* [*葡文版*](/opensanca/angular-2-http-e-autenticação-32c2ed320fa) *的翻译。所以，如果你发现任何错误，请原谅我，并让我知道如何改正。*

谢谢❤

# 参考

 [## 角度文档

### 编辑描述

angular.io](https://angular.io/guide/http) [](http://reactivex.io/) [## ReactiveX

### 编辑描述

reactivex.io](http://reactivex.io/)  [## 安古拉吉斯

### AngularJS 就是 HTML，如果它是为构建网络应用程序而设计的话。带有…的声明性模板

docs.angularjs.org](https://docs.angularjs.org/api/ng/service/$http) [](https://auth0.com/blog/angular-2-series-part-3-using-http/) [## 使用 Angular $Http 和 Angular 2 Http - Angular 2 系列第 3 部分

### 了解 Angular 1.x 中的$http 和 Angular 2 中的 http 的区别。了解如何使用 RxJS 观察仪…

auth0.com](https://auth0.com/blog/angular-2-series-part-3-using-http/)