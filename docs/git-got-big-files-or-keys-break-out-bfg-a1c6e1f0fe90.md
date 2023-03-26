# Git 有大文件或密钥吗？冲出 BFG

> 原文：<https://medium.com/hackernoon/git-got-big-files-or-keys-break-out-bfg-a1c6e1f0fe90>

![](img/0faa1de44127a74733edf61aaefce716.png)

每个人都搞砸了，今天的错误是在 gitignore 处理它之前向 git 添加了一个大文件。因此，github 拒绝推送，甚至在从 git 中“移除”文件之后。原因是文件仍然存在于 git(history)中。是时候收拾残局了，让 BFG 破土而出，从轨道上用核武器摧毁它。-可悲的是，这意味着 java 是不可或缺的恶魔。BFG 可以在下面找到，并且需要安装一个 java jdk。

 [## BFG 回购清洁工由 rtyley

### git-filter-branch 的一个更简单、更快速的替代方法，用于删除大文件和从 git 历史记录中删除密码。

rtyley.github.io](https://rtyley.github.io/bfg-repo-cleaner/) 

首先，让我们来看看 BFG 回购清道夫。欢迎回来，希望有一些阅读。BFG 回购-清洁将用于清理大文件，这也可以用来清理敏感数据，有人不小心添加到回购。“咳咳”aws 键。它通过重写 git 历史并删除文件的所有痕迹来实现这一点。像许多事情一样，git 有时最好不要解释它的神奇之处，直接进入主题。

TLDR:哦，我的天…就这么做吧…黑魔法随之而来。

欢迎从盲目运行在互联网上找到的命令回来，一切工作正常吧？是时候分析一下刚刚发生的事情了。前期工作是设置 BFG 并将其加载到环境中。
在主文件夹中创建一个文件夹结构来存储 jar。
然后下载 jar 并创建一个符号链接，这样当添加新版本时，旧的符号链接可以被删除和重置。
这并不完全需要，但肯定会有所帮助。
接下来，该文件夹被添加到 bash_profile 文件中的 path env 变量中。
然后让 bash_profile 使用新路径和新文件夹。不要求做所有这些，但是，说实话，这种情况会发生不止一次，最好在将来做这些。之后，回购被克隆(很可能它已经存在，所以不要担心。
然后运行 git 垃圾收集。
下一步移出目录，因为 BFG 需要在当前目录之外运行。
触发应该被 nuked 的文件或通配符中的 BFG 传入。
放回文件夹中。
终止 get 引用日志，它清除了 BFG 做的一些事情
最后 git 垃圾收集清除了剩余的 cruft。

也就是说，文件已经被删除，并且它们存在的所有历史已经被清除。当与 git hook 和 regex 结合使用时，这种类型的过程对于文件中的特定内容(如密钥等)特别有用。它也可以很容易地连接到詹金斯建立管道，以保护人们免受自己。祝你好运，如果有疑问，就拿出那把“该死的”大枪

额外收获:julia evans 写了一本很棒的杂志，讲述了一些关于 git 的其他事情

[](https://jvns.ca/blog/2018/10/27/new-zine--oh-shit--git-/) [## 新杂志:哦，妈的，饭桶！

### 你好！上周，凯蒂·塞勒-米勒和我发行了一本名为《哦，妈的，饭桶》的新杂志。它有一堆普通的 git…

jvns.ca](https://jvns.ca/blog/2018/10/27/new-zine--oh-shit--git-/) 

(提及的所有内容的链接:)

 [## 从存储库用户文档中删除敏感数据

### 如果您将敏感数据(比如密码或 SSH 密钥)提交到 Git 存储库中，您可以将其从历史记录中删除…

help.github.com](https://help.github.com/articles/removing-sensitive-data-from-a-repository/)  [## BFG 回购清洁工由 rtyley

### git-filter-branch 的一个更简单、更快速的替代方法，用于删除大文件和从 git 历史记录中删除密码。

rtyley.github.io](https://rtyley.github.io/bfg-repo-cleaner/) [](https://git-scm.com/docs/git-reflog) [## Git - git-reflog 文档

### “show”子命令(在没有任何子命令的情况下，这也是默认命令)显示引用的日志…

git-scm.com](https://git-scm.com/docs/git-reflog) [](https://git-scm.com/docs/git-gc) [## Git - git-gc 文档

### 如果包的数量超过 gc.autoPackLimit 的值，那么现有的包(标有. keep 文件的包除外…

git-scm.com](https://git-scm.com/docs/git-gc)