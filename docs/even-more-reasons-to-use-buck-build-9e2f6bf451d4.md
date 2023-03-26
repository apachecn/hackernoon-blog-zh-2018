# (甚至)更多使用降压构建的理由

> 原文：<https://medium.com/hackernoon/even-more-reasons-to-use-buck-build-9e2f6bf451d4>

![](img/46388a8c8a59a980e46a6387712ee6aa.png)

“Why? signage near grass during daytime” by [Ken Treloar](https://unsplash.com/@kentreloar?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 出色的预编译头文件支持

大多数人对预编译头文件又爱又恨(PCH)。一方面，它们可以极大地加速你的构建时间(30%并非闻所未闻)，但另一方面，它们*可以*对你的增量构建时间产生毁灭性的影响。

一旦使用，预编译头必须在每次包含的头改变时更新。现在，每个使用 PCH 的翻译单元也需要重新编译，这可能比必要的要频繁得多。PCHs 有效地使每个翻译单元依赖于 PCH 中的每个头文件。

然而，有了巴克，你就可以两全其美。通过从头开始为您的构建(例如 CI 构建)切换`enable_pch`，然后为增量构建(例如开发)禁用它，您可以仅在适当的时候使用 PCHs。Buck 将 PCH 编码为一个高级抽象，因此在构建脚本中不需要任何模板代码来实现它。

此外，Buck 确保所有 PCH、库和可执行文件使用相同的编译器和预处理器标志集。

# 缓存共享是内置的

您是否曾在审查 PR 时犹豫过切换分支，因为这可能会触发全面重建？Buck 可以通过您的文件系统或 HTTP 共享您的缓存。这意味着您可以切换到不同的分支并返回，同时维护您的构建缓存。

# 构建是可复制的

构建步骤可能非常复杂，相同的构建会导致二进制级别的细微差异。因此，人工产物的散列是不可再现的，由于缓存未命中而导致许多不必要的重建。Buck 编排了 GCC 之类的常用工具，以确保 artefact hash 只随其输入而变化。有了巴克，你就不会经常重建。

# 对定制构建脚本的良好支持

与大多数构建系统不同，Buck 缓存每一个人工制品，包括生成的文件。每个人工制品只有在其依赖关系的散列改变时才被重建。这比只缓存 C++编译器工件的解决方案(如 CCache)更全面。

# 强大的工具链支持

没有很多抽象的构建系统，比如 Make，依赖于约定来定义你的工具链。例如，C++编译器可能被定义为`$CXX`。这是很脆弱的，因为它要求每个人都遵循相同的约定，当你有很多依赖时，这可能是一场噩梦。Buck 在一个更高的抽象层次上工作，所以你一旦定义了你的工具链，它就能保证它在你的整个项目中得到尊重。

# 额外:对云雀的实验支持

Buck 和 Bazel 正在汇聚到同一个构建描述语言:Skylark。Skylark 是 Python 的一个子集，它提供了更快的解析时间。在一些大型项目中，这将解析时间从几分钟减少到几秒钟。

# 您可能也对…感兴趣

 [## 使用降压构建的 7 个理由

### Buck 是一个跨平台、跨语言构建系统，在脸书为大规模编译而设计。所有牛仔…

hackernoon.com](https://hackernoon.com/7-reasons-to-use-buck-build-5b44d7413585) [](https://hackernoon.com/lessons-learned-from-porting-300-projects-to-buck-build-ff6463b65142) [## 将 300 个 C/C++项目移植到 Buck Build 的经验教训

### 有了 Buckaroo，我们正在将 C/C++项目的庞大生态系统转变为一系列易于组装的构建…

hackernoon.com](https://hackernoon.com/lessons-learned-from-porting-300-projects-to-buck-build-ff6463b65142)