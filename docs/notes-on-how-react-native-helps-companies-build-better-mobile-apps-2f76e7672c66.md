# 关于 React Native 如何帮助公司构建更好的移动应用的说明

> 原文：<https://medium.com/hackernoon/notes-on-how-react-native-helps-companies-build-better-mobile-apps-2f76e7672c66>

![](img/93b69907c1e3d051dedb56a8a25f9698.png)

这些是我最近在 [F8 2018](https://developers.facebook.com/videos/?category=f8_2018) 脸书开发者大会 [React Native](https://hackernoon.com/tagged/react-native) 专题小组上的笔记。点击观看原视频[。](https://developers.facebook.com/videos/f8-2018/how-react-native-helps-companies-build-better-mobile-apps/)

# 用在哪里

*   脸书 —市场、工作、安全检查等
*   Skype — Skype 应用程序
*   TaskRabbit — Tasker 应用程序
*   Postlight — [奥杜邦鸟类指南](https://itunes.apple.com/us/app/audubon-bird-guide/id333227386?mt=8)
*   CondeNast — [Vogue iOS 应用](https://itunes.apple.com/us/app/vogue/id1087973225?mt=8)

# 多个平台之间的代码重用

*   脸书: **~93%**
*   Skype:**85–90%**
*   TaskRabbit: **~86%**
*   后置灯:**90–95%**

# 为什么选择它

## 脸谱网

*   特定于平台的代码库导致
*   开发人员遇到不同的困难
*   每个平台以不同的速度移动
*   失去同步
*   做同样的事情两次
*   两个平台的完善/优化并不一致

## 网络电话

*   平台:iOS、Android、Windows、Mac、Linux——使用 ReactNative 或 Electron+React
*   **在反应之前**
*   跨多个时区的每个平台有 7 名开发人员和 7 名产品负责人
*   主要问题是不同时区的不同平台之间的通信
*   **反应后**
*   从特定于平台的团队转变为专注于跨不同平台交付特性的特性团队
*   特性团队减少了沟通挑战，提高了开发速度
*   减少不同平台中的实施差异
*   因为所有团队都讲同一种语言，所以获得了可转移的知识
*   容易增加新功能的团队，减少其他功能的团队
*   由于使用单一语言，加速时间几乎为零
*   失去了优秀的开发者
*   专注于雇佣多面手
*   React —所有平台使用相同的 UI 描述语言和布局语义
*   react——所有东西都封装在一个组件中，而不是分散在不同层的代码
*   使用 [ReactXP](https://microsoft.github.io/reactxp/)

## 任务兔

*   **反应前**
*   每个移动平台上有 3 个开发人员
*   **反应后**
*   2 人团队比 6 人团队移动更快
*   业务逻辑存在于一个地方，两个平台的错误同时得到修复
*   有助于招聘

## 后灯

*   Postlight 有 React (web)经验
*   1.5 开发，在截止日期前完成，且在预算范围内

## 康德纳斯

*   CondeNast 是一家 JS 商店，因此使用 ReactNative 来利用这种体验
*   **为什么用 JS 代替 Swift？**
*   带有 JSX 的声明性布局
*   在 Swift 中编写布局很难
*   非常快速地迭代
*   降低创建布局的门槛
*   将开发人员从“像素推进”中解放出来
*   让他们有时间关注更难和更有趣的事情

# 经验教训

## 后灯

*   能够针对印度市场的低端设备进行性能调整
*   反应性不是规定性的，它使得在需要时放弃反应性并使用本机模块成为可能
*   遇到了性能问题，当你不期望与本机模块(长滚动列表等)相同

## 康德纳斯

*   成为早期采用者
*   经历多次反应性版本碰撞是艰难的
*   更适合正在开发的应用程序，因为一次批量更新很痛苦

最初发表于 sheshbabu.com 的