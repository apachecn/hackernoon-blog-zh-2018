# [Utility Post]面向困惑和好奇者的 STDOUT、STDERR 和重定向

> 原文：<https://medium.com/hackernoon/utility-post-stdout-stderr-redirection-for-the-perplexed-curious-463dfbe377ee>

(Psst——如果你很忙，想要一份备忘单，请看这里:[https://github . com/valgaze/redirection-fun/blob/master/cheat sheet . MD](https://github.com/valgaze/redirection-fun/blob/master/cheatsheet.md)

简而言之，“重定向”是一种将输出和错误保存到文件(或完全隐藏它们)的方法，而不是它们在屏幕上显示的默认行为。这可能是有用的，因为有时可能希望存储输出/错误日志以供以后分析，而不是让它们在终端上匆匆而过。当使用长时间运行或嘈杂的[工具](https://hackernoon.com/tagged/tools)时，重定向也很有用。

例如，如果您想要运行 npm install，并将输出保存到一个文件中，将任何错误保存到另一个文件中，只需执行以下操作:

```
npm install >npm-log.txt 2>npm-errors.txt
```

[如果你对重定向和 npm 感兴趣，请查看配套回购的**/示例**目录:[https://github.com/valgaze/redirection-fun](https://github.com/valgaze/redirection-fun)

或者想象有一个叫做“myapp”的工具。如果您对“myapp”一无所知，不知道它的用途、功能或输出，那么对于下面的命令，您有什么可以确定的吗？

```
$ /dev/bin/myapp >logfile.log 2>&1
```

可能不多，但是如果 myapp 的行为像大多数其他命令行程序一样(并且您熟悉重定向！)，你至少可以这样说:

*   它的标准输出(如果有)将被写入 logfile.log
*   它的标准错误(如果有的话)也将被写入 logfile.log(与标准输出混合在一起)

本说明将解释这意味着什么，为什么会这样，并详细说明如何在终端中执行“重定向”的一些其他示例。

如果您想按照示例进行操作，请在命令提示符下键入以下内容:

```
git clone [https://github.com/valgaze/redirection-fun](https://github.com/valgaze/redirect-fun) redirection-fun && cd $_
```

# 流和重定向

无论何时在终端中运行一个命令，都可以访问三个“流”:标准输入(stdin)、标准输出(stdout)和标准错误(stderr)

简而言之，无论何时您看到运行命令的“好消息”可能来自其 stdout(标准输出)流，无论何时您看到任何“坏消息”(错误或警告)，它很可能来自 stderr(标准错误。)

默认情况下，一旦命令运行，这些所谓的“标准流”将始终显示在终端中。但是由于这些流是标准化的和明文的(即人类可读的),事实上你可以使用一个非常简单但是强大的语法将它们“重定向”到其他地方。

例如，当您使用 ["ls "命令](http://man7.org/linux/man-pages/man1/ls.1.html)时，您将会看到它的输出(即列出个文件的*列表)如预期的那样显示在您的终端中:*

```
😎 ~/redirection-fun ls1.json  3.json  5.json  README.md
2.json  4.json  6.json  example
```

这是因为 **STDOUT** 流(标准输出)是一个文件列表，STDOUT 流默认选择终端作为其目的地。

然而，如果我们使用重定向，而不是在终端显示，输出可以改为 ***重定向*** 到文件 *:*

```
😎  ~/redirection-fun ls 1>output.txt
```

如果检查 output.txt，您会发现它现在包含我们前面看到的 ls 命令的标准输出:

```
😎 ~/redirection-fun cat output.txt
1.json
2.json
3.json
4.json
5.json
6.json
README.md
example
output.txt
```

换句话说，stdout 不是显示在终端中，而是被“重定向”到 output.txt

对于错误，过程是相同的。如果您键入 ls 命令并要求它列出一个不存在的目录中的文件(这里是一个不存在的名为“bongo”的目录)，您将得到一个错误:

```
😎 ~/redirection-fun ls bongo/
ls: bongo/: No such file or directory
```

上面的错误来自 STDERR，和 STDOUT 一样，默认情况下它的目的地是终端。我们可以使用重定向将其保存到名为“errors.txt”的文件中:

```
😎  ~/redirection-fun ls bongo/ 2>errors.txt
```

如果你检查 errors.txt，猜猜你会发现什么？

```
😎  ~/redirection-fun cat errors.txt
ls: bongo/: No such file or directory
```

这就是重定向的意义所在——将数据从其默认目的地(终端)重定向到其他地方。正如您将在下面看到的，您可以将来自 stdout 和 stderr 的数据发送到单个文件、单独的文件，或者完全丢弃它。

# 标准输出

如果您正在家中进行跟踪([https://github.com/valgaze/redirection-fun](https://github.com/valgaze/redirect-fun))***，*** )，请尝试项目主目录中的以下内容:

```
😎 ~/redirection-fun ls 1>output.txt
```

您应该在屏幕上看不到任何输出，如果您检查 output.txt，它将被 ls 命令的 stdout 填充(即您的终端中显示的内容)。)

**重要提示:**特殊数字 1 被称为*文件描述符*，这里它代表标准输出，在引用 stdout 时可以安全地删除。以下所有命令与`ls 1>output.txt`相同:

```
ls 1>output.txt
ls 1> output.txt
ls > output.txt
ls >output.txt
```

对于本说明的其余部分，我们将遵循上面列表中最后一项的语法(没有文件描述符，没有用于 stdout 的空间。)如果文件 output.txt 不存在，上面的命令将创建它，如果它存在，将覆盖它。请注意，如果您将文件重定向到特定子目录(例如`ls > /usr/abcd/output.txt`)，该子目录必须已经存在，否则该命令将失败。

如果你想将 ***追加*** 到一个已存在的文件(不是覆盖)stdout，你可以做如下操作(两个箭头):

```
😎  ~/redirection-fun ls >>output.txt
```

如果您检查 output.txt，您会看到，由于我们运行了该命令两次，标准输出增加了一倍:

```
😎  ~/redirection-fun cat output.txt
1.json
2.json
3.json
4.json
5.json
6.json
README.md
example
output.txt
1.json
2.json
3.json
4.json
5.json
6.json
README.md
example
output.txt
```

我们知道如何将 stdout 写入一个文件，并将其附加到一个现有的文件中，但是如果我们想完全摆脱 stdout，该怎么办呢？如何运行一个完全没有任何(标准)输出的命令？

要丢弃所有的标准输出数据，您应该将命令重定向到位于路径 **/dev/null** 上的[“null”设备流](https://www.networkworld.com/article/3025497/linux/sending-data-into-the-void-with-dev-null.html)

```
😎  ~/redirection-fun ls >/dev/null
```

对于像 ls 这样的"[信息性的](https://hackernoon.com/tagged/informational)"命令，重定向到/dev/null 没有太大意义，但是对于其他命令(尤其是那些长时间运行和/或有噪声输出的命令)，重定向到/dev/null 是丢弃流数据的最佳方式。

概括地说，要重定向 stdout，您只需知道:

```
ls >/dev/tty # redirects to terminal, default behaviorls >output.txt # redirects stdout to a file, here output.txtls >>output.txt # appends stdout to a file, here output.txtls >/dev/null # discards stdout entirely by redirecting it to /dev/null
```

# 标准误差

标准错误(stderr)类似于 stdout，但是如果 stdout 是大多数“好消息”，那么 stderr 就是所有的“坏消息”大多数语法是相同的，但是，唯一的变化是 stderr 的文件描述符(数字 2)不能被删除。

要将错误重定向到名为 errors.txt 的文件，只需运行:

```
😎  ~/redirection-fun ls bongo 2>errors.txt
```

如果您检查 errors.txt，您将得到您的错误:

```
😎  ~/redirection-fun cat errors.txt
ls: bongo: No such file or directory
```

这与向现有文件追加内容是一样的:

```
😎  ~/redirection-fun ls bongo 2>>errors.txt
```

如果您在追加后检查 errors.txt，您将会看到预期的多个条目:

```
😎  ~/redirection-fun cat errors.txt
ls: bongo: No such file or directory
ls: bongo: No such file or directory
```

就像使用 stdout 一样，如果您想完全丢弃 stderr(既不在屏幕上显示也不写入文件)，只需将 stderr 重定向到空设备:

```
😎 ~/redirection-fun ls bongo 2>/dev/null
```

除了强制文件描述符(即数字 2)之外，重定向标准错误看起来与重定向标准输出非常相似:

```
ls 2>stderr.txt # Write stderr to filels 2>>stderr.txt # Append errors to filels 2>/dev/null # Discard stderr entirely
```

# 组合标准输出和标准误差

通常，您会希望同时捕获 stdout 和 stderr。

## 简单情况:分离文件(覆盖)

```
😎 ~/redirection-fun ls >output.txt 2>errors.txt
```

这里，stdout 被重定向到 output.txt，stderr 被重定向到 errors.txt

## 简单的情况:单独的文件(追加，不要覆盖)

```
😎 ~/redirection-fun ls >>output.txt 2>>errors.txt
```

就像上面一样，stdout 和 stderr 被*附加到它们新的目标文件的末尾*。

## 更棘手的情况:相同的文件(覆盖)

如果您希望 stdout 和 stderr 共享同一个文件，有多种方法。一种方法是使用“&num”语法“复制”流。首先重定向 stdout，然后通过使用&符号和数字指示 stderr 去 stdout 要去的地方，将 stderr 重定向到相同的目的地，例如:

```
😎  ~/redirection-fun ls >shared-output.txt 2>&1
```

这里，stdout (1)被重定向到 *output.txt* ，stderr (2)被重定向到 stdout 要去的任何地方(在本例中是 output.txt)

请注意，下面的结果与上面的结果相同:

```
😎  ~/redirection-fun ls 2>shared-output.txt >&2
```

这里，stderr (2)首先被重定向到 **shared-output.txt** ，然后 stdout (1)被重定向到 stderr 的目的地，因此它也转到 **shared-output.txt**

如果您想使用此语法将**追加** stdout 和 stderr 到同一个文件，您可以执行以下任一操作:

```
😎  ~/redirection-fun ls >>shared-output.txt 2>&1😎  ~/redirection-fun ls 2>>shared-output.txt >&2
```

在第一个命令中，stdout (1)在追加模式(两个箭头)下被重定向到 **shared-output.txt** ，stderr (2)跟在 stdout 后面，因此它也被重定向到 **shared-output.txt** 。在第二个命令中，逻辑完全相同，但是，stdout (1)跟随 stderr)的前导。

一种将 stdout 和 sterr 重定向到空设备的“可移植”方法(即将它们放入垃圾桶)，您可以编写如下代码:

```
😎  ~/redirection-fun ls >/dev/null 2>&1
```

## 危险:秩序很重要！

将 stdout 和 stderr 组合到同一个目标顺序时，顺序很重要。例如，如果我们打算让 stdout 和 stderr 共享一个名为 **shared-output-2.txt** 的文件，下面的命令将会失败:

```
😎 ~/redirection-fun ls >&2 2>shared-output-2.txt# This first command would fail and in fact displays stdout on the screen when run. The reason is that since order matters, stdout (1) copies stderr’s **initial default destination** (the terminal or /dev/tty) and then stderr (2) is redirected to **shared-output-2.txt**. Stdout was copying stderr's first destination (the terminal, it's default) and not it's final redirected destination (the file shared-output2.txt)😎 ~/redirection-fun ls 2>&1 >shared-output-2.txt# This command would **not** redirect stderr to **shared-output-2.txt**. Going in order, stderr (2) is redirected to stdout's initial destination (/dev/tty or terminal) and only later is stdout (1) redirected to shared-output-2.txt
```

概括地说，重定向 stdout 和 stderr 流…

## 不同的目的地:

```
😎  ~/redirection-fun ls >output.txt 2>errors.txt
😎  ~/redirection-fun ls >>output.txt 2>>errors.txt # (no overwrite)
```

## 相同目的地:

```
😎  ~/redirection-fun ls >shared-output.txt 2>&1
😎  ~/redirection-fun ls 2>shared-output.txt 1>&2
😎  ~/redirection-fun ls >>shared-output.txt 2>&1 # (no overwrite)
😎  ~/redirection-fun ls >/dev/null 2>&1
```

# 标准输入(stdin)和管道

## 标准输入(标准输入)

像 stdout 和 stderr 一样，标准输入(stdin)也是一种抽象，数据是如何生成的或来自哪里的细节并不重要。例如，程序的标准输入通常来自键盘等来源，但是重定向标准输入可以使命令的输入来自文件:

```
command < file
```

例如，如果您想列出名称中包含“json”的文件，您可以运行以下两个命令:

```
ls >files.txt # stdout to files.txt
grep json < files.txt # stdin to grep is from files.txt
```

这里`ls`列出了当前的文件/文件夹，而不是终端上显示的 stdout，stdout 被重定向到 files.txt。在下一个命令中，我们运行`grep json`并将 stdin 重定向到 grep。我们没有重定向`grep`的 stdout，所以当它运行时，会在终端中显示文件名中带有 json 的文件列表(如果有的话)。

## 平静的

管道非常类似于重定向到 stdout，但是管道不是写入文件，而是将 stdout 作为 stdin 重定向到另一个程序。

我们不是用两个命令编写上面的代码，而是通过管道完成同样的事情:

```
ls | grep json😎  ~/redirection-fun ls grep | json
1.json
2.json
3.json
4.json
5.json
6.json
```

`ls`命令列出了文件，然后它的 stdout 作为 stdin `grep json`被“传输”过来，后者的 stdout 没有被重定向，所以它显示在终端中。

# 有趣的例子:tweet 上的可启动 Live-CD

安全工程师 Alok Menghrajan 编写了一个小到可以压缩到 280 个字符的命令，为一个复古的计算机游戏生成一个可引导的 live-cd:

这个命令本身有点混乱，但是如果你想当然地认为 [**Alok 出色的 Perl 技巧**](https://www.quaxio.com/bootable_cd_retro_game_tweet/) ，它只是管道化和重定向:Perl 命令输出被管道化为一个 base64 编码的命令，然后 base64 操作的 STDOUT 被重定向到 cd.iso 文件——非常漂亮:

```
$ perl -E "ALOKS_PERL_TRICK" | base 64 -D >cd.iso
```

应该可以了，快乐重定向！

# 进一步阅读

*   [http://www.linfo.org/standard_output.html](http://www.linfo.org/standard_output.html)
*   http://www.linfo.org/output_redirection_operator.html
*   【http://wiki.bash-hackers.org/howto/redirection_tutorial 
*   [https://ryanstutorials.net/linuxtutorial/piping.php](https://ryanstutorials.net/linuxtutorial/piping.php)
*   [https://www . network world . com/article/3025497/Linux/sending-data-into-the-void-with-dev-null . html](https://www.networkworld.com/article/3025497/linux/sending-data-into-the-void-with-dev-null.html)
*   [http://www.informit.com/articles/article.aspx?p=2854374&序列号=5](http://www.informit.com/articles/article.aspx?p=2854374&seqNum=5)
*   [https://www.quaxio.com/bootable_cd_retro_game_tweet/](https://www.quaxio.com/bootable_cd_retro_game_tweet/)