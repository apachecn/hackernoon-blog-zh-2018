# 破解 linux 全盘加密，luks 用 hashcat。

> 原文：<https://medium.com/hackernoon/cracking-linux-full-disc-encryption-luks-with-hashcat-832d5543101f>

![](img/a5d3cd90abf5e4c4bf0a4a6297d5ec77.png)

Luks (linux 统一密钥系统)和 dm-crypt 是 linux 和许多其他公司用来进行整个磁盘加密的标准。当正确实现时，暴力破解几乎是不可能的。然而，当使用一个糟糕的密码或共享密码时，它可能会严重失败。

对于这个例子，我们将使用标准流程和一个糟糕的密码创建我们自己的 luks 容器。然后我们将提取头部，并使用开源 hashcat，快速轻松地破解密码。下面有一篇关于 luks 如何工作的精彩文章，如果这个过程的具体细节对你很重要的话。

[](http://irq5.io/2014/11/19/bruteforcing-luks-volumes-explained/) [## LUKS 卷解释说

### 几周前，我们被迫重启一台服务器，因为它停止了响应。当机器…

irq5.io](http://irq5.io/2014/11/19/bruteforcing-luks-volumes-explained/) 

TLDR，给我下命令吧！

luks 处理主密钥和加密数据的方式是，通过 salt 和迭代循环传递主密钥，然后对该密钥进行加密，然后将散列存储在 luks 头中。然而，尽管因为主密钥被分解在文件系统中的所有标题上而几乎不可能从标题中恢复密钥，但是通过相同的 salt、迭代和密码过程发送密钥的猜测并比较最终结果是可能的。因此，我们需要的只是头文件，然后我们可以将头文件插入 hashcat，并通过密码列表运行它。

密码！该密码将在每次机器启动或每次装载容器时使用。手动输入充满熵的密码是一种痛苦，驱动器是“加密”的，所以它是完全安全的，让我们只使用一个蹩脚的密码…唉。

是时候抓住美味的标题散列了！

> DD if = test of = luks-header bs = 512 count = 4097

这也可以在一个块设备和一个文件上实现，dd 是一个非常灵活的工具。一旦我们有了头文件，我们就从驱动器或容器中获得了所有需要的数据。从那里，我们将加载我们的 1 个密码可信的密码文件，并继续破解容器。是的，知道密码就是作弊，但前提是用户重复使用了一个密码。

共享密码！当密码转储发生时，这些密码被丢弃在 pastebin 上，然后被永久存档。在一个网站上共享一个被破坏并被丢弃的密码将为该密码在密码列表中被重用敞开大门。这一切都归结于密码的复杂性，永远不要重复使用重要的密码。此外，变得可爱和用字母换数字并不能让密码变得更好。Hashcat 背后有一个奇妙的规则引擎，它也可以猜测这些排列。规则集中的内容足够再写一篇博文了。同时，如果你感兴趣的话，看看这里，祝你黑客生涯愉快！

[](https://www.4armed.com/blog/hashcat-rule-based-attack/) [## 如何使用 Hashcat 执行基于规则的攻击

### 在本文中，我们将演示如何使用 hashcat 执行基于规则的攻击来破解密码散列。为此…

www.4armed.com](https://www.4armed.com/blog/hashcat-rule-based-attack/) 

链接和来源！！

[](https://hashcat.net/hashcat/) [## hashcat -高级密码恢复

### 世界上最快和最先进的密码恢复工具

hashcat.net](https://hashcat.net/hashcat/) [](https://gitlab.com/cryptsetup/cryptsetup) [## 密码设置/密码设置

### Cryptsetup 和 LUKS -开源磁盘加密

gitlab.com](https://gitlab.com/cryptsetup/cryptsetup) [](https://gitlab.com/cryptsetup/cryptsetup/wikis/FrequentlyAskedQuestions) [## frequently asked questions Wiki crypt setup/crypt setup

### 1.一般问题 2。设置 3。常见问题 4。故障排除 5。安全方面。备份和数据恢复 7…

gitlab.com](https://gitlab.com/cryptsetup/cryptsetup/wikis/FrequentlyAskedQuestions) [](http://irq5.io/2014/11/19/bruteforcing-luks-volumes-explained/) [## LUKS 卷解释说

### 几周前，我们被迫重启一台服务器，因为它停止了响应。当机器…

irq5.io](http://irq5.io/2014/11/19/bruteforcing-luks-volumes-explained/) [](https://articles.forensicfocus.com/2018/02/22/bruteforcing-linux-full-disk-encryption-luks-with-hashcat/) [## 使用 Hashcat 强制 Linux 全磁盘加密(LUKS)

### 作者 Patrick Bell 这个演练将向您展示如何使用 hashcat 强制 LUK 卷，如何安装 LUK…

articles.forensicfocus.com](https://articles.forensicfocus.com/2018/02/22/bruteforcing-linux-full-disk-encryption-luks-with-hashcat/)