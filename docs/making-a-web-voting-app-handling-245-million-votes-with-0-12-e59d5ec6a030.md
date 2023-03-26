# 用 0.12 美元制作一个处理 2.45 亿张选票的网络投票应用程序

> 原文：<https://medium.com/hackernoon/making-a-web-voting-app-handling-245-million-votes-with-0-12-e59d5ec6a030>

![](img/6ee989424bacf1badf181dccc30dd305.png)

Get Money Change — [Giphy](https://giphy.com/gifs/RiffTrax-change-norman-9c2fd92eNOekM)

# 灵感

我一直很喜欢像大哥大 Brasil 和语音战这样的 1x1 网络投票决斗的情感。它到达数百万人的方式令人难以置信。因此，我决定做一个简单的投票应用程序，能够处理一天的投票，在像巴西和美国这样的大国。带着挑战:我想在两个小时内，从零开始，用一些钱做到这一点。

作为目标，我想象了一个有 3 亿人口的国家，其中 70%的人会投票。即一天 2.1 亿次投票，一小时 875 万次投票，大约 2500 次请求/秒。

# (做…)的方式

为此，我选择了 Golang 和 Redis 的简单而强大的组合(具有 AOF 持久性)。用 Ubuntu 运行在一个简单的数字海洋水滴中，没有任何微调。在这个项目中，我不担心 auth、爬虫和速率限制。

为了这个目标，我创建了一个包含 3 个文件的项目: **docker-compose.yml** 、 **Dockerfile** 和 **main.go** 。([https://github.com/danhenriquesc/go-poll](https://github.com/danhenriquesc/go-poll))

**docker-compose.yml** 和 **Dockerfile** 文件基本上构建了一个 redis 服务器和一个 Golang 1.11 API。在本地主机上运行:8000。我只需要运行一个简单的命令:`docker-compose up`

**main.go** 是一个 96 行的文件，它创建了一个具有两个端点的 API:

*   *POST/vote/{ candidate:[1 | 2]}*要在候选人中投票并返回投票的当前状态，
*   *获取/投票*获取投票的当前状态。

这个 API 中使用了两个 Golang 包:

*   【github.com/gorilla/mux】为了方便 HTTP 路由器，
*   *github.com/go-redis/redis*方便 Redis 访问*。*

# HTTP 基准测试执行

创建完这个项目后，我决定用 wrk 工具检查它能处理多少请求。所以我用便宜又现实的方式进行了测试，用的是[数字海洋水滴](https://www.digitalocean.com/products/droplets/)。

我选择了 3 个标准液滴选项进行测试:

*   5 美元/月 droplet: 1GB RAM，1 个 vCPU 1.8GHz
*   15 美元/月 droplet: 1GB RAM，3vCPU 1.8GHz
*   80 美元/月 droplet: 16GB 内存，6vCPU 1.8GHz

这三款运行的是 Ubuntu 16.04.4 x64，没有任何微调。

您可以在下面看到基准集:

**$ 5/月滴**

*执行 1*

```
root@ubuntu-s-1vcpu-1gb-nyc1–01:~# wrk -t 1000 -c 10000 -d 1m -s ./post.lua [http://68.183.137.138:8000/vote/2](http://68.183.137.138:8000/vote/2)
Running 1m test @ [http://68.183.137.138:8000/vote/2](http://68.183.137.138:8000/vote/2)
 1000 threads and 10000 connections
 Thread Stats Avg Stdev Max +/- Stdev
 Latency 607.99ms 157.48ms 1.99s 82.22%
 Req/Sec 24.63 22.40 292.00 75.02%
 **345301 requests in 1.00m, 52.72MB read**
**Requests/sec: 5755.02**
Transfer/sec: 0.88MB
```

*执行 2*

```
root@ubuntu-s-1vcpu-1gb-nyc1–01:~# wrk -t 1000 -c 20000 -d 1m -s ./post.lua http://68.183.137.138:8000/vote/2
Running 1m test @ http://68.183.137.138:8000/vote/2
 1000 threads and 20000 connections
 Thread Stats Avg Stdev Max +/- Stdev
 Latency 860.65ms 296.29ms 2.00s 65.63%
 Req/Sec 39.72 45.10 686.00 83.99%
 **358947 requests in 1.00m, 54.94MB read**
**Requests/sec: 5982.46**
Transfer/sec: 0.91MB
```

**$ 15/月滴**

*执行 1*

```
root@ubuntu-s-3vcpu-1gb-nyc1-01:~# wrk -t 7500 -c 10000 -d 1m -s ./post.lua [http://204.48.20.138:8000/vote/2](http://204.48.20.138:8000/vote/2)                                                                                                                                   
Running 1m test @ [http://204.48.20.138:8000/vote/2](http://204.48.20.138:8000/vote/2)
  7500 threads and 10000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   631.39ms  107.29ms 798.00ms   91.50%
    Req/Sec     1.25      2.28   272.00     97.79%
 **735058 requests in 1.01m, 112.16MB read**
**Requests/sec:  12091.66**
Transfer/sec:      1.85MB
```

*执行二*

```
root@ubuntu-s-3vcpu-1gb-nyc1-01:~# wrk -t 1000 -c 13000 -d 1m -s ./post.lua [http://204.48.20.138:8000/vote/1](http://204.48.20.138:8000/vote/1)                                                                                                                                   
Running 1m test @ [http://204.48.20.138:8000/vote/1](http://204.48.20.138:8000/vote/1)
  1000 threads and 13000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.08s    89.31ms   1.91s    80.95%
    Req/Sec    22.67     29.20   650.00     87.31%
 **714698 requests in 1.00m, 113.13MB read
Requests/sec:  11884.94**
Transfer/sec:      1.88MB
```

*执行 3*

```
root@ubuntu-s-3vcpu-1gb-nyc1-01:~# wrk -t 1000 -c 15000 -d 1m -s ./post.lua [http://204.48.20.138:8000/vote/1](http://204.48.20.138:8000/vote/1)                                                                                                                                   
Running 1m test @ [http://204.48.20.138:8000/vote/1](http://204.48.20.138:8000/vote/1)
  1000 threads and 15000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     1.32s   128.68ms   1.99s    77.60%
    Req/Sec    24.45     33.76     1.09k    85.97%
 **671964 requests in 1.00m, 102.71MB read
Requests/sec:  11199.40**
Transfer/sec:      1.71MB
```

**$ 80/月滴**

*执行 1*

```
root@ubuntu-s-6vcpu-16gb-nyc1-01:~# wrk -t 1000 -c 70000 -d 1m -s ./post.lua [http://204.48.20.138:8000/vote/1](http://204.48.20.138:8000/vote/1)                                                                                                                                   
Running 1m test @ [http://204.48.20.138:8000/vote/1](http://204.48.20.138:8000/vote/1)
  1000 threads and 70000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   902.54ms  103.71ms   2.00s    78.21%
    Req/Sec    79.04     71.04     3.75k    84.72%
 **4117831 requests in 1.01m, 637.73MB read
Requests/sec:  68630.52**
Transfer/sec:     10.54MB
```

*执行二*

```
root@ubuntu-s-6vcpu-16gb-nyc1-01:~# wrk -t 1000 -c 30000 -d 1m -s ./post.lua [http://204.48.20.138:8000/vote/1](http://204.48.20.138:8000/vote/1) 
Running 1m test @ [http://204.48.20.138:8000/vote/1](http://204.48.20.138:8000/vote/1)
  1000 threads and 30000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   942.14ms  127.26ms   2.00s    85.55%
    Req/Sec    47.32     55.14     1.33k    88.25%
 **1830829 requests in 1.00m, 281.99MB read
Requests/sec:  30513.82**
Transfer/sec:      4.68MB
```

*执行 3*

```
root@ubuntu-s-6vcpu-16gb-nyc1-01:~# wrk -t 1000 -c 20000 -d 1m -s ./post.lua [http://204.48.20.138:8000/vote/2](http://204.48.20.138:8000/vote/2)
Running 1m test @ [http://204.48.20.138:8000/vote/2](http://204.48.20.138:8000/vote/2)
  1000 threads and 20000 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   656.35ms   89.49ms   1.61s    84.31%
    Req/Sec    40.59     41.40     1.13k    87.53%
 **1828994 requests in 1.00m, 292.16MB read
Requests/sec:  30483.21**
Transfer/sec:      4.85MB
```

# 标杆分析

**$ 5/月滴**

首先，我用最便宜的数字海洋测试，每月 5 美元。因为我想处理 1 天的投票，所以，我有 1 天的成本约为 0.16 美元。

在这个 droplet 中，我们大约有 5800 个请求/秒，可以处理:

*   每分钟 348，000 张选票
*   一小时 20，880，000 张选票，0.007 美元
*   一天 501，120，000 张选票，价值 0.16 美元

在这个水滴里，我可以轻松实现一天处理 2.1 亿票的初步目标，0.16 美元。

**$ 15/月滴**

那么，随着任务的完成，为什么不强调一个更强大的液滴呢？所以，我换成了 15 美元/月的滴滴，每小时 0.02 美元，每天 0.53 美元。

在这个微滴中，我们获得了大约 12，000 请求/秒，基本上是$5/mo 微滴的两倍。这并不好，因为多付三倍的钱并没有给我三倍的表现。

**$ 80/月滴**

跳到一个新的水平，我决定尝试 40 美元/月的服务器。但是，由于一次点击失误，我选择了 80 美元/月的服务器，这让我大吃一惊。这种液滴成本为**0.12 美元/小时**和 2.85 美元/天，并达到约 68000 req/秒。

有了这个比率，我们可以处理:

*   每分钟 408 万张选票
*   **每小时 244，800，000 张选票，价值 0.12 美元**
*   一天 5，875，200，000 张选票，2.85 美元

# 结论

有了这个基准，我们可以获得一些有趣的见解:

*   我们可以用 0.16 美元处理 501，120，000 张选票，分散在一天中。这几乎是世界第三大和第四大人口大国美国和印度尼西亚的人口总和。
*   我们可以在一个小时内用 0.12 美元处理 244，800，000 张选票。这比世界第五大人口国巴西的人口还多。
*   我们可以用 2.856 美元处理 5，875，200，000 张分散在一天中的投票。这是世界上人口最多的中国的 4 倍多。

有了这个数字，我们可以意识到一些应用程序看起来比实际上更难、更贵。尽管一些商业规则被轻视，但对任何小企业来说，成本仍然很低。

除此之外，其他架构如 AWS 或 GCP 中的无服务器功能可以以较低的成本提供更好的性能和可伸缩性。这为新业务提供了大量具有高可扩展性应用的机会。

> 可能性就在那里，试一试吧！