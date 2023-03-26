# 通过行星间文件系统到木星的短途旅行

> 原文：<https://medium.com/hackernoon/a-short-trip-to-jupyter-via-the-inter-planetary-file-system-92c36c1e5b45>

IPFS、Python 和 Jupyter 笔记本入门

![](img/58b5295c829e948007b73d797c88a0cb.png)

Cover of Journey to Jupiter (A Dig Allen Space Explorer Adventure)

# 行星间文件系统(IPFS)

[IPFS 是分布式网络](https://ipfs.io/)。它是一种理念和协议，旨在支持分布式文件系统，该系统寻求将所有计算设备与相同的文件系统连接起来。围绕 IPFS 有很多炒作，但大部分是当之无愧的。建立在 IPFS 技术基础上的[优秀应用](https://github.com/ipfs/awesome-ipfs)越来越多，围绕它的[开源社区](https://github.com/ipfs/community)正在激增。你为什么要在乎？因为 IPFS 为[数据共享和访问](http://blog.ibmjstart.net/2016/06/27/interplanetary-file-system-ipfs-jupyter/)、代码版本化以及更多未来的事情提供了令人兴奋的机会。最棒的是，在分布式 web 上开始其实很容易！

虽然 IPFS 的参考实现是用 [Go](https://github.com/ipfs/go-ipfs) 和 [Javascript](https://github.com/ipfs/js-ipfs) 编写的，但是 Pythonistas 仍然可以使用他们最喜欢的语言参与到分布式网络中。在这篇简短的帖子中，我们将介绍入门所需的设置，并指出一些资源来满足您(希望是建立)的好奇心。

# 安装 IPFS

你可以从这里的官方[安装页面开始](https://ipfs.io/docs/install/)。为您的平台获取 IPFS 二进制文件[，并像安装任何软件一样安装它(您也可以阅读自述文件以获得帮助)。这里的技巧是确保它安装在您的`$PATH`中的某个地方。尝试运行`ipfs --version`以确保它正在工作。IPFS 使用一个添加到`~/.ipfs`中的全局本地对象存储库，可以用`ipfs init`初始化。请随意遵循那里建议的命令，并体验一下 IPFS 命令行工具。](https://dist.ipfs.io/#go-ipfs)

一旦你准备好进入下一个阶段，在另一个终端中运行守护进程:`ipfs daemon`。观察(至少)出现的三行，并记下您得到的 tcp 端口。现在，如果您连接到网络，您应该能够看到您的对等机的 ipfs 地址:`ipfs swarm peers` (一开始应该至少有 4 个)。

# 客户端库

现在是时候安装 [Python](https://hackernoon.com/tagged/python) 客户端库来与正在运行的守护程序的本地 IPFS API 进行交互了。你平常的`pip install ipfsapi` 会让你得到你需要的东西。这可能是一个很好的建议，建议你安装最新版本的 IPFS，并确保你运行的是 Python 3.4 或更高版本(不是必需的)，这将使你的生活更加轻松。

# Jupyter 笔记本

让我们启动一台运行 IPython 的 [Jupyter](https://hackernoon.com/tagged/jupyter) 笔记本，开始摆弄 IPFS `jupyter notebook` 然后是`New` > `Python`。我也制作了一个[在 IPFS](https://ipfs.io/ipfs/QmdnFJ2qDYbtxGirZXDNf35Tm4ZYMdXoBmVRkUi3Mrrg6p) 上，让你开始。

我们将从基础开始:

```
import ipfsapiapi = ipfsapi.Client("localhost", 5001)
```

并重现初始化对等机时打印的小示例:

```
print(api.cat("QmYwAPJzv5CZsnA625s3Xf2nemtYgPpHdWEz79ojWnPbdG/readme").decode())
```

您甚至可以执行管理功能，例如`api.id()`和列出本地固定文件`api.pin_ls(type=’all’)`。为了让事情变得更有趣，我们可以通过直接访问图像(在这种情况下，是一个 [XKCD](https://xkcd.com/) 漫画)并在线显示它们来利用笔记本中的绘图功能:

```
from IPython.display import Imagepath = "QmSeYATNaa2fSR3eMqRD8uXwujVLT2JU9wQvSjCd1Rf8pZ/1553 - Public Key/1553 - Public Key.png"Image(api.cat(path))
```

![](img/763e3198c2e8282016281849130c62c4.png)

I guess I should be signing stuff, but I’ve never been sure what to sign. Maybe if I post my private key, I can crowdsource my decisions about what to sign.

因为我们是用 Python 工作的，所以我们可以利用这些绑定提供给我们的一些很好的特性，比如直接向 IPFS 添加字符串和字典的帮助函数:

```
metadata = {"data": "about data"}
cid = api.add_json(metadata)
# You can also check the public gateway:
print("[https://ipfs.io/ipfs/{](https://ipfs.io/ipfs/{)}".format(cid))
```

然后检查我们的数据是否确实可以通过我们的本地同行获得:

```
import json
json.loads(api.cat(cid))
```

暂时就这样吧！在 [Textile](https://www.textile.io) ，我们使用一整套 IPFS 工具来与我们自己的数据进行交互，并使用我们正在开发的不断增长的基础设施来实现分布式网络上的安全照片备份和共享。来[看看我们的](https://www.textile.photos/)，跳到[纺织品照片等候名单](https://www.producthunt.com/upcoming/textile-photos)上，请求早日获得一种全新的方法来控制您的照片。

# 资源

*   [上面代码的笔记本，托管在 IPFS，用 Python 添加的！](https://ipfs.io/ipfs/QmdnFJ2qDYbtxGirZXDNf35Tm4ZYMdXoBmVRkUi3Mrrg6p)
*   [GitHub 上的 IPFS API Python 模块(ipfsapi)](https://github.com/ipfs/py-ipfs-api)
*   [IPFS API 参考](https://ipfs.io/docs/api/)
*   [另一本入门指南](/python-pandemonium/getting-started-with-python-and-ipfs-94d14fdffd10)
*   [包含各种有用代码片段的 Python 笔记本](https://github.com/ibmjstart/notebook-samples/blob/master/Native%2BPython%2BIPFS%2BNotebook.ipynb)
*   [与上述笔记本相应的博文](http://blog.ibmjstart.net/2016/06/27/interplanetary-file-system-ipfs-jupyter/)