# 与 GCP 和库伯内特公司建立部署管道

> 原文：<https://medium.com/hackernoon/building-deployment-pipelines-with-gcp-and-kubernetes-4381f5ccfe6b>

我们认为 Kubernetes 可能是任何 DevOps 专家日常操作中的主要工具之一，因此了解 Kubernetes 的最新特性和一般方法非常有用。

![](img/25a351f1411e7493fc2fea25ca0c071f.png)

Kubernetes 的维护者之一， [Kelsey Hightower](https://github.com/kelseyhightower) ，最近发表了一篇关于使用 [GCP 容器构建器](https://cloud.google.com/container-builder)、 [GitHub](https://github.com/) 和众多 [Kubernetes](https://cloud.google.com/kubernetes-engine) 集群构建端到端软件部署管道的[教程。这里就不赘述了，只列出主要步骤，方便大家参考。](https://github.com/kelseyhightower/pipeline/blob/master/README.md)

1.  [**部署管道**](https://github.com/kelseyhightower/pipeline/blob/master/labs/deployment-pipeline.md) 的描述。
2.  先决条件 [**列表**](https://github.com/kelseyhightower/pipeline/blob/master/labs/prerequisites.md) ，包括创建一个新的 GitHub 项目，为其启用 Google Cloud APIs，并安装几个工具，如 [**git**](https://git-scm.com/downloads) 、**[**hub**](https://github.com/github/hub)和 [**kubectl**](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.8.md#downloads-for-v183) 。**
3.  **[**为作业调配 Kubernetes 集群**](https://github.com/kelseyhightower/pipeline/blob/master/labs/kubernetes-clusters.md) 。**
4.  **创建一个 [**中枢配置文件**](https://github.com/kelseyhightower/pipeline/blob/master/labs/hub-configuration-file.md) 。**
5.  **设置 [**GitHub 库**](https://github.com/kelseyhightower/pipeline/blob/master/labs/github-repositories.md) 。**
6.  **为 Google 容器构建器分配 [**构建触发器**](https://github.com/kelseyhightower/pipeline/blob/master/labs/build-triggers.md) 。**
7.  **测试新创建的 [**构建和部署管道**](https://github.com/kelseyhightower/pipeline/blob/master/labs/test-the-pipeline.md) 。**
8.  **[**最终清理**](https://github.com/kelseyhightower/pipeline/blob/master/labs/cleanup.md) 。**

**因此，将有几个集群和 reposs，每个集群和 repo 都有一个专用于特定环境的 Kubernetes 清单文件。利用众多的 Kubernetes 集群和 GitHub 存储库，确保每个环境的细粒度 RBAC，以及为 DevOps 团队提供简化的自动化构建管道。**

**这个关于如何实现这种管道的通用指南肯定会帮助 DevOps 专家掌握最新的 Kubernetes 特性，以及对构建这种系统的正确方法的一般理解。像这样简洁的解决方案再次证明，当在 [**GCP 或 AWS**](https://itsvit.com/blog/news/aws-vs-gcp-cloud-service-provider-choose/) 之间做出选择时，谷歌云平台有一大堆王牌！**

**之前在[我公司的博客](https://itsvit.com/blog/building-deployment-pipelines-gcp-kubernetes/)上发布过这个提示。**