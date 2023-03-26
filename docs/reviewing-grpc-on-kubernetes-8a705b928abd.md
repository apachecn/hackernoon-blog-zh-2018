# 在 Kubernetes 上查看 gRPC

> 原文：<https://medium.com/hackernoon/reviewing-grpc-on-kubernetes-8a705b928abd>

![](img/efff0197a691f2c43c3d7b7a16528045.png)

*TL；DR:在集群内部使用 gRPC 和 Kubernetes 非常简单。公开 gRPC 服务集群——对外不太公开。也许一个好的实践是:在集群内部使用 gRPC，并使用 HTTP 和/或 WebSockets 提供公共接口？*

S 起点是一个简单的名为[的 gRPC 服务器示例，还有另一个 gRPC echo 服务器(YAGES)](https://github.com/mhausenblas/yages) 。这个 YAGES 示例展示了如何使用 Go 和 Kubernetes 开发和部署 gRPC 服务。当然[你可以使用](https://github.com/mhausenblas/yages#develop)你最喜欢的[编程语言](https://hackernoon.com/tagged/programming)(只要 gRPC[支持](https://grpc.io/docs/))而不是去实现服务。现在，在[将](https://github.com/mhausenblas/yages#as-an-kubernetes-app)gRPC 服务部署为 Kubernetes 应用程序之后，从集群内部访问 gRPC 服务就足够简单了，例如，[使用安装了](https://github.com/mhausenblas/yages#from-inside-the-cluster) [grpcurl](https://github.com/fullstorydev/grpcurl) 的 jump pod :

```
$ grpcurl --plaintext yages.grpc-demo:9000 yages.Echo.Ping
{
  "text": "pong"
}
```

好吧，酷。如果我想从群集外部访问它呢？嗯，让我想想…

gRPC 博客很好地介绍了文章中的主题 [gRPC 负载平衡](https://grpc.io/blog/loadbalancing)讨论了选项，文档中有一些[相关信息](https://github.com/grpc/grpc/blob/master/doc/load-balancing.md)。关于这个主题的精彩介绍，请参见汤姆·威尔基的[演讲](https://www.slideshare.net/kausalco/grpc-kubernetes)。

配备了基本的我开始得到一个设置工作的重点是 UX 和易用性。理想情况下，它可以开箱即用，或者至少只需最少的配置工作。我看了几个负载平衡器/反向代理/服务网格选项: [NGINX](https://hackernoon.com/tagged/nginx) ，三个基于特使的解决方案(Ambassador、Contour 和 Istio)，Linkerd 和 trfik。

以下是我的发现…

## 大使

Datawire 基于特使的 API 网关[大使](https://www.getambassador.io/)拥有一流的 [gRPC 支持](https://www.getambassador.io/user-guide/grpc)，我们也在[kube flow](https://github.com/kubeflow/kubeflow/issues/154)中使用它。UX 很好，唯一需要注意的是它需要一个集群角色和各自的绑定。我还不知道如何在名称空间级别上使用它，也就是说，在我没有权限创建集群范围的资源的环境中。

## 轮廓

Heptio 的[轮廓](https://github.com/heptio/contour)是使用 Envoy 的 Kubernetes 入口控制器。似乎 gRPC 支持正在积极地工作，我发现了一个[问题](https://github.com/heptio/contour/issues/15)需要解决，以使其工作。

## 林克尔德

Linkerd 是 CNCF 的一个初始项目，最初由服务网格先锋浮力开发。它[支持 gRPC](https://linkerd.io/features/grpc/) 开箱即用。有很多关于这个主题的很好的博客帖子——例如 2017 年 4 月的[Kubernetes 第九部分的服务网格:gRPC 的乐趣和利润](https://buoyant.io/2017/04/19/a-service-mesh-for-kubernetes-part-ix-grpc-for-fun-and-profit/)和 2018 年 2 月的[与 GRPC Kubernetes 建立可扩展的微服务&Linkerd](/@riknauta/building-scalable-micro-services-with-kubernetes-grpc-linkerd-7ccafd179599)—但我还没有尝试过。

## trfik

trfik 是一个 HTTP 反向代理和负载均衡器，内置了对 gRPC 的支持。也在我的工作清单上。

## NGINX

我觉得公平的说 NGINX 不需要介绍。在许多其他事情中，你可以使用它作为一个 [Kubernetes 入口控制器](https://github.com/kubernetes/ingress-nginx)。它现在还支持 [gRPC](https://www.nginx.com/blog/nginx-1-13-10-grpc/) (自 2018 年 3 月中旬 1.13.10 起)。我试着在 Minikube 上打补丁(它仍然使用控制器的 v0.9.0 版),但到目前为止没有运气。我也将在 [GKE](https://cloud.google.com/endpoints/docs/grpc/get-started-grpc-kubernetes-engine) 的背景下尝试一下。

## 伊斯迪奥

最后 [Istio](https://istio.io/) 是云原生生态系统的另一个项目，它是一个服务网格，默认情况下将 Envoy 用于数据平面。似乎社区正在积极[致力于 gRPC 支持](https://github.com/istio/issues/issues/87)。一旦有货我就去试试。

为了完整起见，我还会提到，显然也可以使用 HAProxy 来处理 gRPC，但是我没有仔细研究过。此外，还有[sercand/kubersolver](https://github.com/sercand/kuberesolver)，这是一个客户端负载平衡器/Kubernetes 名称解析器，对于集群内部用例来说，这听起来是一个不错的选择。

请注意，我的设置是针对 Minikube (v0.25)和 GKE 的 *Kubernetes 1.9* ，我唯一的硬性要求是它必须与启用 RBAC 的*一起工作。*

结论:我还没能成功地使用上面的任何一个选项向外界展示我的小 YAGES 示例。到目前为止，我和大使走得最远，但还没能和`grpcurl`走到一起。gRPC 要求 HTTP/2 似乎是挑战的一部分，RBAC 是另一个挑战。

我毫不怀疑，鉴于生态系统发展如此之快，围绕配置和使用 gRPC 面向集群外部客户端的 UX/DX 将会有很大改进。根据我的经验，现在还为时尚早。有可能，但就 UX/DX 而言并不容易。

> 也许向外界公开 gRPC 本身并不是一个好主意？也就是说，也许我们可以得到的好的实践是:在集群中使用 gRPC 和 HTTP 和/或 WebSockets 与集群外部的客户端进行通信？

我很乐意在这里了解您在这一领域的经历以及任何想法或建议！

更新 2018-03-27:自从发布这个帖子以来，我收到了大量有价值的反馈，并了解了更多的事情。所以，感谢每一个花时间阅读和跟进的人，非常感谢！现在我想补充两点:

*   Linkerd 的制造商在市场上推出了一个名为[导管](https://conduit.io/)的新项目和产品。现在，我意识到了这一点，并亲自尝试了一下(超级容易使用，例如查看[的这个视频](https://www.youtube.com/watch?v=--b641eQ7U8)，但是到目前为止还没有找到任何正在进行的将 gRPC 服务向外界公开的工作。原来我亲爱的朋友[托马斯 R](https://github.com/grampelberg) 现在在浮力公司工作，谢天谢地，他给我指出了一个抓住其本质的[问题。谢谢！](https://github.com/runconduit/conduit/issues/629)
*   另一个超级有用的输入来自 Twitter:[Andrew Webber](https://twitter.com/andrewvwebber/)[指出](https://twitter.com/andrewvwebber/status/978392651471970306)还有一个非常酷的项目可以使用: [nghttpx 入口控制器](https://github.com/zlabjp/nghttpx-ingress-lb) —我也会检查这个，谢谢！
*   最后，我的同事 [Christian Posta](https://twitter.com/christianposta) ，我们的微服务和 Istio 主题专家，让我意识到 OpenShift 中的[自定义路线](https://blog.zhaw.ch/icclab/openshift-custom-router-with-tcpsni-support/)，利用 TCP/SNI。很好，干杯，又一个我要做的事情:)