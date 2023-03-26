# 黑掉你自己的路由器

> 原文：<https://medium.com/hackernoon/hacking-your-own-router-to-become-more-418e30cee83>

![](img/75c7e94758496e587f17f9059d2b1170.png)

> 这些说明仅用于在您拥有的路由器上安装合法授权的固件。不要将本指南用作违反任何专利或版权法的说明

有一种路由器被称为 T-Mobile (AC-1900 ),它是华硕 AC1900/68u 的更名产品。tmobile 路由器售价 70 美元，华硕路由器售价 130-170 美元。这两者之间有什么区别？除了固件什么都没有。

起初，修改固件可能有点吓人。但是在那第一块砖和第一次起死回生之后，一个人可以获得信心。慢慢来，把说明通读几遍，别忘了呼吸。

基础知识:路由器通常运行一套非常基本的软件。这通常在每次路由器启动时以相同的方式启动。大多数情况下，我们会中断路由器引导，并将其置于引导加载程序或恢复模式。这是运行一个单独的启动选项，通常存储在一个单独的内存芯片或一组特殊的内存地址。这允许我们在正常运行过程之外修改系统固件。不同的路由器有不同的方式进入这种模式，但最终都是一样的。这是一个独立的环境，通常只是工作的基本框架。

让我们开始，固件模块的第一遍。[https://www.bayareatechpros.com/ac1900-to-ac68u/](https://www.bayareatechpros.com/ac1900-to-ac68u/)读一下这个，然后我们可以回到这里。
将路由器转入恢复模式加载旧的 TM 固件。然后修改 cfe/MTD…等等，mtd 和 cfe 是什么？http://www.linux-mtd.infradead.org/doc/general.html[MTD 子系统(代表内存技术设备)它为原始闪存设备提供了一个抽象层。当使用不同的闪存类型和技术时，可以使用相同的 API 提供对原始闪存的 I/O 访问。它们支持许多`ioctl`调用，用于擦除 eraseblock、将它们标记为坏块或检查 erase block 是否坏、获取有关 MTD 设备的信息等。”通用固件环境。](http://www.linux-mtd.infradead.org/doc/general.html)[https://en.wikipedia.org/wiki/Common_Firmware_Environment](https://en.wikipedia.org/wiki/Common_Firmware_Environment)“它是一个灵活的 CPU 初始化和引导代码工具包，用于嵌入式处理器(通常运行在 Broadcom SoCs 中的 MIPS32/64 指令集 CPU 上)。大致类似于[BIOS](https://en.wikipedia.org/wiki/BIOS)’”。接下来，我们修改 CFE，以便使用正确的 mac 地址范围，而不是 TM 版本。这允许安装普通的华硕固件，因为固件只能在特定的 mac 地址范围内运行。

修改后的固件已经启动并运行，棒极了。哎呀，我们升级了路由器，它恢复到 TM 锁定固件。这通常是不经意间发生的，比如不注意，想“嘿，我应该升级了”。坚果，他们还在固件本身禁用了 telnet 和 ssh。而不仅仅是通过隐藏和禁用标签隐藏在用户界面中。看起来他们开始努力阻止我们修改固件了。不知道他们为什么这么努力地想把我们锁在外面，但是好吧。
现在我们如何获得路由器的终端？嗯，有一个网络工具页面，本质上是一个开放的终端。我猜他们认为如果你登录到路由器，他们应该允许任何事情发生。另一种情况是，“如果他们通过了我的防火墙，那么我的内部网络就完蛋了。”(叹气)可悲的是，这种情况在整个科技行业经常重复出现。

经过一番搜索，我发现了这个谷歌文档，它将有助于解释我们将如何入侵我们的路由器。[https://docs . Google . com/document/d/1 nszmomomj 70 zmmoakqjxbtvkytapjptwtpqi 1td 5n 8/edit？我不打算重复已经列出的所有内容，但我会跳过一些有趣的部分。](https://docs.google.com/document/d/1NsZMONmJ70zMmoAKKQJXbTVKytaPJptWTpqih1TD5n8/edit?usp=sharing)

首先，我们需要把需要的文件放到 usb 上。一旦我们将它们全部拉上，我们将把 usb 驱动器插入路由器的背面，并利用内置的 usb 支持。然后，使用我们之前讨论过的网络工具窗口，我们将利用表单的一个代码缺陷。因此实际上是通过关闭网络命令然后运行我们的命令来入侵我们的路由器。
如果我们打开开发者工具的控制台部分，比如 chrome，一旦我们进入页面。我们可以这样做。

> valid form = function(){ document . form . system cmd . value = " ping \ n mount-t tmpfs tmpfs user rpm "；返回 true}

在这里，我们让网络工具运行 ping 命令，然后转到一个新行，因为我们有一个 web shell，所以我们可以运行 mount 命令并装载 usb。然后还需要运行几个命令，让我们重新启动路由器上的服务。这与我们在 linux 机器上做的方式非常相似。

> valid form = function(){ document . form . system cmd . value = " ping \ n service restart _ httpd "；返回 true}

我们再次使用 ping 命令，然后重新启动 httpd 服务，我们稍微调整了一下以完成我们的投标。然后，一旦服务重新启动，我们做一些更多的安装命令和查找，以便从安装的 usb 抓取更多的文件。最后，我们运行了一个可怕的命令，这个命令绝对不允许从 webshell 中运行，但是他们把这个功能留在了那里，所以我们要利用它。\_(ツ)_/

> valid form = function(){ document . form . system cmd . value = " ping \ nmtd-write 2 FW _ RT _ AC68U _ 30043763626 . Trx Linux "；返回 true}

在这里，我们正在重写固件到一个我们播种。哎呀/厉害！好吧，接下来还需要一些清理步骤来完成 mod，并做好维护固件的准备。真正有趣的想法是，也许路由器制造商应该花更少的时间和精力来阻止修改者在现场篡改硬件，而花更多的精力来保护软件本身，因为如果你可以从 gui 做到这一点，你最好相信那里存在潜在的安全漏洞，这会让更糟糕的事情发生。这可能是对特定操作的提升运行级别的一个简单的修复，也许是验证 webform/shell 的输入，以防止额外的命令流过……但是，让我们浪费时间吧，钱花在哪里呢？可悲的是，这只是又一家路由器制造商，生产出一些硬件，留下一堆软绵绵的软件。但是如果没人看，就没人会发现问题，对吗？我希望每个人都喜欢路由器黑客 101–103 的旋风之旅，直到下次。相同的黑客时间相同的黑客频道。

脚注:
旧固件修改教程 t mobile:【https://www.bayareatechpros.com/ac1900-to-ac68u/ 

硬件:[https://www . Amazon . com/T-Mobile-Wireless-AC 1900-Dual-Band-ai protection-Complete/DP/b 01 mytaurw](https://www.amazon.com/T-Mobile-Wireless-AC1900-Dual-Band-AiProtection-Complete/dp/B01MYTAURW)

有深度分解的 Google doc
[https://docs . Google . com/document/d/1 nszmmoammj 70 zmmoakkqjbtxbtvkytapjptwtpqi 1td 5n 8/edit？usp =分享](https://docs.google.com/document/d/1NsZMONmJ70zMmoAKKQJXbTVKytaPJptWTpqih1TD5n8/edit?usp=sharing)