# 如何使用 linbo-remote 轻松管理学校电脑

> 原文：<https://medium.com/hackernoon/how-to-use-linbo-remote-to-manage-school-computers-with-little-to-no-effort-bf0c7d0cecab>

![](img/9b40795155fc139df885e8ae615e3f87.png)

Linbo 是[linuxmuster.net](http://www.linuxmuster.net/en/start)最好的功能之一——一个免费的[学校服务器解决方案](https://openschoolsolutions.org/4-linux-school-server-comparison/)。我已经在我的最后一篇文章的[中概述了 Linbo 的可能性。在那里我提到了一个简单的超级命令行程序，用于管理学校的计算机:`linbo-remote`。你可以做任何你想用 Linbo GUI 做的事情，例如](https://openschoolsolutions.org/linbo-one-best-features-linuxmuster-net/)

*   对计算机进行分区和格式化，启动、关机等。
*   在一个房间或整个学校分发新图像(结合局域网唤醒)
*   随时同步计算机，让您选择的操作系统启动
*   在计算机上创建图像并将它们上传到服务器
*   将`linbo-remote`与其他脚本结合

今天我想描述一下我们在学校是如何使用`linbo-remote`的，并展示一下你可以用它做什么。

# 使用 linbo-remote 部署新映像

我们定期更新我们的 Ubuntu 映像，我们在学校的所有电脑上都使用它。我们通常在节假日或有新的重大更新时这样做(例如 Firefox、LibreOffice、重要的安全更新)。使用以下命令，我们将映像部署到房间中的所有计算机:

```
$ linbo-remote -r room1 -p sync:1,halt -w 0
```

或者

```
$ linbo-remote -r room1 -c sync:1,halt -w 60
```

单个参数含义的简短解释。

*   `-r`表示房间。用`-g`你也可以指定一个组，用`-i`指定一个 IP 或主机名。
*   `-w`代表网络唤醒，即`linbo-remote`唤醒电脑。局域网唤醒必须在 BIOS 中激活，并且只能通过插入的网线工作(例如，对于笔记本电脑)。`-w`后面的数字指定等待 Linbo 执行命令的秒数。
*   `-p`和`-c`的区别在于`-p` Linbo 在服务器上用命令创建一个文件。计算机一启动到 Linbo 就能得到这个文件。`-c`尝试通过 SSH 将命令直接发送到电脑。这就是为什么我输入了 60 秒的等待时间，以便有足够的时间唤醒和引导所有的计算机。
*   在`-c`和`-p`之后，您可以指定不同的命令。输入`linbo-remote -h`获得所有可能性。几乎所有的命令后面都有一个冒号，后面是操作系统的编号。例如，如果你有一个 Windows 和 Ubuntu 的双引导系统，Windows 得到数字 1，Ubuntu 得到数字 2。
*   `sync:1`，halt 表示`linbo-remote`同步电脑上的第一个操作系统，然后关闭电脑。
*   `format:1,sync:1,sync:2,start:2`指 Linbo 格式化并同步第一个操作系统的分区，然后同步第二个操作系统并启动。同步时，Linbo 会检查服务器上是否有新的映像，如果有必要就下载它，并将其复制到相应的分区。

# 学校计算机自动化管理

我们已经在上面看到了`linbo-remote`的可能性。比如连接一个 cronjob，我们可以每天早上同步启动学校或者某个房间的所有电脑，让所有电脑都“新鲜”起来上课用。

作为 root 用户，您可以创建一个新的 cronjob

```
root@server$ crontab -e
```

并输入以下 cronjob:

```
30 6 * * * * /usr/sbin/linbo-remote -r room1 -w 0 -p format:1,sync:1,sync:2,start:2 > /dev/null 2>&1
```

每天早上 6:30，一年中的每一天，Linbo 现在都在 1 号房间照顾我们的电脑，同步两个操作系统，并启动第二个操作系统，这样学生就可以直接登录。

你也可以使用`linbo-remote`关闭所有引导到 Linbo 的电脑。

```
0 13,15,17,20 * * * * /usr/sbin/linbo-remote -r room1 -c halt > /dev/null 2>&1
```

下午 1 点、3 点、5 点和 8 点，1 号房间里所有启动过 Linbo 的电脑都会被关闭。

这些 cronjobs 非常有助于每天早上在学校的计算机上创建一个可靠的状态。这种解决方案的缺点是 cronjobs 每天都在执行，甚至在周末或假期也是如此。但是我们可以解决这个问题🙂

# 将 linbo-remote 与其他程序相结合

Linuxmuster 社区的一名成员编写了一个脚本来检查今天是否是上学日。因为[的原剧本](https://github.com/anschuetz/linuxmuster/tree/master/schultag)是用德语写的，所以我翻译了它。您可以[在 Github](https://github.com/cdscacth/linuxmuster-scripts/tree/master/school-day) 上找到它，并将其安装在服务器上，如下所示:

```
$ git clone [https://github.com/cdscacth/linuxmuster-scripts.git](https://github.com/cdscacth/linuxmuster-scripts.git) $ $ cd linuxmuster-scripts/school-day
```

在文件`holidays.conf`和`exceptions.conf`中，你必须输入公众假期和学校假期的日期，如下所示:

```
# Public Holiday
20180301 Public Holiday 1
20181015 Public Holiday 2# School Holidays
20171223–20180112 Christmas Break
20180319–20180417 Easter Break
20180625–20180813 Summer Break
```

然后，您安装脚本与

```
$ sudo make install
```

如果是学校日，脚本返回返回值“0”，否则，例如“99”(表示假期)。

我们可以调整上面的 cronjobs，使它们只在一个上学日执行:

```
30 6 * * * * is-today-school-day && /usr/sbin/linbo-remote -r room1 -w 0 -p format:1,sync:1,sync:2,start:2 > /dev/null 2>&10 13,15,17,20 * * * * is-today-school-day && /usr/sbin/linbo-remote -r room1 -c halt > /dev/null 2>&1
```

# 结论

Linbo，尤其是`linbo-remote`帮我们做了很多工作。虽然我们在第一年做了很多“运动鞋管理”，但现在 linbo-remote 为我们做了。我们在学校里从一台电脑跑到另一台电脑，为他们提供新形象的日子已经一去不复返了。对我来说，Linbo 是 linuxmuster.net 最好的功能，我还不知道任何其他软件可以帮助我们每天自动创建一个定义好的状态。一旦映像在计算机上，它也可以离线工作，也就是说，如果有东西“坏了”，学生可以自己重置他们的计算机。

*最初发表于*[*openschoolsolutions.org*](https://openschoolsolutions.org/linbo-remote-manage-school-computers/)*。* ***注册*** *订阅我们的时事通讯，获取免费的 PDF 格式的开源应用程序，供您的课堂使用，或者在 Twitter 上关注*[*@ OpenSchoolZ*](https://twitter.com/OpenSchoolZ)*。*