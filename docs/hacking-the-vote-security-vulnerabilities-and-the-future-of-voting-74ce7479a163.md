# 黑掉投票:安全漏洞和投票的未来

> 原文：<https://medium.com/hackernoon/hacking-the-vote-security-vulnerabilities-and-the-future-of-voting-74ce7479a163>

![](img/d18cc1f194c5e2657c05ffcd53134476.png)

Image courtesy: [Pixabay](https://pixabay.com/en/polling-station-poll-election-day-2643466/)

随着初选的进行，11 月即将到来，选民们担心黑客攻击以及它可能会如何影响投票结果。从个人选民信息数据库到投票机，整个基础设施仍然不安全。让我们看看几个主要的易受攻击点，以及可以采取哪些措施来加强安全性:

# 2016 年的投票

大量证据指向[俄罗斯黑了 2016 年选举](https://www.cnn.com/2016/12/26/us/2016-presidential-campaign-hacking-fast-facts/index.html)，但是他们到底做了什么？我们知道 21 个州的在线系统被俄罗斯人扫描过，但是实际上没有任何信息被窃取。

然而，伊利诺伊州的[遭到了攻击](https://edition.cnn.com/2016/08/29/politics/hackers-breach-illinois-arizona-election-systems/)，泄露了多达 20 万份个人选民记录，尽管没有记录被更改。与此同时，在亚利桑那州，一名选举官员打开了一封感染了恶意软件的电子邮件，泄露了该官员的身份。然而，系统本身并没有受到损害。

在更高的层面上，选举援助委员会，一个监管投票机安全的联邦机构，发现一个说俄语的黑客已经获得了 100 份 EAC 雇员证书并打算出售它们。八个州的选举系统提供商 VR Systems 也成为俄罗斯网络钓鱼骗局的目标。[七个目标账户中至少有一个遭到破坏](https://theintercept.com/2017/06/05/top-secret-nsa-report-details-russian-hacking-effort-days-before-2016-election/)。

这些是否会影响投票还没有定论；关键是这些系统仍然很脆弱。

# 数据库

一个威胁是黑客获得了选民信息数据库，就像在伊利诺伊州发生的那样。理论上，信息是可以改变的。[数据库管理](https://businessdegrees.uab.edu/resources/articles/area-of-interest-database-management-vs-project-management/)是这里的关键。不仅应该限制访问，而且密码应该是安全的，并经常更改以迷惑黑客。

# 投票机

# 黄貂鱼

继数据库之后，下一个安全问题是投票机本身。投票数据从投票机发送到中央制表计算机。问题是国土安全部透露，在华盛顿特区发现了大量流氓黄貂鱼，手机模拟器。他们不知道谁在操作这些设备。

黄貂鱼可以通过模仿手机信号塔来截取结果。结果可能会被改变，使投票偏向某个候选人或某项法案。

# DS200

DS200 是一种受欢迎的投票机，它可以光学扫描纸质选票。它是空气间隙的——没有连接到互联网——但它可以通过调制解调器传输非官方的结果。普林斯顿计算机科学家[安德鲁·阿佩尔告诉《纽约客》](https://www.washingtonpost.com/news/inspired-life/wp/2018/06/27/this-adopted-woman-scoured-the-country-for-the-sister-she-never-met-only-to-discover-she-literally-lived-next-door/?sw_bypass=true&utm_term=.646fa0ed2e72)说，问题在于没有任何东西可以阻止投票工作人员通过调制解调器发送官方选票。信息可以被加密，发送者和接收者都需要知道加密，黄貂鱼仍然可以截获数据。

虽然 DS200 制造商选举系统公司和 T4 软件公司声称这种情况极其罕见，但仍有可能发生。尽管如此，纽约、马里兰、弗吉尼亚和阿拉巴马禁止使用调制解调器传输选举结果。

# 选举

有 13 个州包含投票机完全不使用选票的县。这些是最脆弱的，因为无纸触摸屏机器是不安全的。例如，实际投票无法通过硬拷贝来验证。投票人可以触摸一个候选人的名字，但是机器可以记录不同的投票；不会有标记的副本来证明投票人实际上投票给了谁。这可能是偶然的，也可能是由于恶意代码的引入而发生的。这也不是一个假设，因为在宾夕法尼亚州的维南戈县有一台机器[“翻转”选票](https://www.nytimes.com/2018/02/21/magazine/the-myth-of-the-hacker-proof-voting-machine.html)，在触摸屏上触摸一名候选人会导致选票计入另一名候选人的选票。

更糟糕的是，没有实际的方法知道投票是否被真正计算在内。同样，由于没有纸质副本来支持投票，机器可以告诉选民他们的选择被记录下来，但随后会放弃全部或部分投票。

2017 年，由于这些担忧，弗吉尼亚州的 22 个投票站改用了纸质选票，并取消了他们只有触摸屏的机器的认证。虽然只用纸张是最安全的选择，但也更费时。

对于选民和投票者来说，在安全性和易用性之间取得平衡是很棘手的。很明显，2016 年大选的俄罗斯黑客应该加入其他塑造历史的重大安全漏洞的行列，如泄露员工数据的索尼漏洞，或泄露 4000 万份信用卡和借记卡记录的目标数据漏洞。然而，除非投票数据和机器变得更加安全，否则[未来的](https://hackernoon.com/tagged/future)投票可能同样充满黑客攻击，可能会改变结果，改变选民的未来。