# 在 Docker 上运行分布式 Erlang & Elixir 应用程序

> 原文：<https://medium.com/hackernoon/running-distributed-erlang-elixir-applications-on-docker-b211d95affbe>

由[奥列格塔拉森科](https://github.com/oltarasenko)

![](img/77c712435d6caa87cfa2094ad5cd8cb9.png)

docker 平台已经存在好几年了，所以让我们简单回顾一下它提供了哪些好处，以及为什么您应该考虑使用它:

# 在桌面上启动生产服务的简单方法

我仍然记得过去在 OS X 提出 MySql 是多么复杂。但是，如果您考虑这样一个场景，您的生产环境正在运行某个特定版本的 MySQL，尤其是在非 OS X 上构建的 MySQL，情况会更糟。

你会怎么提出来？幸运的是，当你使用 docker 时，这个问题就消失了，因为你可以像执行命令`docker run mysql:5.3`一样容易地调出任何版本的程序。

# 能够在真实的测试环境中工作，而不必实现服务模拟

这一直是个问题。如果你想正确地测试你的软件，你必须提出(或模仿)许多相关的服务。这总是会产生问题，因为模拟从来不会真正匹配完整的运行服务的特征。

使用 docker，您可以插入您的应用程序所依赖的任何软件，并将其作为测试环境的一部分。

# 使用容器创建真实的云基础设施

最后，docker 容器可以通过像 [Apache Mesos](http://mesos.apache.org/) 这样的工具旋转起来，形成全功能的云，以轻松清晰的方式部署，并且非常稳定！

感兴趣吗？让我们看看这项技术在 Erlang 和 Elixir 领域能给你带来哪些好处。

# 用 docker 测试分布式 erlang 应用程序

让我们考虑这样一种情况，当我们需要创建和测试一个运行在同一台机器上的分布式 erlang 应用程序时，假设我们想让它运行，但是它依赖于一些额外的服务。

这种设置可以在下面的 docker-compose 文件中描述:

```
version: "3"
services:
  c1:
    image: erlang:latest
    # We're using the following command to bring up erlang shell
    # for the example purposes, but in the other case the command
    # will describe a running container
    command: erl -noshell -name app@host1.com -setcookie cookie
    container_name: host1.com
    networks:
      - net1

  c2:
    image: erlang:17.5
    command: erl -noshell -name app@host2.com -setcookie cookie
    container_name: host2.com
    networks:
     - net1

  ejabberd:
    image: rroemhild/ejabberd
    ports:
      - 5222:5222
      - 5269:5269
      - 5280:5280
    container_name: host3.com
    environment:
      - ERLANG_NODE=ejabberd@host3.com
      - XMPP_DOMAIN=test.io
      - ERLANG_COOKIE=cookie
      - EJABBERD_ADMINS=admin@test.io
      - EJABBERD_USERS=admin@test.io:admin
      - EJABBERD_SKIP_MODULES_UPDATE=true
    networks:
     - net1

# We're using custom network setup, as it ships with a DNS
# system which allows containers to communicate by hostnames.
networks:
  net1:
    driver: bridge
```

现在让我们启动这个设置，看看我们如何促进这些容器之间的通信:

1.  调出文件中描述的每个容器:`docker-compose up -d`
2.  连接到其中一个主机:`docker exec -it host2.com erl -name test@host2.com -setcookie cookie -remsh app@host2.com`
3.  验证主机连接是否正常工作:

```
(app@host2.com)1> net_adm:ping('app@host2.com').
pong
(app@host2.com)2> net_adm:ping('app@host1.com').
pong
(app@host2.com)4> net_adm:ping('ejabberd@host3.com').
pong
(app@host2.com)5> nodes().
['test@host2.com','app@host1.com','ejabberd@host3.com']
```

在这个简单的例子中，我们使用标准的 erlang 发行版将几个容器连接起来。这是可能的，因为所有的容器都运行在同一个 docker 网络中，并且可以访问彼此的 epmd 守护进程…

但是我们如何连接运行在不同主机上的 docker 容器呢？

# 独立机器上运行的连接梁

最后，如果你想进入生产阶段，创建一个真正的云，运行多台主机，上面的设置将不起作用，因为容器不知道如何相互连接。

事实上，主机不会知道彼此的 docker 网络(实际上，一些使用 docker swarm 或 amazon ec2 containers 服务互连 docker 容器的工作正在进行，但我们无法使其可靠地运行)，我们将不得不组织一个更复杂的连接机制。

实际上，创建替代的 erlang 分发协议的想法来自这篇精彩的文章，[没有 epmd 的 Erlang(和 Elixir)分发](https://www.erlang-solutions.com/blog/erlang-and-elixir-distribution-without-epmd.html)，我们只是决定向前迈出几步，创建一个可插入的应用程序，它只是“找到它们，将它们全部链接起来，并……确保它们始终连接。”

# 无 epmd 方法

正如 Magnus Henoch 在他的博客文章中所描述的那样，在 dockered 设置中并不真正需要 epmd，因为 docker 环境已经在强制暴露特定端口以供外部访问。正因为如此，erlang 发行版只能使用一个端口(因为其他端口不会公开)。

另一方面，在 Erlang 应用程序中存储主机和端口之间的映射非常容易。现在我们已经有了申请: [EpmdLess](https://github.com/oltarasenko/epmdless) (功劳归于 [Dmitry Mazurin](https://github.com/dmzmk?tab=followers) 构建了它的初始版本)。

现在让我们看看它是如何工作的:

*   在 Docker 沙盒应用程序中克隆出 Erlang 发行版:`git clone [https://github.com/oltarasenko/erlang_distribution_in_docker.git](https://github.com/oltarasenko/erlang_distribution_in_docker.git)`
*   签出到 epmdless_example 分支:`git checkout epmdless_example`
*   为应用程序构建 docker 映像:`cd erlang_distribution_in_docker && docker-compose build`
*   调出 docker-compose.yml 中描述的容器:`docker-compose up`
*   让我们检查 epmd 是否真的在节点上运行:

```
docker-compose exec app1 lsof -i -n -P
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
beam.smp   1 root   13u  IPv4 251627      0t0  TCP *:17012 (LISTEN)
```

*   登录其中一个容器:`docker-compose exec app3 _build/default/rel/sample_app/bin/sample_app console`
*   使用 epmdless 连接到另一个节点:

```
> epmdless_dist:add_node(‘app1@host1.com’, 17012). % Add node into epmdless database
> net_adm:ping(‘app1@host1.com’). % Do ping
> nodes(). % Node is discovered 
```

下面是一个简短的视频，展示了上面执行的步骤:

Watch: EPMDLess NewExample

很好，如你所见，我们能够以这种方式连接节点。看起来很难通过这种方式发现节点，因为我们需要为每个节点启动 epmdless_dist:add_node 和 net_adm:ping 调用。这就是为什么我们也想建议一个方便的方法(至少我们希望你会喜欢它),它允许你也为 docker 平台启用自动节点发现…

# 分布式集群上的节点发现

为了能够组织节点发现过程，我们发布了 [Erlang 节点发现](https://github.com/oltarasenko/erlang-node-discovery)应用程序，该应用程序可以自动建立不同节点之间的连接，正如应用程序配置文件中所指定的那样。

除此之外，它还提供了一个简单的 API，允许您扩展节点发现机制，并使用来自任何其他节点/端口数据库(本地文件、redis、数据库等)的数据。

```
{ erlang_node_discovery, [
    % epmdless_dist as our database
    {db_callback, epmdless_dist},
    {hosts, ["host1.com", "host2.com", "host3.com"]},
    % query following appnames/ports
    {node_ports, [
      {'app1', 17012},
      {'app2', 17013},
      {'app3', 17014}
    ]}
  ]}
```

该应用程序现在将为每个可能的{appname@hostname，port}对生成一个进程，并将尝试组织一个完全连接的集群(并将进行重新连接)。

在我们的例子中(如上面配置文件中的定义),它将为以下可能的节点生成进程:

```
app1@host1.com:17012 app2@host1.com:17013 app3@host1.com:17014 app1@host2.com:17012 app2@host2.com:17013 app3@host2.com:17014 app1@host3.com:17012 app2@host3.com:17013 app3@host3.com:17014
```

让我们试一试:

1.  在 Docker 沙盒应用程序中克隆出 Erlang 发行版:`git clone [https://github.com/oltarasenko/erlang_distribution_in_docker.git](https://github.com/oltarasenko/erlang_distribution_in_docker.git)`
2.  签出到主分支(如果在上一步中已经克隆了所有内容，则需要):`git checkout master`
3.  为应用程序构建 docker 映像:`cd erlang_distribution_in_docker && docker-compose build`
4.  调出 docker-compose.yml 中描述的容器:`docker-compose up`

这里有一段视频总结了上述步骤:

Watch: ErlangNodeDiscovery

# 好吧，但是长生不老药呢？

实际上，重用相同的 erlang 库来运行连接的 elixir 集群是没有问题的。唯一的技巧是显示 elixir 在哪里可以找到 erlang libs:)。这次我添加了一个 Makefile 来演示它。

```
start:
    @ERL_LIBS=_build/dev/lib/ iex --name ${NODE_NAME} \
        --erl "+K true" \
        --erl "-config config/sys.config" \
        --erl "-proto_dist epmdless_proto" \
        --erl "-start_epmd false" \
        --erl "-epmd_module epmdless_client" \
        -S mix
```

# 现在让我们回顾一下长生不老药的例子:

1.  git 克隆[https://github.com/oltarasenko/epmdless.git](https://github.com/oltarasenko/epmdless.git)
2.  docker-撰写构建
3.  docker-排版

仅此而已。下面是短视频演示！

Watch: ElixirEpmdLess

*最初发表于*[*www.erlang-solutions.com*](http://www2.erlang-solutions.com/l/23452/2018-03-28/5d3rbc)*。*