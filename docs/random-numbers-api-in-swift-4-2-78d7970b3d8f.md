# Swift 4.2 中的随机数 API

> 原文：<https://medium.com/hackernoon/random-numbers-api-in-swift-4-2-78d7970b3d8f>

## 随机很好…🎳

![](img/454f9d56b3a91e4f9dcc9a1909d0f6a6.png)

随着 **Swift 4.2** 的发布，苹果在 Swift 的标准库中引入了新的随机 API [SE-0202](https://github.com/apple/swift-evolution/blob/master/proposals/0202-random-unification.md) 。

让我们看看如何将这个 API 用于不同用例的细节。

## 1.给定范围内的随机数

为每个数值数据类型提供了一个`random(in:)`方法，即`Int, UInt, Float, Double`，也为`Bool`数据类型提供了一个方法。

`[random(in:)](https://developer.apple.com/documentation/swift/int/2995648-random)` —返回指定范围内的随机值。

```
Int.random(in: -10...10)   //-6 : Random number in range -10 to 10
```

类似地，我们也可以为其他数值数据类型获取一个随机数。

```
Int.random(in: Int.min...Int.max)   //5995714053130751044
UInt.random(in: 0..<10)             //8
Double.random(in: 0...10)           //0.4317509841160627
Float.random(in: 0...1)             //0.05182791
Bool.random()                       //false 
```

## 2.集合中的随机元素

从一个集合中随机抽取一个元素，例如`array, dictionary or sets`，这是我们经常遇到的事情。

`[randomElement()](https://developer.apple.com/documentation/swift/array/2994747-randomelement)` [](https://developer.apple.com/documentation/swift/array/2994747-randomelement)—返回集合中的随机元素。

该方法适用于 swift 中的每种收款类型。

```
var arr = [1, 2, 4]
arr.randomElement()      //Returns an element from arr randomlyvar dict = ["one": 1, "two": 2, "three": 3]
dict.randomElement() //Returns an element from dict randomly(0...10).randomElement() //Returns an element from range randomly
```

如果集合为空，`randomElement()`返回`nil`。

## 3.混合收藏中的元素

集合中的元素可以使用`shuffled()`或`shuffle()`方法混洗。

`[shuffle()](https://developer.apple.com/documentation/swift/array/2994753-shuffle)` —将集合随机放回原位。

`[shuffled()](https://developer.apple.com/documentation/swift/array/2994757-shuffled)` —返回序列的元素，打乱顺序。

这些方法也适用于 swift 中的每种收款类型。

```
var arr = [1, 2, 4]
arr.shuffled()      //returns a new array - [4, 2, 1]
arr.shuffle()       //shuffles elements in arr itself - [4, 1, 2](0...10).shuffled()   //[3, 2, 5, 7, 10, 9, 1, 6, 8, 4, 0]
```

# 进一步阅读

别忘了阅读我的其他文章:

1.  [Swift 4 中关于 Codable 的一切](https://hackernoon.com/everything-about-codable-in-swift-4-97d0e18a2999)
2.  [你一直想知道的关于 iOS 通知的一切](https://medium.freecodecamp.org/ios-10-notifications-inshorts-all-in-one-ad727e03983a)
3.  [深层拷贝与浅层拷贝——以及如何在 Swift 中使用它们](https://medium.freecodecamp.org/deep-copy-vs-shallow-copy-and-how-you-can-use-them-in-swift-c623833f5ad3)
4.  [iOS 11 编码:如何将&拖放到集合&表格](https://hackernoon.com/drag-it-drop-it-in-collection-table-ios-11-6bd28795b313)
5.  [关于 iOS 10](https://hackernoon.com/app-extensions-and-today-extensions-widget-in-ios-10-e2d9fd9957a8) 中的今日扩展(Widget)你需要知道的一切
6.  [UICollectionViewCell 选择变得简单..！！](https://hackernoon.com/uicollectionviewcell-selection-made-easy-41dae148379d)

如果您有任何问题，请随时发表评论。