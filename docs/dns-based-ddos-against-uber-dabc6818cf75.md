# 针对优步的基于 DNS 的 DDoS 攻击

> 原文：<https://medium.com/hackernoon/dns-based-ddos-against-uber-dabc6818cf75>

在过去的几周里，我一直在测试 DNS 解析器。

我一直试图找到最好的 DNS 提供商供个人使用，但主要是为了好玩和好奇。*嘿，谁不想知道 Quad9 阻止的* [*恶意域名*](/@nykolas.z/dns-security-filters-compared-quad9-x-opendns-x-comodo-secure-x-norton-connectsafe-x-yandex-safe-a00ace3bf21f) *是否比 OpenDNS 多，或者哪个提供商拥有最好的色情过滤器*[](https://hackernoon.com/porn-filters-compared-opendns-neustar-cleanbrowsing-norton-yandex-and-adguard-41f207062c4)**。**

*在这样做的同时，我还在公共云中设置了自己的 DNS 解析器来测试 PowerDNS 和 Bind。一切都很顺利，除了它们开始被误用和滥用，作为 DDoS others 的代理。*

## *放大 DDoS 攻击*

*最常见的 DNS DDoS 技术之一叫做 DNS 放大攻击。有人，又名坏人，欺骗他们的受害者的 IP 地址，并发出 DNS 查询作为他们。DNS 服务器响应受害者 IP 地址，而不是坏人。*

*放大和危险来自于 DNS 查询与响应相比很小的事实。因此，如果攻击者发送 1Gbps 的查询，受害者可能会得到 20Gbps 的响应。它把坏人的攻击放大了一大截。有点吓人。*

*为了隐藏他们的身份，他们通过开放的 DNS 解析器代理这些攻击，这就是我测试的内容。举个例子，这是我在绑定查询日志中看到的内容:*

> *03-Jan-2018 20:07:52.493 查询:信息:客户端**191 . 96 . 249 . xx**# 34418(isc . org):**查询:isc.org IN ANY+E**
> 03-Jan-2018 20:07:52.494 查询:信息:客户端**191 . 96 . 249 . xx**# 34411*

*这个坏家伙试图关闭 191.96.249.xx。因此，他在 ANY" 中发出对"*isc.org "的欺骗性查询，作为 191.96.249.xx。这迫使我的解析器向受害者 IP 发送该查询生成的大量响应。每秒数百次，直到我关掉它。**

*然而，Bind 有一种方法可以最大限度地减少滥用，并对每个源 IP 地址的 DNS 响应进行限速。经过一些研究，我在配置中添加了以下内容:*

> *速率限制{
> 每秒响应数 5；
> }；*

*这限制了每个源 IP 地址每秒最多 5 次响应。这可以防止坏人滥用我的服务器进行攻击，因为每秒 5 次请求不足以打倒任何人。*

*你可以在这里阅读更多关于 [DNS 扩增攻击的信息](https://www.us-cert.gov/ncas/alerts/TA13-088A)。*

## *随机子域 DDoS*

*然而，这并不是它的结束。今天，我开始看到一种新类型的 DDoS 攻击，如果不解析日志并通过 iptables 禁止 IP 地址，我仍然无法在 Bind 或 PowerDNS 上找到容易实现的解决方案。*

*受害者是**优步**。*

*有人，可能是另一个坏人或竞争对手，开始从随机的源 IP 地址中查询数百个在**uber.com**下的无效子域。这绕过了我的绑定配置的速率限制，并强制我的 DNS 解析器为这些无效的子域联系优步的 DNS 服务器。日志中是这样的:*

> *2018 年 1 月 11 日 13:01:22.356 查询:信息:客户端 51 . 15 . 57 . 237 # 37071(11–149 . Uber . com):查询:**11–149.uber.com**在一个
> 2018 年 1 月 11 日 13:01:22.357 查询:信息:客户端 51.15.57.237#37071 (15408.uber*

*每小时乘以几千就知道了。上面的日志可能很难看到，但坏人正在查询随机子域，如下所示:*

> *12–229.uber.com
> 16419.uber.com
> 246–154.uber.com
> 4399xiaoyouxidayouquan.uber.com
> 49739431z70y5970530741.uber.com
> 5755619.uber.com
> 6831130420.fls.uber.com
> 8ca16.uber.com
> bet 165 keyiwanma . Uber . com
> aomenguojibqaijale . Uber . com
> bet 3651 yuleechengkaihuxzhi . Uber . com
> biopc 311 . Uber . com
> cdn-m3 . Uber . com
> devpnp . Uber . com*

*它们都不存在，生成一个 NXDOMAIN。然而，DNS 解析器需要为它们中的每一个联系优步的 DNS 服务器。*

*我对日志进行了细分，这是它们平均每小时生成的数量:*

> *2018-1-11 下午 1:16 每秒
> 2018-1-11 下午 2:56 每秒
> 2018-1-11 下午 3:停止
> 2018-1-11 下午 4:停止
> 2018-1-11 下午 5:780 每秒
> 2018-1-11 下午 6:停止
> 2018-1-11*

*这只是我在公共云中的一个小小的解析器。他们可能滥用数百个开放的 DNS 解析器向优步的网络发出数以千计的虚假请求——要么关闭它，要么增加他们的运营成本。*

*在那之后，我将我的测试解析器限制为只有我的 IP 地址来阻止滥用。这种攻击有趣的一点是，它几乎没有放大价值。1 查询的字节大小与 NXDOMAIN 响应非常相似。*

*然而，它仍然可以在保持攻击者匿名的情况下将他们拿下，并真正使他们的 DNS 服务器承受压力。*