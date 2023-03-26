# 如何在 GRUB 中设置调度

> 原文：<https://medium.com/hackernoon/how-to-set-up-scheduling-in-grub-224d56371e71>

![](img/638f3528b35832b5bceb71b1792aa114.png)

[GRUB 2](https://en.wikipedia.org/wiki/GNU_GRUB#Version_2_(GRUB)) 将很多功能外包成模块。这使得只加载所需的函数成为可能，因此更小的核心映像也是可能的。您还可以使用这些模块来扩展 GRUB，例如在 GRUB 中实现调度。这使得在特定时间启动特定的操作系统成为可能。

您将获得所有可用模块的列表

```
$ ls /boot/grub/i386-pc/
```

# 在 GRUB 中配置调度

对于 GRUB 中的调度，您需要模块`datehook`。这允许访问 GRUB 中的以下变量:

*   天
*   小时
*   分钟
*   第二
*   月
*   工作日
*   年

所有这些值都基于 BIOS 中设置的时间和日期(通常是主板硬件时钟)。在 GRUB 中设置调度时，您必须记住这一点。

使用以下命令，您可以获得 GRUB 菜单中所有菜单项的列表:

```
$ grep -E ‘^menuentry|^submenu’ /boot/grub/grub.cfg | cut -d ‘“‘ -f2 | cut -d “‘“ -f2
```

第一个条目的编号为 0，第二个条目的编号为 1，依此类推。

接下来打开文件***/boot/grub/grub . CFG***，在文件开头添加以下几行:

```
insmod datehook# Add an extra zero for minute 0–9, so MINUTE is always double-digit
if[ $MINUTE -lt 10 ]; then PADDING="0"; else PADDING=""; fi
TIME=$HOUR$PADDING$MINUTE# Start Ubuntu by default
set default=0# Start Windows 10 from 5:00 p.m. to 11:00 p.m.
if[ $TIME -ge 1700 -a $TIME -lt 2300 ]; then
  set default=3
fi
```

开始时，会为小于 10 的分钟插入一个额外的零，因此分钟数始终是两位数。然后，我们构造一个新的变量`TIME`，它具有 HHMM 格式的当前时间。这些现在可以用来在 GRUB 中实现调度。

Ubuntu 应该默认启动，但是晚上 Windows 10 优先。可能有必要调整时间，因为时区 UTC 通常是在 BIOS 中设置的。

# 结论

以上更改仅在下一次执行`update-grub`前有效。要永久保留 GRUB 中的更改，您应该在 **/etc/grub.d/** 中用这段代码创建一个模板。

我们使用这个选项来更好地管理学校的电脑。Ubuntu 默认在白天启动，但是在晚上我们让 Linbo 启动，这样[电脑就可以更新或者提供一个新的镜像](https://openschoolsolutions.org/linbo-remote-manage-school-computers/)。

*原载于 2018 年 4 月 2 日*[*【openschoolsolutions.org】*](https://openschoolsolutions.org/set-up-scheduling-in-grub/)*。* ***注册*** *订阅我们的时事通讯，获取免费的 PDF 格式，并为您的课堂提供出色的开源应用程序，或者在 Twitter 上关注*[*@ OpenSchoolZ*](https://twitter.com/OpenSchoolZ)*。*