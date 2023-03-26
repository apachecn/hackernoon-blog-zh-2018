# 如何更新 Check_MK

> 原文：<https://medium.com/hackernoon/how-to-update-check-mk-1463b8db9666>

![](img/92b097e2dc71678cc3ae2180a219571a.png)

我们使用 [OMD/Check_MK](https://mathias-kettner.com/index.html) 来监控我们的 IT 基础设施。它很容易使用，我们总是知道我们的网络组件、[服务器](https://openschoolsolutions.org/shutdown-servers-case-power-failure%e2%80%8a-%e2%80%8aups-nut-co/)、打印机等的状态。直到现在我们用的都是“老-稳定”版本(1.2.8)，但是 CPU 使用率明显增加，所以是时候升级到目前的稳定版本(1.4.0)了。今天我想给大家展示一下如何更新 Check_MK 以及需要考虑的问题。

# 更新 Check_MK

使用 Check_MK，您可以管理几个实例。这些被称为“站点”。可以并行安装几个版本的 Check_MK。每个实例可以使用一个已安装的版本。所以软件升级和你可能习惯的有点不一样。

在我们可以将我们的实例升级到新版本之前，我们必须首先更新 Check_MK。我们下载了 Ubuntu 16.04 (1.4.0)的最新版本。

```
$ wget [https://mathias-kettner.de/support/1.4.0p26/check-mk-raw-1.4.0p26_0.xenial_amd64.deb](https://mathias-kettner.de/support/1.4.0p26/check-mk-raw-1.4.0p26_0.xenial_amd64.deb)
```

然后，我们必须安装软件包和任何缺失的依赖项:

```
$ sudo dpkg -i check-mk-raw-1.4.0p26_0.xenial_amd64.deb$ sudo apt install -f
```

现在我们可以使用`omd versions`来查看新版本是否被设置为默认版本:

```
root@omd:~# omd versions
1.2.8p13.cre
1.4.0p26.cre (default)
```

# 更新 Check_MK 实例

现在已经安装了 Check_MK 的新版本，但是我们的“站点”，即监控实例，仍然使用旧版本。所以我们也必须更新它。为此，我们将用户切换到我们的实例

```
root@omd# su — myInstance
```

首先，我们停止我们的实例:

```
omd stop
```

之后，我们可以执行更新:

```
$ omd update
```

升级时，我们会被问几个问题来解决配置中的冲突。对我们来说，这主要是一些文件的文件权限的调整。这里你不能给出一个笼统的建议，哪个答案是正确的，但只要不是关于自己的调整，你通常可以接管新版本或新文件的权限。

使用`omd version`我们仍然可以检查是否使用了当前版本。然后，我们重新启动实例:

```
$ omd start
```

如果我们现在在浏览器(https://monitoring . server/my instance)中打开 Check_MK，我们可能会看到以下警告:

![](img/3e42dd4312cb844f07a69dc235e09ae9.png)

© [https://mathias-kettner.com](https://mathias-kettner.com)

当然，插件也已经更新或被更新所取代。因此，在接下来的几个小时里，将会反复出现关于不活动或消失的服务的警告。Check_MK 还警告不兼容的更改(见图片)。通过点击“显示未确认的不兼容的 werks ”,我们可以查看这些更改，并通过再次点击“确认所有”来接受它们。

# 添加时差/材料管理插件

我们使用[一个插件](https://github.com/rmblake/check_mk-slack)在出现问题时向我们的 Mattermost #监控通道发送消息。插件没有被转移到新的 Check_MK 版本，所以需要一些手工操作。我们必须将插件复制到正确的位置，并调整权限。

```
$ cp /opt/omd/versions/1.2.8p13.cre/share/check_mk/notifications/slack /opt/omd/versions/1.4.0p26.cre/share/check_mk/notifications/slack
$ chmod +x /opt/omd/versions/1.4.0p26.cre/share/check_mk/notifications/slack
```

如果尚不可用，我们还必须安装以下软件包:

```
$ apt install python requests
```

现在该插件再次出现在 *WATO →通知*下。

# 结论

升级后，Check_MK 再次平稳运行。Check_MK 是[一个伟大的项目](https://openschoolsolutions.org/5-great-open-source-apps-we-use-in-our-school/)，它帮助我们密切关注我们的网络、[我们的防火墙](https://openschoolsolutions.org/pfsense-monitoring-check-mk/)和我们的设备。因此，我们通常可以在别人注意到问题之前发现问题🙂

**您使用什么软件来监控您的 IT 基础设施？**

*原载于*[*openschoolsolutions.org*](https://openschoolsolutions.org/update-check_mk/)*。* ***注册*** *订阅我们的时事通讯，获取免费的 PDF 格式的开源应用程序，供您的课堂使用，或者在 Twitter 上关注*[*@ OpenSchoolZ*](https://twitter.com/OpenSchoolZ)*。*