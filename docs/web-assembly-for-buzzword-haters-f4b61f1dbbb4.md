# 为讨厌流行词的人准备的网络集会

> 原文：<https://medium.com/hackernoon/web-assembly-for-buzzword-haters-f4b61f1dbbb4>

Web 组装看起来是 web 技术中下一个最好的东西。尽管第一次发布发生在最近，即 2017 年初，但很久以前就有传言和预期。例如，在谷歌上快速搜索，我找到了大约 3 年前的这篇文章。

![](img/91a80dc64ceda87c9275b07c3c8c4fa7.png)

那么关于 WebAssembly 的所有宣传是什么呢？正如所有新的(和有前途的)技术一样，空气中有信息超载，我做了一个紧凑的常见问题解答来帮助你了解这个城镇中的新孩子

事不宜迟，我们开始吧:

# 什么是 web 组装？

当你想快速地做事情，每一个皮秒都很重要的时候，Javascript 并不是最好的选择(hello parallelism)。如果你已经开发了一个像实时视频编辑一样占用大量资源的 javascript 应用程序，请告诉我:)

WebAssembly 的第一个动机是速度，因为它有望实现二进制代码所用语言的近乎本机的性能。

# 什么的二进制？

如前所述，javascript 并不以其极端的性能指标为傲。但其他语言，如 C++或 Rust，则不同。Web assembly 可以加载任何受支持语言的可执行文件，并在浏览器中执行，具有接近本机的性能。太棒了，对吧？你想不想让[看到](https://hacks.mozilla.org/2017/07/webassembly-for-native-games-on-the-web/)你最喜欢的[游戏](https://hackernoon.com/games-build-on-webassembly-3679b3962a19)，有一天在浏览器里玩，不管它有什么优点？这一天可能比你想象的要近。当然，这意味着相关的库(是的，Unity 也是)可以移植到 WebAssembly。

为了完整起见，[这里的](https://github.com/appcypher/awesome-wasm-langs)是支持的语言的完整列表。

# 太棒了，这个东西会杀死 javascript 吗？

引用[官方](https://github.com/WebAssembly/design/blob/master/FAQ.md)库的 FAQ

> *没有！WebAssembly 旨在作为 JavaScript 的补充，而不是替代。随着时间的推移，WebAssembly 将允许许多语言被编译到 Web 上，而 JavaScript 有着令人难以置信的发展势头，并将保持 Web 的单一、特权(如上所述)动态语言。*

# 太好了，等不及要用了。你能给我举个例子吗？

谢谢你这么有礼貌。:)你可以在文章最后一节找到一个例子。

# 什么时候发布？

我可以用两个字来回答。已经到了。请查看[路线图](https://webassembly.org/roadmap/)了解更多信息。

# 我的浏览器不被支持怎么办？

所有主流浏览器[都支持](https://caniuse.com/#feat=wasm)WASM(78.64%的用户在撰写本文时)。如果你是那 22%不在俱乐部的人之一，恐怕我没有读过与这个案子有关的东西。您可能需要切换浏览器。

# 哦，我最近听说了一些关于 Blazor.Net 的事情，它看起来很相似。什么情况？

[Blazor](https://blazor.net/index.html) 是微软最近发布的一个框架，允许你在 WebAssembly 的宝贵帮助下用 C#进行全栈开发。正如微软声称的那样，在撰写本文时，该框架还没有准备好用于生产应用程序，但它看起来像是 Node.js 的一个强大竞争对手。

# WASM 的崛起会对 node.js 产生怎样的影响？

我们现在还不能确定，但我有一些想法。

正如上面 Blazor 观点中提到的，看起来 Node.js 将开始有一些严重的竞争。我并不是说没有优秀的 web 框架。有很多这样的人。但到目前为止，只有 javascript 可以在 web 应用程序的后端和前端使用。出于超出本文范围的原因，这被认为是一个很大的好处。竞争通常能让我们变得更好。

# 结论

感谢您花时间阅读这篇文章。我希望我为 WebAssembly 提供了一个干净清晰的入门，为它打下了良好的基础。

下面是一些网站访问，如果你想提高你的知识，这项技术

*最初发表于*[*peri GK . github . io*](https://perigk.github.io/)*。*