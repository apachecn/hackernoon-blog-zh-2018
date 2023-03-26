# 你能相信一个 Keras 模型能区分非洲象和亚洲象吗？

> 原文：<https://medium.com/hackernoon/can-you-trust-a-keras-model-to-distinguish-african-elephant-from-asian-elephant-cd1cd3ed58a6>

([评论](https://www.dlology.com/blog/can-you-trust-keras-to-tell-african-from-asian-elephant/#disqus_thread))

![](img/d8b94ec4edeb1764cf143215256eae19.png)

我不会撒谎，但我不久前刚刚学会区分非洲象和亚洲象。

> *据说看大象耳朵的大小就能知道它来自哪里。非洲的耳朵像非洲的地图，亚洲的耳朵更小，像印度的形状。非洲象的耳朵要大得多，可以伸到脖子以上，这是亚洲象所没有的。*
> 
> *—电子慈善*

另一方面，最先进的 ImageNet 分类模型可以以 82.7%的准确率检测 1000 类对象，当然包括这两种类型的大象。ImageNet 模型经过超过 1400 万张图像的训练，可以找出对象之间的差异。

![](img/6aa66e39d96d0f3e6f4e4c60be078305.png)

你想知道当看图像时模型聚焦在哪里，或者我们应该问我们应该相信模型吗？

有两种方法可以解决这个难题。

艰难的方式。通过研究论文，计算数学，实现模型，并希望最终理解它是如何工作的，来破解 ImageNet 模型的艺术状态。

或者

简单的方法。成为模型不可知论者，我们将模型视为黑盒。我们控制了输入图像，所以我们调整它。我们改变或隐藏图像中对我们有意义的部分。然后，我们将调整后的图像提供给模型，看看它会怎么想。

第二种方法是我们将要尝试的，这个奇妙的 Python 库——LIME——本地可解释模型不可知解释的缩写——使它变得很容易。

像往常一样用 pip 安装，

```
pip install lime
```

让我们直接开始吧。

# 选择你想与之建立信任的模式

有许多用于图像分类的 Keras 模型，其权重是在 ImageNet 上预先训练的。您可以在[可用型号](https://keras.io/applications/)中选择一款。

我打算用**试试我的运气。首次下载预训练体重可能需要一段时间。**

# 使用输入图像查找前 5 个预测

我们选择了两只大象并肩行走的照片，这是测试我们模型的一个很好的例子。

代码为模型预处理图像，模型进行预测。

输出并不令人惊讶，亚洲又名印度大象站在前面，占据了图像的相当大的空间，难怪它得到最高分。

```
('n02504013', 'Indian_elephant', 0.9683744)
('n02504458', 'African_elephant', 0.01700191)
('n01871265', 'tusker', 0.003533815)
('n06359193', 'web_site', 0.0007669711)
('n01694178', 'African_chameleon', 0.00036488983)
```

# 向我解释你在看什么

这个模型正在做出正确的预测，现在让我们请它给我们一个解释。

我们首先创建一个 LIME 解释器，它只要求我们的测试图像和`model.predict`函数。

让我们看看这个模型是如何预测印度象的。`model.predict`函数将输出范围从 0 到 999 的每一类指数的概率。

并且 LIME 解释器需要知道我们希望从哪个类索引中得到解释。

我写了一个简单的函数来简化它

原来“印度象”的阶级指数等于 385。让我们请解释者展示一下这个模型的神奇之处。

![](img/820471d77eaeae5078f85d02e3acb5e0.png)

这很有意思，模特也在关注印度象的小耳朵。

非洲象呢？

![](img/9a78562602e74ae91b8b4a83c09050ae.png)

酷，模型预测的时候也在看非洲象的大耳朵。它也在查看文本“AF **RIC** 一只**大象**蚂蚁”的一部分。这是巧合还是模型足够聪明，可以通过阅读图像上的注释来找出线索？

最后，让我们来看看当模型预测一只印度象时有什么“利弊”。

(绿色表示赞成，红色表示反对)

![](img/d02f4813560e84bddfc810c594922451.png)

看起来这个模型也在关注我们的工作。

# 总结和进一步阅读

到目前为止，你可能仍然不知道模型是如何工作的，但至少对它产生了某种信任。这可能不是一件坏事，因为现在您又多了一个工具来帮助您区分好的模型和坏的模型。一个坏模型的例子可能是在预测物体时关注于无意义的背景。

我只是触及了 LIME 能够做的事情的表面，我鼓励你探索其他的应用，比如文本模型，LIME 会告诉你在做决定时模型关注的是文本的哪一部分。

现在继续吧，在一些深度学习模型背叛你之前，重新检查它们。

[LIME GitHub 仓库](https://github.com/marcotcr/lime)

[局部可解释模型不可知解释介绍(LIME)](https://www.oreilly.com/learning/introduction-to-local-interpretable-model-agnostic-explanations-lime)

我的这个实验的完整源代码可以在我的 GitHub 库中找到[。](https://github.com/Tony607/Can_You_Trust_Keras_Model)

[在 Twitter 上分享](https://twitter.com/intent/tweet?url=https%3A//www.dlology.com/blog/can-you-trust-keras-to-tell-african-from-asian-elephant/&text=Can%20you%20trust%20a%20Keras%20model%20to%20distinguish%20African%20elephant%20from%20Asian%20elephant%3F) [在脸书分享](https://www.facebook.com/sharer/sharer.php?u=https://www.dlology.com/blog/can-you-trust-keras-to-tell-african-from-asian-elephant/)

*最初发表于*[*【www.dlology.com】*](https://www.dlology.com/blog/can-you-trust-keras-to-tell-african-from-asian-elephant/)*。*