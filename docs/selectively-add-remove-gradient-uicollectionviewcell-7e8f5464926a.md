# 选择性添加和移除渐变— UICollectionViewCell

> 原文：<https://medium.com/hackernoon/selectively-add-remove-gradient-uicollectionviewcell-7e8f5464926a>

## 细节不是细节。他们设计了这个图案。

![](img/535f0d005a2b6215cb23e8f5f3ea4680.png)

玩**收藏视图**是如此有趣和具有挑战性。我遇到的一件事是在集合视图中使用渐变。

给`[UICollectionView](https://developer.apple.com/documentation/uikit/uicollectionview)`或`[UICollectionViewCell](https://developer.apple.com/documentation/uikit/uicollectionviewcell)`添加渐变就像给任何其他视图添加渐变一样简单。我们在[之前的文章](https://hackernoon.com/color-it-with-gradients-ios-a4b374c3c79f)中对此进行了详细的讨论。

另一个挑战是，如果我们需要在选择时给一个`UICollectionViewCell`添加渐变，并在取消选择`UICollectionViewCell`时移除渐变，该怎么办。

你觉得这有挑战性吗？让我们一步一步地看看如何实现这一点。

# 先决条件

在我们开始编码之前，让我们看看我们已经有什么，🧐，以及我们如何使用它来解决我们的问题陈述。

1.  **给视图添加渐变**——在[之前的文章](https://hackernoon.com/color-it-with-gradients-ios-a4b374c3c79f)中，我们详细讨论了如何实现这一点。
2.  **改变** `**UICollectionViewCell’s**` **属性基于其选择状态** — [这里](https://hackernoon.com/uicollectionviewcell-selection-made-easy-41dae148379d)我们已经讨论过 ***被选择*** 是如何处理的。

结合以上两点，现在我们只剩下“当单元格被选中时添加渐变，当它被取消选中时移除渐变”。我们已经成功了一半，只需要把我们的知识用在正确的地方。🎯

现在我们知道了实际需要做什么，让我们从一些编码开始。

# 让我们编码吧..

我们的自定义`UICollectionViewCell`，应该实现 2 件事，

1.  我们要在每个单元格上应用的 ***渐变***
2.  覆盖 ***是选择*** 来处理选择状态的改变，即

就是这样。这是我们唯一需要的代码。

# 示例项目

你可以从[这里](https://github.com/pgpt10/CollectionViewGradientSample)下载示例项目。

# 促销

别忘了阅读我的其他文章:

1.  [Swift 4 中关于 Codable 的一切](https://hackernoon.com/everything-about-codable-in-swift-4-97d0e18a2999)
2.  [你一直想知道的关于 iOS 通知的一切](https://medium.freecodecamp.org/ios-10-notifications-inshorts-all-in-one-ad727e03983a)
3.  [iOS 11 编码:如何将&拖放到收藏&表](https://hackernoon.com/drag-it-drop-it-in-collection-table-ios-11-6bd28795b313)
4.  [关于 iOS 10 中的今日扩展(Widget)你需要知道的一切](https://hackernoon.com/app-extensions-and-today-extensions-widget-in-ios-10-e2d9fd9957a8)
5.  [UICollectionViewCell 选择变得简单..！！](https://hackernoon.com/uicollectionviewcell-selection-made-easy-41dae148379d)

如果你有任何疑问，请随时发表评论。