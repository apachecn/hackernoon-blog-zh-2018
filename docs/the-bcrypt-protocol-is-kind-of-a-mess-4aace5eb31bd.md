# Bcrypt 协议…有点乱

> 原文：<https://medium.com/hackernoon/the-bcrypt-protocol-is-kind-of-a-mess-4aace5eb31bd>

![](img/424d1b16905ce88f8e44aa21b2415610.png)

*在编写自己的 Bcrypt 库时，由于对当前 Java 事实上的标准实现 jBcrypt 不满意，我发现了许多围绕 bcrypt 协议的奇怪事情(请注意:不是底层加密原语‘Eksblowfish’)。*

B crypt 是一个密码哈希函数[，由 Niels Provos 和 David Mazières](http://www.usenix.org/events/usenix99/provos/provos_html/node1.html) 于 1999 年设计，作为 OpenBSD [](https://en.wikipedia.org/wiki/Bcrypt)的默认密码哈希算法而流行。与简单的[加密哈希函数](https://en.wikipedia.org/wiki/Cryptographic_hash_function)(像 SHA-256)相比，使用 bcrypt 的主要好处是开发者可以设置计算哈希的成本。这被称为[键拉伸](https://en.wikipedia.org/wiki/Key_stretching)，应该与任何(通常是脆弱的)用户提供的密码一起使用，以防止暴力攻击(即简单的猜测)。

## Bcrypt 简介

那么它是如何工作的呢？首先，您需要一个密码，并将迭代次数设置为一个介于 4-31 之间的对数工作因子，每增加一次就需要加倍计算。例如，它可能是这样的:

```
bcrypt("secretPassword", 8)
```

其可以输出

![](img/264f5aeb4b34bcfd13227a12f947e75a.png)

$2a$08$0SN/h83Gt1jZMR6924.Kd.HaK3MyTDt/W8FCjUOtbY3Pmres5rsma

这是 OpenBSD 定义和使用的[模块化 crypt 格式](https://passlib.readthedocs.io/en/stable/modular_crypt_format.html)。第一部分`$2a$`是协议版本标识符。历史上 bcrypt 曾经是`$2$`,但是因为当时没有人考虑 Unicode 处理，所以必须定义一个新版本。下一部分`08$`是传递给函数的工作因子。接下来的 22 个 ASCII 字符(原始 16 字节)`0SN/h83Gt1jZMR6924.Kd.`代表编码的 salt。大多数实现会自动为调用者创建一个 salt，这很好。这种措施可以防止彩虹表，即使用预先计算的常用密码哈希列表。

最后，最后 23 个字节或编码的 31 个 ASCII 字符是实际的 bcrypt 散列:`HaK3MyTDt/W8FCjUOtbY3Pmres5rsma`。这种格式对于存储密码散列特别方便，因为除了实际的密码之外，所有的参数都包括在内。

## 问题 1:非标准编码

bcrypt 的一个奇怪之处是，它使用非标准的 Base64 方言对 salt 和 hash 进行编码。这种方言被列在[维基百科](https://en.wikipedia.org/wiki/Base64#Radix-64_applications_not_compatible_with_Base64)的“Radix-64 应用”部分，并归于 [crypt(3)](https://man7.org/linux/man-pages/man3/crypt.3.html) ，现代用法仅限于 bcrypt。虽然它使用与标准 Base64 相同的字母表，但它被置换，使其与 [RFC 4648](https://tools.ietf.org/html/rfc4648#section-5) 不兼容。这使得实现它变得不必要的困难和更容易出错，因为几乎所有的编程语言都有 Base64 的 RFC 4648 实现或对它的支持。

## 问题 2:使用 23 字节而不是完整的 24 字节哈希

如前所述，几乎所有 bcrypt 实现都输出一个 23 字节长的散列。然而，bcrypt 算法通过使用密码派生的 blowfish 密钥加密三个 8 字节块来生成 24 字节的密码散列。然而，最初的参考实现选择截断散列输出，据传原因是将其限制在更易于管理的 60 个字符的长度内(如果你问我，这是一个奇怪的原因)。[共识似乎是](https://news.ycombinator.com/item?id=2654586)删除一个散列字节的问题并不是一个有意义的安全性降级，所以它仍然是从参考实现继承来的一个奇怪的东西。

## 问题 3:白皮书的衍生与参考实施

根据 [BouncyCastle Javadoc](http://javadox.com/org.bouncycastle/bcprov-jdk15on/1.53/org/bouncycastle/crypto/generators/BCrypt.html) 的说法，OpenBSD 参考实现衍生自[白皮书](http://www.openbsd.org/papers/bcrypt-paper.ps)中描述的算法:

> 与本文相反，键设置和盐设置的顺序是相反的:声明

There is no reference to a potential security vulnerability because of that (and I wouldn’t expect one). Since basically everybody copied the behavior of the reference implementation, the specification seems to be superseded.

## Issue 4: No handling for passwords longer than 56/72 bytes

According to the [白皮书](http://www.openbsd.org/papers/bcrypt-paper.ps):

> (…)key 参数是一个秘密加密密钥，它可以是用户选择的最长为 56 个字节的密码(当密钥是 ASCII 字符串时，包括一个终止的零字节)。

其他人[指出](https://security.stackexchange.com/questions/39849/does-bcrypt-have-a-maximum-password-length)该算法在内部将事物管理为 18 个 32 位字，总共 72 个字节(包括一个空终止符字节)。请注意，一个 UTF-8 字符可以占用多达 4 个字节的空间，这将在最坏的情况下将密码限制为 14 个字符。

对实际限制的混淆和缺乏对长密码做什么的规范会产生许多兼容性问题，也可能产生 bcrypt 的许多不同方言。

## 问题 5:许多非标准版本

如前所述，bcrypt 最初的版本`$2$`缺乏如何处理非 ASCII 字符的定义，所以最流行的版本`$2a$`解决了这个问题。但是当然其他的实现也有问题，所以 PHP 版本有一个`$2x$`和`$2y$`，还有 [talk 将原来的版本输出](http://undeadly.org/cgi?action=article&sid=20140224132743)转移到`$2b$`。这些版本更改中的大部分解决了特定实现中的错误，可能不适用于其他实现。然而，这使得不同系统(通常是 PHP 和其他系统使用的数据库，例如 [this](https://stackoverflow.com/questions/49878948/hashing-password-with-2y-identifier) )之间更难实现互操作性

## 问题 6:效率稍低的格式

输出格式

```
$2a$08$0SN/h83Gt1jZMR6924.Kd.HaK3MyTDt/W8FCjUOtbY3Pmres5rsma
```

显然已经过优化，便于用户阅读。解析起来也有点低效:首先整个字符串必须作为 ASCII 读入，然后必须逐个字符地解析，直到最后的`$`。之后，接下来的 22 和 32 个字符被分别解码。使用更紧凑的消息格式，并且只编码一次，例如:

![](img/9a98c3dc2fe3fa1cf32ae8106a4ad3b2.png)

存储需求从 60 字节减少到约 56 字节(Base64 编码)。这在大多数用例中是不相关的，但是在存储数百万或数十亿个密码散列的授权范围内，这可以稍微减少存储需求和解析计算时间。

## 问题 7:没有官方的测试载体

当试图实现 bcrypt 时，开发人员面临着缺乏官方测试向量(也称为测试用例)来验证算法的问题。除了一些随机的测试案例，无论是白皮书还是谷歌搜索都没有发现很多有用的例子。这使得很难验证与其他实现的兼容性。这实际上是我张贴我自己的一些的原因，试图捕捉大多数边缘情况；他们可以在这里找到。

## 问题 8:不太适合作为密钥派生函数

OpenBSD 上的 Bcrypt 是为密码存储而设计的。然而，很多时候需要从用户密码创建秘密密钥，这需要密钥导出函数(KDF)。缺少的两个属性是设置任意 out 长度以满足不同键类型的能力，以及只输出原始散列而不输出整个消息格式的能力。目前，需要解析散列消息的最后 31 个字符，然后用适当的 KDF 扩展它。 [HKDF](https://en.wikipedia.org/wiki/HKDF) 。

**注意** : Bcrypt 不是 KDF 不一定是 Bcrypt 的问题(它从未声称是)。然而，开发人员经常在没有真正了解潜在问题的情况下误用它。提供一个选项来使用 KDF 将有所帮助。

# 结论

缺乏权威的强有力的规范、年龄以及许多实现中的各种怪癖和错误使得很难正确地实现这个经过时间考验的密码散列函数。

安全问题很难解决。因此，即使有些问题看起来很挑剔(也许确实如此)，也没有理由去追求最简单、最直接的实现和 API，从而得到可以用作实现基础的经过评审的规范。相信我，我也经历过。

***只是重申一下*** :我不挑战底层“Eksblowfish”(“昂贵的密钥表 blowfish”)的安全强度，那些[分析](https://security.stackexchange.com/questions/4781/do-any-security-experts-recommend-bcrypt-for-password-storage)应该留给密码专家。不过，我想总结一下，bcrypt 仍然属于推荐的密码散列函数的范畴。

请继续关注第 2 部分，在那里我提出了一个基于 bcrypt 和改进的密码散列协议的 KDF。

一个小插件:解释的大多数问题都可以用我的 bcrypt 的 Java 实现来克服(使用 jBcrypt 的 *Eksblowfish* 算法)。这个库的主要目标是尽可能简单明了，让不熟悉 bcrypt 的人很难弄错，但仍然允许尽可能多的灵活性。看看吧，你可能会发现它很有用(它是 Apache v2):

[](https://github.com/patrickfav/bcrypt) [## patrickfav/bcrypt

### 这是 OpenBSD Blowfish 密码散列算法的一个实现，如“适应未来的……

github.com](https://github.com/patrickfav/bcrypt)