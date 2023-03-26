# 宣布推出 Exoframe 2.0 —在几分钟内部署任何东西

> 原文：<https://medium.com/hackernoon/announcing-exoframe-2-0-deploy-anything-within-minutes-264fdcc4d6>

> Exoframe 是一个自托管工具，允许使用 Docker 进行简单的单命令部署。
> 如果你不熟悉，你可以在[关于初始发布的文章](https://hackernoon.com/announcing-exoframe-1-0-simple-docker-deployment-tool-59bde252139d)和[项目库](https://github.com/exoframejs/exoframe)中找到更多信息。

我很高兴地宣布 [Exoframe](https://github.com/exoframejs/exoframe) 2.0 的发布！
该版本包括社区要求的许多附加功能，以及将 Exoframe 服务器迁移到 [Fastify](https://github.com/fastify/fastify) 和测试到 [Jest](https://facebook.github.io/jest/) 。

# 项目模板

2.0 版本的主要亮点是支持第三方模板。
有了这个，Exoframe 不再局限于部署 Node.js、静态 HTML、Docker 和 docker-compose 项目。现在可以部署任何类型的项目——只要您安装了正确的模板！
模板将自动检测您正在部署的项目类型，或者可以通过 Exoframe 配置文件明确设置。

除了对模板的支持，我还将为 [Maven](https://github.com/exoframejs/exoframe-template-maven) 、 [Java](https://github.com/exoframejs/exoframe-template-java) 和 [Tomcat](https://github.com/exoframejs/exoframe-template-tomcat) 项目发布三个基本模板。

你可以在[文档](https://github.com/exoframejs/exoframe/tree/master/docs)中阅读更多关于模板的内容。如果你对创建自己的模板感兴趣，看看这个指南。

# 带有 Letsencrypt 挑战修复程序的 Traefik 1.5

另一个主要变化是对 Traefik 1.5.0 的更新，增加了对 ACME HTTP 质询的支持，这是 letsencrypt 正确工作所必需的，因为 TLS-SNI-0x 质询已被禁用。

# 更多好吃的

其他较小(但很棒)的新增功能包括:

*   现在，您可以通过 Exoframe config 设置[附加容器标签](https://github.com/exoframejs/exoframe/blob/master/docs/Basics.md#project-config-file)(当您想要传递附加 Traefik 参数时非常有用)
*   服务器现在可以通过配置启用可选的 [CORS 支持](https://github.com/exoframejs/exoframe-server#configuration)
*   现在可以[配置与 Exoframe 服务器一起使用的 Traefik 实例](https://github.com/exoframejs/exoframe-server#configuration)(例如，您可以将其与您自己的 Traefik 实例一起使用)
*   对于那些愿意尝试最新版本的人来说，现在有了一种针对服务器和 CLI 更新到夜间版本的方法

我很想听到你对这个新版本的反馈。请随意将它发送到 GitHub 或 Twitter！