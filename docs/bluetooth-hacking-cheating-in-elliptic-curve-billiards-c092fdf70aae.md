# 蓝牙黑客:椭圆曲线台球作弊

> 原文：<https://medium.com/hackernoon/bluetooth-hacking-cheating-in-elliptic-curve-billiards-c092fdf70aae>

最近，来自 Technion 的以色列研究人员发表了一篇关于对易受攻击的蓝牙设备配对过程进行智能攻击的论文。该漏洞允许攻击者绕过蓝牙安全措施，成为中间人(MITM)来窃听甚至改变蓝牙连接的内容。此漏洞的根本原因是这些设备中椭圆曲线加密的实现有缺陷。

![](img/e6ac9e77f58765e5b10e5ceef764e036.png)

正如它经常做的那样，理解这个漏洞是了解事物如何真正工作的绝佳机会。因此，这篇文章并没有停止解释蓝牙漏洞，而是对椭圆曲线加密(ECC)的核心原则进行了人性化的解释，椭圆曲线加密是许多相关技术的核心，包括保护我们互联网通信的 SSL/TLS 协议和保护比特币和以太坊交易免受修改的 ECDSA 签名。

# 作为台球游戏的椭圆曲线密码

遵循 [Cloudflare](https://medium.com/u/a00e599743a7?source=post_page-----c092fdf70aae--------------------------------) 的 Nick Sullivan [博客](https://blog.cloudflare.com/a-relatively-easy-to-understand-primer-on-elliptic-curve-cryptography/)的术语，椭圆曲线密码(ECC)可以被描述为一个 bizzaro 台球游戏。

用方程 y = x + ax + b 描述的椭圆曲线就是我们的台球桌。

![](img/3ab43bd20a447ee94ed31c689e3ffd21.png)

A Schematic Elliptic Curve Plot (credit: CloudFlare)

在曲线上加两点，A 和 B，就是我们的台球击球。要将 A 和 B 相加，将球放在 A 点，并朝 B 点发射。当球击中曲线时，球要么直接向上(如果在 x 轴下方)反弹，要么直接向下(如果在 x 轴上方)反弹到曲线的另一侧，这就是结果 c。

![](img/849c950c65cd98db43832e4065775d2b.png)

Adding Two Points. A+B = C, C+A=D, A+D=E (credit: CloudFlare)

但是当你想“加倍”一个点，也就是给它本身加一个点(A+A)的时候会发生什么呢？你怎么能把球从 A 射向 A 本身呢？

为了做到这一点，让我们选择点 A '非常接近 A，并朝它开枪。当我们使 A '越来越接近 A 时，它们之间的连线就越来越接近 A 的切线。

![](img/ee7534666c1b3def7e37b3f96303a3f7.png)

Doubling the Point P on Curve E: Shooting in the Tangent’s direction (Credit: [SlideShare](https://www.slideshare.net/municsaa/mathematics-towards-elliptic-curve-cryptographyby-dr-rsrinivasan))

既然我们已经学会了基本动作，让我们来定义游戏规则。玩家一个人在房间里玩。当球被放在一个已知的点 P 上时，游戏开始，玩家可以任意选择向同一个点 P 连续射击多少次。当玩家完成时，另一个人进入房间，并试图猜测球被击打了多少次。事实证明，他发现这一点的唯一方法是一次又一次地重复游戏，直到桌子达到相同的状态。然而，预先知道他打算击打的次数的原始玩家可以有效地知道棋盘的最终状态，而不需要实际击打所有这些杆。因此，这个游戏实现了期望的密码术“陷门”特性:容易做到，难以撤销。

用更数学的术语来说，计算椭圆曲线上的 n*P 很容易。然而，给定 n*P 和 P，很难确定 n。

# 椭圆曲线 Diffie-Hellman:双打台球

椭圆曲线 Diffie-Hellman (ECDH)算法进一步扩展了这一概念，并使用“取消”击球次数的难度，以便在存在窃听者(夏娃)的情况下在双方(爱丽丝和鲍勃)之间创建共享秘密

![](img/c8f1bb233e8b6b324a6e11ccf589a6c3.png)

Alice, Bob and Eve (Credit: [etutorials](http://etutorials.org/Programming/Programming+.net+security/Part+III+.NET+Cryptography/Chapter+12.+Introduction+to+Cryptography/12.1+Cryptography+Explained/))

在这场双打比赛中，爱丽丝独自在一个房间里玩我们的台球游戏。她首先将球放在一个已知的点 p 上，然后击打她选择的 S₁时报(S₁*P)。当 Alice 完成时，她将桌子的照片发送给 Bob。根据图片，鲍勃把球放在他桌子上的同一个地方(S₁*P)，然后击打 S₂时报(S₂*(S₁*P)).与此同时，Bob 在另一张桌子上开始了一个新游戏，并点击了《S₂时报》( S₂*P ),向 Alice 发送了一张图片，以便她可以点击《S₁时报》。(S₁*(S₂*P))

通过这样做，Alice 和 Bob 分别到达了相同的决赛桌位置(S₁*S₂*P =S₂*S₁*P).他们现在可以使用这个最终点的 X 坐标作为他们的共享秘密。

在更正式的符号中:S₁,S₂被称为秘密密钥，而 S₁*P,S₂*P 被称为公开密钥，并表示为 Pb₁和 Pb₂.

在这个过程中，爱丽丝和鲍勃都没有了解到对方的个人秘密。更重要的是，Eve 不知道 S₁、S₂或最终的牌桌位置以及由此产生的共享秘密，尽管她可以访问游戏中间交换的牌桌图片。这些图片确实显示了游戏中桌子的状态，但是揭示 S₁或 S₂是不可能的，因为我们记得“取消”球被击打的次数是很难的。

![](img/bdba16b08396b5d9ca57a06e1eabe45e.png)

ECC Diffie-Hellman (credit: [Slideshare](https://www.slideshare.net/BaraniTharan14/elliptical-curve-cryptography))

# 夏娃反击了！

但是夏娃还没有完！尽管她不能仅仅通过窃听来获得共享秘密，但她可以主动篡改 Alice 和 Bob 发送的图片来创建错误的计算，这有望帮助她获得共享秘密。

蓝牙设计者意识到了这种风险。当两个蓝牙设备配对(即相互连接)时，它们使用 ECDH 来创建一个共享秘密，供以后用来保护通信的保密性和完整性。为了防止活跃的中间人篡改 ECDH 消息，增加了一层额外的保护来验证发送的公钥点(我们的比喻中的“图片”)的 X 坐标没有被攻击者改变。

然而，蓝牙协议并不强制要求验证 Y 坐标。这正是研究人员能够滥用的差距，以便完全破坏蓝牙配对过程的安全性。

Eve 可以滥用这一事实的方式是改变桌子的图片，并将球(公钥)放在桌子的另一种形状(另一条曲线)上的一个非常特殊的位置。用数学术语来说，Eve 正在将 Alice 和 Bob 交换的公钥的 Y 坐标归零。因为 Y 坐标没有被协议验证，所以 Eve 可以欺骗他们。

![](img/c04c647538fb23d36fce11234fa7f3c5.png)

The fiddled Public Key (Pb’) is placed on the X axis. Its tangent is a straight line

曲线的这个点有一个非常有趣的性质。通常当我们玩向同一点重复击球的游戏时，球以各种角度击中曲线，并随着每次击球改变其位置，而不会回到同一点。然而，当我们把球放在 X 轴上，并朝那个点反复射击时，游戏就变得非常乏味了。正如你回忆的，加倍一个点是在切线的方向上拍摄的，切线是一条直线(见上图)。这条线实际上从未与曲线相交，因此球需要“人工”停在一个名为“无穷远点”或∞的点上。下一次击球时，球被射向原点，并在那里结束。接下来的镜头只是重复这个过程。在每个偶数加法中，球到达∞，在每个奇数加法中，球落回到 X 轴。这就像是直角射击台球:由于它没有以一个角度击中曲线，所以它只能以一种非常可预测和无聊的方式再次以直角从桌子边缘反弹。

但是无聊和可预测正是 Eve 想要的！她用“修复过的”桌子的图片(pb₂'). pb₁')替换了桌子的原始图片(pb₂pb₁)如果两个 S₁,S₂碰巧是偶数，那么结果将是 S₂*Pb₁' = ∞ =S₁*Pb₂'.因此，配对将会成功，并将创建一个 Eve 知道的共享密钥！在任何其他情况下，即如果 s1 或 s2 是奇数，配对将作为 Pb₁'≠ Pb₂'≠∞.失败

因此，Eve 有 25%的成功率找到允许她窃听和操纵蓝牙流量的共享秘密。25%可能听起来很低，但由于受害者用户很可能在配对失败后重试配对，因此最终 Eve 将会成功。由于 Y 坐标在协议的其余部分没有被验证，Eve 的欺骗没有被暴露。

(研究人员详细描述了一种成功率为 50%的更复杂的攻击，但这是另一篇文章的问题)

# 最后的想法

实现加密是困难的，即使是细微的错误也可能会受到严重的惩罚。需要仔细检查实现，以确保它们确实符合最佳实践。这就是我们在 KZen Networks 公司开源我们的加密算法实现的主要原因。

[](https://github.com/KZen-networks/) [## KZen 网络公司

### GitHub 是人们构建软件的地方。超过 2800 万人使用 GitHub 来发现、分享和贡献超过…

github.com](https://github.com/KZen-networks/) 

另一方面，理解对实现的攻击可以帮助构建者不仅更好地理解正确的实现，还可以更好地理解东西实际上是如何工作的。

享受你的 ECC 台球！