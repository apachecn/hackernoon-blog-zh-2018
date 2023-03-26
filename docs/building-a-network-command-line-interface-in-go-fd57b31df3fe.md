# 在 Go 中构建网络命令行界面

> 原文：<https://medium.com/hackernoon/building-a-network-command-line-interface-in-go-fd57b31df3fe>

![](img/b3dfabd91b8e64dc4993e38caa28af92.png)

credit — [http://www.lighthouseabudhabi.org/global-network/](http://www.lighthouseabudhabi.org/global-network/)

在本文中，我们将使用 Github 上的`urfave/cli`包在 Go 中构建一个非常简单的命令行界面:[https://github.com/urfave/cli](https://github.com/urfave/cli)。

最近，我在不同的主机提供商之间进行了一两次域名迁移，我认为开发一个工具或程序来查询网站的域名服务器、域名、IP 地址等等是一个很酷的想法。

这篇特别教程的总体目标是给你一个如何构建你自己的 CLI 的想法，它可以做各种各样的事情，比如网络监控、图像处理等等。

> *本教程的完整代码可以在这里找到:*[*TutorialEdge/Go/Go-CLI-tutorial*](https://github.com/TutorialEdge/Go/tree/master/go-cli-tutorial)

# 热门项目

Golang 越来越受欢迎，我们已经看到像 Hashicorp 这样的大型企业在许多不同的工具和系统中采用了这种语言。出于很好的理由，Go 的设计非常适合这些类型的应用程序，并且能够轻松地为所有主要平台交叉编译二进制可执行文件是一个巨大的胜利。

# 入门指南

让我们在计算机上创建一个名为`go-cli/`的新目录，或者类似的东西。我们将为我们的项目创建一个如下所示的目录结构:

```
go-cli/
- pkg/
- cmd/my-cli/
- vendor/
- README.md
- ...
```

> *这种结构遵循了 Github 上广泛接受的* [*Go 项目布局*](https://github.com/golang-standards/project-layout) *指南。*

# 进入代码

现在我们已经有了一个基本的项目结构，我们可以开始我们的应用程序了。首先，在新的`cmd/my-cli/`目录中，我们需要一个名为`cli.go`的新文件。我们将用一个非常简单的`Hello World`类型的应用程序来填充它，并将它作为我们发展的基础。

```
// cmd/my-cli/cli.go
package main

import (
  "fmt"
)

func main() {
  fmt.Println("Go CLI v0.01")
}
```

然后，我们可以通过键入以下命令，尝试从项目的根目录运行该命令:

```
➜ go run cmd/my-cli/cli.go Go CLI v0.01
```

太好了，我们已经完成了新 CLI 的构建，现在让我们看看如何添加一些命令并使其变得有用。

# 我们的第一个命令

因为我们将使用`urfave/cli`包，所以我们需要在本地下载这个包以便使用它，我们可以通过一个简单的`go get`命令这样做:

```
$ go get github.com/urfave/cli
```

现在我们有了必要的包，让我们更新我们的`cli.go`文件来使用这个包，并为我们创建一个新的 CLI 应用程序:

```
// cmd/my-cli/cli.go
import (
  "log"
  "os"

  "github.com/urfave/cli"
)

func main() {
  err := cli.NewApp().Run(os.Args)
  if err != nil {
    log.Fatal(err)
  }
}
```

当我们现在运行时，您会看到它充实了我们的程序响应，并添加了版本、我们如何使用 cli 以及我们可以使用的各种命令等内容。

```
➜  go run cmd/my-cli/cli.go
NAME:
   cli - A new cli application

USAGE:
   cli [global options] command [command options] [arguments...]

VERSION:
   0.0.0

COMMANDS:
     help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --help, -h     show help
   --version, -v  print the version
```

太棒了，这很快开始看起来像一个更完美的项目，而不仅仅是一个次要的项目！

我们现在可以开始添加我们自己的`Commands`。这些命令中的每一个都将与我们的一个测试相匹配，所以我们将有一个命令:`ns`，当它被触发并被提供了一个`url`时，它将关闭并查找那个特定主机的名称服务器。

我们最终的命令列表如下所示:

*   `ns` -将检索名称服务器
*   `cname` -将查找给定主机的 CNAME
*   `mx` -将查找给定主机的邮件交换记录
*   `ip` -将查找给定主机的 IP 地址。

简单明了，让我们从创建第一个命令开始:

```
package main

import (
	"fmt"
	"log"
	"net"
	"os"

	"github.com/urfave/cli"
)

func main() {
	app := cli.NewApp()
	app.Name = "Website Lookup CLI"
	app.Usage = "Let's you query IPs, CNAMEs, MX records and Name Servers!"

	// We'll be using the same flag for all our commands
	// so we'll define it up here
	myFlags := []cli.Flag{
		cli.StringFlag{
			Name:  "host",
			Value: "tutorialedge.net",
		},
	}

	// we create our commands
	app.Commands = []cli.Command{
		{
			Name:  "ns",
			Usage: "Looks Up the NameServers for a Particular Host",
			Flags: myFlags,
			// the action, or code that will be executed when
			// we execute our `ns` command
			Action: func(c *cli.Context) error {
				// a simple lookup function
				ns, err := net.LookupNS(c.String("url"))
				if err != nil {
					return err
				}
				// we log the results to our console
				// using a trusty fmt.Println statement
				for i := 0; i < len(ns); i++ {
					fmt.Println(ns[i].Host)
				}
				return nil
			},
		},
	}

	// start our application
	err := app.Run(os.Args)
	if err != nil {
		log.Fatal(err)
	}
}
```

然后，我们可以通过键入以下命令来运行它:

```
$ go run cmd/my-cli/cli.go ns --url tutorialedge.net
```

这将返回我的站点的名称服务器，并在终端中打印出来。我们还可以运行 help 命令，该命令将向我们展示如何在 CLI 中使用我们的新命令。

# 查找 IP 地址

在我们的程序中，我们所有的命令定义看起来都很相似，除了我们如何打印结果。`net.LookupIP()`函数返回一部分 IP 地址，因此我们必须迭代这些地址，以便以一种漂亮的方式打印出来:

```
{
	Name:  "ip",
	Usage: "Looks up the IP addresses for a particular host",
	Flags: myFlags,
	Action: func(c *cli.Context) error {
		ip, err := net.LookupIP(c.String("host"))
		if err != nil {
			fmt.Println(err)
		}
		for i := 0; i < len(ip); i++ {
			fmt.Println(ip[i])
		}
		return nil
	},
},
```

# 仰望我们的 CNAME

然后我们可以添加我们的`cname`命令，该命令将使用`net.LookupCNAME()`函数和我们传入的主机，并返回一个 CNAME 字符串，然后我们可以打印出来:

```
{
	Name:  "cname",
	Usage: "Looks up the CNAME for a particular host",
	Flags: myFlags,
	Action: func(c *cli.Context) error {
		cname, err := net.LookupCNAME(c.String("host"))
		if err != nil {
			fmt.Println(err)
		}
		fmt.Println(cname)
		return nil
	},
},
```

# 查找 MX 记录

最后，我们希望能够查询给定主机的邮件交换记录，我们可以通过使用`net.LookupMX()`函数并传入我们的主机来实现。这将返回 mx 记录的一部分，像我们的 IPs 一样，我们必须迭代才能打印出来:

```
{
	Name:  "mx",
	Usage: "Looks up the MX records for a particular host",
	Flags: myFlags,
	Action: func(c *cli.Context) error {
		mx, err := net.LookupMX(c.String("host"))
		if err != nil {
			fmt.Println(err)
		}
		for i := 0; i < len(mx); i++ {
			fmt.Println(mx[i].Host, mx[i].Pref)
		}
		return nil
	},
}, 
```

# 构建我们的 CLI

既然我们已经有了一个基本的 CLI 并在运行，是时候构建它了，这样我们就可以在生气时使用它。

```
$ go build cmd/my-cli/cli.go
```

这将编译一个`cli`可执行文件，我们可以像这样运行它:

```
$ ./cli help
NAME:
   Website Lookup CLI - Let's you query IPs, CNAMEs, MX records and Name Servers!

USAGE:
   cli [global options] command [command options] [arguments...]

VERSION:
   0.0.0

COMMANDS:
     ns       Looks Up the NameServers for a Particular Host
     cname    Looks up the CNAME for a particular host
     ip       Looks up the IP addresses for a particular host
     mx       Looks up the MX records for a particular host
     help, h  Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --help, -h     show help
   --version, -v  print the version
```

如您所见，我们所有的命令都成功地列在了输出的 commands 部分。

# 结论

因此，在本教程中，我们成功地使用 Github 的`urface/cli`包构建了一个非常简单而有效的 CLI。CLI 可以针对任何主要的操作系统进行交叉编译，而且它具有生产级命令行界面的所有功能。

如果你喜欢我的文章并希望支持我，那么请随时查看我的 YouTube 频道:

[](https://youtube.com/tutorialedge) [## 教学大纲

### 嗨伙计们！欢迎来到我的频道，TutorialEdge！这是我将张贴我所有视频教程的地方…

youtube.com](https://youtube.com/tutorialedge) 

*原载于【tutorialedge.net】[](https://tutorialedge.net/golang/building-a-cli-in-go/)**。***