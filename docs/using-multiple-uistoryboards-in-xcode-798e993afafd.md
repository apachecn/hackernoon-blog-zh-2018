# 在 Xcode 中使用多个 UIStoryboards

> 原文：<https://medium.com/hackernoon/using-multiple-uistoryboards-in-xcode-798e993afafd>

## iOS 开发人员，本教程旨在让你忘记构建依赖单一故事板的 iOS 应用程序。

![](img/87df1cda2226daa7c4a30297f294fe48.png)

在过去的几年里， [iOS](https://hackernoon.com/tagged/ios) 项目(以及一般的移动项目)总共包含不超过 10-15 个屏幕。一个单独的故事板绝对是一个好的选择，因为它可以适合所有的屏幕，并且仍然有一个清晰的线框，对于未来和当前的开发者来说是可以理解的。

但是，如今一个 10-15 屏幕的[项目](https://hackernoon.com/tagged/project)被认为是一个小型项目。实际情况是，目前大多数项目包含 40 多个屏幕。这就是它成为一个真正问题的地方。你正在使用一个故事板，你在里面添加了所有的屏幕，突然你开始面临许多性能问题，并且很容易迷失在控制器的海洋中。更不用说未来继承该项目的开发人员的挫折感。

# 解决方案

最近，我开始从事 [CleanSwift (VIP)架构](https://hackernoon.com/introducing-clean-swift-architecture-vip-770a639ad7bf)的工作，灵感来自鲍勃叔叔的书[Clean Code:A Handbook of Agile Software crafts](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)。在我看来，干净的代码是一个革命性的范例，每个人都应该遵循。

我对这个架构做了一些研究和一些测试，找到了处理大量屏幕的最佳和最有条理的方法。👇

## **使用多个故事板**

我现在将向你解释如何在你的下一个项目中使用多重故事板方法。这并不难理解，我希望这种方法对您有用。

出于本教程的目的，我将创建一个场景，其中我们有一个 20+屏幕项目，我们需要建立。屏幕如下:*登录、注册、忘记密码、编辑个人资料、更改密码、文章摘要、文章详情、文章库、文章地图、创建文章、编辑文章、搜索文章、你的文章、书签文章、条款&条件、隐私政策、关于应用、收件箱、搜索收件箱、聊天室、创建聊天室。*

通过分析屏幕的名称，您会注意到它们属于某个特定的组。一个组由具有相同功能的屏幕组成。每个小组将代表一个单独的故事板。如果你还是困惑，只要再陪我一会儿，一切都会明朗。👇

## **根据以上屏幕名称，我们可以创建几个组:**

1.  **认证** - *登录，注册，忘记密码*
2.  **文章-** *文章提要、文章详情、文章库、文章地图、创建文章、编辑文章、搜索文章、你的文章、书签文章。*
3.  **设置** - *编辑个人资料，更改密码，条款&条件，隐私政策，关于 App*
4.  **聊天** - *收件箱，搜索收件箱，聊天室，创建聊天室。*

这意味着我们需要为这个应用程序创建 4 个故事板(验证，帖子，设置，聊天)。这种方法创建了一个清晰易懂的工作流程，即使屏幕数量是原来的两倍。

## 这就是本教程的内容，如果对你有帮助，请👏或者分享这个故事，让其他像你一样的人也能找到它。感谢您的关注！🚀

[![](img/79c8aca42472a7e427ec524f7bc604fb.png)](http://bit.ly/2KkkwGL)

## 查看我的最新项目:

[](https://apps.apple.com/app/football-score-prediction-bets/id1517623538) [## 足球比分预测赌注

### 我们提供专业的足球比分预测投注提示，胜率高。每个比赛模拟包含一个…

apps.apple.com](https://apps.apple.com/app/football-score-prediction-bets/id1517623538) [](https://play.google.com/store/apps/details?id=betting.tips.sportsbet) [## Google Play 上的篮球、曲棍球和手球应用程序的下注技巧

### 超过 500，000 名职业博彩客户、体彩投注者和体育博彩爱好者信任我们的日常体育博彩…

play.google.com](https://play.google.com/store/apps/details?id=betting.tips.sportsbet) [](https://play.google.com/store/apps/details?id=betting.tips.goals) [## BetScore:Google Play 上的足球博彩技巧、比分和赔率应用

### 我们提供高胜率的专业足球技巧。每个比赛模拟包含三个预测…

play.google.com](https://play.google.com/store/apps/details?id=betting.tips.goals) [](https://apps.apple.com/app/long-video-story-for-instagram/id1511499427) [## Instagram 的长视频故事

### Instagram 的 Long Story 是一个非常棒的工具，允许您录制或选择任意长度的视频并进行转换…

apps.apple.com](https://apps.apple.com/app/long-video-story-for-instagram/id1511499427) 

## 阅读更多我在媒体上的作品:

[](https://hackernoon.com/introducing-clean-swift-architecture-vip-770a639ad7bf) [## 引入干净的 Swift 架构(VIP)

### 忘了 MVC 吧，现在！

hackernoon.com](https://hackernoon.com/introducing-clean-swift-architecture-vip-770a639ad7bf) [](https://medium.freecodecamp.org/how-you-can-use-the-google-maps-sdk-with-ios-using-swift-4-a9bba26d9c4d) [## 使用 Swift 4 在 iOS 上使用 Google Maps SDK 的终极指南

### 许多 iOS 应用程序使用谷歌地图。这是一个非常普遍的特点，所以我决定准备一个终极指南…

medium.freecodecamp.org](https://medium.freecodecamp.org/how-you-can-use-the-google-maps-sdk-with-ios-using-swift-4-a9bba26d9c4d) [](/theappspace/swift-custom-uiview-with-xib-file-211bb8bbd6eb) [## SWIFT —带有 XIB 文件的自定义 UIView

### 用 XIB 文件定制 UIView 是 iOS 开发中非常常见的做法。自定义 UIView 类不包含 XIB 文件…

medium.com](/theappspace/swift-custom-uiview-with-xib-file-211bb8bbd6eb) [](https://hackernoon.com/how-to-add-spotlight-support-to-your-ios-app-4a89054aff89) [## 如何将 Spotlight 支持添加到您的 iOS 应用程序

### Swift 教程将使您的应用程序在 Spotlight search 中可用

hackernoon.com](https://hackernoon.com/how-to-add-spotlight-support-to-your-ios-app-4a89054aff89) [](https://hackernoon.com/core-data-relationships-d813ed66ba8c) [## 核心数据关系

### 理解一对一和一对多关系

hackernoon.com](https://hackernoon.com/core-data-relationships-d813ed66ba8c) [](https://hackernoon.com/understanding-auto-layout-in-xcode-9-2719710f0706) [## 了解 Xcode 9 中的自动布局

### 所有你需要知道的关于自动布局

hackernoon.com](https://hackernoon.com/understanding-auto-layout-in-xcode-9-2719710f0706) 

## 订阅我的时事通讯: