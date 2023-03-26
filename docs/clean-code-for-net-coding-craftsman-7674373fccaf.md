# 的干净代码。网络编码工匠

> 原文：<https://medium.com/hackernoon/clean-code-for-net-coding-craftsman-7674373fccaf>

![](img/5de50dc10bf2c0347c6c2d2529c53176.png)

[Source](https://pixabay.com/en/tea-plantation-landscape-vietnam-3358870/)

当我多年前开始我的第一份软件开发职业时，我记得我真的很喜欢通过**学习和获得软件开发的很多技能**，特别是阅读这一领域的大师们的书籍，以及阅读他们的博客**。我读过的一些非常有名的书有 [**代码全集:软件构造实用手册**](https://www.amazon.com/Code-Complete-Practical-Handbook-Construction/dp/073561967) ， [**干净的程序员:职业程序员行为准则**](https://www.amazon.com/Clean-Coder-Conduct-Professional-Programmers/dp/0137081073) ， [**设计模式:可重用面向对象软件的元素**](https://www.amazon.com/Design-Patterns-Elements-Reusable-Object-Oriented/dp/0201633612) ， [**重构:改进现有代码的设计**](https://www.amazon.com/Refactoring-Improving-Design-Existing-Code/dp/0201485672) 等等。我坚信所有像我一样的开发人员也是通过这种方式进入软件开发行业的。**

那时，我想知道如何与软件开发社区分享我所学到的东西，以便帮助他们(T2)为他们职业生涯的第一阶段(T3)节省精力。就在去年，我在 [**JavaScript**](https://github.com/ryanmcdermott/clean-code-javascript) 和 [**PHP**](https://github.com/jupeter/clean-code-php) 语言中看到有几个关于 **Clean Code** 的项目，现在是 [**Ruby**](https://github.com/uohzxela/clean-code-ruby) 语言。然后我想，如果我像他们一样开始列表，但为 [**定制部分，那就太好了。NET/。NET Core**](https://github.com/thangchung/clean-code-dotnet) ，这促使我在那天晚上立即开始**创建第一个干净的代码。NET 开发者**名单在[**https://github.com/thangchung/clean-code-dotnet**](https://github.com/thangchung/clean-code-dotnet)

为了方便读者，[。NET 开发人员，我从不同的方式开始使用其他列表(JavaScript、PHP 或 Ruby 语言)。我将它们归入带有可折叠子项的**大组，这有助于读者在浏览包含大量项目的大列表时不会感到害怕。如果他们想调查或详细阅读，他们可以点击分项目的标题阅读更多内容。**](https://github.com/thangchung/clean-code-dotnet) **[**托尼**](https://github.com/ngohungphuc)——我的同事——也是**渴望加入这个项目**，所以我告诉他**帮助我，他也是提出这个** [**想法**](https://github.com/thangchung/clean-code-dotnet) 的人，实际上它使清单变得简短而简洁。谈了很多这个列表的历史，现在让我们来详细看看这些组里面有什么。**

# [命名](https://github.com/thangchung/clean-code-dotnet#2-naming)

*   避免使用坏名字
*   避免虚假信息名称
*   使用易发音的名字
*   使用骆驼格符号
*   使用域名。

# [变量](https://github.com/thangchung/clean-code-dotnet#3-variables)

*   使用有意义且容易发音的变量名
*   对相同类型的变量使用相同的词汇
*   使用可搜索的名称
*   使用解释变量
*   避免筑巢过深，提早返回
*   避免心理映射
*   不要添加不必要的上下文
*   使用默认参数，而不是短路或条件
*   避免魔术串

# [功能](https://github.com/thangchung/clean-code-dotnet#4-functions)

*   函数参数(理想情况下为 2 个或更少)
*   函数应该做一件事
*   函数名应该说明它们是做什么的
*   功能应该只是抽象的一个层次
*   不要使用标志作为函数参数
*   避免副作用
*   不要写全局函数
*   不要使用单一模式
*   封装条件
*   避免消极条件句
*   避免条件句
*   避免类型检查
*   删除死代码

# [对象和数据结构](https://github.com/thangchung/clean-code-dotnet#5-objects-and-data-structures)

*   使用 getters 和 setters
*   使对象具有私有/受保护的成员

# [类](https://github.com/thangchung/clean-code-dotnet#6-classes)

*   使用方法链接
*   偏好组合而非继承

# [坚实](https://github.com/thangchung/clean-code-dotnet#7-solid)

*   单一责任原则
*   开放/封闭原则(OCP)
*   利斯科夫替代原理
*   接口隔离原则(ISP)
*   从属倒置原则
*   不要重复自己(干)

# [测试](https://github.com/thangchung/clean-code-dotnet#8-testing)

*   每个测试一个概念

# [并发](https://github.com/thangchung/clean-code-dotnet#9-concurrency)

*   使用异步等待

# [错误处理](https://github.com/thangchung/clean-code-dotnet#10-error-handling)

*   不要忽略捕获的错误
*   使用一致的大写
*   函数调用者和被调用者应该接近

# [格式化](https://github.com/thangchung/clean-code-dotnet#11-formatting)

*   用途*。编辑器配置*文件

# [评论](https://github.com/thangchung/clean-code-dotnet#12-comments)

*   只评论有业务逻辑复杂性的东西
*   不要在你的代码库中留下注释掉的代码
*   没有日志评论
*   避免位置标记

# **重述**

我们仍在做这个列表，我相信它会更长，更准确。**我们的团队期待你们所有人的参与、贡献和帮助，使这份清单对** [**更有用。NET/。网芯**](https://github.com/thangchung/clean-code-dotnet) **社区。**

再次，**为[清理代码列表](https://github.com/thangchung/clean-code-dotnet)**。NET/。网芯可以在[**https://github.com/thangchung/clean-code-dotnet**](https://github.com/thangchung/clean-code-dotnet#12-comments)找到

感谢阅读！如果你喜欢这篇文章，一定要点击👏符号，以便其他人可以看到它。