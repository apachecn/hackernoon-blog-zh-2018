# VS 代码中调试 Jovo 的技巧

> 原文：<https://medium.com/hackernoon/debugging-jovo-skills-in-vs-code-d117f908fbc2>

我一直想尝试一下 [Jovo](https://hackernoon.com/tagged/jovo) webhook，因为它提供了灵活的调试视图，但我也需要在我的[代码](https://hackernoon.com/tagged/code)中设置断点，我宁愿不使用 Chrome DevTools。它们很好，但是如果我用 VS 代码开发，那也是我想设置断点的地方。

事实证明，让它工作并不困难，只是花了一点时间通读 VS Code launch.json 文档。

将启动程序设置为`jovo`并传递`run`和`--inspect`参数是显而易见的选择: