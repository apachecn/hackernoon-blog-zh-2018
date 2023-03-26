# 谷歌铸造设备一直敲你离线！

> 原文：<https://medium.com/hackernoon/google-casting-devices-have-been-knocking-you-offline-5541e90fa91d>

嗯……这解释了很多。对于所有谷歌 Chromecast 和谷歌主页的用户来说，这也能解释很多事情。

谷歌 Cast 设备导致了暂时的无线信号中断。Netgear、TP-Link 和华硕等公司的路由器已经确认了这些铸造设备的问题。在许多情况下，路由器在一段时间内完全不可访问。

那么是什么导致了这些恼人的 WiFi 信号丢失呢？这些设备应该每 20 秒左右只发送几个数据包，但在最近版本的 Android 中，连接到这些广播设备的应用程序有时会发送非常大的数据包。当这些设备被唤醒时，连接到这些设备的应用程序会发送多播域名系统(mDNS)发现数据包。一名 TP-Link 工程师甚至报告说，他们可以在很短的时间内高速发送多达 100，000 个包，并且该应用程序停止使用的时间越长，当它被唤醒时发送的包就越多。

这些 WiFi 掉线的原因是由于大量的数据包涌入您的路由器，导致它关闭与互联网或连接设备的连接。

那么解决办法是什么？Linksys 和 TP-Link 路由器所有者可以尝试更新他们设备的固件来解决这个问题，但是对许多人来说这是行不通的。目前你最好的选择是断开你的谷歌设备与网络的连接，直到谷歌解决这个问题。

[](https://www.pyler.io) [## Pyler.io -实时编码课程

### 在本课程中，我们将深入探讨 Python。如果你几乎没有编程经验，这个课程是为你准备的。本课程…

www.pyler.io](https://www.pyler.io) ![](img/65d0581c2635e776dcbcc8d43ad3ce72.png)

Slither into Python: An introduction for beginners