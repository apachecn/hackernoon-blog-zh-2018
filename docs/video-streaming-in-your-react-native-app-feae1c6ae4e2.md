# React 原生应用中的视频流

> 原文：<https://medium.com/hackernoon/video-streaming-in-your-react-native-app-feae1c6ae4e2>

![](img/9319b8b7d6fc0756f0e3f41e42f26a69.png)

Photo by [Zach Meaney](https://unsplash.com/photos/XQdcBNRM8IY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/mobile-phone?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

我最近一直在与 react-native-video 库合作，将视频整合到应用程序中。我必须说，这是一个来自 react 原生社区的令人印象深刻的库，有许多贡献者和用户。

# 关键特征

该库的一些关键特性包括:

*   本地和远程文件回放支持。
*   带字幕的音频和文本轨道的选择。
*   可配置速率(提高或降低视频速度)。
*   应用程序在后台运行时的音频播放。
*   外部回放。
*   当然也支持 iOS 和 Android 设备。

这些特性使这个库成为 react 本机应用程序中视频流的可靠选择。

# 设置

iOS 和 Android 的设置都非常简单。

运行以下命令来安装 react-native-video 包

```
npm install --save react-native-video
```

链接 react-native-视频库，支持 iOS 和 Android

```
react-native link react-native-video
```

有了这两个命令，您就可以开始编码您的视频组件了。

# 开始使用组件

我已经创建了一个 *VideoComponent.js* ，它将用于渲染视频。任何时候我们想要在这个应用程序中播放视频，我们都可以使用 *VideoComponent* 。

您可以观察到， *renderVideo()* 方法从 react-native-video 库中调用了 *< Video >* 组件。

为了渲染这个组件，我们需要提供视频源和播放器在我们的应用程序中播放的样式。

在下面的例子中，我们提供了一个来自 assets 文件夹的 mp4 文件和一个播放器的基本样式。 *<视频>* 附带的所有其他道具都是可选的，根据我们的需要进行配置。

现在我们的 *VideoComponent* 已经准备好了，我们可以从任何页面/屏幕调用它。对于我们的例子，我将从 *App.js* 文件中调用它，这是我们应用程序的起始页面。

*render()* 方法调用< VideoComponent / >，这样我们就可以开始运行了。

运行 iOS 或 android 的代码

```
//iOS
react-native run-ios // Android
react-native run-android
```

模拟器现在打开，应用程序内有视频流。

![](img/278bb93c8e2e63e398203d987d481214.png)

Gif showing the emulator streaming video

# 有用的道具

一旦我们集成了基本的视频流，我们就可以使用这个库附带的道具了。

我们可以增加或减少音量，静音，确保应用程序后台播放音频，选择音频/文本轨道等..

需要注意的一点是新增了 *ignoreSilentSwitch* 道具。在我们的例子中，我将它设置为“*服从*”。这是 iOS 设备特有的，指的是用户设备上的硬件静音开关。如果您注意到设备上没有任何音频的视频流，设备静音开关可能已启用。你可以覆盖它并设置道具为"*忽略*"

请记住，将 *ignoreSilentSwitch* 设置为*“忽略”*可能不是理想的解决方案，因为这不会让用户满意。

# 远程文件回放

在前面的示例中，我们看到了一个从本地资产文件夹中回放的文件。集成同一个库来从远程 url 传输视频也很简单。

如下所示，修改源以从 url 提取视频。

```
source={{uri: '<REMOTE URL>'}}
```

欲了解更多关于该库的信息，请查看 [github](https://github.com/react-native-community/react-native-video) 上的文档。希望你喜欢将视频流集成到 react 原生应用中。

这个故事最初发表在[www.adhithiravichandran.com](http://adhithiravichandran.com)

更多教程和文章请在 twitter 上关注我@[adhithilavi](https://twitter.com/AdhithiRavi)并访问我的博客[www.adhithiravichandran.com](http://www.adhithiravichandran.com)