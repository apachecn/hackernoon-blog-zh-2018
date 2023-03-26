# 3 分钟黑客本地构建一个本地扩展

> 原文：<https://medium.com/hackernoon/3-min-hack-for-locally-building-a-native-extension-serverless-technologies-5f86d613bef1>

## 无服务器技术

![](img/63cd862f397579a7ac6c353cc8e7b98e.png)

illustration/Ortal Avraham

无服务器是软件架构领域的热点。许多供应商致力于无服务器，当然，亚马逊、谷歌、IBM 和微软也在这方面投入了大量资金。但是像其他热门技术一样，它也有一些缺点。

在这篇文章中，我分享了一个非常方便的技巧，可以帮助你构建一个本地扩展。

需要是发明之母，我们的黑客也不例外。从单片到无服务器，Rookout 有助于跨平台的生产调试，但我们的许多客户已经在更广泛地使用无服务器，他们促使我们更深入地研究这项技术。

对 Python 和 Node 的 Rookout 支持依赖于本机扩展来实现它的魔力。如果您已经读到这里，您可能知道在 Lambda 和其他无服务器技术上运行本机扩展需要您将正确的二进制文件预打包到函数 zip 文件中。由于 [AWS Lambda](https://aws.amazon.com/lambda/) 运行在亚马逊 Linux 上，如果你运行在 Windows、Mac 或 Debian Linux 上，这可能会很痛苦。

亚马逊[为 Lambda 构建本地扩展的方法](https://aws.amazon.com/blogs/compute/nodejs-packages-in-lambda/)将它们构建在专用的 EC2 实例上，这远不是最愉快的体验。我们的许多客户要求我们帮助他们从任何操作系统中构建和部署 Lambda 功能。

在头脑风暴会议之后，我们的一些工程师带回来一个漂亮的小技巧，我很高兴与你分享。结果是， [Docker](https://www.docker.com/) 可以轻松地执行一个本地任务，就好像它在我们自己的计算机上运行一样。以下命令行摘自官方 Docker [指南](https://docs.docker.com/engine/reference/commandline/run/#mount-volume--v---read-only)，允许您在当前文件夹中运行 Docker 命令:

在当前目录中运行 Docker

*$ docker run-v ` pwd `: ` pwd `- w ` pwd `- I-t Ubuntu pwd*

要让 Docker 容器模拟 AWS Lambda 环境，我们只需看看 LambCI 项目。要构建 AWS Lambda 兼容的本机扩展，只需运行以下命令行:

节点:

使用 Docker 构建节点依赖关系:

*docker run-v ` pwd `: ` pwd `- w ` pwd `- I-tλci/lambda:build-nodejs 8.10 NPM 安装*

Python:

用 Docker 构建 Python 依赖关系:

*docker run-v ` pwd `: ` pwd `- w ` pwd `- I-tλci/lambda:build-python 2.7 pip install-r requirements . txt*

我们可以通过使用脚本隐藏使用 Docker 构建扩展的本质细节来做得更好。例如，让我们看看这个 package.json 文件，它允许您动态构建扩展:

使用 docker 的 package.json 文件

*{*

*【姓名】:【举例】，*

*"main": "index.js "，*

*“依赖关系”:{ }，*

*【脚本】:{*

*“安装模块”:“docker run-it-v ` pwd `: ` pwd `- w ` pwd ` node:6 NPM install”，*

*" build-package ":" zip-r package . zip * "，*

*“构建”:“npm 运行安装-模块& & npm 运行构建-打包”*

*}*

*}*

要构建，只需运行:

*国家预防机制运行建立*

在开发 Lambda 函数时，我们的团队和我们的客户都发现这种方法非常有帮助，可以节省时间。希望你也觉得有用。

*撰稿:Liran Haimovitch。最初发表于*[*www.rookout.com*](https://www.rookout.com/3_min_hack_for_building_local_native_extensions/)*。*