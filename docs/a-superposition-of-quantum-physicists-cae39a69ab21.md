# 量子物理学家的叠加

> 原文：<https://medium.com/hackernoon/a-superposition-of-quantum-physicists-cae39a69ab21>

## 量子计算机未来的一个例子，从它们的过去创造出来。

![](img/9b86d8e525feaeaa6264c35af55eec3b.png)

在上个世纪，许多人对量子物理学做出了贡献。通过他们的工作，我们现在对量子领域有了巨大的了解。我们可以控制它来创造新的更好的技术。

一个新兴的例子是量子计算，它目前正在经历原型量子处理器的复杂性和可用性的革命。他们现在有足够的量子比特让[玩战舰](/p/982a9329ed02?source=linkShare-cd2ed3119495-1525683155)，甚至在云上有[可供任何人实验。一个硬件和软件生态系统正在开始形成，有像 IBM 和谷歌这样的大公司，也有许多初创公司。](https://quantumexperience.ng.bluemix.net/qx)

我决定用一个基于云的设备来庆祝我们站在他们肩膀上的巨人。我拍了 16 位获得诺贝尔奖的量子物理学家的照片，然后把它们组合起来。

我用量子叠加法做到了这一点。这是一种有效地允许量子系统同时做多件事情的现象。具体来说，我创建了 16 个图像的文件名的量子叠加，创建了同时表示所有文件的东西。

这听起来可能很复杂，但是量子 SDK 的当前产品使它变得容易。我使用的是 QISKit SDK，它是对 IBM 设备的原生支持。

[](/qiskitters/qiskit-turns-one-looking-back-cbc2c48d7a95) [## 回顾 QISKit 的一年

### 一岁生日快乐，奇斯基特！

medium.com](/qiskitters/qiskit-turns-one-looking-back-cbc2c48d7a95) 

使用 QISKit，您可以通过简单的 Python 控制量子计算机。设置叠加所需的代码只有下面简单的四行。

```
program.h( qubit[b[0]] )
program.h( qubit[b[1]] )
program.h( qubit[b[2]] )
program.h( qubit[b[3]] )
```

一旦这段代码被执行，量子芯片中就存在叠加态。但由于这是一个微小的设备，位于宇宙中最冷的点之一(在 IBM 的约克镇高地研究实验室创建)，它不是很容易接近。为了让我们的创造变得更加有形，我们首先必须提取某种输出。

在这里，叠加不再是你在科幻小说中会发现的那种东西，而是变得更加平凡。尽管叠加态在量子计算中非常强大，允许我们从输入到输出走更快的路线，但在一个量子计算结束时，它们就没那么有趣了。事实上，在这一点上，它们只不过是二进制字符串的一个奇特的随机生成器。

这就是文件名叠加的结果。我们向[量子计算机](https://hackernoon.com/tagged/quantum-computer)询问文件名，它只是随机吐出其中的一个。通过重复多次，我们可以统计出每一次出现的频率。

这些信息随后被用来构建我们的图像。16 幅图像中每一幅的阿尔法通道都经过选择，这样图像的强度就与量子计算机喜欢它的程度相对应。从构建量子程序到处理结果，一切都在[这个 Jupyter 笔记本](https://github.com/decodoku/Quantum_Programming_Tutorial/blob/master/image-superposer/nobel-superposer.ipynb)中完成。

![](img/9b86d8e525feaeaa6264c35af55eec3b.png)

理想情况下，所有 16 个图像应该被同等地表示。但是目前的量子处理器并不是完全没有噪音。首先，量子比特有从`1`翻转到`0`的轻微趋势。因此，带有更多 0 的文件名更有可能出现它们的图像。这可能就是为什么我们能很好地看到马普的胡子(0100.png)和薛定谔的眼镜(0100.png)。在 1111.png 的塞尔日·阿罗什得到公平对待之前，这些设备还需要改进。

即便如此，所有这些物理学家的平均水平都有一个明确的特征:相当老，大部分是白人，而且非常男性化。这可能在很大程度上代表了我们量子努力的过去，但不会是未来。基于云的量子处理器及其附带的 SDK 为每个有互联网连接的人提供了量子突破的机会。不管你是想做科学，还是想做游戏，都可以用量子计算机试试。

[](/qiskitters/how-to-program-a-quantum-computer-982a9329ed02) [## 如何给量子计算机编程

### 战列舰用的偏不是城门

medium.com](/qiskitters/how-to-program-a-quantum-computer-982a9329ed02) [](/qiskitters/making-a-quantum-computer-smile-cee86a6fc1de) [## 让量子计算机微笑

### 如何玩量子计算机的一个简单例子

medium.com](/qiskitters/making-a-quantum-computer-smile-cee86a6fc1de) [](/@decodoku/playing-with-the-worlds-most-advanced-technology-c7fcc21f293f) [## 玩弄世界上最先进的技术

### 我是一名理论物理学家。我的工作是思考问题。其实做事是留给更有能力的人。

medium.com](/@decodoku/playing-with-the-worlds-most-advanced-technology-c7fcc21f293f) 

当我们在几年后创建一个量子程序员的叠加时，希望它看起来会非常不同。