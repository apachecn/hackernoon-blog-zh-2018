# 10 大 Kubernetes 技巧和窍门

> 原文：<https://medium.com/hackernoon/top-10-kubernetes-tips-and-tricks-27528c2d0222>

在大多数情况下，这些都没有特定的顺序。这些只是我在使用 Kubernetes 一年后所学到的最好的技巧。

# **#1 Bash 为我完成我的 kubectl 命令**

这可能是最简单的做法，但也是使用 Kubernetes 时最有帮助的事情之一。要添加自动完成功能(如果使用 bash ),只需运行以下命令:

```
echo "source <(kubectl completion bash)" >> ~/.bashrc
```

这会将自动完成功能添加到您的。bashrc，这样无论你什么时候打开一个 shell，它都会启用它。我发现自动完成对于像
— *全名称空间*这样的长东西来说是最好的

# **#2 为名称空间添加默认内存限制和 cpu 限制**

人们会犯错，这是常有的事。如果有人编写了一个应用程序，比方说，每秒钟打开一个到数据库的连接，但从不关闭它，那么我们现在在集群上的一个应用程序中有一个内存泄漏。如果它们部署到没有限制设置的群集，可能会使节点崩溃。

为了防止这种情况，Kubernetes 允许在每个名称空间的基础上设置默认限制。要做到这一点，只需为有限范围创建一个 yaml 并将其应用到名称空间即可。下面是一个 yaml 示例:

```
apiVersion: v1  
 kind: LimitRange  
 metadata:  
   name: mem-limit-range  
 spec:  
   limits:  
   - default:  
       memory: 512Mi  
     defaultRequest:  
       memory: 256Mi  
     type: Container
```

用它创建一个 yaml，并将其应用于您想要的任何名称空间。例如名称空间*限制——例如*。在应用它之后，部署到该名称空间的任何没有设置限制的容器都将获得 512Mi 的限制。

# 你能清理一下我的 docker 图片吗

默认情况下，kubelet 已经这样做了。如果在启动 kubelet 时没有设置标志，当 var/lib/docker 达到 90%容量时，它将开始垃圾收集。这一切都很好，但是没有为 inode 阈值设置默认值(在 Kubernetes 1.7 之前)。

您可以让 var/lib/docker 只使用 50%的磁盘空间，但是您的 inodes 可能会被全部使用。这会给你的工人带来很多问题。
如果您运行的是 1.4–1.6 版本的 kubelet，那么您必须为您的 kubelet 添加一个标志。

```
--eviction-hard
=memory.available<100Mi,nodefs.available<10%,nodefs.inodesFree<5%
```

这些是运行 1.7 或更高版本时的默认值。默认情况下，1.6 不监视 inode 的使用情况，所以添加这个标志可以解决这个问题。

# **#4 迷你库…小巧但功能强大，适合本地使用**

Minikube 是让 Kubernetes 集群在本地运行的最简单的方法。只需按照这些说明进行操作。下载所有的东西。

一旦安装好一切，就像运行一样简单:

```
minikube start
```

该命令完成后，您将在本地拥有一个正在运行的 kubernetes 集群。

当您想要在本地构建应用程序并在本地运行它时，技巧就来了。如果您没有运行其他命令，那么当您进行 docker 构建时，映像仍然会在您的计算机上构建。

要让您的 docker 构建将映像推送到本地 kubernetes 集群，您需要用这个命令告诉 docker 机器:

```
eval $(minikube docker-env)
```

这应该让您开始在本地 kubernetes 集群上构建应用程序。

# **#5 不要随便给任何人 kubectl 访问权限**

这可能是显而易见的，但是当多个团队部署到一个集群时……这就是 Kubernetes 的目的，不要只是给每个人一个通用的 kubectl。我的建议是基于名称空间分离团队，然后使用 RBAC 策略只允许访问该名称空间。

您可能会对以 pod 为基础的访问、读取、创建和删除变得非常疯狂，这只是其中的几个例子。但是首先要做的事情之一是只允许管理员访问机密，这将区分谁可以管理集群，谁可以部署到集群。

我希望稍后在另一篇博客中对此进行更深入的探讨。

# **#6 吊舱破坏预算是你的朋友。**

有人如何保证 kubernetes 集群的应用程序不会停机？

pod disruption budget
pod disruption budget
pod disruption budget

集群将被更新。节点会被抽干，东西会被移动，这是既定的。PDB 的(PodDisruptionBudget)应该放在拥有 1 个以上实例的每个部署上。可以使用应用于集群的简单 yaml 来创建 PDB，并使用标签选择器来确定特定 PDB 覆盖的内容。

注意:PDB 只考虑自愿中断，像硬件故障这样的情况不会考虑 PDB。

PDB 的一个例子如下:

```
apiVersion: policy/v1beta1  
 kind: PodDisruptionBudget  
 metadata:  
   name: app-a-pdb  
 spec:  
   minAvailable: 2  
   selector:  
       matchLabels:  
         app: app-a
```

最值得关注的两件事是 matchLabels 和 minAvailable 部分。kubernetes 通过 matchLabels 来查看部署是否与 PDB 相关联。例如:如果我有一个标签为 app: app-a 的部署和一个标签为 app: app-b 的部署，那么示例 PDB 将仅适用于第一个部署。

minAvailable 是 kubernetes 在做类似节点流失的事情时所查看的内容。假设 app-a 正在节点 1 上运行，如果在节点 1 上开始消耗，它只会驱逐 app-a，因为当前至少有 2 个在运行。

这使您可以控制在任何给定时间需要运行多少实例。

# **#7 您的应用程序是否活跃并准备好了？**

在 Kubernetes 中，您可以定义 kubelet 用来确定您的 pod 和应用程序是否健康的探针。

有两种类型来确定这些事情，就绪性探测和活性探测。

准备就绪探测用于确定容器何时准备好接收流量。
活性探测器用于确定容器是否健康或需要重启。

这些配置只需添加到部署 yaml 中，并且可以定制超时、重试和延迟。关于如何使用它们的更深入的解释，请阅读[本](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)。

# **#8 标签太阳底下的一切。**

标签是 Kubernetes 的主要基础之一。它允许对象与其他对象松散耦合，还允许您基于标签进行查询。你甚至可以使用 Kubernetes go 客户端，根据标签观察事件。

使用标签几乎可以做任何事情，但是同一个集群中的多个环境就是一个很好的例子。
假设您为开发和 qa 使用同一个集群。这意味着你可能同时在 *qa* 和 *dev* 中运行 app-a。要实现这一点，最简单的方法是服务对象，一个服务对象在 app: app-a 和 environment: dev 上选择标签，另一个服务对象在 app: app-a 上选择标签，但用 qa 切换 dev。

这为您提供了两个相同的应用程序，每个应用程序有不同的端点，允许同时进行测试。

# **#9 收拾自己的烂摊子。**

Kubernetes 是一个非常非常强大的系统，但像任何系统一样，它最终会陷入困境。kubelet 必须完成你告诉它的每一项检查，同时完成自己的检查。

当然，拥有一个不连接任何东西的服务不会使系统陷入困境，当然，kubernetes 是为规模化而构建的。但是如果将一项服务扩大到数百万项，kubelet 就会突然陷入困境。

如果您出于任何原因删除了一个部署(或任何相关的东西),请确保您清理了所有其他东西，简单明了。

# **#10 你到底去不去老弟？**

我把我最喜欢的留到了最后。去学围棋吧。Kubernetes 建立在 GO 之上，所有的插件都建立在 GO 之上，他们甚至为 GO 编写了一个客户端。

client-go 可以用来做各种有趣的事情。你可以根据自己的喜好用它来扩展 kubernetes。想想数据收集、部署引擎或简单的清理应用程序。

学习使用 GO 的客户端并在 Kubernetes 中使用它可能是我能给任何使用 Kubernetes 集群的人的最大建议。