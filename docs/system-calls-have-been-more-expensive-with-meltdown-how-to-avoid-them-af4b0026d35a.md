# 随着 Meltdown 的出现，系统调用变得更加昂贵。如何避开它们？

> 原文：<https://medium.com/hackernoon/system-calls-have-been-more-expensive-with-meltdown-how-to-avoid-them-af4b0026d35a>

![](img/2eb892c32475527e50c9923cc9fe4196.png)

你现在应该听说过熔毁吧。这是一个硬件漏洞，允许未经授权的进程访问特权内存。它会影响自 1995 年以来生产的英特尔处理器。以下是一些细节:[https://en . Wikipedia . org/…/Meltdown _(security _ vulnerability)](https://en.wikipedia.org/wiki/Meltdown_%28security_vulnerability%29)

解决这个问题的唯一有效方法是对您的操作系统(Linux、Windows、macOS)的内核应用补丁，这将显著增加系统调用的成本:这将导致您在 Linux、Windows 或 macOS 上运行的所有程序的性能平均下降 5–30%。更多详情在此:[https://www . the register . co . uk/…/01/02/Intel _ CPU _ design _ flaw/](https://www.theregister.co.uk/2018/01/02/intel_cpu_design_flaw/)

这对你使用的软件意味着什么？每次操作的系统调用越多，效果越差。这对[数据库](https://hackernoon.com/tagged/database) [管理](https://hackernoon.com/tagged/management)系统(DBMS)来说尤其糟糕，因为它们通常会在每次查询时进行大量的系统调用。这是传统关系 DBMS 处理事务的一个非常简化的执行链:

1.  接受连接(系统调用)。
2.  创建一个进程或线程(系统调用)，或者(如果数据库使用预先创建的线程池)在线程池中找到一个空闲线程(这意味着访问一个共享变量，该变量存储应该锁定和解锁的线程的状态，从而产生两个系统调用)
3.  从套接字读取查询(系统调用)。
4.  锁定互斥体(系统调用)。
5.  向 RAM 读取/写入内容。
6.  向磁盘读/写东西(至少一个 syscall)。
7.  解锁互斥锁(系统调用)。
8.  将事务结果写入事务日志(syscall)。
9.  向套接字写入响应(系统调用)。

许多传统的数据库管理系统(MySQL、Postgres、Oracle 等。)在每个事务中进行多次系统调用。在打了补丁的操作系统上，它们可能会变得非常慢，因为系统调用已经变得更加昂贵。

幸运的是，有一些现代的 DBMSs，如 Redis、Aerospike 或 Tarantool，它们的目标是每个 CPU 内核每秒 100K+事务的性能，它们采用了许多聪明的技巧来减少每个事务的系统调用次数。

其中一个技巧是对并行执行的多个查询使用单个套接字，这允许一次读取多个并行查询，一次将它们保存到事务日志中，一次将所有响应写入套接字。更少的系统调用——更多有用的工作。

请点击此处查看详情:

[](/@denisanikin/asynchronous-processing-with-in-memory-databases-or-how-to-handle-one-million-transactions-per-36a4c01fc4e4) [## 内存数据库的异步处理或如何处理每秒一百万个事务…

### 嘿！

medium.com](/@denisanikin/asynchronous-processing-with-in-memory-databases-or-how-to-handle-one-million-transactions-per-36a4c01fc4e4)