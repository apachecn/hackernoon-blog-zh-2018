# IPsec 中发现的安全漏洞

> 原文：<https://medium.com/hackernoon/security-gaps-found-in-ipsec-5a075b44609e>

![](img/dd1ddc0d3363f17f6bfc3f97aea648bf.png)

你可能听说过[互联网](https://hackernoon.com/tagged/internet)协议[安全](https://hackernoon.com/tagged/security) (IPsec)被许多人称为最安全的数据加密方法之一。

它由几个安全协议组成，这些协议通过 IP 网络发送数据包，使数据包隐藏起来，第三方无法访问。

但是最近的研究警告说，即使 IPsec 也有它的缺陷。

# 黑客可以解密 IPsec 信息

通常，当人们使用不安全且可公开访问的门户时，如浏览互联网时，IPsec 确保加密安全的通信。

然而，一组研究人员[发现了大量与名为“IKEv1”的互联网密钥交换协议相关的安全漏洞](https://phys.org/news/2018-08-gaps-internet-protocol-ipsec.html)当双方在通信期间定义和交换共享密钥时，IPsec 维护双方之间的加密连接。

在调查此事的过程中，该团队成功地策划了一场名为“布莱肯巴赫攻击”的事件。它发明于 2008 年，涉及有目的地用错误填充编码信息，然后重复发送该内容到服务器。

然后，黑客可以研究服务器的回复，并获得关于加密信息内容的越来越准确的情报。最终，渗透者获得足够的信息来虚假地假定通信方之一的身份并窃取数据。

研究人员意识到这个问题影响了由 Clavister、Zyxel、Cisco 和华为制造的硬件，并立即联系了这四家公司。所有人都已经修复了这个问题，Zyxel [在其网站的支持部分发布了关于这个问题的内容](https://www.zyxel.com/support/bleichenbacher_attack_vulnerability.shtml)，并鼓励客户更新他们的固件。

澄清一下，这个漏洞不是一个 bug，但它与制造商如何在他们的设备中实现 IPsec 协议有关。

因此，这是一个可以避免的实现错误。同样值得注意的是，入侵者必须先进入网络，然后才能成功利用现在已经修复的漏洞[。](https://www.helpnetsecurity.com/2018/08/15/vulnerable-ipsec-ike/)

# 也存在与密码相关的问题

科学家们进行的进一步研究表明，另一个名为“IKEv2”的互联网密钥交换协议也存在安全缺陷 IKEv1 和 IKEv2 之间存在诸多差异[。后者是两者中较新的一个。尽管 IKEv1 被认为是过时的，但它仍然很受欢迎，甚至在较新的设备上使用。](http://www.differencebetween.net/technology/protocols-formats/difference-between-ikev1-and-ikev2/)

网络安全专家研究的第一阶段涉及使用 IPsec 的基于登录的加密模式。

然而，他们决定也检查与 IKEv1 和 IKEv2 协议的基于密码的登录相关的漏洞。密码认证需要哈希值，这类似于指纹。

当用户输入密码时，密码被散列并与存储的散列值进行比较。如果匹配，这个人就可以访问。然而，发现第一个漏洞的同一团队不久前进行的研究发现，IKEv1 和 IKEv2 协议很容易被黑客攻击，主要是当人们使用弱密码时。

他们的研究结果迫使研究人员建议，当人们通过基于密码的登录使用 IPsec 时，他们应该只[选择极其复杂的密码](https://us.norton.com/internetsecurity-how-to-how-to-choose-a-secure-password.html)。

# 增强您的密码

如果您需要帮助创建更好的密码，请使用大小写字母、数字和特殊字符的组合。此外，避免使用字典中的单词、宠物的名字或组成你的生日、周年纪念日或其他容易通过社交媒体或其他方式找到的信息的数字。

关于密码漏洞的更多细节，可以考虑[在研究人员的博客](https://web-in-security.blogspot.com/2018/08/practical-dictionary-attack-on-ipsec-ike.html)上阅读关于它的详细帖子。

在了解到这个问题后，该小组联系了计算机应急响应小组(CERT)。一旦研究人员将他们的发现提供给整个技术行业，该组织就协调回应并协助联系了 250 多个方面。

# 为什么这些启示如此重要？

在构建虚拟专用网络(VPN)时通常依赖于 IPsec。许多人使用这些网站来消费他们所在地区没有的内容。

但是，更重要的是，用户经常使用 VPN 工具或其他资源，以便在远程工作或出差时进行异地访问。

通过在访问公共互联网门户时通过 VPN 查看与工作相关的内容，他们理论上可以保护敏感数据的安全。不过，研究人员发现的问题在某些情况下可能会使数据不安全。

# 甚至长期存在的协议也不是完美的

这个网络安全专家团队证明，尽管 IPsec 是一种通过加密来保护基于互联网的通信的长期方法，但它也不是没有可能失败。

因此，主动和好奇的互联网安全专家正在进行的研究将继续有价值，因为新知识突出了以前未知的弱点。

*图像由* [*负空格*](https://www.pexels.com/photo/office-working-app-computer-97077/)