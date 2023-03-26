# 你的码头上有个洞。如何停止带有 pihole 的广告:Raspbian 和 Docker 版本！

> 原文：<https://medium.com/hackernoon/there-is-pi-hole-on-your-dockers-how-to-stop-ads-with-a-pihole-raspbian-and-docker-version-15f2611edafe>

![](img/0760a35238089de1db5ff029b8f88951.png)

[https://pi-hole.net/](https://pi-hole.net/)

写这篇博文的时候，本来我只是打算说说在 raspbian 上安装 pihole。但是后来我发现 pihole 可以在 docker 上运行。就我个人而言，我喜欢我的 raspberry pis，但当有一个非常好的 docker 主机时，我宁愿不要实验室中的另一个硬件。

不要在安装上罗嗦，让我们直接切入 TLDR 版本，让它在 pi 和 docker 上运行。享受吧，一个解释会随之而来。

TLDR!!!

好了，现在我们已经安装了 pihole，它正在运行并阻止我们本地网络上的广告。但是它在做什么，怎么做的？有些人可能不关心它是如何工作的，那些关心的人，请继续阅读。

Pihole 由许多使其工作的系统组成，最重要的是 core、gravity 和 ftldns。pihole 的文档指出，“Pi-hole 的核心脚本提供了将许多 DNS 相关功能捆绑到一个简单且用户友好的管理系统中的能力，因此可以轻松阻止广告等不想要的内容。”Ftldns 是驱动一切的 dns 引擎。这意味着 ftldns 将充当转发 dns 服务器，如果它不知道 dns 响应，它将转发它。然而，当它检测到一个广告网络的 dns 条目时，pihole 不会转发该请求，而只是将其封锁。还有一些更加用户友好的命令，用于添加自定义条目、更新和管理 dns 服务器。然后，gravity 模块允许将所有自定义白名单和黑名单输入到系统中。gravity 模块还支持各种正则表达式来定制条目。

差不多就是这样。这些子系统中的每一个都有更多的内容，但是还有它的阅读文档。下面包含了所有的文档链接。

[](https://docs.pi-hole.net/core/pihole-command/) [## pihole 命令- Pi-hole 文档

### Pi-hole 的核心脚本提供了将许多 DNS 相关功能绑定到一个简单且用户友好的…

docs.pi-hole.net](https://docs.pi-hole.net/core/pihole-command/) [](https://docs.pi-hole.net/core/pihole-command/#gravity) [## pihole 命令/重力 pihole 文档

### Pi-hole 的核心脚本提供了将许多 DNS 相关功能绑定到一个简单且用户友好的…

docs.pi-hole.net](https://docs.pi-hole.net/core/pihole-command/#gravity) [](https://docs.pi-hole.net/ftldns/) [## 概述-钻孔文件

### DNS(皮霍尔-FTL)在皮霍尔项目中提供 DNS 服务。它提供极快的 DNS 和 DHCP 服务…

docs.pi-hole.net](https://docs.pi-hole.net/ftldns/) [](https://pi-hole.net/) [## 黑洞:网络广告的黑洞

### curl-sSL https://install.pi-hole.net | bash Docker install:Docker pull pi hole/pi hole 我们的代码是完全开放的，但是…

pi-hole.net](https://pi-hole.net/) [](https://github.com/pi-hole/pi-hole) [## π孔/π孔

### 网络广告的黑洞。在 GitHub 上创建一个帐户，为 pi-hole/pi-hole 开发做贡献。

github.com](https://github.com/pi-hole/pi-hole) [](https://github.com/pi-hole/docker-pi-hole/blob/master/docker_run.sh) [## 码头/码头

### 码头集装箱上的孔。通过在 GitHub 上创建一个帐户，为 pi-hole/docker-pi-hole 开发做出贡献。

github.com](https://github.com/pi-hole/docker-pi-hole/blob/master/docker_run.sh) [](https://www.raspberrypi.org/downloads/raspbian/) [## 为树莓 Pi 下载 Raspbian

### Raspbian 是基金会官方支持的操作系统。你可以用 NOOBS 安装或者下载镜像…

www.raspberrypi.org](https://www.raspberrypi.org/downloads/raspbian/) [](https://learn.adafruit.com/pi-hole-ad-blocker-with-pi-zero-w/install-pi-hole) [## 用 Pi Zero W | Adafruit 学习系统安装 Pi Hole | Pi Hole 广告拦截器

### 使用广告拦截定制 DNS 迷你服务器清除广告

learn.adafruit.com](https://learn.adafruit.com/pi-hole-ad-blocker-with-pi-zero-w/install-pi-hole) [](https://blog.cloudflare.com/announcing-1111/) [## 宣布 1.1.1.1:最快、隐私优先的消费者 DNS 服务

### Cloudflare 的使命是帮助建立一个更好的互联网。今天，我们很兴奋能够朝着这个目标又迈进了一步…

blog.cloudflare.com](https://blog.cloudflare.com/announcing-1111/)