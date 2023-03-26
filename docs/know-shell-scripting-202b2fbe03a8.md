# 了解 Shell 脚本！！！

> 原文：<https://medium.com/hackernoon/know-shell-scripting-202b2fbe03a8>

![](img/19c4622b76ba5194b853944c37c3bdff.png)

什么是 [shell 脚本](https://hackernoon.com/tagged/shell-scripting)？它为 shell 编写一系列命令来执行它们。什么是贝壳？一个操作系统有两个主要组成部分外壳和内核，外壳所做的是解释命令，然后执行它们。

在本文中，我将带您经历编写 shell/bash 脚本的旅程。什么是空壳[剧本](https://hackernoon.com/tagged/script)？它是一个脚本或文件，其中写有一系列命令，可以由 shell 读取和执行。一个 shell 脚本有一个**。*sh*分机。Bash 代表(Bourne Again SHell)。Bash 不同于 shell。它是使用最多的 UNIX shell。在本文中，我们将编写 bash 脚本。**

让我们创建最简单的脚本。

1.  我们将创建一个文件 first.sh(您可以使用任何文本编辑器)
2.  第一行会是 **#！/bin/sh** (它告诉解释器这是一个 bash 脚本**)。**
3.  我们将编写一个命令 **ls** (列出目录中的子文件夹)

```
*#! /bin/sh* ls
```

然后要运行这个脚本，首先在 shell 中编写**bash**first . sh。所以，我们写了我们的第一个 shell 脚本，简单吗？你可能会想这有什么用，比如说我们必须一次又一次地手动编写一系列命令。我们可以只在 shell 脚本中编写命令，然后运行 shell 脚本，而不是运行一系列命令。

假设，您必须将一个提交推送到您的 git 存储库。我们可以编写一个 shell 脚本来自动化整个过程，而不是一个接一个地编写命令。创建文件 git.sh

```
#! /bin/sh 
git add .
git commit -m $1
git push origin master
```

在上面的脚本中，我们自动化了将提交推送到回购的整个过程。我在上面的代码中使用了$1，它就像是用户将提供的输入的占位符*一样。要运行这个脚本，编写**bash git . sh‘初始提交’**。这里的“初始提交”将放在$1。*

我们不是手动编写 git 命令，而是通过运行脚本来自动化这个过程。让我们深入研究一下 shell 脚本。

## 评论

shell 脚本中的注释后跟#。

```
# This is a comment.
```

## 变量

变量是使用=

```
MY_NAME='Morty' #No spaces 
```

使用 **$** 符号访问变量。

## **回音**

Echo 用于在 shell 中显示语句。这就像 c 语言中的 *printf* 。

```
MY_NAME='Morty' 
echo "My name is $MY_NAME"# My name is Morty
```

## **条件语句**

让我们看看 bash 脚本中的 if else 语句是什么样子的。

```
if [[condition]];
then
# write the *then instructions here
else* # write the else *instructions here
fi*
```

要了解更多关于 bash 的语法， [**查看这里**](https://devhints.io/bash) 。

![](img/1207510d2cd596484d03b18d258c83d7.png)

总结一下我们所学到的，让我们编写一个 bash 脚本，它自动执行 heroku 命令来创建一个 heroku 应用程序并将其推送到 heroku。
创建一个文件 **deploy.sh** 。

我们首先需要检查的是应用程序名称是否已被占用。我们可以通过向应用名称的 **url** 发送请求来检查它，并通过检查响应来检查应用名称是否存在。CURL 命令允许我们这样做。 ***收拢*** 命令在这里为我们提供了一个响应。让我们看看如何，

```
*#! /bin/sh*CURL_RESPONSE=`curl -s -o /dev/null -w "%{http_code}" 'https://www.youtube.com/'`echo $CURL_RESPONSE    #200
```

如果响应为 404，则该应用名称不存在。如果响应为 404，则运行 heroku 命令，否则将显示“App 已经存在”。第一步是使用应用程序名称创建请求的 url。

```
#! /bin/shHTTP='https://'
APP_NAME=$1
DOMAIN='.herokuapp.com'REQ_URL=$HTTP$APP_NAME$DOMAINecho $REQ_URL*# REQ_URL =* [*http://newname.herokuapp.com/*](http://newname.herokuapp.com/)
```

下一步是使用 curl 命令检查响应，就像我们上面做的那样。然后在 404 响应上运行 heroku 命令。完整的代码将如下所示

```
#! /bin/shHTTP='https://'
APP_NAME=$1
DOMAIN='.herokuapp.com'REQ_URL=$HTTP$APP_NAME$DOMAIN
*# REQ_URL =* [*http://newname.herokuapp.com/*](http://newname.herokuapp.com/)echo $REQ_URLCURL_RESPONSE=`curl -s -o /dev/null -w "%{http_code}" $REQ_URL`echo $CURL_RESPONSE       *#404,200,502,000*if [[ $CURL_RESPONSE -eq 404 ]];   *#app doesn't exist*then
heroku create $APP_NAME
git add .
git commit -m $2
git push heroku masterelse
echo App already exists
fi
```

运行这个:

```
bash deploy.sh firstapp "first commit"
```

这个脚本可以用于您想要在 heroku 上部署的任何项目。Shell 脚本自动化了整个过程。Hope，你学了一些 shell 脚本。如果你做到了，请尽情鼓掌，并跟随我了解更多。