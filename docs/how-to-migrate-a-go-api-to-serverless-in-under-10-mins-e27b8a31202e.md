# 如何将 Go API 迁移到无服务器(不到 10 分钟)

> 原文：<https://medium.com/hackernoon/how-to-migrate-a-go-api-to-serverless-in-under-10-mins-e27b8a31202e>

我将带您逐步完成将现有 Go [API](https://hackernoon.com/tagged/api) 转换为[无服务器](https://hackernoon.com/tagged/serverless)并将其部署到带有 [AWS 无服务器应用程序模型(SAM)](https://aws.amazon.com/about-aws/whats-new/2016/11/introducing-the-aws-serverless-application-model/) 的 AWS Lambda & API 网关的过程。整个过程应该不到 10 分钟。我们开始吧！

# 1.设置

我们的示例 API 使用了`[HttpRouter](https://github.com/julienschmidt/httprouter)` [包](https://github.com/julienschmidt/httprouter)，所以让我们先安装它。

```
$ go get github.com/julienschmidt/httprouter
```

我们定义了一个 HTTP 处理程序，它将返回一个 200 HTTP 响应，其主体为`ok`。

```
# handlers.go
package mainimport "net/http"func HealthHandler(w http.ResponseWriter, r *http.Request) {
    w.WriteHeader(http.StatusOK)
    w.Write([]byte("ok"))
}
```

我们应用程序的入口点是`main`函数，它将`HealthHandler`连接到`/healthz`路由，并监听端口`8080`上的 HTTP 请求。

```
# main.go
package mainimport (
    "fmt"
    "log"
    "net/http"
    "github.com/julienschmidt/httprouter"
)const (
    serverPort = 8000
)func main() {
    router := httprouter.New()
    router.Handler("GET", "/healthz", http.HandlerFunc(goserverlessapi.HealthHandler)) fmt.Printf("Server listening on port: %d\n", serverPort)
    log.Fatal(http.ListenAndServe(fmt.Sprintf(":%d", serverPort), router), nil)
}
```

让我们在本地构建并运行它，以检查一切是否正常。

```
$ go build -o go-serverless-api && ./go-serverless-api
Server listening on port: 8080
```

# 2.将应用程序代码转换为无服务器

为了部署到无服务器后端，我们需要能够处理来自 AWS Lambda 的请求。Lambda 函数的签名与常规 HTTP 处理程序不同。想象一下，如果我们的应用程序中有数百个而不是一个。我们将不得不手动更新所有功能并重新编写测试，这样做我们将丧失部署到非无服务器后端的能力。

有一个解决方案可以避免刚才列出的所有问题。我们将为 AWS Lambda 创建一个修改的入口点。使用`[gateway](https://github.com/apex/gateway)` [包](https://github.com/apex/gateway)，我们将把`net/http`的`ListenAndServe`换成`gateway.ListenAndServe`，这将把 AWS Lambda 提供的有效载荷转换成 HTTP 处理程序接受的`[*http.Request](https://godoc.org/net/http#Request)`类型。

为了实现第二个入口点，我们需要重新组织我们的项目。我们将为 AWS Lambda one 创建一个目录，并将我们原来的入口点也移到一个新文件夹中。

```
# original entrypoint moved to new location
$ mkdir -p cmd/go-serverless-api# new entrypoint for lambda
$ mkdir -p cmd/go-serverless-api-lambda
```

我们将把`main.go`文件复制到每个新目录中，然后从我们项目的根目录中删除它。

```
$ cp main.go cmd/go-serverless-api
$ cp main.go cmd/go-serverless-api-lambda
$ rm main.go
```

我们项目根目录中的包不再是`main`包(Go 用来运行应用程序的包)。我们将把它重命名为`goserverlessapi`,这样我们就可以把它作为一个库导入到我们的新入口点，这两个入口点都将成为`main`包。

```
$ grep -l 'package main' *.go | xargs sed -i 's/package main/package goserverlessapi/g'
```

在简单地查找并替换项目根目录下的 Go 文件后，你的`handlers.go`应该是这样的。

```
# handlers.go
package goserverlessapiimport "net/http"func HealthHandler(w http.ResponseWriter, r *http.Request) {
    w.WriteHeader(http.StatusOK)
    w.Write([]byte("ok"))
}
```

现在我们需要更新我们的原始入口点来导入`goserverlessapi`包并访问`HealthHandler`函数作为导出。注意，您需要修改`goserverlessapi`包的导入路径，以匹配您的项目根目录在`$GOPATH`中的位置。本教程中使用的 github 上的[示例应用程序的正确路径是`github.com/techjacker/go-serverless-api`。](https://github.com/techjacker/go-serverless-api)

```
# cmd/go-serverless-api/main.go
package mainimport (
    "fmt"
    "log"
    "net/http""github.com/julienschmidt/httprouter"
    "github.com/techjacker/go-serverless-api"
)const (
    serverPort = 8080
)func main() {
    router := httprouter.New()
    router.Handler("GET", "/healthz", http.HandlerFunc(goserverlessapi.HealthHandler))

    fmt.Printf("Server listening on port: %d\n", serverPort)
    log.Fatal(http.ListenAndServe(fmt.Sprintf(":%d", serverPort), router), nil)
}
```

接下来更新 AWS Lambda 入口点。我们将使用`[gateway](https://github.com/apex/gateway)`包，它是在 AWS Lambda & API 网关上运行时 Go net/http 的替代包，所以让我们先安装它。

```
$ go get github.com/apex/gateway
```

然后将 AWS Lambda 入口点文件更新为以下内容。

```
# cmd/go-serverless-api-lambda/main.go
package mainimport (
    "log"
    "net/http" "github.com/apex/gateway"
    "github.com/julienschmidt/httprouter"
    "github.com/techjacker/go-serverless-api"
)func main() {
    router := httprouter.New()
    router.Handler("GET", "/healthz", http.HandlerFunc(goserverlessapi.HealthHandler))
    log.Fatal(gateway.ListenAndServe("", router), nil)
}
```

我们已经将`gateway`添加到我们的导入中，并将其替换为`http.ListenAndServe`。端口值在 Lambda 上下文中是多余的，`gateway`包丢弃了它，因此我们可以安全地删除端口常量并用空字符串替换它。此外，我们还包含了来自`go-serverless-api`包的`HealthHandler`(这个包在项目的根目录中，曾经是我们的主包)作为`/healthz`路径的处理程序。

# 3.在本地构建和运行

让我们再次构建并运行我们最初的 HTTP API。

```
$ go build \
    -o go-serverless-api \
    ./cmd/go-serverless-api$ ./go-serverless-api
Server listening on port: 8080
```

打开另一个终端窗口并进行查询。

```
$ curl -s http://localhost:8080/healthz ok
```

一切都还能用！

让我们对 AWS Lambda 版本做同样的事情。

```
$ go build \
    -o go-serverless-api-lambda \
    ./cmd/go-serverless-api-lambda$ ./go-serverless-api-lambda
```

再次，打开一个新的终端并查询它。

```
$ curl -s [http://localhost:8080/healthz](http://localhost:8080/healthz)
http: error: ConnectionError: HTTPConnectionPool(host='localhost', port=8080): Max retries exceeded with url: /healthz (Caused by NewConnectionError(': Failed to establish a new connection: [Errno 111] Connection refused',)) while doing GET request to URL: [http://localhost:8080/healthz](http://localhost:8080/healthz)
```

不要担心，这个错误是意料之中的，因为 AWS Lambda 版本没有监听 HTTP 连接，而是期望获得一个`[APIGatewayProxyRequest](https://godoc.org/github.com/aws/aws-lambda-go/events#APIGatewayProxyRequest)`类型。

# 4.AWS Lambda 的程序包应用程序

为 AWS Lambda 使用的操作系统 linux 构建二进制文件。

```
$ GOOS=linux go build \
    -o go-serverless-api-lambda \
    ./cmd/go-serverless-api-lambda
```

AWS Lambda 要求将函数代码捆绑到一个 zip 文件中，所以让我们继续压缩二进制文件。

```
$ zip go-serverless-api-lambda.zip go-serverless-api-lambda
```

我们将在最后一步部署中使用创建的`go-serverless-api-lambda.zip`。

# 5.部署

我看过一些使用 [AWS CLI 工具](https://aws.amazon.com/cli/)的教程，使用特别命令部署到 AWS Lambda。这绝对是错误的做法！您应该像应用程序的其他方面一样自动化您的基础设施。部署的行业标准要么是[地形](https://www.terraform.io/)要么是 [AWS 云形成](https://aws.amazon.com/cloudformation/)。两者都为您提供了一种构建基础设施的声明式方法。您将此配置保存在提交到存储库的 YAML/JSON (Cloudformation)或 HCL (Terraform)文件中。这样做的问题是，您必须处理堆栈的所有底层细节。如果我们能够用不到 20 行代码而不是数百行代码来描述我们的基础设施，那就太好了。进入 [AWS 无服务器应用模型(SAM)](https://aws.amazon.com/about-aws/whats-new/2016/11/introducing-the-aws-serverless-application-model/) 。

## AWS 无服务器应用程序模型(SAM)

SAM 是由 Amazon 倡导的新标准，旨在使部署无服务器基础设施更简单、更简洁。SAM 是一个开源规范— [参见完整的参考指南](https://github.com/awslabs/serverless-application-model/blob/develop/versions/2016-10-31.md)。希望其他云供应商将来会采用这种技术，这样就有可能用一个配置无缝地部署到多个云中。

## 使用 AWS SAM 部署

将以下 YAML 文件添加到项目的根目录中。这是一个配置 AWS Lambda 函数的 SAM 模板，该函数运行您的 Go 应用程序，并将其部署在 AWS API Gateway 提供的 HTTP 接口后面。

```
# template.yaml
AWSTemplateFormatVersion: '2010-09-09'
Transform: 'AWS::Serverless-2016-10-31'
Description: 'Boilerplate Go API.'
Resources:
  GoAPI:
    Type: AWS::Serverless::Function
    Properties:
      Handler: go-serverless-api-lambda
      Runtime: go1.x
      CodeUri: ./go-serverless-api-lambda.zip
      Events:
        Request:
          Type: Api
          Properties:
            Method: ANY
            Path: /{proxy+}
```

第`Type: AWS::Serverless::Function`行创建了一个 Lambda 函数，由我们之前构建的二进制文件处理(`Handler: go-serverless-api-lambda`)。这个 Lambda 函数可以响应由其他 AWS 服务触发的任意数量的事件，例如由 AWS S3 和 Kinesis 触发的事件。文件包含[事件源](https://github.com/awslabs/serverless-application-model/blob/develop/versions/2016-10-31.md#event-source-types)的完整列表。在我们的例子中，我们希望它通过 API 网关响应 HTTP 请求，因此我们将事件设置为`Type: Api`。SAM 隐式地为我们创建了一个 API 网关，作为其中的一部分，然后我们通过将方法设置为`ANY`，对其进行配置以响应任何类型的 HTTP 请求。我们告诉我们的 API 通过添加`Path: /{proxy+}`来处理下面包括根的所有路径。

我们仍然需要将包含 Go 二进制文件的 zip 文件上传到 AWS S3。确保您已经创建了一个 S3 桶，准备接收我们的 zip。这是您希望手动执行的一次性操作。

```
$ aws s3 mb s3://my-bucket
```

以下命令将上传 zip 文件并创建打包的 SAM 模板。

```
$ aws cloudformation package \
    --template-file template.yaml \
    --s3-bucket my-bucket \
    --output-template-file packaged-template.yaml
```

您现在应该有一个指向上传的 zip 文件的`packaged-template.yaml`文件。

```
# packaged-template.yaml
AWSTemplateFormatVersion: '2010-09-09'
Transform: 'AWS::Serverless-2016-10-31'
Description: 'Boilerplate Go API.'
Resources:
  GoAPI:
    Type: AWS::Serverless::Function
    Properties:
      Handler: go-serverless-api-lambda
      Runtime: go1.x
      CodeUri: s3://my-bucket/8982639e71e0d433cd99f9fa4207ecbe
      Events:
        Request:
          Type: Api
          Properties:
            Method: ANY
            Path: /{proxy+}
```

现在让我们使用这个新的打包模板进行部署。

```
$ aws cloudformation deploy \
    --template-file packaged-template.yaml \
    --stack-name go-serverless-api-stack \
    --capabilities CAPABILITY_IAM
```

cloudformation 需要`--capabilities CAPABILITY_IAM`标志来为您创建堆栈，因为这将涉及修改 IAM 权限——AWS 要求您明确地将它设置为一种安全措施。在幕后，SAM 模板被编译成一个长达数百行的常规 cloudformation 模板。所有这些对用户来说都是完全隐藏的(尽管如果愿意，您可以自由地检查编译好的 cloudformation 模板)。

# 设置 6。测试部署的无服务器 API

为了发现我们部署的 API 的端点，我们需要找出 API 网关 REST `id`。

```
$ aws apigateway get-rest-apis
{
    "items": [
        {
            "id": "0qu18x8pyd",
            "name": "go-serverless-api-stack",
            "createdDate": 1523987269,
            "version": "1.0",
            "apiKeySource": "HEADER",
            "endpointConfiguration": {
                "types": [
                    "EDGE"
                ]
            }
        }
    ]
}
```

AWS API 网关地址采用以下格式。

```
https://<api-rest-id>.execute-api.<your-aws-region>.amazonaws.com/<api-stage>
```

一个[阶段](https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-stages.html)是亚马逊对部署的术语。SAM 为您创建了两个不同的阶段:`Stage`和`Prod`。请注意标题的大小写，它也在 URL 中使用！我想 AWS 忘记了每个人都称他们的测试环境为`Staging`而不是`Stage`，但是没关系！

萨姆在以下地点为我们设置了终端。

```
https://0qu18x8pyd.execute-api.eu-west-1.amazonaws.com/Stage [https://0qu18x8pyd.execute-api.eu-west-1.amazonaws.com/Prod](https://0qu18x8pyd.execute-api.eu-west-1.amazonaws.com/Prod)
```

让我们调用我们的 API。

```
$ curl -s https://0qu18x8pyd.execute-api.eu-west-1.amazonaws.com/Prod {"message":"Missing Authentication Token"}
```

不用慌！当你向根资源发出请求而没有为它定义一个处理程序时，这就是标准 API 网关错误。我们定义的唯一处理程序是`/healthz`，所以让我们试试。

```
$ curl -s https://0qu18x8pyd.execute-api.eu-west-1.amazonaws.com/Prod/healthz ok
```

瞧啊。我们的 API 现在由一个无服务器的后端提供支持。

本教程的完整代码可在 [github](https://github.com/techjacker/go-serverless-api) 上获得。

我将很快在 Go 和无服务器上发布更多文章。在推特[上关注我](https://twitter.com/agriffonline)以便在我关注时得到通知。

*最初发表于*[*andrewgriffithsonline.com*](https://andrewgriffithsonline.com/blog/180412-migrate-go-api-to-serverless-under-10-mins/)*。*