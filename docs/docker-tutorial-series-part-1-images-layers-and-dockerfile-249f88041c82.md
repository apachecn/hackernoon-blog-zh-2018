# Docker 教程系列第 1 部分:图像、图层和 docker 文件

> 原文：<https://medium.com/hackernoon/docker-tutorial-series-part-1-images-layers-and-dockerfile-249f88041c82>

本文是关于[码头工人](https://hackernoon.com/tagged/docker)系列文章的第一篇。今天我们要介绍 Docker 的核心概念。然后我们将使用 Dockerfile 构建我们的第一个 Docker 映像，然后实例化一个容器。

如果您想获得容器和虚拟机的简要概述，请访问此链接:

 [## Docker 时代:容器 vs 虚拟机

### 虚拟机和容器执行相同的任务—它们隔离应用程序及其依赖项，允许它…

medium.com](/@adityadixit06/age-of-docker-containers-vs-virtual-machines-c164c9e914bc) 

Docker 是一个[平台](https://hackernoon.com/tagged/platform)，它允许你将你的应用从你的基础设施中分离出来。这种分离使得开发、扩展和运行应用程序变得更加容易。Docker 基于客户机-服务器架构。“Docker 守护进程”执行构建、部署和管理对象的繁重任务。而‘Docker 客户端’是与 Docker 守护进程交互的命令行界面。

在开始之前，我想介绍几个我们将在 Docker 的这篇文章中用到的术语:

*   图:带有创建 Docker 容器说明的只读模板。
*   容器:图像的可运行实例。它是可配置的，并与其他容器隔离。
*   服务:允许您定义应用程序的预期状态。
*   Dockerfile:包含创建图像的说明的文本文件。
*   Docker Store:Docker 图像的公共存储库。

使用 Docker 的第一步是获取映像的基础版本，然后在这个基础映像上安装应用程序文件和库。我们可以手动执行这个过程，但是随着复杂性的增加，这种方法很可能变得无法管理。一个更好的选择是使用 Dockerfile。Dockerfile 文件是创建图像的一系列指令。它位于应用程序的根文件夹中。这个 Dockerfile 文件由 build 命令使用，以生成应用程序的自定义映像。

最简单的 Dockerfile 将执行以下操作:

1.  从 Docker 存储中提取操作系统和程序的基本映像
2.  安装库和二进制文件
3.  将应用程序文件复制到容器
4.  设置工作目录
5.  开放港口与外界交流
6.  设置容器启动时运行的命令

创建图像的构建过程将消耗 Dockerfile 并生成我们的图像。然后，这个映像用于运行保存我们的应用程序的容器。

`docker image build -t [container-name] .`
`docker container run [container-name]`

图像不是单一的整体块。它由几层组成，每个 Dockerfile 指令通常代表一个单独的层。每一层都是不可变的，并且建立在先前层的基础上。一个专用层跟踪对图像所做的更改。因此，当我们重建一个映像时，这个过程并不是从零开始的。如果构建图层所需的底层文件保持不变，则不会触及图层，而是使用缓存版本。重建过程从发现与先前版本有差异的层开始。这种协调方法使得构建过程非常快。

请记住，在编写 docker 文件时，每天更新几次的源代码文件应该放在更高的层中，以加快构建过程。而安装诸如第三方模块和内置库之类的应用程序依赖项应该更早进行。如果没有记住这一点，就意味着您将不得不在每次映像构建时重新安装所有的依赖项，从而大大降低映像构建过程的速度。

*原载于*[*blog . adityadixit . me*](http://blog.adityadixit.me/docker-tutorial-series-part-1.html)*。*