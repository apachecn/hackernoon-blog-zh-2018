# 使用 Go 将密码和机密从您的日志中删除

> 原文：<https://medium.com/hackernoon/keep-passwords-and-secrets-out-of-your-logs-with-go-a2294a9546ce>

当使用分布式系统时，通常日志越多越好——复杂之处在于开放可见性，同时保守敏感信息的私密性和安全性。这并不容易，只要问问 [GitHub](https://hackernoon.com/tagged/github) 和 Twitter 最近谁发现自己在记录原始密码。

GitHub 和 Twitter 事件的细节没有公开，但我猜是一个[工程师](https://hackernoon.com/tagged/engineer)在没有意识到后果的情况下改变了日志级别配置或记录了请求体。

事实是，这就是工作的现实——在这样复杂的系统中，没有人能够知道每一个变化的后果，尤其是在技术人员流动速度如此之快的情况下。如果我们要解决这样的问题，我们需要更深入。

墨菲定律引发了这样的问题:任何可能出错的事情都会出错——如果你留下一个陷阱，就会有人走进去。我们不要那样做。在写 Go 的时候，我会做以下事情来避免这样的陷阱，并防止秘密被记录。

假设您有一个注册请求，如下所示:

```
type CreateUserRequest struct {
    Credentials Credentials `json:”credentials”`
}type Credentials struct {
    Email string `json:”email”`
    Password string `json:”password”`
}
```

假设您正在登录 JSON 进行 Elasticsearch:

```
// logger we’re using
logger := log.NewJSONLogger(os.Stdout)// request we decoded
request := CreateUserRequest{
    Credentials: Credentials{
        Email: “[bilbro@theshire.net](mailto:bilbro@theshire.net)”,
        Password: “theonering”,
    },
}// our call to log the request
logger.Log(“request”, request)// the output
{“request”:{“credentials” {“email”:”[bilbro@theshire.net](mailto:bilbro@theshire.net)”,”password”:”theonering”}}}
```

结果是用户的密码被记录。不太好。

为了解决这个问题，我们将实现自己的 MarshalJSON 来编辑用户的密码。

```
func (co Credentials) MarshalJSON() ([]byte, error) {
    type credentials Credentials
    cn := credentials(co)
    cn.Password = "[REDACTED]"
    return json.Marshal((*credentials)(&cn))
}
```

现在，当我们记录相同的请求时，用户的密码不会被记录:

```
// the output
{“request”:{“credentials”:{“email”:”[bilbro@theshire.net](mailto:bilbro@theshire.net)”,”password”:”[REDACTED]”}}}
```

现在，无论是谁登录，也无论是在何时何地登录，都不可能记录用户的密码——至少如果您使用这个实现来记录 JSON。如果您以另一种格式登录，同样的技术也适用，并且应该有相应的封送方法供您实现。

–

请向 [@travisjeffery](https://twitter.com/travisjeffery) 问好。

击中👏如果你觉得这有用，请分享。

感谢阅读。