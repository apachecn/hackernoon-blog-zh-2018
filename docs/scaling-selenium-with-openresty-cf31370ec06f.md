# 用 Openresty 缩放硒

> 原文：<https://medium.com/hackernoon/scaling-selenium-with-openresty-cf31370ec06f>

![](img/e3a6debe61919e2a35982868c5f229e6.png)

Selenium 成为浏览器/UI 自动化的事实上的自动化标准/工具已经有一段时间了。

selenium 的创建者还创建了 selenium grid 来扩展 UI 测试。网格由一个 selenium hub 和几个 selenium 节点组成，这些节点连接/托管浏览器，从而提供了一种同时运行几个 UI 测试的方法。Selenium 还提供了一些定制/扩展现有功能的简单方法。对于大多数用例，基于 selenium hub /node 的架构伸缩性很好，不需要太多定制。

## 需要更高的可用性和规模

尽管该架构支持扩展 selenium 节点，但它并不容易扩展 hub 本身。在典型的部署中，会有一个集线器和几个与之相连的节点。

会话/浏览器实例的映射保存在集线器内部的内存中。因此，如果中枢出现故障，整个电网将变得不可用。

启用某种级别的 HA 的一个简单方法是至少运行 2 个集线器。

## 多中心挑战

**粘性会话需求**

由于 selenium hubs 不能互相对话，所以负载到不同 hub 的分布需要某种粘性。这里的粘性需要确保一旦在一个 hub 上创建了会话，该特定会话的所有后续请求都会到达同一个 hub。

会话粘性并不是一个新鲜事物。基于浏览器的客户端的典型粘性是通过 cookies 实现的。LB 查看被传递的 cookie 信息，以决定请求将被转发到哪个上游服务器。

**曲奇基粘性排除**

在 selenium 自动化场景中，使用 cookie 传递这些额外的信息是不可能的，所以这不是我们用例的选项。

**基于客户端 ip 的粘性**

到目前为止，我们一直在使用的一个选项是基于客户端 ip 创建粘性。

所以从一个客户端 ip 发起的所有呼叫将总是到达给定的集线器。虽然这适用于大多数场景，但是集线器上的负载并不是均匀分布的。

这种特殊的技术解决了 HA 问题，但是如果负载不均匀，这可能不是一个很好的扩展解决方案。

**测试套件中不均衡的负载和不均衡的测试用例**

一个包含大量 UI 测试的大型测试套件可以完全阻塞所有测试的中心。

为了使我们的测试基础设施更加可靠和健壮，我们可能必须探索一些不同的途径。

## 乐于救援

Openresty 是 nginx 的一个软件包，包括一些高质量的 Lua 库。它允许您自定义负载平衡行为并添加自定义行为。

## 设计

我们可以在 hub 之前添加 openresty 作为代理层。

我们可以在 hub 前面部署几个 openresty 实例，LB 可以以循环方式将请求转发到这些 openresty 实例中的一个(之前 LB 使用客户端 ip 哈希直接将请求转发到 hub)。

Openresty 实例作为反向代理，所有 hub 实例都是每个 openresty 实例的上游服务器。

## 魔力

请求转发的工作方式是:

1.  每个新的会话请求将以循环方式被转发到一个集线器。
2.  一旦在 hub 上创建了会话，并且响应返回到 openresty 代理实例，它就检查响应主体，寻找会话 id，并将上游主机 ip(数字格式的主机 ip)附加到现有的会话 id 上，并向客户端返回响应。
3.  客户端接收一个新的会话 id，其中嵌入了上游集线器节点。
4.  客户端对该会话的任何后续操作都在 url 路径中使用该会话 id，现在会话 id 中嵌入了上游集线器的 ip。
5.  代理检查请求 url，并从请求中提取会话 id。
6.  它通过删除嵌入的主机 ip 部分来重写 url 路径。
7.  它还将上游主机设置为在会话 id 的 ip 部分中找到的特定主机。
8.  然后，它将请求转发到所需的集线器。
9.  集线器看到的会话 id 与它生成时的格式完全相同，并继续照常处理。

嵌入 lua 代码的 nginx 配置示例:

[](https://github.com/Mitendra/code_samples/blob/master/scaling_selenium/nginx.conf) [## Mitendra/code_samples

### 在 GitHub 上创建一个帐户，为 Mitendra/code_samples 开发做贡献。

github.com](https://github.com/Mitendra/code_samples/blob/master/scaling_selenium/nginx.conf) 

## 更进一步

在使用 openresty 代理解决规模和可用性问题的同时，我们可以将该代理用于构建在 Selenium hub 之上的更多功能。

1.  速率限制:尽管这有助于扩展集线器和节点，但在某些情况下，新会话的数量可能会超过可用资源的数量。目前，所有请求都被放入队列中，并在资源可用时进行处理，这导致了会话创建的延迟。不同的测试用例/框架有不同的超时设置，虽然其中一些会继续，但很多会停滞。很多时候，当客户端/测试用例层的会话超时时，集线器仍然会继续运行并创建一个会话。这些未使用的会话稍后会被清理掉，但这会使资源短缺更加严重。为了解决这个问题，我们可以添加一个速率限制功能，根据容量和流量模式，只允许队列中特定数量的会话，超过这个数量，我们可以返回一个 http 429 状态，其中包含一些详细信息，如当前活动会话和估计的重试时间。
2.  监控数据:我们可以从代理层添加许多关于请求的会话、创建新会话的时间、活动会话时间等的监控数据，这可以进一步了解如何优化基础架构的使用。

## 参考

图片来源:[https://pix abay . com/en/摩天大楼-高层建筑-高层-1209407/](https://pixabay.com/en/skyscraper-highrise-building-tall-1209407/)

硒:【https://www.seleniumhq.org/ 

硒栅:【https://www.seleniumhq.org/docs/07_selenium_grid.jsp 

openresty:[https://openresty.org/en/](https://openresty.org/en/)

[](https://github.com/openresty/lua-nginx-module#body_filter_by_lua) [## openresty/Lua-nginx-模块

### 将 Lua 的能力嵌入 NGINX HTTP 服务器。为 openresty/lua-nginx-module 开发做出贡献，创建一个…

github.com](https://github.com/openresty/lua-nginx-module#body_filter_by_lua) [](https://github.com/openresty/lua-nginx-module#access_by_lua) [## openresty/Lua-nginx-模块

### 将 Lua 的能力嵌入 NGINX HTTP 服务器。为 openresty/lua-nginx-module 开发做出贡献，创建一个…

github.com](https://github.com/openresty/lua-nginx-module#access_by_lua)