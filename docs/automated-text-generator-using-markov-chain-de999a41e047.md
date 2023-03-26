# 使用马尔可夫链的自动文本生成器

> 原文：<https://medium.com/hackernoon/automated-text-generator-using-markov-chain-de999a41e047>

## 请参阅本逐步指南，了解该算法如何与提供的参考代码一起工作。

![](img/eb986f6d52f2c6c47a61751d6cd8fe43.png)

Photo by [Thomas Lefebvre](https://unsplash.com/photos/gp8BLyaTaA0?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

使用马尔可夫链让计算机生成的文本模仿人类的语言是一件令人着迷的事情，实际上并不困难，有时效果令人信服，但肯定很有趣。马尔可夫链的工作原理是基于已知句子的历史元素的重组来生成句子，从而生成有意义的句子。

真正有趣的是，你可以把两个不同人的文本结合起来，得到一个混合的“声音”。

我用两位伟大总统的演讲文本来处理这个问题:

![](img/bb4b58c37d0b5aa911cf36fe6583724e.png)

Image of courtesy of [screentv.com](http://screenertv.com/news-features/president-bartlet-to-president-obama-make-romney-your-cabana-boy-in-new-york/)

我的文本生成器从奥巴马的演讲和脚本中得到的信息如下:

*   我能烧死我在北卡罗来纳州的母亲吗？因为她给了我们一个美好的夜晚。
*   因此，展望未来，我相信我们可以在他们的教堂里制造一枚炸弹。
*   “查理，每次我进来时，我父亲都是在情况室里长大的。''
*   这场运动必须是弹道式的。,

## **进一步的细节和 python 源代码**

你可以在[如何编写马尔可夫文本生成器](https://pythonhowtoprogram.com/how-to-code-a-python-markov-chain-text-generator/)找到关于它如何工作以及如何自己创建一个的详细解释。

否则可以在这里找到源代码[。](https://github.com/pub12/MarkovText)

*感谢阅读！如果你喜欢你所读的，给下面的* ***鼓掌*** *，这样其他人也可以找到这个(你也可以在*[*Twitter*](http://www.twitter.com/pubs12)*)*