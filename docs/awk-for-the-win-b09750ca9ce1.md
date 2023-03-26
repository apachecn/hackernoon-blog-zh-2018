# AWK 赢了

> 原文：<https://medium.com/hackernoon/awk-for-the-win-b09750ca9ce1>

![](img/93c19477fca5f8d422616c17cd51af27.png)

几个月前，我接到任务，要对 Creditas 的一些数据进行大规模更新。作为一个“ruby 主义者”，我想:“好吧，我将简单地构建一个 ruby 脚本来读取运营部门给我的 CSV 文件，并执行更新”。简单的任务，对吧？

是的，但是如果你仔细想想，用那种方法我不得不浪费时间写脚本，在本地或者在试运行环境中测试(脚本也有错误！)并最终设置生产中的一切来运行脚本。这很简单，但要做到这一切需要时间。

现在谁来拯救我们？

# AWK

[AWK](https://hackernoon.com/tagged/awk) 是一种[编程](https://hackernoon.com/tagged/programming)语言，创建于 1977 年。该语言是逐行解释的，其目的是使 shell 脚本更加强大。它基于 C 语言，通常用于**处理文本**和**操作文件。**在这里你可以找到 awk 的创作者们写的书:[https://www . Amazon . com/AWK-编程-语言-Alfred-Aho/dp/020107981X](https://www.amazon.com/AWK-Programming-Language-Alfred-Aho/dp/020107981X)

所以 AWK 给了你一些工具，可以让你快速处理文本和文件。让我们看一些例子:

假设您有一个包含以下内容的 **test.txt** 文件:

```
test1 test2 test3
```

如果您在控制台中键入以下命令:

```
awk ‘{print $0}’ test.txt
```

您将看到整个文件的结果:

```
test1 test2 test3
```

我们可以看到，AWK 给了我们一些变量，0 美元在控制台中打印文件的全部内容。现在，如果在最后一个命令中使用$1 变量，将会得到以下结果:

```
test1
```

变量$2 和$3 也是如此，分别打印“test2”和“test3”。

好的，但是 AWK 如何帮助我之前描述的场景呢？我将举例说明一个类似于我不得不处理的例子。假设您需要更新所有客户端的名称，您会收到一个 csv 文件，如下所示:

```
id,new_name
1,new_name_1
2,new_name_2
...
```

您可以处理 **test.csv** 文件，并使用如下命令生成所有更新语句:

```
awk -F "," '{print "UPDATE client SET name='\''"$2"'\'' WHERE id='\''"$1"'\''"}' test.csv
```

结果将会是:

```
UPDATE client SET name='new_name_1' WHERE id='1'
UPDATE client SET name='new_name_2' WHERE id='2'
...
```

注意，我们需要用 **-F** 标志指定逗号分隔符。而且我们还需要转义**’**字符。除了这两点，这个命令并不复杂。

现在有了所有的 update 语句，您可以直接连接到您的数据库并一次执行所有的语句。不需要连接到您的生产机器来执行任何脚本，也不需要为了保证 bug 安全性而在试运行环境中进行测试。

AWK 语言中还有很多其他的特性，比如 for 循环和 if 语句。我认为 AWK 是一种未知的语言，特别是对于那些开始职业生涯的人来说，一旦你习惯了它，你的工作效率会提高很多。你可以点击这个链接查看更多例子:[https://likegeeks.com/awk-command/](https://likegeeks.com/awk-command/)

大家到此为止，感谢阅读！