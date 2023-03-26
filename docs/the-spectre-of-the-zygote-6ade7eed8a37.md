# 合子的幽灵

> 原文：<https://medium.com/hackernoon/the-spectre-of-the-zygote-6ade7eed8a37>

## Spectre 和 Meltdown 如何影响移动设备安全

![](img/d9b18090dc518645c29181098a9173e2.png)

在 [CriticalBlue](https://criticalblue.com) ，我们一直在讨论新披露的 Meltdown 和 Spectre 缺陷及其对[设备安全性的影响](https://approov.io)。我们的首席技术官理查德·泰勒写了两篇博文，很好地概述了这些漏洞及其对移动设备安全的可能影响。[第 1 部分](https://hackernoon.com/you-just-need-to-speculate-to-exfiltrate-a093a31aa904)对这些攻击提供了清晰的解释，比大多数攻击更深入一些。第 2 部分，主要关注 Android 漏洞，包含在下面以帮助宣传。

在这篇博客的第 1 部分中，我提供了 Meltdown 和 Spectre 的概述，并且在这篇博客中，我着眼于对移动安全的潜在影响。

在本周的所有骚动中，英特尔似乎首当其冲受到了对此问题的愤怒。也许他们应该，我认为他们值得。从公共关系的角度来看，他们没有很好地处理,关注的焦点是几乎完全影响英特尔的崩溃。 [Google](https://googleprojectzero.blogspot.co.uk/2018/01/reading-privileged-memory-with-side.html) 在第一时间发现了问题，而 [ARM](https://developer.arm.com/support/security-update) 已经以及时和清晰的方式进行了沟通。然而，当这一切在几周内尘埃落定时，我相当怀疑谷歌和安卓(几乎完全在 ARM 内核上运行)将感受到 Spectre 的持续痛苦。

Spectre 的第一个变种是 Bounds Check Bypass([CVE-2017–5753](http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5753))，本质上允许编写具有可测量副作用的代码，即使它受到不应让它执行的特定条件检查的保护。这是因为该代码可能被推测性地执行，并在缓存状态中留下该执行的痕迹。这个漏洞对于网络浏览器来说尤其令人担忧，因为[已经证明](https://spectreattack.com/spectre.pdf)可以构建 Javascript 来触发这个漏洞，并允许读取网络浏览器本身进程中的任何数据。这可能包括在浏览器 Javascript 的沙盒环境中不应该访问的各种秘密信息。浏览器的修复程序已经发布或者正在进行中。幸运的是，在之前，我们已经在[进行过浏览器更新，自从 Android 5.0 以来，谷歌能够通过正常的应用程序更新机制，通过 Play store 自动向浏览器推送更新。因此，绝大多数设备将快速接收浏览器更新，无需用户参与。然而，这个](https://www.howtogeek.com/208853/warning-your-android-phone%E2%80%99s-web-browser-probably-isn%E2%80%99t-getting-security-updates/)[可能](https://developer.android.com/about/dashboards/index.html)仍然会让高达 20%的安装基础滞留在旧版本上，这些旧版本可能根本不会针对这个严重的 bug 获得更新。

然而，特别值得关注的是 Spectre bug 的第二种变体，分支目标注入([CVE-2017–5715](http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5715))。您应该记得，这是一个使用分支目标缓冲区(BTB)中毒来强制特定的间接分支跳转到攻击者控制的推测小工具的程序，该小工具旨在将一些秘密信息泄漏到缓存状态中，攻击者稍后可以读取这些信息。攻击者需要知道代码及其所在的地址，以便生成正确的 BTB 中毒，并知道受害者进程代码中已有的合适推测小工具的位置。
这个特殊的问题有机会对 Android 产生持久的影响，原因我认为还没有得到充分的讨论:

*广泛的 ARM 漏洞*:根据他们的[安全更新](https://developer.arm.com/support/security-update)，这个 bug 存在于各代 ARM 内核中。该攻击导致大量的推测周期，因为它与在流水线早期发生的分支预测相关联。因此，代码序列有足够的机会在缓存中留下足迹。

*推送系统更新*:谷歌在这方面做得越来越好，但从历史上看，安卓生态系统在向所有用户推送补丁方面表现不佳。谷歌 Android 设备已经有了[补丁](https://support.google.com/faqs/answer/7622138)。各种 OEM 厂商和不受支持的设备非常复杂，用户不愿意按系统更新按钮，设备没有足够的磁盘空间来下载更新。我认为底线是，将会有非常多的 Android 设备永远不会得到补丁来修复 Spectre。一些未打补丁的设备可能会存在几年。苹果在 Spectre 上也有类似的问题，但在他们严密控制的生态系统中，他们在让用户升级上要成功得多。

*应用*:当用户下载一个应用时，他们正在运行可能是恶意的本地代码，并可能试图利用 Spectre 从设备上的其他应用窃取数据。由于支持本机代码，因此可以运行非常精确的代码序列。谷歌正在尽他们的[最大努力](https://source.android.com/security/reports/Android_WhitePaper_Final_02092016.pdf)通过静态分析寻找恶意应用，但是利用 Spectre 的代码序列都可以在运行中生成，并由一些后来的事件触发。正如进入游戏商店的各种[银行木马](https://blog.avast.com/mobile-banking-trojan-sneaks-into-google-play-targeting-wells-fargo-chase-and-citibank-customers)所证明的那样，很难发现这种事情。请记住，利用 Spectre 不需要任何奇怪的应用程序权限，只需要能够像任何应用程序一样运行代码。因此，如果用户下载了一个看起来运行正常的应用程序，它可能正在后台做一些更邪恶的事情。这一直是 Android 应用生态系统中的一个持续问题，但 Spectre 似乎进一步加剧了这种担忧。我们仍在处理在未打补丁的设备上使用 Dirty COW 的[漏洞利用](https://nakedsecurity.sophos.com/2017/09/29/android-malware-zniu-exploits-dirtycow-vulnerability/)。

*合子(* [*再次*](https://serializethoughts.com/2016/05/25/security-implications-of-zygote-process-creation-model/) *)* :如前所述，为了利用 Spectre 的 BTB 中毒变种，有必要知道在不同进程中运行的代码的地址。不幸的是，所有在 Android 设备上运行的应用程序都在同一个地址共享大量的库代码。从节省内存和效率的角度来看，这一切都很好，但对安全性来说从来都不是好事。对于 Spectre 来说，这似乎有些灾难性。如果你下载了一个恶意应用程序，那么它可以立即知道你想要攻击的任何应用程序的确切代码内容和系统库地址。这些库中似乎有各种各样的站点保存着机密数据，例如在间接分支或调用之前的注册状态中要呈现的按键或字符。因此，攻击应用程序可以合成一种合适的 BTB 毒药，专门为指向合适的投机工具的分支调制。攻击者应用程序有许多公共系统库和框架可供选择推测小工具。此外，BTB 中毒更容易，因为攻击者可以使用他们想要的实际间接分支，并使其分支到选择的推测小工具。当训练时(因此这个小工具不是推测性的)，这可能会崩溃，但使用牺牲线程很容易解决。这比猜测 BTB 寻址是如何工作的，以及在攻击者的进程中排列将在同一地址别名的东西要容易得多。

那么，这给运行在未打补丁的设备上的敏感 Android 应用程序带来了什么安全问题呢？利用 Spectre 是非常困难的，但现在这种方法被披露，我毫不怀疑许多人会尝试。你会信任你的银行、支付、比特币应用，或者任何需要输入密码的对话框吗？所有这些问题可能会在未来几周内出现，但很明显，应用程序确实需要在不久的将来开始开发一些更好的对抗敌对环境的对策。我们已经在许多应用中有了复杂的根检测和防御。也许我们需要考虑幽灵对策太早了。

感谢阅读！有关移动 API 安全性的更多信息，请查看。

如果你推荐这篇文章(点击👏按钮)以便其他人可以找到它。