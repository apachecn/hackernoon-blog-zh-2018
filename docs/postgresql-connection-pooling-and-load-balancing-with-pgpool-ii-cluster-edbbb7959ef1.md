# 使用 PgPool-II 集群的 PostgreSQL 连接池和负载平衡(更新)

> 原文：<https://medium.com/hackernoon/postgresql-connection-pooling-and-load-balancing-with-pgpool-ii-cluster-edbbb7959ef1>

在这篇文章中，我将带您了解如何使用 PgPool-II 执行负载平衡和连接池，使用 PgPool-II 的好处和动机是什么，最重要的是，如何设置 PgPool-II 集群。

在本教程中，你会发现我在使用`rds_pgpool`，这是一个 Docker 镜像，目的是为了简化安装和配置。

# 动机