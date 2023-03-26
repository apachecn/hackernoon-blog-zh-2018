# 如何用 Bytenode 编译 Node.js 代码？

> 原文：<https://medium.com/hackernoon/how-to-compile-node-js-code-using-bytenode-11dcba856fa9>

**更新** (2019/11/06):显然，黑客正午网站存在问题，使得 GitHub gists 只显示我的个人资料图片。
**请**阅读 Medium 上的故事([此处](/hackernoon/how-to-compile-node-js-code-using-bytenode-11dcba856fa9))以避免这个恼人的错误。

![](img/7b70d6a3d53f26ef48d741964d3f7e1a.png)

在这篇文章中，我将向您展示如何“真正地”将 Node.js (JavaScript)代码编译成 V8 字节码。这使你能够以一种比混淆或其他效率不高的技巧更好的方式隐藏或保护你的源代码(比如使用秘密密钥加密你的代码，这将被嵌入到你的应用程序二进制文件中，这就是为什么我在上面说“真正地”)。

因此，使用`bytenode`工具，您可以分发 JavaScript 文件的二进制版本`.jsc`。您也可以使用 Browserify 捆绑所有的`.js`文件，然后将单个文件编译成`.jsc`。

检查 Github 上的[字节节点库](https://github.com/OsamaAbbas/bytenode)。

## 长话短说..

1.  全局安装 byte node:
    `[sudo] npm install -g bytenode`
2.  要编译您的`.js`文件，运行这个命令:
    `bytenode --compile my-file.js my-file.jsc`
3.  将`bytenode`也安装到您的项目中:
    `npm install --save bytenode`
4.  在您的代码中，要求`bytenode`、
    、`const bytenode = require('bytenode');` 在 Node.js 模块系统中注册`.jsc`扩展。这也是我们在本地安装它的原因。
5.  你现在可以要求`my-file.jsc`作为一个模块:
    `const myFile = require('./my-file.jsc');` 你也可以从产品构建中移除`my-file.js`。
6.  如果您想使用`bytenode` cli 运行`my-file.jsc`:
    `bytenode --run my-file.jsc`

现在您知道了如何编译`.js`文件，如何在代码中要求编译后的版本，以及如何从终端运行`.jsc`文件。让我们继续说这个长故事。

V8 引擎(Node.js 基于该引擎)使用了所谓的即时编译(JIT ),即 JavaScript 代码在执行前编译，然后随后进行优化。

从 Node.js v5.7.0 开始，`vm`模块在`vm.Script`构造函数中引入了一个名为`produceCachedData`的属性，所以如果你这样做:

然后，获取字节码或`cachedData`缓冲区:

通过将这个`helloBuffer`传递给`vm.Script`构造函数，它可以用来创建一个相同的脚本，该脚本将在运行时执行相同的指令:

但这将失败，V8 引擎将抱怨第一个参数(即空字符串`''`)，当它检查它是否与最初用于生成`helloBuffer`缓冲区的代码相同时。然而，这个检查过程非常简单，重要的是代码的长度。所以，这是可行的:

我们给它一个空字符串，长度(28)和原码一样(`console.log("Hello World!");`)。就是这样！

这很有趣，使用缓存的缓冲区和原始代码长度，我们能够创建一个相同的脚本。两个脚本都可以使用`.runInThisContext();`功能运行。所以如果你运行它们:

你会看到“你好，世界！”两次。

(注意，如果你使用了错误的长度，或者你使用了另一个版本的 Node.js/V8:`anotherHelloScript`将不会运行，它的属性`cachedDataRejected`将被设置为`true`)。

现在到我们的最后一步，当我们定义`anotherHelloScript`时，我们使用硬编码值(28)作为我们的代码长度。我们如何改变这一点，以便在运行时我们不必知道原始源代码到底有多长？

在 V8 源代码中做了一些挖掘之后，我发现头信息在这里[定义](https://github.com/v8/v8/blob/9bcb5eb590643db0c1f688fea316c7f1f4786a3c/src/snapshot/code-serializer.h#L103-L113)(在这个文件`code-serializer.h`):

但是，Node.js 缓冲区是 Uint8Array 类型的数组。这意味着来自`uint32`数组的每个条目将占用`uint8`缓冲区中的四个条目。因此，有效载荷长度(在索引`[2]`处为`source hash`，在节点缓冲区中为`[8, 9, 10, 11]`字节)将为:

大概是这样的:`<Buffer 1c 00 00 00>`，是小端的，所以是:`0x0000001c`。这就是我们的代码长度(十进制 28)。

要将这四个字节转换成一个数值，您可以这样做:

`firstByte + (secodeByte * 256) + (thirdByte * 256**2) + (forthByte * 256**3)`，

或者用更优雅的方式，你可以这样做:

正如我在我的图书馆[这里](https://github.com/OsamaAbbas/bytenode/blob/4c3058e12f29ca33449a126c77ceaa57e967d45d/index.js#L29-L30)所做的，查看完整的食谱。

或者，我们可以使用`[buf.readIntLE()](https://nodejs.org/api/buffer.html#buffer_buf_readintle_offset_bytelength)`函数，它完全符合我们的要求:

一旦你读取了原始代码的长度(用来生成`cachedData`缓冲区)，你现在可以创建你的脚本了:

最后，这种技术对性能有影响吗？好吧，在 v8(和 Node.js)的最近版本中，性能都差不多。使用 octance 基准测试，我没有发现任何性能差异。我知道 Google 反对 octance(因为浏览器和 JS 引擎作弊)，但是在我们的情况下结果是显著的，因为我们在相同的 JS 引擎上比较相同的代码。所以，最后的答案是:Bytenode 对性能没有负面影响。

查看我的 [Github 库](https://github.com/OsamaAbbas/bytenode)，在那里你可以找到完整的工作示例。我为 electronic(它完全没有源代码保护)和 [NW.js](https://github.com/OsamaAbbas/bytenode/tree/master/examples/nwjs-hello-world) (它有一个类似的工具`nwjc`，但它只对浏览器端代码有效)添加了一个[示例。我将很快添加更多的例子(和测试)，希望如此。](https://github.com/OsamaAbbas/bytenode/tree/master/examples/electron-hello-world)