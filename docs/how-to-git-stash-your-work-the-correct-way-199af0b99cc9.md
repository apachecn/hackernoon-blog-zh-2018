# 如何藏起你的工作——正确的方法

> 原文：<https://medium.com/hackernoon/how-to-git-stash-your-work-the-correct-way-199af0b99cc9>

想象一下，你正在做一个项目的一部分，它开始变得混乱。有一个紧急的 bug 需要您立即关注。是时候**保存**你的更改并切换分支了。问题是，你不想做半途而废的工作。解决方法是`git stash`。

> *如果你需要快速切换上下文并处理其他事情，但是你正在修改代码，还没有准备好提交，那么存储是很方便的。由* [*位桶*](https://www.atlassian.com/git/tutorials/saving-changes/git-stash)

# 隐藏

假设您目前有一些局部修改。运行`[git status](https://kolosek.com/git-commands-tutorial-part2/)`，检查您的当前状态:

```
$ git status
# On branch master
# Changes to be committed:
#   (use "git reset HEAD <file>..." to unstage)
#      modified: index.html
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed
#      modified: assets/stylesheets/styles.css
```

你需要解决那个紧急的问题。首先，您想要保存未完成的工作变更而不提交它们。这就是`git stash`成为救世主的原因:

```
$ git stash
Saved working directory and index state WIP on master:
  bb06da6 Modified the index page
HEAD is now at bb06da6 Modified the index page
(To restore them type "git stash apply")
```

您的**工作目录**现在是干净的，所有未提交的本地更改都已保存！此时，您可以自由地进行新的更改，[创建新的提交](https://kolosek.com/git-merge/)，[切换分支](https://kolosek.com/git-branches/)，并执行任何其他 Git 操作。

> *默认情况下，栈被标识为“WIP”——正在进行的工作，位于创建它们的分支和提交之上。*

# 重新使用你的存货

Git stash 是一个临时仓库。当您准备好从您离开的地方继续时，您可以轻松地恢复保存的状态:`git stash pop`。

弹出您的存储库会从您的存储库中移除更改，并重新应用上次保存的状态。如果你想把修改保存在存储库中，你可以使用`git stash apply`来代替。

# 其他提示和技巧

你还可以用藏毒做一些其他的事情。我们来看看吧！

*   **保存藏匿点**
    保存一个带有**消息的藏匿点** : `$ git stash save <message>`。

> 尝试在你的风格中添加[*CSS-line high*](https://kolosek.com/css-line-height/)*并加上漂亮的注释。*

*   **隐藏未跟踪文件**
    这是保存**未跟踪文件** : `$ git stash -u`或`$ git stash --include-untracked`的唯一方法
*   **列出多个 stashes**
    当你`git stash`或者`git stash save`的时候，Git 会创建一个带有名称的 Git commit 对象，然后保存到你的 repo 中。您可以随时查看自己制作的的**清单！`$ git stash list`。**

```
$ git stash list
[[email protected]](https://kolosek.com/cdn-cgi/l/email-protection){0}: On master: Modified the index page
[[email protected]](https://kolosek.com/cdn-cgi/l/email-protection){1}: WIP on master: bb06da6 Initial Commit
```

*   **部分隐藏**
    您可以选择**隐藏**单个文件、文件集合或文件内的单个更改:`$ git stash -p`或`$ git stash --patch`。

> [*RSpec 测试*](https://kolosek.com/rails-rspec-setup/) *在 Ruby on Rails 项目中是必不可少的，但是它们可能并不总是完整的。只隐藏准备好的部分！*

*   **查看隐藏差异**
    查看隐藏有两种方式:查看一个隐藏的**完全差异**—`$ git stash show -p`或仅查看**最新隐藏**—`$ git stash show`。

```
$ git stash show
index.html | 1 +
style.css | 2 ++
2 files changed, 3 insertions(+)
```

*   **从隐藏的**
    创建一个**新的分支**来应用你隐藏的更改，然后将你隐藏的更改弹出到它上面:`$ git stash branch <branch_name> <stash_id>`。

> 这是另一种在继续进行项目前节省开支的方法。

*   取出你的藏物小心使用，它可能很难恢复。唯一的恢复方法是，如果你在删除存储后没有关闭终端。
    如果你不再需要某个**特定的隐藏**，你可以用`$ git stash drop <stash_id>`删除它。或者你可以**用`$ git stash clear`从回购中删除你所有的**库存。

希望这篇文章能帮助你更好地理解 stashing 是如何工作的。一定要测试出来！

*原载于 2018 年 5 月 14 日*[*kolosek.com*](https://kolosek.com/git-stash/?utm_source=me)*。*