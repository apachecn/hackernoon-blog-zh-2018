# 如何对任何应用程序进行归档

> 原文：<https://medium.com/hackernoon/how-to-dockerize-any-application-b60ad00e76da>

如何对任何申请进行备案的 10 步清单。

![](img/17fdd4ea1f6e8b4201b9947afc91c917.png)

互联网上已经有很多关于如何[将](https://hackernoon.com/tagged/dockerie)应用程序 dockerize 的教程，那么*为什么我要写另一个*？

我看到的大部分教程都是专注于某个**特定的** [**技术**](https://hackernoon.com/tagged/technology) (比如 Java 或者 Python)可能没有涵盖你所需要的。他们也没有**处理**所有**相关** **方面**，这些方面对于在**开发**和**运营**团队之间建立明确定义的合同是必要的(这就是**集装箱化**的意义)。

我根据自己最近的**经历**和**教训** **学到的**编制了以下步骤。这是一份清单，列出了你将在周围看到的其他指南所忽略的细节和事情。

免责声明:*这是* ***不是初学者*** *指南。我建议您首先学习如何设置和使用 docker 的基础知识，并在创建和启动一些容器后再回到这里。*

让我们开始吧。

# 1.选择基础图像

[技术有许多](https://hackernoon.com/tagged/technology)特有的基础图像，例如:

*   [https://hub.docker.com/_/java/](https://hub.docker.com/_/java/)
*   【https://hub.docker.com/_/python/ 
*   [https://hub.docker.com/_/nginx/](https://hub.docker.com/_/nginx/)

如果它们都不适合你，你需要从一个基础操作系统开始，自己安装所有的东西。

大多数教程都是从 Ubuntu 开始的(比如 ubuntu:16.04)，这并不一定是错误的。

我的建议是你可以考虑使用阿尔卑斯山的图片:

[https://hub.docker.com/_/alpine/](https://hub.docker.com/_/alpine/)

它们提供了一个小得多的基本映像(小到 5 MB)。

注意:“apt-get”命令对这些图像不起作用。Alpine 使用自己包存储库和工具。详情请见:

[https://wiki . Alpine Linux . org/wiki/Alpine _ Linux _ package _ management](https://wiki.alpinelinux.org/wiki/Alpine_Linux_package_management)

【https://pkgs.alpinelinux.org/packages 号

# 2.安装必要的软件包

这通常是微不足道的。你可能遗漏了一些细节:

a-)你需要在同一行写 apt-get **更新**和 apt-get **安装**(如果你在 Alpine 上使用 apk 也是一样)。这不仅是一种常见的做法，**你需要这样做**，否则“apt-get update”临时镜像(层)可能会被缓存，之后可能不会立即更新你需要的包信息(见本讨论[https://forums . docker . com/t/docker file-run-apt-get-install-all-packages-at-once-or-one-by-one/17191](https://forums.docker.com/t/dockerfile-run-apt-get-install-all-packages-at-once-or-one-by-one/17191))。

b-)仔细检查您是否只安装了您真正需要的****(假设您将在生产中运行容器)。我见过有人在他们的镜像中安装 **vim** 和其他**开发** **工具**。****

****如果需要，为构建/调试/开发时间创建一个不同的 docker 文件。这不仅仅是图像大小的问题，还要考虑安全性、可维护性等等。****

# ****3.添加您的自定义文件****

****改进你的文档的一些提示:****

****a-)理解复制和添加之间的区别:****

****[https://docs . docker . com/develop/develop-images/docker file _ best-practices/# add-or-copy](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#add-or-copy)****

****b-)(尝试)遵循文件系统关于放置文件的约定:****

****[http://www.pathname.com/fhs/](http://www.pathname.com/fhs/)****

****例如对于解释型应用(PHP，Python)，使用 */usr/src* 文件夹。****

****c-)检查您正在添加的文件的属性。如果需要执行权限，就不需要在你的镜像上添加新层(运行 chmod +x …)。只需修复代码库中的原始属性。****

****即使您使用的是 Windows，也没有理由这样做，请参见:****

****[](https://stackoverflow.com/questions/21691202/how-to-create-file-execute-mode-permissions-in-git-on-windows) [## 如何在 Windows 上的 Git 中创建文件执行模式权限？

### 没有必要分两次提交，您可以在一次提交中添加文件并将其标记为可执行…

stackoverflow.com](https://stackoverflow.com/questions/21691202/how-to-create-file-execute-mode-permissions-in-git-on-windows) 

# 4.定义哪个用户将(或可以)运行您的容器

首先，休息一下，看看下面这篇**伟大的**文章:

[](/@mccode/understanding-how-uid-and-gid-work-in-docker-containers-c37a01d01cf) [## 理解 uid 和 gid 如何在 Docker 容器中工作

### 了解用户名、组名、用户 id(uid)和组 id(GID)如何在

medium.com](/@mccode/understanding-how-uid-and-gid-work-in-docker-containers-c37a01d01cf) 

读完这篇文章你会明白:

a-)如果您的应用程序需要访问用户或组表( */etc/passwd* 或 */etc/group* )，您只需要使用特定的(固定 ID)用户运行您的容器。

b-)尽可能避免以 root 身份运行你的容器。

不幸的是，不难发现一些流行的应用程序需要您使用特定的 id 来运行它们(例如，uid:gid = 1000:1000 的[弹性搜索](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html))。

尽量不要成为另一个…

# 5.定义暴露的端口

这通常是一个非常琐碎的过程，请不要让你的容器以 root 用户身份运行，因为你想让它暴露一个特权低端口(80)。只需公开一个非特权端口(例如 8080)并在容器执行期间映射它。

这种差异来自很久以前:

[https://www . w3 . org/Daemon/User/Installation/privileged ports . html](https://www.w3.org/Daemon/User/Installation/PrivilegedPorts.html)

# 6.定义入口点

最普通的方法:马上运行你的可执行文件。

一个更好的方法是:创建一个" *docker-entrypoint.sh* "脚本，您可以在其中使用环境变量来挂钩诸如配置之类的事情(下面将详细介绍):

这是很常见的做法，举几个例子:

[](https://github.com/elastic/elasticsearch-docker/tree/master/build/elasticsearch/bin) [## 弹性/弹性搜索-docker

### 弹性搜索-docker -官方弹性搜索 docker 图片

github.com](https://github.com/elastic/elasticsearch-docker/tree/master/build/elasticsearch/bin) [](https://github.com/docker-library/postgres/tree/de8ba87d50de466a1e05e111927d2bc30c2db36d/10) [## docker-图书馆/postgres

### Postgres-Docker Postgres 官方形象包装

github.com](https://github.com/docker-library/postgres/tree/de8ba87d50de466a1e05e111927d2bc30c2db36d/10) 

# 7.定义配置方法

每个应用程序都需要某种参数化。基本上有两条路可以走:

1-)使用特定于应用程序的配置文件:您将需要记录格式、字段、位置等等(如果您有一个复杂的环境，应用程序跨越不同的技术，这并不好)。

2-)使用(操作系统)环境变量:简单高效。

如果你认为这不是现代的或推荐的方法，记住这是十二因素的一部分:

 [## 十二因素应用程序

### 构建现代化、可扩展、可维护的软件即服务应用的方法。

12factor.net](https://12factor.net/config) 

这并不意味着您需要扔掉您的配置文件并重构您的应用程序的配置机制。

只需使用一个简单的 [envsubst](https://linux.die.net/man/1/envsubst) 命令来替换一个配置模板(在 *docker-entrypoint.sh* 内，因为它需要在运行时执行)。

示例:

[](https://docs.docker.com/samples/library/nginx/#using-environment-variables-in-nginx-configuration) [## nginx

### Nginx 的官方版本。GitHub repo:https://github.com/nginxinc/docker-nginx 图书馆参考这个内容是…

docs.docker.com](https://docs.docker.com/samples/library/nginx/#using-environment-variables-in-nginx-configuration) 

这将封装特定于应用程序的配置文件，在容器内布局和细节。

# 8.将您的数据具体化

黄金法则是:**不要在容器**中保存任何持久数据。

容器文件系统应该是暂时的，短暂的。因此，任何用户生成的内容、数据文件、流程输出都应该保存在**挂载的** **卷**或**绑定挂载的**(也就是说，在容器内链接的基本操作系统的文件夹上)。

老实说，我在挂载卷方面没有太多经验，我一直倾向于将数据保存在一个**绑定** **挂载**上，使用之前创建的文件夹**仔细地使用**配置** **管理**工具(如 Salt Stack)定义**。****

正如**精心**创造了**，我的意思是这样的:**

1.  **我在基本操作系统上创建了一个非特权用户(和组)。**
2.  **所有绑定文件夹(-v)都是使用该用户作为所有者创建的。**
3.  **相应地授予权限(仅授予该特定用户和组，其他用户无权访问)。**
4.  **容器将以此用户运行。**
5.  **你将完全控制这一切。**

# **9.确保您也处理日志**

**我意识到我之前的“**持久** **数据**”远不是一个精确的定义，日志有时会落入灰色区域。你应该如何处理它们？**

**如果你正在创建一个新的应用程序，并希望它遵守 [docker](https://hackernoon.com/tagged/docker) 约定，那么根本不应该写日志**文件**。应用程序应该使用 **stdout** 和 **stderr** 作为**事件** **流**。就像环境变量推荐一样，它也是十二因素中的[之一。参见:](https://12factor.net/)**

 **[## 十二因素应用程序

### 构建现代化、可扩展、可维护的软件即服务应用的方法。

12factor.net](https://12factor.net/logs)** 

**Docker 将自动捕获你发送到 **stdout** 的所有内容，并通过“ **docker** 和 **logs** 命令使其可用:**

**[https://docs.docker.com/engine/reference/commandline/logs/](https://docs.docker.com/engine/reference/commandline/logs/)**

**然而，在一些实际案例中，这是非常困难的。如果您正在运行一个简单的 nginx 容器，您将至少有两种不同类型的日志文件:**

*   **HTTP 访问日志**
*   **错误日志**

**对于不同的结构、配置和预先存在的实现，在标准输出上传输它们可能并不容易。**

**在这种情况下，只需按照上一节所述处理日志文件，并确保循环使用它们。**

# **10.旋转日志和其他仅附加文件**

**如果您的应用程序正在写日志文件或附加任何可以无限增长**的文件，您需要担心文件**旋转**。****

****这对于防止服务器耗尽**空间**，应用数据**保留**策略(当涉及 GDPR 和其他数据法规时，这一点至关重要)至关重要。****

****如果您正在使用**绑定**挂载**挂载**，您可以依靠基础操作系统的一些帮助，并使用您将用于本地旋转配置的相同工具，即 **logrotate** (此处为手动)。****

****我最近发现的一个简单而完整的例子是这个:****

 ****[## 配置-日志旋转

### 使用 Linux 工具 logrotate 和 Aerospike 内存中的 NoSQL 数据库来管理日志循环。

www.aerospike.com](https://www.aerospike.com/docs/operations/configure/log/logrotate.html)**** 

****另一个很好的例子:****

****[](https://www.digitalocean.com/community/tutorials/how-to-manage-logfiles-with-logrotate-on-ubuntu-16-04) [## 如何在 Ubuntu 16.04 | DigitalOcean 上用 Logrotate 管理日志文件

### Logrotate 是一个系统实用程序，用于管理日志文件的自动旋转和压缩。如果日志文件不是…

www.digitalocean.com](https://www.digitalocean.com/community/tutorials/how-to-manage-logfiles-with-logrotate-on-ubuntu-16-04) 

—

如果你有任何反馈，请告诉我。看看我在 https://hackernoon.com/@htssouza 的其他技术文章********