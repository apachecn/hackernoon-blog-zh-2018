# 抵抗是一种服务

> 原文：<https://medium.com/hackernoon/resistance-as-a-service-a6a9cd14192f>

## 探索一种商业模式，在这种模式下，初创企业向整个政府发起挑战，要求它们比慈善机构更快地治愈世界。

![](img/28a5277878555e60ed4506fc4d1ad6bf.png)

Resistance as a Service *(image courtesy* of the *author).*

## 我的互联网是什么样的

嗨，我叫彼得。我来自俄罗斯。我用虚拟专用网，这样你就可以很容易地在 LinkedIn 和 Reddit 上找到我。你也可以用电报给我发短信，因为我用的是它的代理。

我有一套复杂的密码系统。比起 touch id，我更喜欢手机的 pin 码。我的磁盘被加密了。我用双底隐藏所有敏感数据是为了保护它不被小偷窃取，更重要的是保护那些发誓保护我不被小偷窃取的人。

是的，我有点偏执，但是……人们仅仅因为喜欢和转发就被关进监狱。所以我想我真的有被逮捕的危险，因为我要在这里讨论的那种事。没有什么能阻止警官“使用”我的手指(不，不，不要和我分开，没那么糟糕)解锁我的手机并获取对我不利的证据。

尽管采取了这些措施，我仍在冒险。但我更看重自由和真理。

![](img/dadc81ab822b69726a991aa7f5480148.png)

Telegram proxies

## 我不是独一无二的

一点也不。在俄罗斯——我相信在许多其他威权领导的国家也是如此——几乎每个与互联网打交道的人(意思是每个人)都感到有点受限。感到自由的唯一方法是使用上述的一些或全部方法。

一方面，情况越来越糟。服务被禁止。Telegram、LinkedIn、archive.org、deviantart.com 和许多其他看似随机的网站，比如 PornHub 和 Reddit 的新设计(old.reddit.com 很好——试着找出标准！)都是俄罗斯没有的。

从另一方面来看，这是一种技术上的回应。初创公司已经在提供帮助。有几十亿人想使用他们国家无法提供的服务。拥有不可阻挡的技术的公民开始反对他们自己的政府。

我相信这里隐藏着一个巨大的市场，被企业家(就收入而言)和政府(就威胁而言)低估了。在这篇文章中，我想从一个企业家的角度来探索它的机会。

# 电报示例

今年发生在俄罗斯的电报事件是科技与政府斗争的一个很好的例子。这场战斗让 Telegram 变得更加先进，带来了一大批新用户，并让他们变得超级忠诚。它把人们心目中的电报转移到另一个层面，现在把它与抵抗、自由和希望联系在一起。

2017 年 10 月 FSB(俄罗斯联邦安全局)[要求 Telegram](https://www.bloomberg.com/news/articles/2018-03-20/telegram-loses-bid-to-stop-russia-from-getting-encryption-keys) 交出用户的加密密钥。该公司拒绝了，支付了 14000 美元罚款，并发现自己陷入了长达半年的徒劳争论。

FSB 凌驾于宪法之上。他们不在乎言论自由和匿名交流的权利。他们只需要加密密钥。FSB 高于逻辑。他们不在乎端到端加密意味着电报甚至没有密钥。他们只需要加密密钥。否则他们会关闭俄罗斯的电报。

2018 年 4 月 10 日看到论证毫无用处，杜罗夫后退一步，决定以“官方”回应的形式将“密钥”发送给俄罗斯联邦安全局局长:

![](img/4c7fba167f5742d0745fef51e0c92871.png)

The joke originates from a [third party](https://twitter.com/dmitrosel007/status/983698678425767936?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E983698678425767936&ref_url=https%3A%2F%2Fmeduza.io%2Fshapito%2F2018%2F04%2F10%2Fnapravlyayu-vam-klyuchi-2-sht-ot-telegram-kak-moglo-by-vyglyadet-pismo-pavla-durova-v-fsb). But as Telegram team [helped to promote it](https://vc.ru/flood/36107-klyuchi-ot-telegram-dlya-fsb), it became the only “official” response Russian Internet community is aware of.

不到一周前，另一只俄罗斯官僚巨兽——媒体监督机构 RosKomNadzor——开始屏蔽电报。战斗开始了。RosKomNadzor 每天阻止数百万个 IP，Telegram 创建更多。

在最初的几天里[，1700 万个 ip 地址被封锁](https://www.wired.co.uk/article/russia-google-telegram-ban-blocks-ip-address)。俄国电报用户的数量是[1300 万](https://www.theguardian.com/world/2018/apr/17/russia-blocks-millions-of-ip-addresses-in-battle-against-telegram-app)。比那少 400 万！另一个事实——1700 万是世界上所有[可能的公共 IPv4 地址](https://stackoverflow.com/a/2437185/6650003)的 0.5%！

一个人不可能关闭 0.5%的互联网而不承担后果。首先，亚马逊 AWS、数字海洋和其他外国主机服务托管的许多“好”俄罗斯网站都关闭了。第二，谷歌、Viber、脸书——都开始停电，但或多或少保持可用。然后阿萨纳(任务经理)、达什莱恩(密码经理)、特雷罗、npmjs.com 都倒下了。这是我开始使用 VPN 的时候，只是为了继续我的慈善区块链项目。随后是全面崩溃。互联网变得毫无用处。不仅仅是互联网。人们抱怨自动取款机和信用卡终端不工作。在远地点 RosKomNadzor [甚至挡住了自己！](https://www.mk.ru/social/2018/04/17/izza-telegram-roskomnadzor-zablokiroval-25-mln-adresov-i-rukhnul-sam.html)

![](img/3a4482730b07098c9af4f9e4c5d0aa4a.png)

RosKomNadzor [community revised](https://vk.com/durov?z=photo1_456306797%2Fphotos1) logo

两周前，企业预计损失 20 亿美元。RosKomNadzor 最终听取了俄罗斯各大媒体公司的意见，恳求其冷静下来。互联网逐渐变得可用。尽管如此，我仍然无法想象自己没有 VPN。

![](img/e52609b85fd3511f551ef75225bea844.png)

From Durov’s [official vk.com profile](https://vk.com/durov?z=photo1_456317315%2Fphotos1)

正如人们经常听到的那样，RosKomNadzor 破坏了互联网。但是电报断了吗？嗯，有一天我有大约两个小时不能上网。可耻。

## **这场战斗的后果**

我认为俄罗斯政府发动这场战争犯了现代史上最严重的错误之一。他们破坏了互联网，损害或摧毁了商业，浪费了税款。一切都是为了与一些虚幻的恐怖分子战斗(但被视为控制大众的战斗)。哪一个更重要——他们输掉了这场战斗。不出所料，这导致了抗议，并使我们与他们之间已经越来越强烈的对抗更加强烈。

![](img/f68928afb11e7f659f2775510c391cc5.png)

[People made paper planes](https://www.reuters.com/article/us-russia-telegram-protest/protesters-demand-russia-stop-blocking-telegram-messenger-app-idUSKCN1IE0NG) and flew them into the air through out the country demanding Telegram blocking to stop (the protest was organized through Telegram). [REUTERS/Tatyana Makeyeva.](https://www.reuters.com/article/us-russia-telegram-protests/paper-plane-protesters-urge-russia-to-unblock-telegram-app-idUSKBN1I11O5)

## 不仅在俄罗斯

技术发展如此之快，没有一个政府能够跟上它。最聪明的人会合作，那些试图对抗技术的人会失败。创业公司更有竞争力，永远领先。杜罗夫理解这种不对称。他有能力看到他的应用程序的健壮性。他认为与官员的谈判没有积极的结果。他看到了俄罗斯反对派领导层的真空。他利用这种情况，用“钥匙”嘲笑官员，组织纸飞机运动，推广挑衅性的艺术作品，并敦促人们进行“数字抵抗”。

![](img/61a8b2748f419620b0f224f7a37e751d.png)

Durov [proposes](https://lenta.ru/news/2018/04/16/digital_resistance/) Digital Resistance logo.

一个年轻聪明的弃儿对抗整个政府。你能想象出更好的公关活动吗？

俄罗斯政府在技术方面的能力很差，只是成功地创造了一个重要的先例。这表明我们拥有他们无法与之抗衡的东西——不可阻挡的技术。

# **其他企业家如何受益？**

帮助 Telegram 生存下来的一个特征是它的 MTProto 代理。它允许任何人创建一个电报代理，并邀请广告商。反过来，广告商可以将他们的聊天添加到用户的聊天列表中。使用代理时，用户不能取消订阅此聊天。通过这种方式，用户可以用他们的注意力来获得交流的自由。电报获得无限的新代理来源。

![](img/137ff2c695cdb0ba020517bff34982df.png)

Notice the Proxy Sponsor mark at the very first chat. The check-marked shield at the top means the proxy is active.

一家外国公司提供服务来抵抗现政权。政权对此无能为力。人们愿意为此买单。从一个遥不可及的司法管辖区向基本人权提供不可阻挡的技术——听起来像是一种商业模式！

为了便于进一步交谈，让我们为它创造一个术语。

自由即服务太悲观了，因为它暗示你已经失去了自由。革命作为一种服务更接近，但太浪漫，并建议失去自由。在你失去自由并不得不通过革命重新获得自由之前会发生什么？你反抗。我认为抵抗即服务(RaaS)是最好的选择。

让我们保持定义的简单和宽泛，不要限制我们进一步的思考。**RaaS——一项提供基本人权的服务。**

现在我们有了一个术语，让我们进一步探索商业模式。我们仍然是从一个企业家的角度来看。我们需要理解机会，也许还需要产生想法。

## 需求在不断增长，而且是自发的。

在维基百科上按国家查看[互联网审查和监控](http://Internet censorship and surveillance by country)，并尝试估计这些国家的人数。有几十亿人。需求已经很大了。

而且需求还在增长。任何政府都必须捍卫自己的主权。外国影响是一种威胁。防御线是通过通讯手段挖成的。但这些是人们和企业运作的相同手段。推动太少，你可能会失去*一些*让外国思想渗入的权力。逼得太紧，你会发现自己在拖慢经济，让人们沮丧。这可能导致以更戏剧性的方式失去所有的力量。

务实的解决办法是放松下来，停止争斗。将庞大的战争预算转用于社会项目。做好事是最划算的策略，因为一个人无法影响一个幸福的国家。

但是权力最重要的目标——是保持权力。暴虐的国家往往会转向战争。他们打得越多，反抗的要求就越多……这就是我们在俄罗斯看到的——正反馈循环。反抗的需求是自我产生的。

## **更多势不可挡的技术即将到来。**

RaaS 可以通过现有的 VPN、torrents、 [IPFS](https://ipfs.io/) 和各种点对点技术实现。但是真正不可阻挡的技术即将到来。

以区块链为例。随着权力下放、信任、安全，有时还有现成的隐私，区块链获得了一个关键的成功驱动因素——激励。人们实际上可以通过保持系统安全来赚钱。[采矿](https://en.bitcoin.it/wiki/Mining)就是一个最简单的例子。Telegram 及其 MTProto 代理创建了一个平台，让广告商与代理提供商见面。区块链会释放出各种看不见的互动。杜罗夫已经顺便带着他的[电报公开网](https://en.wikipedia.org/wiki/Telegram_(service)#Telegram_Open_Network_ICO)到了。

![](img/1b1d3d9fab573a0b11e417914d9b373d.png)

Credits: Project Loon

考虑卫星或平流层互联网。可能会非常接近，因为 Starlink、T2、Project Loon 和 T4 的 OneWeb 都在努力实现这一目标。如今，政府只能通过伤害自己来阻止数字抵抗。在不久的将来，将没有任何机会。除非有人考虑发射 650 颗卫星。

把开放的硬件 DIY 设备加起来，你就有了一个相当不错的原始技术大杂烩。

这种技术的结合是如此强大，坦白地说，这让我感到害怕。我可以想象一个政府无能为力(甚至无用)的世界。不管你有多自由，你都不希望政府垮台。让我们逐渐取代它的功能。

## **竞争对手。没有纯粹的 RaaS 解决方案。**

有一种 [VPN](http://www.suissl.com/) 以宣传未经审查的互联网为主要特征。还有 [Lantern](https://getlantern.org/) ，这是一个点对点的网络工具和由它驱动的 [FireTweet](http://firetweet.io/) (“解封”twitter)。还有信访平台[change.org](https://www.change.org/)。这些可能被视为纯粹的 RaaS 解决方案。

但是通常审查规避和 RaaS 是副产品。或者某项服务仅适用于特定人群。取决于你在哪里，你在做什么。

你可以使用 Torrents 或 IPFS 下载免费的音乐和视频，也可以分享你所在国家禁止的书籍。你可以避开电视网的广告，或者你可以建立一个抵制审查的反对网站。你可以使用 VPN 在公共 WiFi 上保持安全，或者你可以联系来自中国的脸书上的某人(或者享受来自俄国的 Reddit 的新设计！).

纯粹的 raa 数量如此之少，这本身就是一个机会。

![](img/39af931e5055715b011ae3b4cb6cffdc.png)

Created with photofunia.com

## **想法。RaaS 不仅仅是一个互联网接入。**

我们将 RaaS 确定为一种提供基本人权的服务。这听起来可能有点晦涩，尤其是如果你不需要努力就能获得这些权利的话。

这里有一些例子，让你弄清楚哪些人权可以外包(也许 RaaS 更好的定义是外包政府职能？).抱歉，这份清单又长又悲观:

*   一个巨大的恶霸不断殴打酒吧游客，保安不敢阻止他，因为他是警察
*   一名妇女试图为她的儿子在制裁下获得药品，最终作为一名毒贩入狱
*   你上个月赚的钱现在还不到一条面包的价值
*   40 名儿童[死于购物中心火灾](https://www.thenational.ae/world/europe/forty-one-children-died-in-russian-mall-fire-1.716429)，没有官员受到惩罚(真他妈疼)。
*   你从战场回来，发现有人占据了你的房子，没有记录证明它属于你
*   你不信任选举(或者你别无选择)
*   你没有身份
*   你不能上网(没有设备，没有线路)

这些在世界许多地区仍是未解决的问题。有了技术和能力，解决方案可能会很简单，甚至有利可图。这听起来可能有点愤世嫉俗，但也许这是让人快乐的最快方法。

也许 RaaS 可以解决慈善机构和非营利机构无法解决的问题。也许这是值得尝试找出一个营利性的解决方案？尝试提出新的问题。我们如何提供基本人权服务？或者——换个思路——我们怎么把政府职能外包出去？RaaS 能比慈善更有效率甚至更有道德吗？

这些问题可能会带来很多想法。需求是巨大的，而且还在增长。资源丰富。竞争很少。最重要的是，这是一个让世界变得更美好的大好机会。

# **反抗不死**

如果你是一个安全地方的创业者，喝杯咖啡，好好想想。你可能有技能和资源去帮助很多人。

> 坏人需要更多的是到*完成他们的目标*，*而不是*好人需要*旁观*和*无所作为——约翰·斯图尔特·米尔*。

如果你是一个暴虐国家的公民。记住，一个希望破灭后，总会有另一个希望。反抗。