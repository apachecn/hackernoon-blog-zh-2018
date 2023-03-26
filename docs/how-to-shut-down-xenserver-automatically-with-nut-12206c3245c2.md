# 如何用坚果自动关闭 XenServer

> 原文：<https://medium.com/hackernoon/how-to-shut-down-xenserver-automatically-with-nut-12206c3245c2>

![](img/0b744288362e0d12e2e5735b643354c4.png)

我们学校的核心基础设施由 UPS 保护。不幸的是，我们总是有电源故障(大多是短暂的)，在我们的 UPS 的帮助下，我们至少可以稍微桥接它们。在我们的 UPS 上挂了一个[树莓 Pi](https://amzn.to/2HOnPde) 、[配置成坚果服务器](https://openschoolsolutions.org/shutdown-servers-case-power-failure%e2%80%8a-%e2%80%8aups-nut-co/)。如果电源出现故障，UPS 切换到电池供电，Raspberry Pi 会在特定充电状态后向所有连接的服务器发送命令。这些然后自动关闭。我们所有的虚拟机、备份系统和服务器都将关闭。然而，仍然缺少一个重要的服务器:虚拟化主机。所以今天我想简单描述一下如何通过设置 XenServer 为坚果客户端来自动关闭它。

# 安装螺母客户端

XenServer 基于 CentOS，但是不允许安装第三方包。您还应该知道，如果您安装了额外的软件包，您可能不再获得支持(如果您有支持合同)。

为了安装坚果客户端，我们必须修改文件***/etc/yum . repos . d/CentOS-base . repo***中的 XenServer 7。在`[extras]`部分，我们用`7`替换了`baseurl`行中的`$releaseserver`。

```
$ nano /ect/yum.repos.d/CentOS-Base.repo
…
[extras]
name=CentOS-$releasever — Extras
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&rep$
baseurl=http://mirror.centos.org/centos/7/extras/$basearch/
…
```

然后我们可以设置 Epel Repo 并安装 NUT 客户端:

```
$ yum clean all
$ yum — enablerepo=extras -y install epel-release
```

现在让我们看看哪个包提供了我们的 NUT 客户机并安装它:

```
$ yum provides /usr/sbin/upsmon
Geladene Plugins: fastestmirror
Loading mirror speeds from cached hostfile
* epel: mirrors.bangmodhosting.com
nut-2.7.2–3.el7.x86_64 : Network UPS Tools
Quelle : epel
Übereinstimmung von:
Dateiname : /usr/sbin/upsmon$ yum install nut-client-2.7.2–3.el7.x86_64
```

# NUT 客户端设置

既然已经安装了 NUT 客户机，我们需要对它进行配置，以便我们的 XenServer 在电源故障时关闭。首先，我们必须创建一个文件来告诉 NUT 我们的 XenServer 是一个客户端:

```
$ nano /etc/ups/nut.conf
MODE=netclient
```

主配置发生在文件***/etc/ups/up smon . conf***中。在那里我们改变了两行:

```
$ nano /etc/ups/upsmon.conf…
MONITOR ups@192.168.1.2 1 upsremote password slave
…
SHUTDOWNCMD "etc/ups/xen-shutdown.sh"
…
```

根据 NUT master 的配置，UPS 名称、IP 以及用户名和密码仍必须在此处进行调整。

在我们的 XenServer 关闭之前，它必须首先关闭所有虚拟机。我找到了下面的[脚本](https://github.com/serrc-techops/NUT-Configuration/blob/master/slave/xen/xen-shutdown.sh)，我们下载它并使其可执行。

```
$ cd /etc/ups/
$ wget [https://raw.githubusercontent.com/serrc-techops/NUT-Configuration/master/slave/xen/xen-shutdown.sh](https://raw.githubusercontent.com/serrc-techops/NUT-Configuration/master/slave/xen/xen-shutdown.sh)
$ chmod +x xen-shutdown.sh
```

为了让 NUT 客户机也自动启动，我们还必须设置一个 systemd 服务。我们创建文件***/etc/systemd/system/nut-monitor . service***并插入以下内容:

```
$ nano /etc/systemd/system/nut-monitor.service[Unit]
Description=Network UPS Tools — power device monitor and shutdown controller
After=local-fs.target network.target[Service]
ExecStart=/usr/sbin/upsmon
PIDFile=/run/nut/upsmon.pid
Type=forking[Install]
WantedBy=multi-user.target
```

现在，我们激活并启动该服务:

```
$ systemctl enable nut-monitor.service
$ systemctl daemon-reload
$ systemctl start nut-monitor
```

用`systemctl status nut-monitor`我们可以看到一切是否都在运行:

```
nut-monitor.service — Network UPS Tools — power device monitor and shutdown controller
Loaded: loaded (/etc/systemd/system/nut-monitor.service; enabled; vendor preset: disabled)
Active: active (running) since Mi 2018–04–18 17:13:42 ICT; 1 day 12h ago
Process: 997 ExecStart=/usr/sbin/upsmon (code=exited, status=0/SUCCESS)
Main PID: 1001 (upsmon)
CGroup: /system.slice/nut-monitor.service
├─1000 /usr/sbin/upsmon
└─1001 /usr/sbin/upsmon
```

# 测试自动关机

首先，我们可以测试关闭 XenServer 的脚本，看看是否所有启动的虚拟机都关闭了。为此，我们只需执行脚本:

```
$ sh /etc/ups/xen-shutdown.sh
```

为了模拟电源故障，我们可以在*螺母主*上发出以下命令:

```
$ sudo upsmon -c fsd
```

之后，您必须手动打开所有设备，或者通过局域网唤醒来唤醒它们。

# 结论

[坚果](http://networkupstools.org/)是一个伟大的项目，它允许我们非常灵活的场景。目前，我们只有一个大型 UPS 连接到螺母主设备，但也可以将多个 UPS 连接到螺母主设备。结合 Check_MK，我们可以持续监控 UPS 的状态，还可以统计电源故障的频率。

【openschoolsolutions.org】最初发表于[](https://openschoolsolutions.org/shut-down-xenserver-automatically-nut/)**。* ***注册*** *订阅我们的时事通讯，获取免费的 PDF 格式，并为您的课堂提供出色的开源应用，或者在 Twitter 上关注*[*@ OpenSchoolZ*](https://twitter.com/OpenSchoolZ)*。**