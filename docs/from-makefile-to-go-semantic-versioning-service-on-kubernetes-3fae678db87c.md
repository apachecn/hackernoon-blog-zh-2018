# 从 Makefile 到 Go 语义版本服务

> 原文：<https://medium.com/hackernoon/from-makefile-to-go-semantic-versioning-service-on-kubernetes-3fae678db87c>

![](img/8c882a5c83ddf87d8728c03497900185.png)

Photo by Alexander Andrews ([https://unsplash.com/@alex_andrews](https://unsplash.com/@alex_andrews))

大约一周前，我试图弄清楚是否有一个好的 Makefile 可以用于 go 项目。

Huge number of followers I have couldn’t help me much …

我能够找到一堆资源，从非常简单的 Makefiles 到非常高级的东西，包括各种任务，如运行测试、林挺、go 格式等。

当我浏览所有这些资源时，我偶然发现了来自[杰斯·弗雷泽勒](https://github.com/jessfraz)的 Github repos 的[makefile](https://github.com/genuinetools/img/blob/master/Makefile)中的一个。除了我在这里考虑的“标准”目标(构建、lint、格式)，她还使用了一个增加项目/包版本的`bump-version`目标。为了增加版本，Jess 使用了她创建的 sembump 工具，我在想，如果有一个我可以使用的服务就好了，它会做几乎相同的事情——你可以为它提供一个版本，即你想要提升的版本部分(主要版本、次要版本或补丁),服务会用一个新版本来响应。

一开始是寻找 Go 的首发`Makefile`，最后变成了一个简单的碰撞 semversion 服务。我创建的服务在 [https://semver.xyz](https://semver.xyz) 启动并运行，你可以使用它，也可以去[https://bump.semver.xyz/minor?version=1.0.0](https://bump.semver.xyz/minor?version=1.0.0)试用它

我还将整个项目上传到了 GitHub，以防你想继续下去并实际查看代码。

由于其他人已经做了大量的工作，实际代码并不多——只有几行[代码](https://github.com/peterj/semver/blob/master/pkg/semver/semver.go#L24)。我正在使用一个名为 [semver](https://github.com/blang/semver) 的现有库，由 [Benedikt Lang](https://github.com/blang) 构建。这个库完成了解析版本的最难的部分，我只取了你想要增加的版本的一部分。除了这个库之外，我还使用了 [Gorilla](https://github.com/gorilla/mux) 来公开一个端点，您可以调用它来修改它接收到的版本。

编码部分基本完成后，我开始思考将该服务快速部署到 Kubernetes 集群的最简单、最快的方法。

此时，一个巨大平板卡车上的玩具卡车的图像浮现在脑海中——在集群上运行这么小的东西可能很浪费，但是我已经有一个集群在运行，所以我可以原谅:)

无论如何，为了将东西部署到集群，我从 Kubernetes 部署和服务开始—类似这样:

部署这是一个运行`kubectl apply -f filename.yaml`的过程，你很好。但是，一旦您对代码进行了一些更改，您就必须重新构建 Docker 映像，将其推送到注册表中，然后重新启动 Pod，以便获得新的映像。这意味着我可能需要一个为我构建和推送图像的`Makefile`目标，大致如下所示(我确实参数化了`Docker`文件名和图像名):

因此，现在我能够构建我的图像并将其推送到注册表。下一步是用新的映像名称修改 Kubernetes `yaml`，然后部署它。起初，我想在`yaml`文件中定义一些变量(例如`%IMAGE_NAME%`、`%VERSION%`、…)并用`sed`将它们替换为实际值。听起来像是一个好方法，但后来我想起我可以使用 [Helm](https://github.com/kubernetes/helm) 并对部署进行模板化——从长远来看这将使事情变得更容易，因为我将能够升级版本并以这种方式进行一些跟踪。

安装 Helm 后，我开始创建一个图表(`helm create semver-svc`)。幸运的是，空的 chart Helm 几乎包含了我需要的所有东西，所以这只是更新`values.yaml`文件中的值的问题。一旦我通过运行`helm install`进行了测试，我就回到了`Makefile`并创建了另一组安装和升级头盔版本的目标:

我为该服务定义了一个`install`和`upgrade`目标——首次尝试创建 Helm 版本时使用 install，升级用于该版本的后续更新。

随着大多数目标的参数化，我能够定义 uber `upgrade`目标，该目标修改版本，构建并发布 Docker 映像，最后调用服务上的 upgrade 来实际升级已经在 Kubernetes 集群中运行的东西。

`Makefile`的`bump-version`部分与 Jess 在她的 [Makefile](https://github.com/genuinetools/img/blob/master/Makefile) 中的[部分非常相似，不同之处在于，一旦我部署了我的服务，我就能够像这样调用服务来升级版本:](https://github.com/jessfraz)

我还想通过一个定制的域( [https://www.semver.xyz](https://www.semver.xyz) )来公开这个服务，所有这些都使用 SSL 和其他东西！设置这个需要安装`[kube-lego](https://github.com/jetstack/kube-lego)`(用于从 Letsencrypt 自动获得 SSL 证书)和一个 Nginx 入口(向外界公开服务)，如下所示:

```
helm install stable/kube-lego \
  --set config.LEGO_EMAIL=[YOUR_EMAIL] \
  --set config.LEGO_URL=https://acme-v01.api.letsencrypt.org/directoryhelm install stable/nginx-ingress --namespace [NAMESPACE]
```

因为为服务创建的默认舵图已经包含了一个`ingress.yaml`模板，所以只需要为它设置值:

最后，我必须去我的域名注册商(name.com)那里，把我得到的酷域名和 Nginx 入口控制器的 IP 地址连接起来(你基本上需要为你的域名创建一个指向 IP 地址的 A 记录)。

搞定了。我现在可以通过 https 公开访问这项服务了！(后来我还添加了一个简单的静态 HTML 页面，基本上使用了相同的目标和一个新的舵图来部署它)。

# 结论

实际服务的实现非常简单明了。我花了大部分时间计算出`Makefile`的目标，创建掌舵图，为我的目标取名字(安装？升级？部署？释放？).

下一步，我会考虑尝试用 Makefile/Helm charts 创建一个好的基本模板，我可以用它来开发任何新的服务/项目。

# 感谢阅读！

欢迎对本文的任何反馈！你也可以在 [Twitter](http://twitter.com/pjausovec) 和 [GitHub](http://github.com/peterj) 上关注我。如果你喜欢这个并且想在其他部分准备好的时候得到通知，你应该订阅[我的简讯](https://tinyletter.com/pjausovec)！