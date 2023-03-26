# 将 Raspberry PI 3 设置为 AWS VPN 客户网关

> 原文：<https://medium.com/hackernoon/setup-raspberry-pi-3-as-aws-vpn-customer-gateway-7432f653707>

![](img/ff1be2dedfe80aa5c6561da1da1fc492.png)

在我的[上一篇文章](https://hackernoon.com/aws-openvpn-access-server-c9edaece035a)中，我向你展示了如何使用 **VPN 软件解决方案**，比如 **OpenVPN** ，来创建一个到你的 **AWS** 私有资源的安全隧道。在这篇文章中，我将带你一步一步地了解如何用一个 **Raspberry PI** 作为**客户网关**，从你的家庭网络建立到你的远程 **AWS VPC 子网**的安全桥。

![](img/8ca7061d14f9f6166ae0134b87f6126c.png)

首先，找到您的**家庭路由器**面向公众的 IP 地址:

![](img/4cf3cf94ff62aae97fad7c52003e5b85.png)

接下来，登录 [AWS 管理控制台](http://console.aws.amazon.com/console/home?region=us-east-1)，导航到 **VPC 仪表板**并创建一个新的 **VPN 客户网关**:

![](img/aa3ba92cb1155d3179b12b24f234bf69.png)

接下来，创建一个**虚拟专用网关**:

![](img/167538572f51831e4d91beb41ddbce23.png)

并将其连接到目标 **VPC** :

![](img/b7324911ab8f803e7edfbc38e759e4ed.png)

然后，创建与**客户网关**和**虚拟专用网关**的 **VPN 连接**:

![](img/0b990185b4831c8fe9becac2d6e011a9.png)

注意:确保将您的**主 CIDR 子网**添加到**静态 IP 前缀**部分。

一旦 **VPN 连接**被创建，点击“**隧道详细信息**”选项卡，您应该看到两条冗余隧道:

![](img/99470b13b81639e512f191259342bc03.png)

创建 VPN 连接可能需要几分钟时间。准备好后，选择连接，选择“**下载配置**，打开配置文件，记下您的**预共享密钥**和**隧道 IP** :

我用了一个**Raspberry PI 3**(Quand Core CPU 1.2 GHz，1 GB RAM)搭配 **Raspbian** ，启用 **SSH server** (默认用户名&密码: *pi* / *raspberry* ，你可以登录并开始操作 PI:

![](img/2d486eff2334946bf51ba0f3ab971846.png)

**必须安装 IPsec** 内核支持。因此，您必须在您的 PI 上安装 **openswan** :

> sudo apt-get install-y open swan lsof

更新 */etc/ipsec.conf* 文件，如下所示:

在*/etc/IPsec . d/home-to-AWS . conf*中创建新的 **IPsec 连接**:

*   **左**:你的树莓派私人 IP。
*   **leftid** :您的家用路由器面向公众的 IP。
*   **左子网**:您的主子网的 CIDR。
*   **右**:虚拟专用网关隧道 IP。
*   **右子网**:你 VPC 的 CIDR。

将隧道**预共享密钥**添加到*/var/lib/open swan/IPSec . secrets . Inc*:

> 89.95 . x . y 52.47.119.151:PSK

要启用 **IPv4 转发**，请编辑 */etc/sysctl.conf* ，并确保以下行未被注释:

运行 *sysctl -p* 重新加载。然后，重启 **IPsec** 服务:

> 服务 ipsec 重新启动

验证服务是否正常运行:

![](img/47bdc080f7bb1fcafe3d12c09c56a2b2.png)

如果您返回到您的 **AWS 仪表板，**您应该看到第一通道状态变为 **UP** :

![](img/3525338ffdc817b3b2b326d9a5cf083a.png)

添加一个新的路由条目，通过 VPN 网关将流量转发到您的家庭子网:

![](img/9275c08d338c5fc8e0cbdcd3215e7283.png)

注意:按照上述相同步骤设置第二个隧道，以实现 VPN 连接的弹性和高可用性。

在私有子网中启动一个 **EC2 实例**来验证 VPN 连接:

![](img/41142617db399047ec240ac7ab0c1e10.png)

只允许从你的**家庭网关 CIDR** 使用 SSH:

![](img/cfce35a939e6384a90b4774376934c68.png)

创建实例后，使用服务器私有 ip 地址通过 **SSH** 进行连接:

![](img/8bc155b7693bc1d867c2c767f965efd8.png)![](img/f975114824b6863e5a1e11edcdf6af34.png)

恭喜你！现在，您可以安全地连接到您的私有 EC2 实例。

为了进一步从同一个**家庭网络**中的其他机器进行连接，添加一个静态路由，如下所述:

**1 —窗户**

> 路由添加 10.0.0.0 掩码 255.255.0.0 192.168.1.81

![](img/01e46101677b34a7138f4b15044ea19d.png)

**2 — Linux**

> sudo 上行路由添加网络 10.0.0.0 网络掩码 255.255.0.0 gw 192.168.31.232

**3 —麦克·OS X**

> sudo route -n 地址 10.0.0.0/16 192.168.31.232

测试一下:

![](img/19c4648acd67fa0d64ddebec8efbe101.png)