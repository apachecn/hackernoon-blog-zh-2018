# 用带调度的 Laravel 5 实现 cron 作业

> 原文：<https://medium.com/hackernoon/implementing-a-cron-job-with-laravel-5-with-scheduling-ef491d672f45>

![](img/38c649933af7d904b88bb683ae55ac57.png)

Photo by [Aron](https://unsplash.com/photos/BXOXnQ26B7o?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/time?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

Laravel 包括“调度”来处理 cron 作业。您可以在 Laravel 中使用一种富于表现力且易于使用的语法轻松地安排它们。

# 行程安排

“App\Console\Kernel”类处理 Laravel 中的所有调度。

但是你不应该在这里放入任何逻辑。这种方法应该只用于设置时间表。要实际定义要运行的内容，应该为每个要运行的任务创建一个新的 artisan 命令。您可以通过运行`php artisan make:command`创建一个新命令。如果您的命令叫做“HourlyUpdate ”,运行`php artisan make:command HourlyUpdate`。

它将在应用程序/命令中创建新文件。打开它，编辑它的“签名”。它将是从内核类和命令行调用您的任务时使用的名称。

编辑其描述。它应该是对命令目的的易读解释。

编辑它的句柄。句柄是执行任务的方法。

现在在“应用程序/控制台”中打开“Kernel.php ”,并将我们新创建的类添加到“命令”数组中:

[https://gist.github.com/01c903d943a91bbca4b4911489bb6b46](https://gist.github.com/01c903d943a91bbca4b4911489bb6b46)

编辑“时间表”来安排我们的任务。例如，`everyMinute()`每分钟运行一次任务，而`everyHour()`每小时运行一次。您也可以使用`monthlyOn(1, '00:01')`来指定一个月中的某一天。要查找所有可用选项的列表，请查看[官方文档][4]

您还可以告诉 Laravel 将任务的输出打印到一个文件`appendOutputTo(PATH)`。

对于我们的例子，我们将在每月初运行我们的任务。出于调试目的，我们还将输出保存到一个文件中，并且我们希望确保如果有另一个任务正在运行，一个任务不会启动。

[https://gist.github.com/5cb1a689c4b6dbe691e37ce4a0f241c0](https://gist.github.com/5cb1a689c4b6dbe691e37ce4a0f241c0)

# 测试

您可以通过从终端手动调用 cron 作业来测试它。您可以使用列出所有自定义动作和默认 artisan 动作

[https://gist.github.com/1b64da5a8797667df698f2d9c6a7ff5b](https://gist.github.com/1b64da5a8797667df698f2d9c6a7ff5b)

它将返回所有可用操作的列表。

[https://gist.github.com/f31b531ac186430fb08bc0bbf23a8a2f](https://gist.github.com/f31b531ac186430fb08bc0bbf23a8a2f)

“每月:配额”是我们的习惯动作。我们可以用`php artisan monthly:quota`来调用。

# 添加到 Cron

最后，您需要实际进入 Linux 安装中的 crontab，并告诉系统每分钟调用 Laravel。

然后，Laravel 将评估所有的 cronjobs，并决定是否需要调度某个东西来执行。如果它不需要执行任何动作，它会直接返回。

使用终端打开 crontab。它通常位于`/etc/crontab`中，并添加下面一行，确保写在您的 Laravel 项目的路径中。

[https://gist.github.com/03f2d39e6beb05d98f481a5cc902a0a7](https://gist.github.com/03f2d39e6beb05d98f481a5cc902a0a7)

我们还忽略了输出，因为 Laravel 已经允许我们配置如何处理输出。

*和往常一样，安排日程时要注意不同的时区。*

# 结论

Laravel 通过一个即时语法和许多配置选项，可以非常容易地安排任务以特定的时间间隔运行。

不到 24 小时，我就推出了[*Markdown Love*](https://markdown.love/)*，这是一个 API，可以将网络上的任何文章都变成 Markdown，用 Laravel。*

你可以在我的网站和推特上找到我。