# 苹果修补影响所有 iPhone 和 Mac 设备上个人数据的漏洞

> 原文：<https://medium.com/hackernoon/apple-patches-vulnerabilities-affecting-personal-data-on-all-iphone-and-mac-devices-73e71acb3dc7>

iPhone 和 Mac 用户在新年伊始收到了一系列设备更新，大多数旨在关闭 Spectre 和 Meltdown 漏洞，这些漏洞可能允许未经授权访问他们设备上存储或运行的任何内容。英特尔认为允许此类利用的漏洞[不是缺陷，英特尔](https://www.intel.com/content/www/us/en/architecture-and-technology/facts-about-side-channel-analysis-and-intel-products.html#3)报告称，这些漏洞在该公司的 CPU 中发挥了应有的作用，要求制造商和操作系统提供商直接解决这些问题。用户应该更新到[最新的苹果安全版本](https://support.apple.com/en-us/HT201222)，以获得对 Spectre 和 Meltdown 的最佳保护。

# Spectre 是什么？

Spectre 是一种利用，它使用集成系统之间的软件授权的推测性访问来危害存储在设备上的数据，包括 iPhone X 和最新的 Mac 迭代。推测性访问让计算机芯片对信息将流经的路径做出有根据的猜测，然后丢弃接收到的多余数据。

作为 iPhone X 和类似设备的核心的英特尔核心系统具有一系列使用推测访问的[安全](https://hackernoon.com/tagged/security)检查，以确保不必要的访问更难获得，但 Spectre 使用这些检查的数据来获得对其他系统的访问，并访问先进的现代智能手机和台式电脑系统上的用户数据。这个漏洞是由谷歌 Zero 项目的 Jann Horn 和 Paul Kocher 发现的。

# 熔毁如何影响设备？

Meltdown 直接从操作系统到系统上的每个应用程序，而不是依赖 Spectre 使用的“防错”检查。这使得它有可能访问存储在设备上的任何数据，甚至允许远程操作应用程序和未经授权的执行。Meltdown 通过表现为操作系统正常功能的一部分，利用新形成的漏洞进入设备上可能可用的任何系统，从字面上消除了现有的安全检查。

这对 [iPhone FaceID 故障](https://hackernoon.com/my-younger-brother-can-access-my-iphone-x-face-id-is-not-secure-376c904f88bc)构成了类似的威胁，给予任何利用该漏洞的人完全的控制权，但更为阴险，因为它允许真正的操作系统级系统访问。Meltdown 最初是由谷歌的 Project Zero、Cyberus Technology 和格拉茨科技大学发现并报道的。

# 补丁修复了什么

苹果发布的补丁解决了许多被 [Spectre 和 Meltdown](https://spectreattack.com/) 利用的关键漏洞。这关闭了许多投机性访问，使英特尔 CPU 单元运行更快，执行系统更快，但它们也大大降低了安全风险。

最新的补丁通过 Meltdown 关闭了几乎所有已知的漏洞和攻击区域。截至 2018 年 1 月 9 日，一些 Spectre 漏洞仍然存在，苹果公司已宣布他们将继续发布补丁，以保护所有受影响的系统。推测性内存访问漏洞是英特尔 CPU 固有的，需要一些时间来纠正，尤其是芯片制造商显然不愿意直接解决这个问题。

# 剩余漏洞

最新的补丁更彻底地保护系统免于崩溃，但仍有许多关于 Spectre 的问题。由于英特尔硬件继续使用推测性内存访问，Spectre 和潜在的其他漏洞继续困扰着用户和操作系统提供商。

英特尔为负责任地披露和报道 Meltdown 和 Spectre 提供了奖金。bounty 系统仍然是评估计算机系统安全的最佳[技术之一](https://graduatedegrees.online.njit.edu/resources/mscs/mscs-articles/techniques-for-evaluating-the-security-of-computer-systems/)，苹果正在努力确保[未来发布的 iOS、macOS 和 tvOS 尽可能多地解决这些漏洞。](https://hackernoon.com/tagged/future)