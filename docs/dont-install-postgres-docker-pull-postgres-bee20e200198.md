# 不要安装 Postgres。码头工人拉邮件

> 原文：<https://medium.com/hackernoon/dont-install-postgres-docker-pull-postgres-bee20e200198>

![](img/918c34b81d4f05986b95352ef80bef79.png)

“black sperm whale” by [Sho Hatakeyama](https://unsplash.com/@shohatakeyama?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

自从 Docker 出现以来，我很少发现自己在本地机器上直接安装开发软件。无论是数据库服务器(如 Postgres)、缓存系统(如 Redis、Memcache)还是消息传递系统(如 Kafka)，我几乎总是试图找到或构建一个合适的 docker 映像，以便在开发过程中使用。

安装软件很难。而且和你作为开发人员的专业知识没有任何关系。每次开始安装新软件时，我们都会看到版本冲突、深奥的构建失败消息和遗漏的依赖错误。我们花了无数的时间将 Stack Overflow 的代码片段复制粘贴到我们的终端上，并运行它们，希望其中一个能够神奇地解决安装问题，让软件运行起来。结果大多是绝望、沮丧和丧失生产力。

Docker 通过将安装和运行软件的任务减少到两个命令( *docker run* 和 *docker pull* )提供了一种摆脱这种混乱的方法。在本帖中，我们将通过一步一步地了解用 docker 安装 Postgres 是多么的容易和简单来看看这个过程。

注意，这不是 docker 上的教程。如果你对 docker 的内部工作方式和你可以用它做的一切感到好奇，我鼓励你去网上冲浪，因为有很多关于这个主题的高质量材料。

这篇文章假设你有一个有效的 docker 帐户和一个 docker 守护进程正在运行。如果你对 docker 完全陌生，我会推荐从这里的[开始](https://www.docker.com/get-started)。

## 获取 Postgres Docker 图像

要下载 Postgres 最新稳定版本的图像，只需运行

```
docker pull postgres
```

这将从官方 Postgres docker hub [资源库](https://hub.docker.com/_/postgres/)中下载最新的稳定版 Postgres 映像。要下载最新稳定版本之外的版本，我们可以为上面的 *docker pull* 命令提供一个合适的图像标签名称

```
docker pull postgres:[tag_you_want]
```

## 创建一个目录作为 Postgres 数据文件的本地主机挂载点

如果我们希望在容器的生命周期之外持久化容器内运行的 Postgres 实例生成的数据，我们需要将本地挂载点作为数据卷映射到容器内的适当路径。通常，我会在我的 *home* 目录中创建一个 *volumes* 文件夹(我们可以给这个文件夹起任何我们喜欢的名字),然后为我需要为其创建数据卷挂载点的每个应用程序创建子文件夹。

```
mkdir -p $HOME/docker/volumes/postgres
```

## 运行 Postgres 容器

启动 Postgres 容器就像运行 *docker run* 命令一样简单

```
docker run --rm   --name pg-docker -e POSTGRES_PASSWORD=docker -d -p 5432:5432 -v $HOME/docker/volumes/postgres:/var/lib/postgresql/data  postgres
```

我们为 *docker run* 命令提供了几个选项:

*   *— rm* :退出时自动移除容器及其关联的文件系统。一般来说，如果我们运行大量短期容器，将 *rm* 标志传递给 *docker run* 命令进行自动清理并避免磁盘空间问题是一个很好的实践。我们总是可以使用 *v* 选项(如下所述)在容器的生命周期之外持久保存数据
*   *—名称:*集装箱的识别名称。我们可以选择任何我们想要的名字。请注意，两个现有的(即使已停止)容器不能同名。为了重用某个名称，您需要将 *rm* 标志传递给 *docker run* 命令，或者使用 *docker rm【容器名称】命令显式删除容器。*
*   *-e:* 将名为 *POSTGRES_PASSWORD* 值为 *docker* 的环境变量暴露给容器。此环境变量设置 PostgreSQL 的超级用户密码。我们可以将 *POSTGRES_PASSWORD* 设置为我们喜欢的任何值。我就选它做 *docker* 做演示。您还可以设置其他环境变量。其中包括 *POSTGRES_USER* 和 *POSTGRES_DB。POSTGRES_USER* 设置超级用户名。如果未提供，超级用户名默认为 *postgres。* *POSTGRES_DB* 设置要设置的默认数据库的名称。如果没有提供，则默认为 *POSTGRES_USER 的值。*
*   *-d:* 以分离模式或换句话说，在后台启动容器。
*   *-p* :将本地主机上的端口 5432 绑定到容器内的端口 5432。该选项使运行在容器外的应用程序能够连接到运行在容器内的 Postgres 服务器。
*   *-v* :将主机上的$HOME/docker/volumes/postgres 挂载到容器内创建的容器端卷路径/var/lib/postgresql/data。这确保了 postgres 数据即使在容器被移除后仍然存在。

## 连接到 Postgres

一旦容器启动并运行，从应用程序连接到它与连接到 docker 容器外运行的 Postgres 实例没有什么不同。例如，要使用 *psql* 进行连接，我们可以执行

```
psql -h localhost -U postgres -d postgres
```

希望这篇文章展示了在 docker 中使用 Postgres 是多么容易和简单。对于大多数其他应用程序，这个过程也很简单。所以下一次你需要安装一个软件的时候，在你使用 *brew install、yum install、apt-get install* 或者任何你的系统包管理器的命令之前，想想 *docker pull*