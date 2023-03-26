# 如何备份个人 GitHub 存储库

> 原文：<https://medium.com/hackernoon/how-to-backup-personal-github-repositories-f42235185806>

我将展示如何用 [python-github-backup](https://github.com/josegonzalez/python-github-backup) 来备份[GitHub 库](https://github.com/sasa-buklijas?tab=repositories)的[T4](https://hackernoon.com/tagged/backup)

# 为什么要麻烦 GitHub 的备份呢

我已经可以看到会有关于为什么要做 [GitHub](https://hackernoon.com/tagged/github) 的备份的评论。

*   “这是浪费时间。”
*   " GitHub 内部已经有备份了."(希望如此)
*   “他们不会丢失你的代码”(但也许我会)
*   "他们不会一夜之间倒闭。"

对所有这些评论的回应是:
**如果你有自己的备份，你不会是最糟糕的。**

如果永远的原因(GitHub 破产，意外删除所有库，外星人攻击)GitHub 不再可用，我有自己写的代码备份。

# 付费解决方案

如果你正在寻找一个付费的解决方案， [BackHub](https://github.com/marketplace/backhub) 看起来是一个不错的解决方案。
我没有使用 BackHub 的经验，也与它没有任何关系。

# 免费解决方案

在研究了所有可用的[选项](https://github.com/kanishkegb/github-backup-all)之后，我决定选择 [python-github-backup](https://github.com/josegonzalez/python-github-backup) ，因为它在 github 上拥有比其他项目更多的明星和贡献者。

我用 GitHub 上的明星和贡献者的数量来假设 [python-github-backup](https://github.com/josegonzalez/python-github-backup) 比其他解决方案更常用，所以将来会有更多的人继续支持它。

为了访问您的 GitHub 个人数据，您需要有一个[个人访问令牌](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/#creating-a-token)。

之后就可以用`pip/pipenv` :
安装了(我已经在单独的 virtualenv 里安装了)

`pipenv install github-backup`或者

`pip install github-backup`

用
`/full/path/github_backup/venv/bin/github-backup sasa-buklijas -t your_personal_access_token -o /full/path/github_backup --all`运行

这个命令会将你所有的 GitHub 信息备份到`/full/path/github_backup directory`。

每天运行这个命令会令人厌倦，所以我在我的在线主机上自动运行了这个命令。

# Crontab

我的用例:
`15 00 * * * /full/path/github_backup/venv/bin/github-backup sasa-buklijas -t your_personal_access_token -o /full/path/github_backup --all > /full/path/github_backup/last_log.txt`

`> /full/path/github_backup/last_log.txt`用于输出上一个备份命令。
`>>`可以用来输出所有的备份命令，但是我发现有最后一个就足够了。

# 结论

**“如果你有自己的备份，情况不会更糟。”**

你有你个人的 GitHub 库的备份吗，如果有，你用什么来备份？

*原载于 2018 年 8 月 1 日*[*buklijas . info*](http://buklijas.info/blog/2018/08/01/how-to-backup-personal-github-repositories/)*。*