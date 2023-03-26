# 利用开源的力量，不要重新发明轮子

> 原文：<https://medium.com/hackernoon/leverage-the-power-of-open-source-and-dont-reinvent-the-wheel-9f7c82042b19>

![](img/53f5d008b4a714103c984c90fdfeac20.png)

Photo by [rawpixel](https://unsplash.com/@rawpixel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在这篇文章中，我将谈论我如何需要一个实用程序，以及我如何克隆和修改一个存储库来实现我所需要的。

## 介绍

我在 [waveapplication](https://waveapplication.com/) 所做的事情之一是使用 calabash 进行自动化测试，以确保应用在发布到应用商店之前的质量。我们正在努力实现完整的持续集成和持续交付，为此，我们需要端到端的功能测试(除了单元和集成测试)。在这个特定的案例中，我需要在 Android 设备上伪造 GPS 位置。

## 要求

鉴于我将需要这个实用程序来设置应用程序到一个特定的状态，以便检查应用程序的特定特性/功能，我有如下具体要求。

*   基于终端的实用程序
*   轻量级选手
*   能够复制作为*传递给它的路线。gpx* 文件。

## 研究

在开始疯狂编码之前，我停下来对完成给定任务的可用方法和技术做了一些研究。我发现 android 的 ADB 为开发者提供了改变设备位置的特定命令。

## `geo fix`

给定十进制度数的两个参数(`Longitude`和`Latitude`)和可选参数`satelites`和`altitude`，它将 android 设备的位置模拟为给定的参数。
一旦我知道整个项目可以用`geo fix`完成，我就寻找已经实现了这个功能的开源库，因为我确信它们的存在。我找到的第一个项目是由 [luv](https://github.com/luv) 创建的 [mockgeofix](https://github.com/luv/mockgeofix) 这个库满足了我的所有要求，除了一个要求，它需要一个安装在 android 设备上的应用程序来模拟设备的位置。这对我来说不是一个选项，但是我还是决定克隆这个库并尝试一下。

## 远程登录

这将用于向给定端口和 IP 的 android 设备发送`geo-fix`命令。

## 让 mockgeofix 适应我的特定需求

这个工具有我需要的一切来模拟设备的 GPS 定位。它甚至可以根据给定的公里/小时速度调整路线。唯一不符合我要求的是需要一个外部应用程序才能工作，但幸运的是，有一个替代方案。这些是我根据自己的需要调整实用程序的步骤。

## 克隆存储库并删除我不需要的代码。

当我检查完代码后，我删除了所有的 java 代码(用于 android 应用程序),只保留了我感兴趣的文件`run_sim.py`和它所依赖的所有文件。我还创建了一个`requirements.txt`文件来简化启动和运行代码所需的过程。

## 添加 telnet 所需的 includes 和 telnet 安全验证的参数

要使用 Telnet，该程序要求您输入位于`~/.emulator_console_auth_token`的安全密码。这向客户端授予了执行`geo fix`等方法的权限。这意味着我必须在代码中包含参数选项。

## 改编主线

这个线程连接到 android 应用程序，然后向它发送`geo fix`命令。现在，它只需通过 telnet 连接到手机，并直接向其发送命令。

```
def start_geofix(args):
    # Start the telnet connection
    tn = telnetlib.Telnet(args.ip, args.port)
    # Wait until keyword "OK" appears
    tn.read_until("OK")
    # Perform security verification
    # The code is located in ~/.emulator_console_auth_token
    tn.write("auth " + args.auth + "\n")
    tn.read_until("OK")
    while True:
        # Send "geo fix" commands forever
        tn.write("geo fix %f %f\r\n" % (curr_lon, curr_lat))
        time.sleep(UPDATE_INTERVAL)
```

## 如何运行代码:

## 要求

1.  [python 2.7](https://www.python.org/downloads/release/python-2715/)
2.  [虚拟人](https://virtualenv.pypa.io/en/latest/)

## 使用

注意:要工作，这需要连接的设备或运行的模拟虚拟设备

1.  `git clone git@github.com:SergioRosello/mockgeofix.git`
2.  在项目根目录:`source ENV/bin/activate`
3.  在项目根目录:`pip install -r requirements.txt`
4.  在项目根目录:`python run_sim.py -i targetIP -g path/to/gpx/file.gpx -t emulatorConsoleAuthToken`

## `run_sym.py`的参数

下面是`run\_sym.py`脚本使用的参数列表。

## 必需的参数

*   `-i`或`--ip`:查找设备的 ip 地址
*   `-g`或`--gpx-file`:用于追踪路线的`.gpx`文件
*   `-t`或`--auth`:用于 telnet 认证的认证令牌

## 非必需参数

*   `-p`或`-port`:用于连接的端口。默认值:5554
*   `-S`或`--sleep`:在轨迹点之间睡眠。默认值:0.5
*   `-s`或`--speed`:以 km/h 为单位的速度(优先于-S)
*   `-I`或`--listen-ip`:运行一个 HTTP 服务器，可视化这个 ip 上被模仿的位置
*   `-P`或`--listen-port` : HTTP 服务器的端口。默认值:80

## 决赛成绩

项目的最终状态可以在这里看到[。它利用 telnet 的能力与电话通信，以便删除之前发送`geo fix`命令所必需的应用程序。最后，这段代码完成了前面列出的所有需求，我可以用它在端到端功能测试中模拟一条路由。](https://github.com/SergioRosello/mockgeofix)

## 结论

这个项目是我拥有的第一批真正的开源体验之一。这就是开源的意义所在。与世界各地的开发人员合作，使事情变得更容易。这真的是一件非常美好的事情。