# 反应原生动画加载屏幕:)

> 原文：<https://medium.com/hackernoon/react-native-animated-splash-screen-87b1f8c857e4>

![](img/1c594f476a06d2a07d1ff31cef9f7d73.png)

video

## 我将谈论我在 React Native 中的动画加载屏幕，以及如何使用 React Native 动画 API 轻松构建自己的加载屏幕。

有描述动画 API 的教程。所以，我不想谈这个。我将只谈论我如何使用动画 API 制作我自己的动画。

首先，有四个**组件**你可以使用动画 API 制作动画。

在我的例子中，我想动画化一个视图，一个在另一个视图中的图像。

为了制作动画，你需要做以下事情

## **1。动态值。**

从 react native 导入动画。然后，**创建一个新的动画值对象**。**在构造函数中给这个对象的属性赋值。**您将把它作为动画方法的第一个参数。

## 2.为动画组件提供一个样式属性

让我们转移到主题，我想动画这两个动画组件。

**动画。查看**

**动画。图像**

如果你不懂，也不用担心。我将给出完整的代码示例。

## 要制作组件的动画，需要给组件一个样式属性，就像 CSS 一样。我给出了两种风格:

## truckStyle，scaleText

现在是时候定义这些风格了。我为我的案例使用了变换样式，并根据动画值对其进行了缩放。

## 3 .动画方法，不要忘记启动和停止它们。

动画 API 附带了各种方法。

它们有两个参数。第一:一个动画值，第二:一个配置对象

**使用最多的是:**

`[Animated.timing(](https://facebook.github.io/react-native/docs/animated#timing))`

`[Animated.spring()](https://facebook.github.io/react-native/docs/animated#spring)`

这两个是独立的。它们与其他动画方法一起使用，如:

`[Animated.parallel()](https://facebook.github.io/react-native/docs/animated#parallel)`

`[Animated.sequence()](https://facebook.github.io/react-native/docs/animated#sequence)`

我只在我的案例中使用了上面两个。但是你可以把它们和后两者结合起来，创造出更具动态美感的动画。

我想开始屏幕上的动画渲染。所以，在 ComponentDidMount 中，我用参数调用它们。**配置对象必须具有 toValue 属性。其他值是可选的…**

终于，我用 start()方法启动了动画。

**游戏结束，如下图所示。(是的！我也是漫威的粉丝)**

感谢阅读…完整的代码如下…

[](https://github.com/nahidmbstu/RN-Animated) [## nahidmbstu/RN-动画

### 在 GitHub 上创建一个帐户，为 nahidmbstu/RN 动画开发做贡献。

github.com](https://github.com/nahidmbstu/RN-Animated) 

有关详细信息，请查看文档…

[](https://facebook.github.io/react-native/docs/animated) [## 动画反应原生

### 动画库旨在使动画流畅、强大、易于构建和维护。动画焦点…

facebook.github.io](https://facebook.github.io/react-native/docs/animated)