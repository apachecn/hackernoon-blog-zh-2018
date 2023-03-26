# 感恩——终端的感恩日记

> 原文：<https://medium.com/hackernoon/gratitude-a-simple-cli-gratitude-journal-32314e64fcf7>

我最近一直在尝试定期练习[感恩](https://hackernoon.com/tagged/gratitude)，以改善我的观点和整体情绪。它一直工作得很好，但我会不断地经历补丁，它会靠边站或我会平原忘记。

所以我建立了一个非常简单的提示来确保我每天花一点时间去感恩。

很好用，全局安装即可。

```
yarn global add gratitude
```

或者

```
npm i gratitude -g
```

并将其添加到您的 shell 的配置中。通常是`~/.bashrc`或`~/.zshrc`

```
#~/.bashrc or ~/.zshrc
gratitude
```

每天第一次打开终端时，系统会提示您。它会将您的答案写入 json db，并跟踪您当前的记录。如果你真的不想做，那就去做吧。虽然翘一天课会打破你的记录。

它也是适度可配置的。

```
| Option | Description               | Default              |
|--------|---------------------------|----------------------|
| times  | number of prompts per day | 1                    |
| db     | path to db file           | ~/.gratitude/db.json |
```

**举例:**

```
gratitude --times=2 --db=/some/folder/file.json
```

**改进:**

希望我有时间添加一些更智能的条纹计数，这样你就可以配置它忽略周末等。添加一些很酷的 cli 视觉效果来表示条纹和数据库也不错。

当然，欢迎任何意见或贡献。

保持乐观👍