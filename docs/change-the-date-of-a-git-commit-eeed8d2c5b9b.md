# 更改 git 提交的日期

> 原文：<https://medium.com/hackernoon/change-the-date-of-a-git-commit-eeed8d2c5b9b>

![](img/6f6f52e8a38164c6453f4b9c0af77974.png)

[Sean Mungur](https://unsplash.com/@seanmungurdp?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge) on Unsplash

使用 [git](https://hackernoon.com/tagged/git) 最大和最糟糕的事情之一就是你可以重写历史。这里有一个卑鄙的滥用方式，我想不出一个合法的理由这样做。

和其他事情一样，感谢 StackOverflow 为我提供的所有选择👍。

`GIT_COMMITTER_DATE="$(date)" git commit --amend --no-edit --date "$(date)"`