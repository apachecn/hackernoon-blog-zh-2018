# 如何在 Cocos2d-x (Android)中禁用多点触控

> 原文：<https://medium.com/hackernoon/how-to-disable-multitouch-in-cocos2d-x-android-2888172f863d>

![](img/a44aa895c8ce399d379466c1673ef953.png)

Cocos2d-x(3.14 版之前)有一个问题。如果你想在 Android 中使用单点触摸，你必须在 Cocos2d-x 中注释代码。但是当你用新版本替换 Cocos2d-x 时，你必须记得再次注释代码。本文将回答如何禁用所有版本的 Cocos2d-x 的多点触摸，而不评论代码。

如何通过注释这里讨论的代码[来禁用多点触摸](https://discuss.cocos2d-x.org/t/how-to-disable-multitouch/4959/9?u=pearson)。

合乎逻辑的假设是，为了解决这个问题，有必要移除 id 大于 0 的所有触摸。怎么做呢？

在安卓系统中，所有的触摸都要通过 **Cocos2dxGLSurfaceView** 。让我们考虑动作触地的情况。

我们只需要使用 **idDown == 0** 的触控。但是有一个问题。 **mCocos2dxRenderer** 是私有成员，当我们继承 **Cocos2dxGLSurfaceView** 时，我们不能使用 **mCocos2dxRenderer** 。

让我们考虑 **Cocos2dxRender** 方法 **handleActionDown、handleActionMove** 、 **handleActionUp** 和 **handleActionCancel** 。

这个方法使用了 **Cocos2dxRender:** 的静态方法

幸运的是，我们使用 Java。在 Java 中，我们可以访问私有方法。让我们编写访问私有方法的方法。

现在要继承 **Cocos2dxGLSurfaceView** ，使用 **Cocos2dxRender** 的静态方法，剪切 touches。**真菌 2dxGLSurfaceView 的完整代码:**

我们应该在**AppActivity.java**中改变方法 **onCreateView**

在[讨论过](https://discuss.cocos2d-x.org/t/how-to-disable-multitouch-in-cocos2d-x/245/13)这个问题后，Cocos2d-x 开发者在版本≥3.14 的 **Cocos2dxGLSurfaceView** 中增加了一个方法**setmultipletuchenabled**。现在单点触摸是这样完成的:

好的。我们对不同版本的 Cocos2d-x 进行了单点触摸。