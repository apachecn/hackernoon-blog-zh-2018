# TotalCloud BuildBot:一个高效快速的 Unity 构建管道开源工具(第 1 部分)

> 原文：<https://medium.com/hackernoon/totalcloud-buildbot-a-cost-efficient-rapid-unity-build-pipeline-open-source-tool-part-1-55b4d7a3ba96>

![](img/95b38462db0ac5fde53ab2338e5c9fbf.png)

不断生成 Unity 构建，对于游戏开发来说，使用个人电脑不是好玩的游戏。手动生成它们要麻烦得多，因为每个构建都需要花费大量时间来配置和编译。此外，由于内存利用率高，这些构建在个人计算机上很慢，并且容易出现频繁的人为错误。

自动化构建管道，将它们从开发人员的机器卸载到“云”中，或者使用 Travis CI 或 CircleCI 这样的构建平台，看起来似乎是可行的。然而，由于成本较高，开发商被迫采取替代路线。

> 下面是我们为什么要构建 BuildBot 的故事，build bot 是 Unity builds 的开源构建管道工具。

# Unity 构建的时间和成本分析

我们 TotalCloud.io 从一开始就利用 Unity3D 平台来构建我们的沉浸式可视化云管理平台。毫无疑问，Unity3D 是一个出色的多平台游戏引擎，无论是游戏开发还是非游戏交互模拟和可视化。

然而，解决 Unity 3D 的高构建时间对我们来说是一个相当大的挑战。有时，生成一个构建所花费的时间接近一个小时。有时，我们每天推进接近 10 个构建！现在，想象一下我们花在生成构建上的疯狂的时间。我们希望我们的开发人员尽快看到构建中的变化，而不是等待几个小时。

更糟糕的是，随着构建时间的增加，成本也在增加。我们迫切需要一种解决方案，能够在非常短的时间内自动构建，而无需使用昂贵的构建平台。

就像我们一样，让独立游戏开发者和小工作室远离构建平台或 CI 解决方案的一个重要因素是生成游戏构建的成本。不像大型游戏工作室可能利用内部构建服务器或 CI 服务器来持续生成他们的构建，这些独立开发者或小团队没有那种钱来挥霍在硬件和其他构建平台上。

> **因此，如果一个游戏开发者每天生成 10 个版本，那么每月的总成本将达到数百美元，这对他来说是一笔沉重的开支。**

除了成本和时间因素，手动生成 Unity 构件还有其他缺点，例如:

*   无法处理复杂的构建管道
*   对环境的控制更少
*   使得代码签名变得困难
*   减少团队协作

所有这些因素导致我们构建 TotalCloud BuildBot，这是一个用于 Unity builds 的开源构建管道工具。

现在，让我们更深入地了解一下每次构建的成本。

# 每次构建的成本比较:CircleCI Vs. Unity Cloud Vs. AWS

让我们假设您每天生成 10 个构建，并且能够并行进行 3 个构建。这相当于每月大约 300 次构建。如果每个构建花费了将近 30 分钟，那么你每个月将消耗大约 9000 分钟的构建时间。现在，让我们快速比较一下不同构建平台的成本:

*   使用 CircleCI，每月的费用在 249 美元到 449 美元之间
*   有了 Unity Cloud Build，每个月大概要花 125 美元购买 Pro 许可证，同时构建只需要几美元
*   有了 AWS，如果你在基于 AMI 和 i3 的 Windows 上使用 EC2 实例，每个月大概要花费 6 到 7 美元

这清楚地表明，使用 AWS 是最具成本效益的解决方案之一。

> 因此，我们利用 AWS 并构建了 BuildBot，这是一个开源的按需构建管道工具，允许独立开发人员或小型团队每月生成数百个构建，其成本仅是市场上任何其他解决方案的一小部分。

# 关于我们为什么选择 AWS Spot 实例和 Windows AMI 的更多信息

使用 EC2 spot instance 和 Windows Base AMI 和 i3.large machine 而不是 Mac OS 的原因是，在 Linux 中生成 Unity builds 是非常不可靠的，并且由于 headless 模式问题，大多数时候都不工作。另一方面，AWS 没有 Mac OS AMIs。这就给我们留下了 Windows 机器。

并且，i3.large 是一个大机器，15.25 GB 内存，475 SSD 存储，读写能力高，CPU 好，足以生成更大的 builds。此外，我们将利用 NNVM SSD 卷来更快地生成构建，而不是使用内置的 EBS 卷。

由于构建机器只需要在需要时启动，我们将利用 AWS Spot 实例来实现成本节约。因为，在 70%的折扣率下，大型 spot 实例每小时的费用约为 0.0468 美元。我们可以很容易地在机器不使用时停止它，并在新的提交可用时快速启动它。我们将在即将发布的构建管道帖子中对此进行更多讨论。现在，让我们带您了解在 AWS 中生成 Unity builds 的步骤。

# 如何在 AWS 中生成 Unity 构建

1.使用 Windows 2016 Base AMI 和 i3 从 AWS 控制台启动并连接 EC2 Spot 实例。对于不熟悉 AWS 或者想快速了解如何从 AWS 控制台启动 i3.large 实例类型的开发人员，[点击这里](https://blog.totalcloud.io/creating-aws-ec2-spot-i3-large-instance-using-windows-ami/)。

2.在 Windows 计算机中，从“开始”菜单启动 Powershell ISE。使用下面的 Powershell 脚本创建两个文件。第一个脚本是创建生成版本所需的额外 SSD 卷；第二个脚本是将 Unity3D 快速安装到 Windows 机器上。

下面是创建附加 NNMV SSD 卷的脚本。在脚本中，我们将它命名为“Jenkins Volume ”,因为它将是我们的 Jenkins Build Pipeline 的一部分。

```
If (!(Test-Path D:)) {Get-Disk | Where-Object IsOffline –Eq $True | Set-Disk –IsOffline $False$newdisk = @(get-disk | Where-Object partitionstyle -eq ‘raw’)$Labels = @(‘Jenkins’,’Other1',’Other2')for($i = 0; $i -lt $newdisk.Count ; $i++){$disknum = $newdisk[$i].Number$dl = get-Disk $disknum |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSizeFormat-Volume -driveletter $dl.Driveletter -FileSystem NTFS -NewFileSystemLabel $Labels[$i] -Confirm:$false}}
```

下面是安装 Unity3D 引擎的脚本。

页（page 的缩写）s:确保将这个 Powershell 脚本保存在 c 盘中。

```
$uri_editor = “[https://download.unity3d.com/download_unity/a9f86dcd79df/Windows64EditorInstaller/UnitySetup64-2017.3.0f3.exe](https://download.unity3d.com/download_unity/a9f86dcd79df/Windows64EditorInstaller/UnitySetup64-2017.3.0f3.exe)" ;$uri_linux_support = “[https://download.unity3d.com/download_unity/a9f86dcd79df/TargetSupportInstaller/UnitySetup-Linux-Support-for-Editor-2017.3.0f3.exe](https://download.unity3d.com/download_unity/a9f86dcd79df/TargetSupportInstaller/UnitySetup-Linux-Support-for-Editor-2017.3.0f3.exe)" ;$uri_mac_support = “[https://download.unity3d.com/download_unity/a9f86dcd79df/TargetSupportInstaller/UnitySetup-Mac-Support-for-Editor-2017.3.0f3.exe](https://download.unity3d.com/download_unity/a9f86dcd79df/TargetSupportInstaller/UnitySetup-Mac-Support-for-Editor-2017.3.0f3.exe)" ;$out_editor = “C:\Users\Administrator\Documents\UnitySetup64.exe” ;$out_linux_support = “C:\Users\Administrator\Documents\UnitySetup-Linux-Support.exe” ;$out_mac_support = “C:\Users\Administrator\Documents\UnitySetup-Mac-Support.exe” ;$start_time = Get-Date ;$wc = New-Object System.Net.WebClient ;$wc.DownloadFile($uri_editor, $out_editor) ;Write-Output “Download complete, $out_editor > Time taken: $((Get-Date).Subtract($start_time).Seconds)s” ;$wc.DownloadFile($uri_linux_support, $out_linux_support) ;Write-Output “Download complete, $out_linux_support > Time taken: $((Get-Date).Subtract($start_time).Seconds)s” ;$wc.DownloadFile($uri_mac_support, $out_mac_support) ;Write-Output “Download complete, $out_mac_support > Time taken: $((Get-Date). Subtract($start_time).Seconds)s”;
```

3.从 c 盘的 Unity 文件夹中查找 Unity 安装程序，并为 Windows、Linux 以及 MacOS 安装软件包。

4.等待 Unity 安装。

5.从 Repo 下载您的代码，并查找您想要编译构建的项目的路径。

6.创建一个 BAT 文件，在构建生成时自动存储它们。这是一个 BAT 文件的例子。确保您为相应的命令添加了正确的路径。示例:在代码的第 2 行使用您用来保存项目的项目路径 URL(在引号内)。

```
C:\Development\Unity\Editor\Unity.exe -quit -batchmode -projectPath "D:\jenkins\workspace\unity-dev-build-4x"-buildWindows64Player "D:\jenkins\workspace\unity-dev-build-4x\builds\win\64\TotalCloud.exe" -buildOSXUniversalPlayer "D:\jenkins\workspace\unity-dev-build-4x\builds\osx\64\TotalCloud.app" -buildLinux64Player "D:\jenkins\workspace\unity-dev-build-4x\builds\linux\64\TotalCloud.x86_64" -logFile "D:\jenkins\workspace\unity-dev-build-4x\logs\35.txt" || EXIT /B 1
```

7.尝试生成一个新的构建，并检查新的构建是否在输出路径中可用，以及日志文件是否在它们的指定路径中。

一定要尝试一下，并分享你的经验。

> **这是提供使用 TotalCloud BuildBot 的踏脚石的三部分系列博客的第 1 部分。请继续关注我们的第二篇博客，我们将提供关于如何使用 BuildBot 进行 CI/CD 管道的详细信息。**

*原载于 2018 年 3 月 14 日*[*blog . total cloud . io*](https://blog.totalcloud.io/totalcloud-buildbot-a-cost-efficient-rapid-unity-build-pipeline-open-source-tool-part-1/)*。*