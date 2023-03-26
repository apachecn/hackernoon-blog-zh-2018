# 如何在 Flutter 中创建自定义布局小部件

> 原文：<https://medium.com/hackernoon/how-to-create-custom-layout-widgets-in-flutter-d9419312a9bd>

![](img/e4256b31083f1cbf8733194c9b5a3886.png)

这篇博客假设你对 Flutter 有初步的了解。否则，开始学习[基础知识](https://flutter.io/docs/)，然后马上回来！

现在，我们如何在 Flutter 中布局小部件？通常我们使用堆栈、行或列。但是如果我们的要求不止这些呢？如果我们想要水平排列一些部件，我们使用一个行部件。如果我们想要垂直排列它们，我们可以将它们包装在一列中，并使用 Stack 将元素排列在另一个之上。但是我们不能期望我们的 UI 就这么简单。例如，我需要一个圆形的小部件，上面有五个等间距的按钮(请看下图！).

![](img/9c4307ae607eeda5c14d637bb1679559.png)

放心吧！我们可以用一个只接受两个参数的 CustomMultiChildLayout 小部件来实现。

```
CustomMultiChildLayout(
  delegate: _CircularLayoutDelegate(
    itemCount: 5,
    Radius: 150.0,
  ),
  children: products,
)
```

1.  delegate 属于 MultiChildLayoutDelegate 类型，负责定位子项。
2.  children —这是要定位的小部件的数组。children 参数类似于 row 和 columns 中使用的参数，但一个不同之处是数组中的每一项都应该用一个布局 id 来标记，以标识该子元素。

```
for (int i = 0; i < 5; i++) {
  products.add( LayoutId(
    id: ‘BUTTON$i’,
    child: item,
  ));
}
```

现在，重要的部分是代表。MultiChildLayoutDelegate 是一个抽象类。所以，我们必须扩展它以供我们使用。覆盖 performLayout 方法，我们就可以编写代码来定位其中的项目。

```
class _CircularLayoutDelegate extends MultiChildLayoutDelegate {
  static const String actionButton = 'BUTTON';
  Offset center;
  final int itemCount;
  final double radius;

  _CircularLayoutDelegate({
    @required this.itemCount,
    @required this.radius,
  });

  @override
  void performLayout(Size size) {
    center = Offset(size.width / 2, size.height / 2);
    for (int i = 0; i < itemCount; i++) {
      final String actionButtonId = '$actionButton$i';

      if (hasChild(actionButtonId)) {
        final Size buttonSize =
            layoutChild(actionButtonId, BoxConstraints.loose(size));
        final double itemAngle = _calculateItemAngle(i);

        positionChild(
          actionButtonId,
          Offset(
            (center.dx - buttonSize.width / 2) + (radius) * Math.cos(itemAngle),
            (center.dy - buttonSize.height / 2) +
                (radius) * Math.sin(itemAngle),
          ),
        );
      }}}

  @override
  bool shouldRelayout(_CircularLayoutDelegate oldDelegate) =>
      itemCount != oldDelegate.itemCount ||
      radius != oldDelegate.radius ;
}
```

1.  performLayout 方法给出了小部件的大小，从中我们可以计算出圆心。
2.  因为我们必须沿着圆圈布置五个小部件，所以使用 for 循环。
3.  请记住，我们创建的每个项目都附有一个 id。所以我们使用相同的 id 来检查该项目是否存在。
4.  现在，使用 layoutChild 方法检索按钮的大小。
5.  使用函数 _calculateItemAngle 计算项目和中心轴之间的角度。

```
const double _radiansPerDegree = Math.pi / 180;
final double _startAngle = -90.0 * _radiansPerDegree;
double _itemSpacing = 360.0 / 5.0;
double _calculateItemAngle(int index) {
 return _startAngle + index * _itemSpacing * _radiansPerDegree;
}
```

6.最后，使用该项的 id 和偏移量调用 positionChild 方法。使用数学公式计算偏移量，

圆心为(x0，y0)，半径为 r 的圆上的角为θ的点为(x0+r cosθ，y0+r sinθ)。

7.调用 shouldRelayout 方法来比较当前布局和旧布局，如果需要重新布局，则返回 true。

所以，这就是在 Flutter 中创建自定义布局有多容易。如果你想了解更多关于我们的颤振经验，请查看我们最近的[博客](https://www.sayonetech.com/blog/why-we-chose-flutter-mobile-app-development/#.W0hxRtIzZPY)。颤振一直是我们的核心专业领域。所以，敬请关注更多类似的更新和信息交流！

你可以在 [GitHub](https://github.com/sayonetech) 中进一步了解我们对颤振的贡献。

*最初发布于*[*https://www . sayone tech . com/blog/how-create-custom-layout-widgets-flutter/*](https://www.sayonetech.com/blog/how-create-custom-layout-widgets-flutter/)*。*