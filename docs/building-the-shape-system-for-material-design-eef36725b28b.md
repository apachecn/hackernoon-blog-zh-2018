# 谷歌如何建立材料设计的形状系统

> 原文：<https://medium.com/hackernoon/building-the-shape-system-for-material-design-eef36725b28b>

## 我是 Yarden，是 Material Design 的 iOS 工程师，Material Design 是谷歌设计和构建优秀用户界面的开源系统。我帮助建立和维护我们的 iOS 组件，但我也是材料形状系统的工程负责人。

![](img/c20d6dd585ed080581c0c968258f5e8d.png)

# 形状:这是一件大事

没有形状就没有 UI。卡片、按钮、表单、文本字段——以及你在屏幕上看到的几乎所有东西——通常都显示在某种“表面”或“容器”中在计算的大部分历史中，这意味着矩形。很多长方形。

但材料团队知道，让设计师和开发人员有能力在我们所有的材料设计 UI 组件中系统地应用独特的形状是有潜力的。圆角！角度切割！对于设计师来说，这意味着能够创造漂亮的界面，更擅长于引导注意力、表达品牌和支持互动。对于开发人员来说，在所有主要平台上拥有一致的形状支持意味着我们可以轻松地跨应用程序应用和定制形状。

我作为工程主管的角色确实令人兴奋——我与我们的设计主管合作来确定项目范围，并找到创建这个复杂新系统的最佳方式。与字体和颜色系统(有清晰的结构和先例，如网络的 H1-H6 类型层次，或原色/二次色的概念)相比，形状是狂野的西部。这是一个相对未开发的领域，规则和最佳实践仍有待定义。为了迎接这一挑战，我开始与所有不同的材料设计工程平台合作，以确定可能的障碍，确定工作范围，并建立它！

构建系统时，我们有两个高层次的目标:

*   为我们的组件添加形状支持——使开发人员能够定制按钮、卡片、芯片、薄片等的形状。
*   定义和开发一种使用 shape 为我们的组件设置主题的好方法——这样开发人员可以一次设置他们产品的 shape story，并通过他们的应用程序进行级联，而不需要单独定制每个组件。

从工程的角度来看，增加形状支持承担了大部分的工作和复杂性，而主题化有更多的设计驱动的挑战。在这篇文章中，我将主要关注工程工作以及我们如何给组件添加形状支持。

以下是我将在此介绍的内容概要:

*   确定形状支持功能的范围
*   跨平台构建一致的形状支持很难
*   在 iOS 上实现形状支持:
*   *—形状核心实现*
*   *—为组件添加形状支持*
*   在组件上应用自定义形状
*   最后的话

# 确定形状支持功能的范围

我们很快发现，没有限制会使定义这样一个系统变得极其困难。更多的灵活性不一定意味着更好的结果。例如，定义一个灵活而简单的 API 来制作蛇形和火车形的卡片是一件非常了不起的事情。但这些元素几乎肯定会与材料设计指南所倡导的清晰而直接的方法相矛盾。

![](img/1bbf35c52bede1b884703e04c3ce1d89.png)

This truck-shaped FAB is a definite “don’t” in Material Design guidance.

我们必须权衡时间和资源的开销与我们所能提供的每项功能的附加值。

为了解决这些开放性问题，我们决定召开为期一周的研讨会，包括来自设计、工程和模具部门的团队成员。事实证明这非常有效。即使有很多输入，我们也能够磨练出哪些功能是可行的，对我们的用户最有影响。我们最终的提议是让最初的系统支持三种形状:方形、圆形和切割形。这些形状可以通过 API 定制组件的角来实现。

![](img/dcaac1747186834460693b854a40c1eb.png)

# 跨平台构建一致的形状支持(这很难)

任何为多个平台构建的人都知道**一致性是关键**。但在我们的研讨会期间，我们意识到为我们所有的平台提供完全相同的功能是多么困难:Android、Flutter、iOS 和 web。我们最大的阻碍？走捷径在网上工作。

与尖角或圆角不同，切角在 web 上没有内置的本地解决方案。

我们的网络团队考虑了一系列解决方案——我们甚至考虑了在每个角上添加背景色方块的想法，以掩盖它，使它看起来被切割了。当然，它的缺点也很明显:阴影被掩盖了，当背景不是静态的或者有不止一种颜色时，方块本身需要充当变色龙。

![](img/ad06e3922502370e022fe81656b26070.png)

然后，我们调查了 [Houdini(画图工具)API](https://goo.gl/hQDbAB) 和 [polyfill](https://goo.gl/FiDfpJ) ，这最初看起来是一个可行的解决方案，实际上是可行的。但是，添加这种支持需要额外的工作:

*   我们的 UI 组件使用阴影来显示高度，新的画布阴影看起来与原生 CSS box-shadow 不同，这需要我们在整个系统中重新实现阴影。
*   我们的 UI 组件在被点击时还会显示出视觉涟漪效果——以示棘手。对于我们来说，继续在 paint worklet 中使用 ripple，我们需要重新实现它，因为没有跨浏览器的遮罩解决方案不会提供显著的性能提升。

即使我们决定增加更多的工程努力，沿着胡迪尼的道路走下去，价值与成本的问题仍然存在，特别是胡迪尼在整个网络生态系统中仍然“没有准备好”。

基于我们的研究和对努力成本的权衡，我们最终决定在不支持 web UIs 的情况下*(至少目前是这样)。但是好消息是我们已经详细说明了需求，并且可以开始构建了！*

# 在 iOS 上实现形状支持

在完善了特性集之后，每个平台的工程师就可以开始构建了。我帮助构建了对 iOS 的形状支持。我们是这样做的:

## 核心实现

在 iOS 中，用户界面的基本构建块基于`UIView`类的实例。每个`UIView`都由一个`CALayer`实例支持，以管理和显示其可视内容。通过修改`CALayer`的属性，您可以修改其视觉外观的各种属性，如颜色、边框、阴影以及几何图形。

当我们提到`CALayer`的几何形状时，我们总是以矩形的形式谈论它。

它的框架是由位置的(x，y)对和大小的(宽度，高度)对构成的。操纵层的矩形形状的主要 API 是通过设置它的`cornerRadius`，它接收一个半径值，并依次设置它的四个角为该值的圆角。矩形背景和圆角的简单 API 的概念在 Android、Flutter 和 web 上普遍存在。但是像偷工减料和定制边缘这样的事情通常不那么简单。为了能够提供这些特性，我们构建了一个形状库，它提供了一个生成器来创建具有特定的、定义良好的形状属性的`CALayer`。

谢天谢地，苹果为我们提供了类`CAShapeLayer`，它是`CALayer`的子类，有一个`customPath`属性。将这个属性分配给一个自定义的`CGPath`允许我们创建任何我们想要的形状。

考虑到路径功能，我们构建了一个类，它利用了`CGPath`API，并提供了用户在构建组件时所关心的属性。下面是 API:

通过提供这样一个 API，用户可以只为一个角或一条边生成一条路径，上面的`MDCRectangleShapeGenerator`类将创建一个考虑到这些属性的形状。对于我们的初始形状系统的这个初始实现，我们只使用了角属性。

如您所见，角本身由类`MDCCornerTreatment`组成，它封装了三条重要信息:

*   角的值(每个特定的角类型都有一个值)。
*   所提供的值是表面高度的百分比还是绝对值。
*   基于给定值和角类型返回路径生成器的方法。这将为`MDCRectangleShapeGenerator`提供一种方法来接收角的正确路径，然后它可以将该路径添加到形状的整体路径中。

为了使事情变得更简单，我们不希望用户必须通过计算拐角路径来构建自定义拐角，所以我们为生成圆角、弯曲角和切角的`MDCCornerTreatment`提供了 3 个方便的子类。

例如，我们的切割角处理接收一个称为“切割”的值，它基于从角的边缘开始并在 X 轴和 Y 轴上走相等距离的 UI 点的数量来定义切割的角度和大小。如果形状是一个大小为 100x100 的正方形，我们将它的所有角都设置为`MDCCutCornerTreatment`，切割值为 50，那么最终结果将是一个大小为 50x50 的菱形。

以下是切角处理实现路径生成器的方式:

切割角的路径只关心决定切割的 2 个点(角的每条边上各有一个)。点是(0，切割)和(sin(角度)*切割，cos(角度)*切割)。在我们的例子中，因为我们只讨论其角为 90 度的矩形，后一点相当于(cut，0)，其中 sin(90) = 1，cos(90) = 0。

以下是圆角处理实现路径生成器的方式:

从(0，半径)的起点，我们绘制一个圆弧到(sin(角度)*半径，cos(角度)*半径)的点，类似于切割示例，转换为(半径，0)。最后，半径值是圆弧的半径。

## 为组件添加形状支持

在为`MDCRectangleShapeGenerator`提供了设置角和边的方便 API 之后，我们需要为每个组件添加一个属性来接收形状生成器并将形状应用到组件。

现在，每个受支持的组件在其 API 中都有一个`shapeGenerator`属性，该属性可以接收一个`MDCShapeGenerator`或任何不同的实现了`pathForSize`方法的形状生成器:给定组件的宽度和高度，它返回形状的一个`CGPath`。我们还需要确保生成的路径被应用到组件的`UIView`的底层`CALayer`中，以便显示。

通过在组件上应用形状生成器的路径，我们必须记住一些事情:

**添加适当的阴影、边框和背景颜色支持**

因为阴影、边框和背景颜色是默认的`UIView` API 的一部分，不需要考虑自定义的`CALayer`路径(它们遵循默认的矩形边界)，我们需要提供额外的支持。所以我们实现了`MDCShapedShadowLayer`作为视图的主`CALayer`。该类的作用是获取形状生成器路径，然后将该路径作为图层的阴影路径，这样阴影将跟随自定义形状。它还提供了不同的 API 来设置背景颜色和边框颜色/宽度，方法是在保存自定义路径的`CALayer`上显式设置值，而不是调用顶级的`UIView`API。例如，当设置背景颜色为黑色时(而不是调用`UIView`的`backgroundColor`)，我们调用`CALayer`的`fillColor`。

**注意设置图层的属性，如阴影路径和圆角半径**

因为形状的图层设置不同于视图的默认图层，所以我们需要注意在现有组件代码中设置图层属性的位置。例如，设置组件的`cornerRadius`——这是使用 Apple 的 API 设置圆角的默认方式——如果您还设置了自定义形状，实际上是不适用的。

**支持触摸事件**

接收触摸也仅适用于视图的原始矩形边界。使用自定义形状，我们会遇到矩形边界内没有绘制层*的地方，或者边界外绘制了层*的地方。因此，我们需要一种方法来支持适当的触摸，对应于形状的位置，并相应地采取行动。**

为了实现这一点，我们覆盖了我们的`UIView`的`hitTest`方法。`hitTest`方法负责返回应该接受触摸的视图。在我们的示例中，我们实现了它，因此如果触摸事件包含在生成的形状路径中，它将返回自定义形状的视图:

**墨水波纹支持**

与其他属性一样，我们的墨迹波纹(作为触摸反馈向用户提供波纹效果)也构建在默认的矩形边界之上。对于墨迹，我们更新了两件事:1)对边界的屏蔽。在形状小于或大于边界的情况下，必须更新`maxRippleRadius`。在这些情况下，我们不能依赖边界，因为对于较小的形状，墨水会波动得太快，而对于较大的形状，波纹不会覆盖整个形状。墨水层的`maskToBounds`也需要设置为 NO，这样当自定义形状大于默认边界时，我们可以允许墨水扩散到边界之外。

# 将自定义形状应用于组件

完成所有实现后，下面是如何为材料按钮组件提供捷径的每个平台的示例:

**安卓:**

**颤振:**

**iOS:**

**腹板(圆角):**

# 最后的话

我真的很兴奋能够解决这个问题，并让它成为材料设计系统的一部分。我特别高兴能与设计团队如此合作。作为一名工程师，我或多或少倾向于从相似的角度解决问题，也和其他工程师一样思考问题。但当与设计师一起解决问题时，感觉挑战实际上是从所有正确的角度来看的(双关语)，解决方案往往会更好，更周到。

我们处于良好的状态，可以继续发展材料形状系统，并为边缘处理和更复杂的形状提供更多支持。有一天(当胡迪尼准备好的时候)我们甚至能够支持网络上的捷径。

请在 GitHub 上跨平台查看我们的代码: [Android](https://goo.gl/fpwhmZ) ， [Flutter](https://goo.gl/AVRsSs) ， [iOS](https://goo.gl/iXkmAa) ， [Web](https://goo.gl/deomkm) 。并查看我们最新更新的[形状](https://goo.gl/9StPZB)材料设计指南。

*原载于 2018 年 12 月 18 日*[*【developers.googleblog.com】*](https://developers.googleblog.com/2018/12/building-shape-system-for-material.html)*。*