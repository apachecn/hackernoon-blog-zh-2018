# Android 开发人员的矢量插图基础-第 1 部分:基本形状

> 原文：<https://medium.com/hackernoon/vector-illustration-basics-for-android-developers-part-1-primitive-shapes-5af2e03ad24a>

![](img/dfb36ab56d32604b9f00aee0e10d1634.png)

## 比你想象的容易！

***看这个五部曲系列的第二部《笔画与路径》，*** [***在这里！***](/@sebastian212000/vector-illustration-basics-for-android-developers-part-2-strokes-and-paths-b44118368ffa)

***读第三部分，“布尔运算”，这里有*** [***！***](/@sebastian212000/vector-illustration-basics-for-android-developers-part-3-boolean-operations-8a0ced922030)

***阅读第四部分，“z-排序”，*** [***此处！***](/@sebastian212000/vector-illustration-basics-for-android-developers-part-4-z-ordering-6d1f0928e17)

***阅读第 5 部分，“基本变换”，*** [***此处！*T38**](/@sebastian212000/vector-illustration-basics-for-android-developers-part-5-basic-transformations-bfbe3400ad9)

***阅读加分部分，*** [***在此！***](/@sebastian212000/vector-illustration-basics-for-android-developers-bonus-part-working-with-text-e2bff3cecbed)

## 介绍

相信我:即使你是一名开发人员(尤其是一名单独的应用程序开发人员),拥有基本的矢量插图技能也会非常有用。

作为一个基本规则，为了一致性，我总是建议对一个应用程序使用一个图标包。

但是如果你正在使用的图标包没有你真正需要的对象或概念的图标呢？如果你需要一个“皇冠”的图标呢？或者是“鳄梨”的图标？或者是“括约肌”的图标？

如果(或者更现实地说，*当*便秘鳄梨食用者皇家学会要求你开发他们的应用程序，那你就真的有麻烦了。

事实是，创建特定风格的基本矢量图标和插图比你想象的要容易得多。事实上，我相信通过掌握五个基本概念，你就能自己创造出大部分你需要的东西。

在这个由五部分组成的系列中，我将一次强调一个概念，并举例说明如何有效地使用这个概念。

这五个概念是:

1.  原始形状
2.  中风
3.  布尔运算符
4.  z 排序
5.  基本转换
6.  (奖金概念！)使用文本

对于这个系列，我将使用一个奇妙的矢量工具，叫做 [Gravit Designer](https://www.designer.io/) 。[</shape>](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[**android:shape** 部件定义了一个原始形状，在本例中是一个 *oval* ，我们用它来创建一个圆形或椭圆形。Android 中的其他有效值有*矩形*、*直线*和*圆环。*](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[因此，基本形状是一个基本的简单形状，我们可以用它(与其他形状结合)来创建更复杂的形状。](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[幸运的是，插图应用程序并不局限于 Android 提供的微薄选择。](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[例如，Gravit Designer 提供了*线条、* *矩形、*椭圆、*三角形、*、*星形、*多边形、*形状。其他插图程序提供了更多的基本形状选择，如新月、眼泪和语音气泡。*](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[这是我上面展示的文件夹示例的轮廓视图(没有填充，只有描边),所以你可以看到实际发生了什么](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[![](img/39d50ab3ad4f52be3359f1f37ae12385.png)](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[如你所见，文件夹图标是用圆角矩形创建的。当然，“圆角矩形 3”的右上角有一块被切掉了，但这是你将在第 3 部分中学习到的:“布尔运算符”。再说一次，这比你想象的要简单。](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[在结束这一部分之前，我必须提到，一般来说，这些基本形状都有一些属性，可以在所有矢量插图工具中定制。](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[在我们的例子中，矩形是圆形的，但它们不是必须是圆形的。事实上，在 Gravit Designer 中，预设的形状是一个带有尖角的规则矩形。要使它变圆，你可以操作它的一个属性，叫做“角”。](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[改变这些属性会导致形状的巨大变化。下面，您可以看到 Gravit Designer 中基本形状的概述，并且在每一行中，您将看到它们的一些变化，基于被操纵的属性:](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[![](img/dfb36ab56d32604b9f00aee0e10d1634.png)](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[正如你所看到的，在某些情况下，差异如此之大，以至于原始形状几乎无法辨认。这是我们会多次利用的优势。](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[现在，我建议你打开 Gravit Designer(或者你最喜欢的矢量插图程序),开始摆弄形状和它们的属性。30 分钟应该足够让你熟悉基本形状和它们的属性。](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[然后作为一个练习，看看你是否可以用这些简单的形状开始创造一些更复杂的东西，比如把它们组合起来，比如一支铅笔。如果你真的创造了一支好看(或者难看，无所谓)的铅笔，请在下面的评论中与我分享。](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[关于形状的有用提示:按住 ***按住*** *然后点击并拖动来绘制形状，这将让你创建完美的圆形和正方形。否则，你会画出规则的矩形和椭圆形。*](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[我们明天再见，这是这个简短系列的第二部分，叫做“中风”。不是乐队。](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

# [神圣更新，蝙蝠侠！](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[*由于对这个系列的积极反应和各种读者的要求(谢谢你，你的留言让我开心！)，我已经开始着手编写本系列的书籍版本，内容有所扩展，还有各种练习，可以让你快速高效地入门。*](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[这本书将于 3 月中旬发行，但你现在可以通过访问我的 BuyMeACoffee 页面(下面的链接)和**捐赠超过 3 美元**(显然包括 3 美元)的任何金额来预订。捐赠 6 美元或 9 美元还可以让你在做练习时得到我的电子邮件支持！卡住了？给我发一封电子邮件，我会以极快的速度回复。](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[发行后，**这本书将回到 15 美元的正常价格，并永远保持这个价格(或者至少直到迈克尔·贝拍出一部好电影，实际上是永远)，所以如果你喜欢这些文章，想要一个华丽的版本，有更多的内容和更多糟糕的笑话，你知道该怎么做！(提示:单击下面的链接)**](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[**重要提示:当你捐款时，请给我留下你的电子邮件地址**，这样我就可以联系你，并在书发行后尽快寄给你！](https://medium.com/u/f2de0daa7b3d#FFffff” /> </span><span id=)

[](https://www.buymeacoffee.com/XozUExS) [## 给安东尼斯·查加利斯买杯咖啡——BuyMeACoffee.com

### 我是一名 Android 开发人员和设计师，热爱漂亮的用户界面！

www.buymeacoffee.com](https://www.buymeacoffee.com/XozUExS)